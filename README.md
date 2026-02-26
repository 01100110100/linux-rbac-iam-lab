# Linux RBAC IAM Lab
## 1. Project Overview
This project simulates Role-Based Access Control (RBAC) using native Linux identity and permission mechanisms.
The objective was to design and validate controlled access to sensitive resources using:
Unique user identities (UID)
Role-based grouping via GID
Permission enforcement (rwx model)
Least privilege principle
Real session-based access testing

## This lab demonstrates practical IAM fundamentals using Linux as a security enforcement system.

## 2. Environment
OS: Ubuntu (Virtual Machine)
User management: /etc/passwd, /etc/group
Access control model: Unix permission bits (rwx)

## Commands used:
- groupadd
- useradd
- chown
- chmod
- su
- ls -l

## 3. Architecture Design 
### 3.1 logical Access Flow
1. User authenticates into system (Authentication).
2. System identifies user UID and group memberships (GID).
3. User attempts to access protected resource.
4. Kernel validates permission bits (owner/group/others).
5. Access is granted or denied based on least privilege.

### 3.2 Role Structure

The RBAC model in this lab is based on a single role abstraction:

- Role Name: finance
- Group ID (GID): XXXX2
- Authorized User: mack
- Unauthorized User: anis

Only users assigned to the `finance` group are permitted to access protected financial resources.



### 3.3 Protected Resource Design

A dedicated directory was created to simulate a restricted financial data environment.

Directory: /finance  
Owner: root  
Group: finance  
Permissions: drwxrwx---  

File: /finance/budget.txt  
Owner: root  
Group: finance  
Permissions: -rw-r-----  

Security Design Rationale:

- Owner (root) maintains administrative control.
- Group (finance) is granted controlled access.
- Others have no permissions.
- File-level permissions are stricter than directory-level permissions.
- Least privilege principle is enforced.



## 4. Implementation Steps

### 4.1 Create Role Group

A dedicated group named `finance` was created to represent the finance role within the RBAC model.

```bash
sudo groupadd finance
```


### 4.2 Create Users

Two users were created to simulate role-based access:

- mack (authorized finance member)
- anis (non-finance user)

```bash
sudo useradd mack
sudo useradd anis
```

The authorized user was added to the finance group:

```bash
sudo usermod -aG finance mack
```

Group membership verification:

```bash
id mack
id anis
```



### 4.3 Directory Setup

A protected directory was created and assigned group ownership to the finance role.

```bash
sudo mkdir /finance
sudo chown root:finance /finance
sudo chmod 770 /finance
```

This ensures:

- Only root and finance group members can access the directory.
- No access is granted to others.



### 4.4 File Permission Enforcement

A sensitive file was created inside the protected directory.

```bash
sudo touch /finance/budget.txt
sudo chgrp finance /finance/budget.txt
sudo chmod 640 /finance/budget.txt
```

Permission breakdown:

- Owner: Read + Write
- Group: Read
- Others: No access



### 4.5 Access Testing

Access validation was performed using real user session switching.

Authorized access:

```bash
su - mack
cat /finance/budget.txt
```

Result:
Access granted.

Unauthorized access:

```bash
su - anis
cat /finance/budget.txt
```

Result:
Permission denied.

This confirms correct RBAC enforcement.



## 5. Security Principles Demonstrated

### 5.1 Least Privilege

Users were granted only the minimum level of access required to perform their role.

- mack → Read-only access
- anis → No access



### 5.2 Role-Based Access Control (RBAC)

Access rights are not assigned directly to users.  
Instead, permissions are assigned to a group (role), and users inherit privileges through group membership.



### 5.3 Separation of Duties

Administrative control (root ownership) is separated from operational access (finance group).



### 5.4 Kernel-Level Enforcement

All access decisions are enforced by the Linux kernel using:

- UID comparison
- GID comparison
- Permission bit validation

No application-layer enforcement was used.



## 6. Conclusion

This lab demonstrates how native Linux identity and permission mechanisms can be used to implement a simplified but functional Role-Based Access Control system.

Through controlled group membership, strict file permissions, and session-based testing, the system successfully enforces least privilege and prevents unauthorized access.

This implementation reflects foundational IAM principles used in enterprise security environments.