

**Access Checks**
- Performed by **SRM** via `SeAccessCheck` using: **thread token** (SIDs, privs), **requested access mask**, **object security descriptor**.    
- Triggered on **open by name** (`ObpCreateHandle` → `ObpGrantAccess` → `ObCheckObjectAccess`) or **handle reuse** (`ObReferenceObjectByHandle`).    
- Objects with _default security_ store SD in header (method = `SeDefaultObjectMethod`). Others (e.g., files) use custom method (NTFS driver).    
- **Rule**: request only the access you need — broad masks (`PROCESS_ALL_ACCESS`) fail more often and are risky if leaked to other threads.    

---

**Security Identifiers (SIDs)**
- Unique numeric IDs for principals (users, groups, machines, services). Format: `S-Rev-Auth-SubAuth...-RID`.
- **Well-known SIDs**: same on all systems (e.g., Everyone = `S-1-1-0`, Network = `S-1-5-2`, Admin = RID 500).
- **Machine SID** issued at OS install → local accounts = MachineSID + RID.
- **Domain SID** issued at DC promotion → domain accounts = DomainSID + RID.
- **Logon SIDs**: `S-1-5-5-X-Y`, unique per interactive session; used in ACEs to allow session-lifetime access.    

---

**Virtual Service Accounts**
- Format: `NT SERVICE\<ServiceName>` → unique SID per service.
- Auto password management, no group membership, appear in ACLs like normal accounts.
- Isolation benefit over shared built-ins (LocalService, NetworkService).

---

**Security Descriptors (SDs) & Access Control**

- SD fields: **Owner SID**, **Group SID**, **DACL** (who has access), **SACL** (who is audited), plus revision & flags.
- **DACL** = ordered ACEs (allow/deny, optional object GUIDs for AD, callback types for AuthZ).
    - Null DACL = full access to everyone.
    - Empty DACL = no access.
- **SACL** = audit ACEs (log success/failure for operations).
- **Inheritance**: ACE flags define propagation to children (dirs, registry keys).
- Stored in object header (if using default security) or custom by subsystem (e.g., NTFS).