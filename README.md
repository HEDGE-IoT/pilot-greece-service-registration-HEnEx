# Registration Service

## General Information and Purpose

### Service Name/Title
**Registration Service**

### Description and Purpose
The Registration Service is designed to manage user registrations efficiently and securely. It handles the creation, validation, and storage of user accounts, ensuring that all necessary information is collected and verified. This service streamlines onboarding for new users, reduces administrative overhead, and enhances user experience with a seamless registration process. It also includes password management, account recovery, and user authentication to maintain high security standards.

### Owner/Contact Information
**Hellenic Energy Exchange (HEnEx)**

---

## Functional Requirements

### 1) User Registration
- **Requirement:** The system shall allow users to create a new account by providing necessary information such as username, password, email address, and other required details.  
- **Description:** Users register via a form with personal information plus company details and documents. The system validates input for required format and constraints.

### 2) Email Verification
- **Requirement:** The system shall send a verification email to the user’s provided email address upon registration.  
- **Description:** After registration, an email with a verification link is sent. The account is activated only after the user clicks the link.

### 3) Password Management
- **Requirement:** The system shall allow users to reset their password if they forget it.  
- **Description:** Users can request a password reset link sent to their registered email. The link allows setting a new password securely.

### 4) User Authentication
- **Requirement:** The system shall authenticate users by verifying their username and password during login.  
- **Description:** Users enter credentials to access their account. The system validates against stored data and issues tokens.

### 5) Data Security
- **Requirement:** The system shall ensure that all user data is stored securely and protected from unauthorized access.  
- **Description:** Encrypt data in transit and at rest; enforce secure storage and handling.

---

## Non-Functional Requirements

### 1.3.1 Performance
- **Requirement:** The system shall handle up to **1,000 concurrent** user registrations without performance degradation.  
- **Description:** Maintain acceptable response times under high registration load.

### 1.3.2 Reliability and Availability
- **Requirement:** **99.9%** uptime.  
- **Description:** Implement redundancy and failover to minimize downtime.

### 1.3.3 Security
- **Authentication:** Simple access token authentication (Bearer) to verify user identities during login.  
- **Authorization:** Role-based access control (RBAC) to manage permissions.  
- **Data Encryption:** Encrypt sensitive data **in transit and at rest** using industry-standard protocols (e.g., **TLS**, **AES-256**).  
- **Data Privacy:** The system shall comply with relevant data privacy regulations (e.g., **GDPR**).

---

## Service Interfaces

### API Endpoints

#### Endpoint 1 — Login to the Platform
- **URL:** `/auth/login`  
- **Method:** `POST`  
- **Description:** Authenticates a user with email and password; returns access and refresh tokens.  
- **Headers:**  
  - `Content-Type: application/json`

**Request Parameters**
- `email` *(string)* – User’s email address  
- `password` *(string)* – User’s password

**Request Example**
```http
POST /auth/login
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "yourpassword123"
}
```

**Response Parameters**
- `accessToken` *(string)*  
- `refreshToken` *(string)*  
- `user` *(object)* – Includes `usr_id`, `usr_email`, `usr_name`, `usr_role`, `usr_company`

**Response Example**
```json
{
  "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "usr_id": 12,
    "usr_email": "user@example.com",
    "usr_name": "John Doe",
    "usr_role": {
      "rl_id": 2,
      "rl_name": "User"
    },
    "usr_company": {
      "cmp_id": 5,
      "cmp_name": "ABC Energy"
    }
  }
}
```

**Error Handling**
- `400 Bad Request` – Invalid input data  
- `401 Unauthorized` – Missing or invalid authentication token  
- `404 Not Found` – User not found  
- `500 Internal Server Error` – Unexpected condition

---

#### Endpoint 2 — User Logout from the Platform
- **URL:** `/auth/logout`  
- **Method:** `POST`  
- **Description:** Logs out the currently authenticated user by invalidating their tokens.  
- **Headers:**  
  - `Content-Type: application/json`  
  - `Authorization: Bearer <token>`

**Request Parameters**
- *(none)*

**Request Example**
```http
POST /auth/logout
Content-Type: application/json
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

**Response Parameters**
- `200 OK`

**Response Example**
```json
{ "message": "Logged out successfully." }
```

**Error Handling**
- `400 Bad Request` – Invalid input data  
- `401 Unauthorized` – Missing or invalid authentication token  
- `404 Not Found` – User not found  
- `500 Internal Server Error` – Unexpected condition

---

#### Endpoint 3 — Register New User
- **URL:** `/auth/register`  
- **Method:** `POST`  
- **Description:** Registers a new user in the system.  
- **Headers:**  
  - `Content-Type: application/json`  
  - `Authorization: Bearer <token>`

**Request Parameters (Body)**
The body contains **three objects**:
- **User information**  
- **Documents information**  
- **Company information**

**Request Example**
```json
{
  "user": {
    "usr_email": "newuser@example.com",
    "usr_name": "Jane Smith",
    "usr_phone": "+30 210 0000000",
    "usr_password": "StrongP@ssw0rd!"
  },
  "documents": {
    "tax_cert_number": "EL123456789",
    "id_document_type": "passport",
    "id_document_no": "AB1234567"
  },
  "company": {
    "cmp_id": 5,
    "cmp_name": "ABC Energy",
    "cmp_vat": "EL123456789"
  }
}
```

**Response Parameters**
- Newly created **user** object (without password).

**Response Example**
```json
{
  "usr_id": 15,
  "usr_email": "newuser@example.com",
  "usr_name": "Jane Smith",
  "usr_company": {
    "cmp_id": 5,
    "cmp_name": "ABC Energy"
  },
  "usr_role": {
    "rl_id": 3,
    "rl_name": "Standard User"
  },
  "usr_date_created": "2025-05-26T14:22:30Z"
}
```

**Error Handling**
- `400 Bad Request` – Invalid input data  
- `404 Not Found` – User not found  
- `500 Internal Server Error` – Unexpected condition

---

### UI Mockups (if applicable)
*Not provided.*

---

## Data Model

### Entities and Relationships
This section defines the core structure for managing users, credentials, companies, and roles. It supports secure authentication, user‑role mapping, and organizational hierarchies for companies.

#### User
- Represents an individual with system access.  
- Connected to a company and has one set of credentials.  
- Activation status controls login permission.

#### User Credentials
- Stores login and token information.  
- Linked one‑to‑one with a specific user.  
- Supports password reset and refresh workflows.

#### Company
- Organization to which users belong.  
- Maintains relationships with roles, assets, portfolios, and users.

#### Role
- Defines access level and permissions.  
- Assigned to companies (not users directly).  
- Enables RBAC across the platform.

### Database Schema
*To be provided.*

---

## Integration and Dependencies
- **External dependencies:** Libraries, frameworks, and internal services needed for the system.  
- **System dependencies:** *(to be specified)*  
- **Third‑party integrations:** External services and APIs the system interacts with.  
- **HEDGE‑IoT devices/cloud integration:** Connecting with IoT devices and cloud services for data handling.  
- **Integration with the App Store:** *(if applicable)*  
- **Integration with the data space:** *(if applicable)*

---

## Security and Privacy
- **Data Sensitivity:** All user and credential data treated as sensitive.  
- **Access Control:** RBAC via roles at company level; token scopes for endpoints.  
- **Audit Logs:** Login/logout events, registration events, token issuance/revocation, and data access logs.

---
