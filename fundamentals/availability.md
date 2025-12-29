# Availability

Availability is a critical aspect of system design, referring to the proportion of time a system is operational and accessible when required. It's typically expressed as a percentage over a given period. High availability (HA) means the system is designed to minimize downtime and provide continuous service.

## Measuring Availability

Availability is often measured in "nines":

*   **99% (Two Nines):** ~3 days, 15 hours, 36 minutes downtime per year.
*   **99.9% (Three Nines):** ~8 hours, 45 minutes, 56 seconds downtime per year.
*   **99.99% (Four Nines):** ~52 minutes, 35 seconds downtime per year.
*   **99.999% (Five Nines):** ~5 minutes, 15 seconds downtime per year.

The required level of availability depends on the business needs and cost constraints. Achieving higher nines becomes exponentially more expensive and complex.

## Key Concepts for High Availability

### 1. Redundancy

The most fundamental principle of high availability. Having duplicate components in a system so that if one fails, a backup can take its place.

*   **N+1 Redundancy:** Having N components to handle the load plus 1 additional component for failover.
*   **Active-Passive:** One component is active and handles requests, while the redundant component is passive and takes over only if the active one fails.
*   **Active-Active:** All redundant components are active and process requests simultaneously. This can provide better resource utilization and horizontal scalability.

### 2. Fault Tolerance

The ability of a system to continue operating without interruption when one or more of its components fail. This involves:

*   **Failure Detection:** Quickly identifying when a component has failed.
*   **Isolation:** Containing the failure to prevent it from spreading to other parts of the system.
*   **Recovery:** Restoring the system to a healthy state after a failure.

### 3. Failover

The process of switching to a redundant or standby system or component upon the failure or abnormal termination of a previously active system or component.

*   **Automatic Failover:** The system detects a failure and automatically switches to the backup. This is preferred for minimizing downtime.
*   **Manual Failover:** Requires human intervention to initiate the switch, which typically results in longer downtime.

### 4. Disaster Recovery (DR)

The process of restoring operations after a catastrophic event (e.g., natural disaster, major outage) that impacts an entire data center or region. DR often involves:

*   **Geographic Redundancy:** Deploying systems across multiple data centers or regions.
*   **Backup and Restore:** Regular backups of data and the ability to restore them quickly.
*   **Recovery Time Objective (RTO):** The maximum tolerable downtime after a disaster.
*   **Recovery Point Objective (RPO):** The maximum tolerable data loss after a disaster.

### 5. Monitoring and Alerting

*   **Proactive Monitoring:** Continuously observing system health, performance, and resource utilization.
*   **Alerting:** Notifying engineers immediately when critical thresholds are crossed or failures are detected. This is crucial for rapid response and recovery.

### 6. Graceful Degradation

Designing a system to continue operating, possibly with reduced functionality or performance, even when some components are unavailable. This prevents complete outages and provides a better user experience during partial failures.

## Design Considerations for High Availability

*   **Eliminate Single Points of Failure (SPOF):** Identify and eliminate any component whose failure would bring down the entire system.
*   **Automate Everything:** Automate deployment, monitoring, scaling, and failover processes to reduce human error and improve recovery times.
*   **Test Failures:** Regularly test how your system behaves under various failure scenarios to ensure that your HA mechanisms work as expected.
*   **Statelessness:** Design services to be stateless as much as possible, making them easier to scale horizontally and recover from failures.
