# Database Schema

An HR system deals with highly sensitive and transactional data, making relational databases (like PostgreSQL or MySQL) a common choice for their ACID properties and robust consistency models. However, some aspects like audit logs might benefit from NoSQL solutions.

## 1. User/Identity Service (PostgreSQL)

Manages authentication credentials and basic user roles.

**Users Table**

| Column Name   | Data Type | Description                              |
|---------------|-----------|------------------------------------------|
| `user_id`     | `uuid`    | Unique User ID (PK)                      |
| `email`       | `varchar` | User's email (unique, for login)         |
| `password_hash`| `varchar` | Hashed password                          |
| `role`        | `enum`    | 'employee', 'manager', 'hr_admin', 'payroll_admin' |
| `employee_id` | `uuid`    | FK to Employees table (optional, for employees) |
| `created_at`  | `timestamp`| Account creation timestamp               |
| `last_login_at`| `timestamp`| Last login timestamp                     |

## 2. Employee Service (PostgreSQL)

Core service for managing comprehensive employee master data.

**Employees Table**

| Column Name   | Data Type | Description                              |
|---------------|-----------|------------------------------------------|
| `employee_id` | `uuid`    | Unique Employee ID (PK)                  |
| `first_name`  | `varchar` | Employee's first name                    |
| `last_name`   | `varchar` | Employee's last name                     |
| `date_of_birth`| `date`   | Employee's date of birth                 |
| `gender`      | `enum`    | 'male', 'female', 'other'                |
| `contact_email`| `varchar` | Personal contact email                   |
| `work_email`  | `varchar` | Work email (unique)                      |
| `phone_number`| `varchar` | Contact phone number                     |
| `address`     | `jsonb`   | JSON object for full address             |
| `hire_date`   | `date`    | Date of hire                             |
| `termination_date`| `date` | Date of termination (if applicable)      |
| `status`      | `enum`    | 'active', 'inactive', 'on_leave'         |
| `department_id`| `uuid`   | FK to Departments table                  |
| `job_title`   | `varchar` | Employee's current job title             |
| `manager_id`  | `uuid`    | Self-referencing FK to Employees table (their manager) |
| `salary_amount`| `numeric` | Current base salary                      |
| `social_security_number`| `varchar`| Encrypted SSN/National ID (highly sensitive) |
| `bank_account_details`| `jsonb`| Encrypted JSON for direct deposit (highly sensitive) |

**Departments Table**

| Column Name   | Data Type | Description                              |
|---------------|-----------|------------------------------------------|
| `department_id`| `uuid`   | Unique Department ID (PK)                |
| `name`        | `varchar` | Department name (e.g., "Engineering")    |
| `head_id`     | `uuid`    | FK to Employees table (department head)  |

## 3. Time-Off Service (PostgreSQL)

Manages time-off requests, balances, and accruals.

**TimeOffRequests Table**

| Column Name     | Data Type | Description                              |
|-----------------|-----------|------------------------------------------|
| `request_id`    | `uuid`    | Unique Request ID (PK)                   |
| `employee_id`   | `uuid`    | FK to Employees table                    |
| `type`          | `enum`    | 'vacation', 'sick', 'personal'           |
| `start_date`    | `date`    | Start date of time-off                   |
| `end_date`      | `date`    | End date of time-off                     |
| `status`        | `enum`    | 'pending', 'approved', 'rejected', 'cancelled' |
| `requested_by`  | `uuid`    | FK to Users table (who made request)     |
| `approved_by`   | `uuid`    | FK to Users table (who approved/rejected) |
| `submitted_at`  | `timestamp`| When request was submitted               |
| `reviewed_at`   | `timestamp`| When request was reviewed                |
| `notes`         | `text`    | Manager notes for approval/rejection     |

**TimeOffBalances Table**

| Column Name     | Data Type | Description                              |
|-----------------|-----------|------------------------------------------|
| `employee_id`   | `uuid`    | FK to Employees table (PK)               |
| `year`          | `integer` | Year for the balance (PK)                |
| `type`          | `enum`    | 'vacation', 'sick', 'personal' (PK)      |
| `accrued_days`  | `numeric` | Days accrued                             |
| `taken_days`    | `numeric` | Days taken                               |
| `remaining_days`| `numeric` | Days remaining                           |
| `updated_at`    | `timestamp`| Last update timestamp                    |

## 4. Payroll Service (PostgreSQL)

Manages payroll calculations, runs, and historical data.

**PayrollRuns Table**

| Column Name     | Data Type | Description                              |
|-----------------|-----------|------------------------------------------|
| `run_id`        | `uuid`    | Unique Payroll Run ID (PK)               |
| `start_date`    | `date`    | Start date of payroll period             |
| `end_date`      | `date`    | End date of payroll period               |
| `status`        | `enum`    | 'pending', 'processed', 'approved', 'completed' |
| `processed_by`  | `uuid`    | FK to Users table (who processed)        |
| `processed_at`  | `timestamp`| When payroll run was processed           |
| `approved_by`   | `uuid`    | FK to Users table (who approved)         |
| `approved_at`   | `timestamp`| When payroll run was approved            |

**Payslips Table**

| Column Name     | Data Type | Description                              |
|-----------------|-----------|------------------------------------------|
| `payslip_id`    | `uuid`    | Unique Payslip ID (PK)                   |
| `run_id`        | `uuid`    | FK to PayrollRuns table                  |
| `employee_id`   | `uuid`    | FK to Employees table                    |
| `gross_pay`     | `numeric` | Gross pay for the period                 |
| `net_pay`       | `numeric` | Net pay for the period                   |
| `deductions`    | `jsonb`   | JSON object for itemized deductions      |
| `taxes`         | `jsonb`   | JSON object for itemized taxes           |
| `period_start`  | `date`    | Start date of pay period                 |
| `period_end`    | `date`    | End date of pay period                   |
| `generated_at`  | `timestamp`| When payslip was generated               |

## 5. Audit Service (Cassandra / MongoDB)

For immutable, high-volume audit logs.

**AuditLogs Table/Collection**

| Column Name     | Data Type | Description                              |
|-----------------|-----------|------------------------------------------|
| `log_id`        | `uuid`    | Unique Log ID (PK)                       |
| `timestamp`     | `timestamp`| When the action occurred                 |
| `user_id`       | `uuid`    | User who performed the action            |
| `service`       | `varchar` | Service where action originated          |
| `action_type`   | `varchar` | Type of action (e.g., 'UPDATE_EMPLOYEE', 'APPROVE_TIME_OFF') |
| `entity_type`   | `varchar` | Type of entity affected (e.g., 'Employee', 'TimeOffRequest') |
| `entity_id`     | `uuid`    | ID of the entity affected                |
| `old_value`     | `jsonb`   | JSON of old state (optional)             |
| `new_value`     | `jsonb`   | JSON of new state (optional)             |
| `ip_address`    | `varchar` | IP address of requestor                  |
