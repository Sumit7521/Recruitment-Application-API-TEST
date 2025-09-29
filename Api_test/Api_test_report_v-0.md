# API Test Report – Version 0

**Project**: Recruitment App  
**Module**: Authentication & User Profile  
**APIs Covered**: Register, Login, Profile Fetch, Profile Update  
**Tester**: Sumit  
**Date**: 30-Sep-2025  
**Environment**: Localhost 

---

## Summary

- **Total Test Cases**: 17  
- **Passed**: 11  
- **Failed**: 4  
- **Pending**: 2  


---
## Observations & Defects

1. **Login**  
   - Wrong password still logs in (Security bug).  
   - Short password accepted (Policy mismatch with Register API).  

2. **Profile Fetch & Update**  
   - Returning `403 Access Denied` instead of proper profile access for valid JWT.  
   - Possible role/permission misconfiguration.  

---

## 1. Register API

**Endpoint**: `POST /api/auth/register`  
**Description**: Register a new user account  

| Test Case ID | Scenario | Input | Expected Result | Actual Result | Status | Remarks |
|--------------|----------|-------|-----------------|---------------|--------|---------|
| REG-001 | Valid user registration | Valid details | 201 Created | 201 Created | ✅ Pass | - |
| REG-002 | Duplicate email | Existing email | 409 Email already exists | 409 Email already exists | ✅ Pass | - |
| REG-003 | Invalid email format | `"email": "abc.com"` | 400 Invalid email | 400 Invalid email | ✅ Pass | - |
| REG-004 | Password too short | `"password": "ab"` | 400 Password must be at least 4 characters | 400 Error received | ✅ Pass | - |
| REG-005 | Missing required fields | Removed fields one by one | 400 Bad Request with field-specific message | Got 400 with correct field message | ✅ Pass | - |
| REG-006 | Empty strings | `""` for all fields | 400 Bad Request with not allowed empty messages | Got 400 with all empty-field errors | ✅ Pass | - |
| REG-007 | Extra field | Added extra field in JSON | 400 Error: field not allowed | Got 400 not allowed | ✅ Pass | - |
| REG-008 | Very long email | >100 chars email | 400 Invalid email | Got 400 valid email required | ✅ Pass | - |
| REG-009 | Very long password | 47 chars password | Should allow (no max limit defined) | 201 Created | ✅ Pass | Policy check needed |

---

## 2. Login API

**Endpoint**: `POST /api/auth/login`  
**Description**: Authenticate user and return JWT token  

| Test Case ID | Scenario | Input | Expected Result | Actual Result | Status | Remarks |
|--------------|----------|-------|-----------------|---------------|--------|---------|
| LOG-001 | Valid login | Correct email & password | 200 Success + JWT | 200 Success | ✅ Pass | - |
| LOG-002 | Wrong email | Wrong email + valid pass | 401 Invalid credentials | 401 Invalid credentials | ✅ Pass | - |
| LOG-003 | Wrong password | Correct email + wrong pass | 401 Invalid credentials | 200 Logged in | ❌ Fail | Password validation missing |
| LOG-004 | Short password (2 letters) | `"password": "ab"` | 400 Password must be at least 4 characters | Accepted login | ❌ Fail | Policy inconsistent with Register |
| LOG-005 | Empty password | `"password": ""` | 400 Error: password not allowed empty | Got 400 | ✅ Pass | - |

---

## 3. Profile Fetch API

**Endpoint**: `GET /api/users/me`  
**Description**: Get authenticated user profile (JWT required)  

| Test Case ID | Scenario | Input | Expected Result | Actual Result | Status | Remarks |
|--------------|----------|-------|-----------------|---------------|--------|---------|
| PRO-001 | Valid token | Valid JWT token | 200 Profile returned | 403 Access denied | ❌ Fail | Permission issue |
| PRO-002 | No token | No JWT header | 401 Unauthorized | Not tested | - | Pending |
| PRO-003 | Invalid token | Fake/expired JWT | 401 Unauthorized | Not tested | - | Pending |

---

## 4. Profile Update API

**Endpoint**: `PATCH /api/users/me`  
**Description**: Update current user profile (partial updates allowed, JWT required)  

| Test Case ID | Scenario | Input | Expected Result | Actual Result | Status | Remarks |
|--------------|----------|-------|-----------------|---------------|--------|---------|
| UPD-001 | Valid update with token | Change name/phone | 200 Profile updated | 403 Access denied | ❌ Fail | Permission issue |
| UPD-002 | No token | No JWT header | 401 Unauthorized | Not tested | - | Pending |
| UPD-003 | Invalid token | Fake/expired JWT | 401 Unauthorized | Not tested | - | Pending |

---

## Conclusion

- **Register API** is stable with proper validations.  
- **Login API** has critical issues with password validation.  
- **Profile APIs** are blocked due to permission errors.  
- Needs fixes before moving to Version-1 testing.  

---