# Bandit Level 14 → 15

## 🎯 Objective

Submit the current level's password to a service listening on **port 30000** on **localhost**, and receive the next level's password in return.

---

## 🧠 Concepts Covered

### Localhost
`localhost` resolves to the loopback IP address `127.0.0.1` — a reserved address that routes traffic back to the same machine without touching any external network. It is used to access services running locally on the current host.

### Ports
A **port** is a numbered endpoint (0–65535) that allows a single machine to host multiple services simultaneously. Each service binds to a specific port and listens for incoming connections on it.

| Port | Service |
|------|---------|
| 22 | SSH (Secure Shell) |
| 80 | HTTP (HyperText Transfer Protocol) |
| 443 | HTTPS (HyperText Transfer Protocol Secure) |
| 30000 | Bandit challenge service (this level) |

### Netcat (`nc`)
**Netcat** is a command-line networking utility that can establish raw TCP (Transmission Control Protocol) or UDP (User Datagram Protocol) connections to any host and port. It sends and receives data directly without an application layer in between, making it ideal for manual service interaction and network reconnaissance.

### TCP vs UDP
| Feature | TCP | UDP |
|---------|-----|-----|
| Full name | Transmission Control Protocol | User Datagram Protocol |
| Connection required? | Yes (3-way handshake) | No |
| Guaranteed delivery? | Yes | No |
| Order preserved? | Yes | Not guaranteed |
| Speed | Slower | Faster |
| Use case | Accuracy-critical data | Speed-critical data |

`nc` defaults to **TCP**, which is appropriate here — the service must receive the password completely and correctly before it can validate it.

---

## 🗂️ Key Files & Paths

```
/etc/bandit_pass/bandit14   # Password for the current level (bandit14)
```

Each level's password is stored under `/etc/bandit_pass/bandit<N>` and is only readable by the corresponding user.

---

## 🛠️ Solution Walkthrough

### Step 1 — Log in as bandit14
```bash
ssh bandit14@bandit.labs.overthewire.org -p 2220
```

### Step 2 — Retrieve the current level's password
```bash
cat /etc/bandit_pass/bandit14
```

### Step 3 — Connect to the service on port 30000
```bash
nc localhost 30000
```

The terminal pauses — the service is now waiting for input.

### Step 4 — Submit the password
Paste the output from Step 2 and press **Enter**.

### Step 5 — Receive the next password
```
Correct!
<bandit15_password>
```

---

## 💡 What Actually Happened (Under the Hood)

```
[bandit14 shell]
     |
     | nc localhost 30000
     ↓
[TCP connection established to 127.0.0.1:30000]
     |
     | password string sent as raw bytes
     ↓
[Service at port 30000]
     | validates against stored bandit14 password
     ↓
[Response: "Correct!\n<next_password>"]
     |
     ↓
[bandit14 shell receives and displays it]
```

---

## 🔐 Password Obtained

```
8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo
```

---

## 🏥 Real-World Relevance (Healthcare Cybersecurity Context)

Manual port interaction with `nc` mirrors what a security professional does during **network reconnaissance** — probing open ports on a target system to identify running services, understand their behavior, and test for vulnerabilities.

In a hospital environment, networked medical devices (e.g. patient monitors, infusion pumps) expose ports for communication. An attacker — or a defender assessing risk — may use tools like `nc` to:
- Identify what service is running on an unexpected open port
- Send probe data and observe responses
- Test whether a service accepts unauthenticated input

Understanding TCP vs UDP also matters: devices that stream real-time vitals over UDP (no delivery guarantee, no connection handshake) present a higher injection risk than TCP-based services, because there is no established session to verify the sender's identity.

---

## 📌 Commands Summary

| Command | Purpose |
|---------|---------|
| `cat /etc/bandit_pass/bandit14` | Read the current level's password |
| `nc localhost 30000` | Open a raw TCP connection to port 30000 on localhost |

---

*OverTheWire Bandit — NullScalpel*
