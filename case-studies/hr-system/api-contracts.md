# API Contracts

## 1. User/Identity Service

### Employee Login

*   **Endpoint:** `POST /api/v1/auth/login`
*   **Request Body:**
    ```json
    {
      "email": "employee@example.com",
      "password": "securepassword"
    }
    ```
*   **Response:**
    ```json
    {
      "access_token": "jwt-token-...",
      "refresh_token": "jwt-refresh-token-...",
      "user_id": "uuid-user-123",
      "role": "employee"
    }
    ```

## 2. Employee Service

### Get Employee Profile

*   **Endpoint:** `GET /api/v1/employees/{employee_id}`
*   **Response:**
    ```json
    {
      "employee_id": "uuid-employee-abc",
      "first_name": "Jane",
      "last_name": "Doe",
      "work_email": "jane.doe@example.com",
      "job_title": "Software Engineer",
      "department": "Engineering",
      "manager_id": "uuid-employee-xyz",
      "status": "active"
      // Sensitive data like SSN, bank details excluded unless authorized
    }
    ```

### Update Employee Profile (HR Admin/Employee)

*   **Endpoint:** `PUT /api/v1/employees/{employee_id}`
*   **Request Body:**
    ```json
    {
      "contact_email": "jane.personal@example.com",
      "phone_number": "123-456-7890"
      // Only fields allowed for update by current user's role
    }
    ```
*   **Response:** `HTTP 200 OK`

## 3. Time-Off Service

### Submit Time-Off Request

*   **Endpoint:** `POST /api/v1/time-off-requests`
*   **Request Body:**
    ```json
    {
      "employee_id": "uuid-employee-abc",
      "type": "vacation",
      "start_date": "2024-07-01",
      "end_date": "2024-07-05",
      "notes": "Annual vacation"
    }
    ```
*   **Response:**
    ```json
    {
      "request_id": "uuid-request-789",
      "status": "pending"
    }
    ```

### Approve/Reject Time-Off Request (Manager)

*   **Endpoint:** `PUT /api/v1/time-off-requests/{request_id}/approve` or `/reject`
*   **Request Body (for reject):**
    ```json
    {
      "notes": "Insufficient coverage"
    }
    ```
*   **Response:** `HTTP 200 OK`

### Get Employee Time-Off Balance

*   **Endpoint:** `GET /api/v1/employees/{employee_id}/time-off-balance`
*   **Response:**
    ```json
    {
      "employee_id": "uuid-employee-abc",
      "balances": [
        {"type": "vacation", "remaining_days": 15.5},
        {"type": "sick", "remaining_days": 8.0}
      ]
    }
    ```

<h2> 4. Payroll Service </h2>

<h3>Initiate Payroll Run (Payroll Admin) </h3>

*   **Endpoint:** `POST /api/v1/payroll-runs`
*   **Request Body:**
    ```json
    {
        "period_start_date": "2024-06-01",
        "period_end_date": "2024-06-30"
    }
    ```
*   **Response:**
    ```json
    {
        "run_id": "uuid-payroll-run-123",
        "status": "pending"
    }
    ```

<h3>Get Payslip </h3>

*   **Endpoint:** `GET /api/v1/employees/{employee_id}/payslips/{payslip_id}`
*   **Response:**
    ```json
    {
        "payslip_id": "uuid-payslip-456",
        "employee_id": "uuid-employee-abc",
        "gross_pay": 5000.00,
        "net_pay": 3500.00,
        "deductions": {"federal_tax": 500, "state_tax": 200, "health_insurance": 150},
        "period_start": "2024-06-01",
        "period_end": "2024-06-30"
    }
    ```
