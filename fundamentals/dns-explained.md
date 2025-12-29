# DNS (Domain Name System) Explained

## Why DNS Exists

Computers only speak numbers (`192.158.1.38`). Humans speak names (`twitter.com`).
DNS is the **distributed phonebook** that bridges this gap.

## How DNS Resolution Works

It is a hierarchical recursive search. The browser doesn't know the IP, so it asks...

1.  **Local Cache**: "Do I already know `google.com`?" (Browser/OS Cache).
2.  **ISP Resolver**: "Comcast/Verizon, do you know?" (The Recursive Resolver).
3.  **Root Server (.)**: "I don't know, but here is the address for the `.com` registry."
4.  **TLD Server (.com)**: "I don't know specific sites, but here is the address for `google.com`'s Name Server."
5.  **Authoritative Name Server (ns1.google.com)**: "Yes! `google.com` is `1.2.3.4`."

**Result**: You get the IP.

## DNS Record Types (Backend Must-Know)

### 1. A Record (Address)

Maps **Hostname -> IPv4**.

- `api.myapp.com` -> `52.1.1.1`
- _Usage_: Pointing your domain to a Load Balancer or EC2 instance.

### 2. AAAA Record

Maps **Hostname -> IPv6**.

- `api.myapp.com` -> `2001:0db8:85a3...`

### 3. CNAME (Canonical Name)

Maps **Hostname -> Hostname**.

- `www.myapp.com` -> `myapp.com`
- `app.myapp.com` -> `lb-1234.aws.amazon.com`
- _Usage_: Aliasing. Very common with AWS Load Balancers (ALB) because AWS IPs change, but their DNS names don't.
- _Restriction_: You usually cannot put a CNAME at the "Root" (`myapp.com`). Only subdomains.

### 4. MX (Mail Exchange)

- Usage: "Where should emails for `@myapp.com` go?" (e.g., Google Workspace/Outlook).

### 5. NS (Name Server)

- Delegation. "Who is in charge of this domain?" (e.g., `ns1.digitalocean.com`).

## Concepts for Production

### TTL (Time To Live)

The lifespan of a DNS record in caches.

- **High TTL (24 hours)**: Good for static stable sites. Faster for users (cached). Bad if you need to change IPs quickly.
- **Low TTL (60 seconds)**: Good during migrations or for Load Balancing.
- **Disaster**: If you set TTL to 24h, and your server crashes, and you spin up a new server IP... users will still constantly hit the old broken IP for 24 hours. **Keep TTL low (5-60 min) for critical infrastructure.**

### DNS Round Robin

A poor man's Load Balancer.
You can give `api.myapp.com` **three** A Records (`1.1.1.1`, `1.1.1.2`, `1.1.1.3`).
The browser picks one.

- _Problem_: If `1.1.1.1` goes down, DNS keeps sending people there. It has no "Health Check".
