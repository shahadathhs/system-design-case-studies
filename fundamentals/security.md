# Security

Security in system design is paramount, ensuring that systems and data are protected from unauthorized access, use, disclosure, disruption, modification, or destruction. It involves a combination of processes, technologies, and practices.

## Key Security Concepts

### 1. Authentication vs. Authorization

*   **Authentication:** Verifying the identity of a user, process, or device. It answers the question, "Who are you?" Common methods include passwords, multi-factor authentication (MFA), and certificates.
*   **Authorization:** Granting or denying access to specific resources or functionalities based on the authenticated identity. It answers the question, "What are you allowed to do?" This often involves roles, permissions, and access control lists (ACLs).

### 2. Encryption

The process of converting information or data into a code to prevent unauthorized access.

*   **Encryption in Transit (TLS/SSL):** Securing data as it moves across networks (e.g., HTTPS for web traffic). This prevents eavesdropping and tampering.
*   **Encryption at Rest:** Securing data stored in databases, file systems, or object storage. This protects data even if the storage medium is compromised.

### 3. Hashing

A one-way function that transforms input (e.g., a password) into a fixed-size string of characters. Hashing is used to store passwords securely: instead of storing the actual password, its hash is stored. When a user attempts to log in, their provided password is hashed and compared to the stored hash.

*   **Salting:** Adding a unique, random string (salt) to a password before hashing. This protects against rainbow table attacks.

### 4. Least Privilege

A security principle that dictates that a user, program, or process should have only the bare minimum privileges necessary to perform its function. This minimizes the potential damage if an entity is compromised.

### 5. Input Validation

The practice of ensuring that data submitted by a user or application conforms to expected formats and constraints. This is crucial for preventing common vulnerabilities such as:

*   **SQL Injection:** Malicious SQL code injected into input fields to manipulate database queries.
*   **Cross-Site Scripting (XSS):** Injecting malicious scripts into web pages viewed by other users.

### 6. Security Audits and Logging

*   **Logging:** Comprehensive logging of security-relevant events (e.g., login attempts, access to sensitive data) is essential for detecting and investigating security incidents.
*   **Auditing:** Regularly reviewing logs and system configurations to identify vulnerabilities and ensure compliance with security policies.

### 7. Firewall and Network Security

*   **Firewalls:** Devices or software that monitor and filter incoming and outgoing network traffic based on predefined security rules.
*   **Network Segmentation:** Dividing a network into smaller, isolated segments to limit the lateral movement of attackers if one segment is compromised.

## Designing for Security

*   **Threat Modeling:** Proactively identifying potential threats and vulnerabilities early in the design process.
*   **Security by Design:** Incorporating security considerations into every stage of the software development lifecycle, rather than treating it as an afterthought.
*   **Regular Security Updates:** Keeping all software, libraries, and operating systems up to date to patch known vulnerabilities.
