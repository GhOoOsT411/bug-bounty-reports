# IDOR Leading to Vertical Privilege Escalation via User Name Update Endpoint

## Severity
Medium (P3)

## Vulnerability Type
Broken Access Control / Insecure Direct Object Reference (IDOR)

## Description
A low-privileged user with no assigned role is able to update another user’s profile name by reusing an owner-level endpoint.

The backend does not properly enforce authorization checks to validate the user’s privilege level before processing the request. As a result, unauthorized users can modify sensitive user attributes, leading to **vertical privilege escalation**.

---

## Affected Component
User profile management API (user attribute update functionality)

---

## Attack Scenario
The application contains multiple user roles with different privilege levels:
- **Owner** (high-privileged user)
- **No-role user** (low-privileged user)

Both roles interact with the same backend endpoint for updating user profile attributes, without proper role-based access control enforcement.

---

## Steps to Reproduce

1. Authenticate as an **owner** and capture the request used to update a user’s name:
```http
POST /api/user/update-name
Content-Type: application/json

{
  "user_id": "REDACTED",
  "name": "Updated by Owner"
}
```
2. Authenticate as a low-privileged (no-role) user
3. Reuse the same endpoint and request structure
4. Modify the request body:
```http
POST /api/user/update-name
Content-Type: application/json

{
  "user_id": "REDACTED",
  "name": "Updated by Low Privileged User"
}
```

5. Send the request using the low-privileged session
6. The user name is successfully updated despite insufficient privileges

## Proof of Concept (PoC)

The backend fails to verify whether the authenticated user has sufficient privileges to update another user’s profile attributes.
Any authenticated user can reuse an owner-level endpoint to modify user information.

## Impact

A low-privileged attacker can:
- Modify other users’ profile information
- Tamper with user identity data
- Cause loss of data integrity
- Abuse trusted account functionality
- Undermine access control mechanisms
