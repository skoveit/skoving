# 2 Zero-Days in sliver! What if I found your Sliver payload?
I’ve been working on a feature to allow operators to move a running implant between servers on the fly; no fresh builds, no re-execution.

Through [PR1](https://github.com/BishopFox/sliver/pull/2175), [PR2](https://github.com/BishopFox/sliver/pull/2220) and [PR3](https://github.com/BishopFox/sliver/pull/2195), I implemented a way to dynamically switch C2 endpoints at runtime via a `reconfig --c2-uri` command. Whether it’s switching from `mTLS` to `HTTP` or moving to a different server entirely, it’s now possible; If you haven't tried it yet, give it a spin.

To build that migration feature, I had to spend days deep-diving into Sliver’s transport layer and how the server handles internal state. I was taking notes on places that could be interesting, and then I returned to these notes.

The first one was a piece of code that checks an object but doesn't check its children. In Go, the runtime will panic if you try to access a nil pointer.

## 1- Remote Runtime Panic ([CVE-2026-29781](https://github.com/skoveit/CVE-2026-29781))
**CVSS v3.0 Score: 7.7 (High)** > `CVSS:3.0/AV:N/AC:L/PR:L/UI:N/S:C/C:N/I:N/A:H`

> CWE-476 (NULL Pointer Dereference), CWE-248 (Uncaught Exception).


In Go's `proto3` implementation, if you send a message and omit a nested sub-message, Go unmarshals it perfectly fine but leaves that nested pointer as `nil`.

Sliver’s handler was taking the raw envelope data, unmarshalling it, and immediately attempting to read `beaconReg.Register.Uuid` **without checking if `Register` was `nil`**.

I had to test it. I crafted a malicious `BeaconRegister` payload, deliberately stripped the `Register` field.

**Attempt 1 (HTTP Transport):** Nothing happened. The connection closed, but the server survived. I was confused! why didn't it crash? After some research, I found the "invisible hero": Go’s standard `net/http` library. It has a built-in `recover()` middleware that catches panics and isolates them to that single request. _But Is this protection everywhere?_

**Attempt 2 (mTLS, WireGuard, DNS):** I pointed the exploit at the multiplexed transports. These listeners handle streams in raw, unprotected goroutines. The moment the server tried to dereference the missing pointer, it triggered a massive runtime panic!

The entire Go runtime crashed. Every single active session across the entire C2 fleet dropped instantly. All operators were evicted. It was a literal infrastructure kill-switch triggered by a single malformed packet.

> While severity can sometimes be subject to **personal judgment** regarding trust boundaries, this score reflects the reality of **Adversary Simulation**. In this framework, implants are deployed in hostile environments by design. A single compromised beacon (implied `PR:L`) should not grant the ability to trigger a total loss of availability (`A:H`) for the entire C2 infrastructure.

[Official Advisory GHSA-hx52-cv84-jr5v](https://github.com/BishopFox/sliver/security/advisories/GHSA-hx52-cv84-jr5v)
## 2- Unauthenticated Memory Exhaustion ([CVE-2026-32941](https://github.com/skoveit/CVE-2026-32941))
**CVSS v3.0 Score: 7.7 (High)** > `CVSS:3.0/AV:N/AC:L/PR:L/UI:N/S:C/C:N/I:N/A:H`

> CWE-770 (Allocation of Resources Without Limits), CWE-400 (Uncontrolled Resource Consumption).

Second note was a line in `socketReadEnvelope` (mTLS) and `socketWGReadEnvelope` (WireGuard). It uses a 4-byte length prefix to tell the server how big the incoming message is.

Here was the logic flow:
1. Read the 4-byte length.
2. Check if the length is less than `ServerMaxMessageSize` (which was set to a whopping **~2 GiB**).
3. **Allocate the buffer in memory.**
4. Read the data.
5. _Then_ verify the Ed25519 signature.

The server allocates memory _before_ proving the message is cryptographically valid. Sliver uses Yamux, which allows up to 128 concurrent multiplexed streams over a single connection.

I wrote a quick PoC:
- Connect with a valid extracted implant cert.
- Open 128 parallel Yamux streams.
- On every stream, send a fake 4-byte prefix claiming a payload size of `0x7FFFFFFF` (2 GiB).
- Watch the fireworks.

The server obediently tried to run `make([]byte, 2GB)` 128 times simultaneously. It demanded **~256 GiB of RAM** in a fraction of a second. The host OS's Out-Of-Memory (OOM) killer immediately stepped in and assassinated the Sliver process.


[Official Advisory GHSA-97vp-pwqj-46qc](https://github.com/BishopFox/sliver/security/advisories/GHSA-97vp-pwqj-46qc)

## SliverBane
After spending weeks deep in Sliver’s internals, I realized I had the perfect blueprint to build a Counter-C2 framework.

**SliverBane** is a Counter-C2 framework. it’s a deception engine. It connects to an active Sliver server and spawns sessions and fake implants that are indistinguishable from real ones.

To an operator, these ghosts look identical to legitimate compromised hosts, complete with realistic hostnames, process trees, and a virtual filesystem of 150+ files. While the operator is busy chasing shadows and interacting with my ghosts, their database is being polluted and their time is being burned. 

**SliverBane** comes pre-loaded with pluggable DoS modules, including the **Nil-pointer panic** and the **OOM death blow** we discussed. One command, and the target infrastructure goes dark.

But it doesn't just waste their time, it watches them back. Acting as a high-interaction honeypot, SliverBane logs exactly what the attackers are actually doing. Every command they run, every payload they try to drop, and every lateral movement attempt is recorded, handing you direct, actionable intelligence on their tactics.

**Check it out here:** [skoveit/SliverBane](https://github.com/skoveit/SliverBane)

_Thanks for reading. If you're defending against C2s or just like breaking things, go star the repo, spin it up, and let me know what you think!_