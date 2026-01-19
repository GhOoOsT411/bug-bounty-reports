# IDOR Leading to Vertical Privilege Escalation via Company Update Endpoint

## Severity
Medium (P3)

## Vulnerability Type
Broken Access Control / Insecure Direct Object Reference (IDOR)

## Description
A low-privileged user with no assigned role is able to update sensitive company information by reusing an owner-level company update endpoint.

The backend does not properly validate the user’s privilege level before processing the request. As a result, unauthorized users can modify company data, leading to **vertical privilege escalation**.

---

## Affected Component
Company management API endpoint (company update functionality)

---

## Attack Scenario
The application contains multiple user roles with different privilege levels:
- **Owner** (high-privileged user)
- **No-role user** (low-privileged user)

A low-privileged user can reuse the same endpoint used by an owner to update company information without proper authorization checks.

---

## Steps to Reproduce

1. Authenticate as a **low-privileged (no-role) user**
2. Intercept any authenticated request and keep the session active
3. Authenticate as an **owner** in a separate session
4. Capture the request used by the owner to update the company name:
```http
PUT /api/company/{company_id}
Content-Type: application/json

{
  "new_name": "Company Name Updated by Owner"
}
```
5. Reuse the same endpoint and request structure using the low-privileged user session
6. Modify the request body: 
```http
PUT /api/company/{company_id}
Content-Type: application/json

{
  "new_name": "Company Name Updated by Low Privileged User"
}
```
7. Send the request
8. The company name is successfully updated despite insufficient privileges

## Proof of Concept (PoC)

The backend does not enforce role-based authorization checks on the company update endpoint.
Any authenticated user can modify company information by directly referencing the company identifier and reusing owner-level functionality.

## Impact

- A low-privileged attacker can:
- Modify sensitive company information
- Tamper with internal business records
- Cause loss of data integrity
- Lead to reputational damage
- Potentially introduce compliance or regulatory risks
