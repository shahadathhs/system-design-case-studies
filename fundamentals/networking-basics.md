# Networking Basics for Backend Engineers

## 1. The Core Concepts

At its heart, networking is data transmission between nodes. For backend engineering, the focus lies on the application layer protocols and transport mechanisms that deliver data to the API endpoints.

### IP Addresses (The House Address)

- **Public IP**: The external address. Unique across the entire internet.
  - _Analogy_: Your home mailing address.
- **Private IP**: Internal address. Only works inside a specific network (like AWS VPC).
  - _Analogy_: "Room 203". Meaningless unless you are already inside the building.
  - _Crucial for Backends_: Your Database should **ALWAYS** have a Private IP. If it has a Public IP, it is being scanned by hackers right now.

### Ports (The Apartment Number)

An IP address gets you to the machine. A **Port** gets you to the specific application running on that machine.

- `80`: HTTP (Unsecure Web).
- `443`: HTTPS (Secure Web).
- `22`: SSH (Remote access).
- `5432`: Postgres default.
- `6379`: Redis default.

### NAT (Network Address Translation)

**Problem**: We ran out of IPv4 addresses.
**Solution**: Your office has 1 Public IP. Inside the office, you have 50 computers with Private IPs. All outgoing traffic looks like it comes from that 1 Public IP. The router remembers who asked for what.

---

## 2. Protocols: The Language of Data

### TCP (Transmission Control Protocol)

- **Reliable**.
- **Ack-based**: "I sent packet 1. Did you get it?" -> "Yes". -> "Okay, sending packet 2."
- **Usage**: Websites, APIs, Databases, File Downloads. _Basically everything you build._

### UDP (User Datagram Protocol)

- **Fast & Fire-and-Forget**.
- **No guarantees**: "Sending packets! Good luck!"
- **Usage**: Live Video, Gaming, Voice Calls. If you lose a pixel in a video call, you don't pause to ask for it again.

### HTTP vs TLS/SSL

- **HTTP**: Plain text TCP. Anyone with Wireshark can read your API keys.
- **TLS (Transport Layer Security)**: The encryption layer that wraps HTTP.
- **Handshake**:
  1.  Client: "Hello, I want to talk securely."
  2.  Server: "Here is my Certificate (Identity Card)."
  3.  Client: "I trust that Certificate Authority. Here is a session key."
  4.  _All future data is encrypted._

---

## 3. The Lifecycle of a Request

"What happens when you type `google.com` and hit enter?"

1.  **DNS Lookup**: Browser asks DNS "Where is `google.com`?" -> Gets IP `1.2.3.4`.
2.  **TCP Handshake**: Browser sends SYN packet to `1.2.3.4` on Port 443.
3.  **TLS Handshake**: Negotiate encryption keys.
4.  **HTTP Request**: Browser sends `GET / HTTP/1.1`.
5.  **Load Balancer**: Receives request, picks a healthy server.
6.  **Backend Processing**: Node.js handles logic, queries DB.
7.  **HTTP Response**: JSON/HTML sent back.
8.  **Browser Paint**: User sees the page.

Every step involves **Latency**.

- DNS: 20-100ms.
- TCP Handshake: 1 RTT (Round Trip Time).
- TLS Handshake: 2 RTT.
- Backend: Processing Time.

**Optimization**: KeepAlive (Reuse connection) saves those TCP/TLS handshake times on subsequent requests.
