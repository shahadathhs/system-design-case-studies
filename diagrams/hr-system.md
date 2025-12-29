# HR System Architecture

```mermaid
graph TD
    subgraph Clients
        EmployeeApp[Employee Portal]
        ManagerApp[Manager Portal]
        HRAdminApp[HR Admin Portal]
        PayrollAdminApp[Payroll Admin Portal]
    end

    EmployeeApp -- HTTPS --> API_GW[API Gateway]
    ManagerApp -- HTTPS --> API_GW
    HRAdminApp -- HTTPS --> API_GW
    PayrollAdminApp -- HTTPS --> API_GW

    API_GW -- gRPC/HTTPS --> UserSvc[User/Identity Service]
    API_GW -- gRPC/HTTPS --> EmployeeSvc[Employee Service]
    API_GW -- gRPC/HTTPS --> TimeOffSvc[Time-Off Service]
    API_GW -- gRPC/HTTPS --> PayrollSvc[Payroll Service]
    API_GW -- gRPC/HTTPS --> ReportSvc[Reporting Service]
    API_GW -- gRPC/HTTPS --> NotifSvc[Notification Service]

    UserSvc <--> UserDB[(User DB - PostgreSQL)]
    EmployeeSvc <--> EmployeeDB[(Employee DB - PostgreSQL)]
    TimeOffSvc <--> TimeOffDB[(Time-Off DB - PostgreSQL)]
    PayrollSvc <--> PayrollDB[(Payroll DB - PostgreSQL)]

    EmployeeSvc -- Event (New Hire) --> MessageBroker[Message Broker (Kafka)]
    TimeOffSvc -- Event (Approved) --> MessageBroker
    PayrollSvc -- Event (Payslip) --> MessageBroker

    MessageBroker --> NotifSvc
    MessageBroker --> AuditSvc[Audit Service]
    AuditSvc <--> AuditLogDB[(Audit Log DB - Cassandra)]

    PayrollSvc -- Integrates --> BankingAPI[External Banking API]
    ReportSvc -- Reads from --> DataWarehouse[Data Warehouse]
    ReportSvc <--> EmployeeDB
    ReportSvc <--> TimeOffDB
    ReportSvc <--> PayrollDB
```
