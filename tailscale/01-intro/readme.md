Tailscale is a networking tool that lets you securely connect devices over the internet **as if they were on the same local network (LAN)**. It is built on top of the **WireGuard VPN protocol**, which is known for being fast, secure, and simple.

For your company's situation, the goal is likely:

> "Allow developers to securely access the office server from home without exposing the server to the public internet."

---

# Why is Tailscale needed?

Let's first understand the problem.

## Without Tailscale

Imagine your office network looks like this:

```
Office

            Internet
                │
         Public IP
                │
         Company Router
                │
     ┌──────────┴─────────┐
     │                    │
Server               Employee PCs
192.168.1.24
```

The server has a **private IP address**:

```
192.168.1.24
```

Private IPs only work **inside the local network**.

When you go home:

```
Home
Laptop
192.168.0.15
```

Your laptop has no route to

```
192.168.1.24
```

because those are completely different networks.

---

Normally companies solve this by creating a VPN server.

Example:

```
OpenVPN
IPSec
WireGuard
```

But configuring VPN servers means dealing with

- firewalls
- routers
- port forwarding
- certificates
- static public IPs

This can become complicated.

---

# What Tailscale does

Tailscale creates a private network over the Internet.

Imagine it builds an invisible cable between all your devices.

Instead of this

```
Home Laptop

       Internet

Company Server
```

it becomes

```
Home Laptop
      │
      │ encrypted tunnel
      │
Company Server
```

The devices behave almost like they're on the same LAN.

---

# Example

Your office server

```
Ubuntu Server

192.168.1.24
```

Install Tailscale.

It receives

```
100.95.23.15
```

Your laptop

```
Windows
```

Install Tailscale.

It receives

```
100.110.54.88
```

Now from home you can simply

```
ssh 100.95.23.15
```

or

```
ssh server-name
```

No router configuration needed.

---

# How it works

Each device installs the Tailscale client.

```
Laptop
     │
Server
     │
Phone
     │
Desktop
```

All log into the same Tailscale account (or organization).

Tailscale creates something called a **tailnet**.

A **tailnet** is Tailscale's name for your private virtual network—a collection of trusted devices that can securely communicate with each other.

```
Tailnet

Laptop
Server
Desktop
Phone
```

Only members of this tailnet can communicate.

---

# Does it expose my server?

No.

Unlike opening ports on your router:

```
Internet
     │
Port 22 open
     │
Server
```

which anyone can try to attack,

with Tailscale:

```
Internet

No ports open

Only authenticated Tailscale devices
can reach the server.
```

This greatly reduces the attack surface.

---

# Authentication

Instead of usernames/passwords on the VPN,

Tailscale uses identity providers.

Examples:

- Google
- Microsoft
- GitHub
- Microsoft Entra ID (Azure AD)
- Okta

A company often uses its Microsoft or Google Workspace accounts.

Only authorized users can join the tailnet.

---

# Encryption

Every connection is encrypted using **WireGuard**.

Example:

```
Laptop
    │
Encrypted
    │
Internet
    │
Encrypted
    │
Server
```

Even if someone intercepts the traffic, they cannot read it.

---

# Direct connection

Whenever possible, Tailscale tries to create a **peer-to-peer (P2P)** connection.

```
Laptop
   │──────────────Server
```

Traffic goes directly between the two devices.

This is the fastest option.

---

# Relay server (DERP)

Sometimes a direct connection isn't possible because of restrictive NATs or firewalls.

Then Tailscale uses a relay.

```
Laptop
    │
DERP Server
    │
Server
```

The relay forwards encrypted traffic. It **cannot decrypt** the data because the encryption is end-to-end.

---

# Typical company setup

```
Office

Ubuntu Server
Docker
Jenkins
Git
Databases
```

Install Tailscale.

Developers install Tailscale on their laptops.

From home they can

```
ssh server
```

or

```
http://100.x.x.x:8080
```

or

```
microk8s kubectl ...
```

exactly as if they were connected to the office network.

---

# Common uses

- Remote SSH access
- Access internal websites
- Connect to databases
- Access Kubernetes clusters
- Remote Desktop (RDP)
- File servers (SMB/NFS)
- NAS access
- Home lab access
- Development servers

---

# Example for your company

Suppose the office server is

```
192.168.1.24
```

Running

- Azure DevOps Agent
- Docker
- MicroK8s
- Jenkins

At home you simply:

```
SSH
↓

ssh azureagent@100.88.44.10
```

or

```
http://100.88.44.10:8080
```

No public IP is required.

No port forwarding is required.

---

# Does the office need a public IP?

**No.** This is one of Tailscale's biggest advantages.

As long as the office server can make **outbound** connections to the internet (which is almost always allowed), it can join the tailnet and become reachable from authorized devices.

---

# Why your supervisor chose Tailscale

Compared with setting up a traditional VPN, Tailscale offers several advantages:

| Traditional VPN             | Tailscale                                     |
| --------------------------- | --------------------------------------------- |
| Configure VPN server        | No VPN server to manage                       |
| Open firewall ports         | Usually no inbound ports required             |
| Configure routers           | Little or no router configuration             |
| Manage certificates         | Handled automatically                         |
| Public IP often needed      | No public IP required                         |
| More complex setup          | Typically takes only a few minutes per device |
| Manual client configuration | Install client and sign in                    |
