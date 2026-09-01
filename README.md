# SafeLine Web Application Firewall Deployment

## What is this project?

This project is about putting a **Web Application Firewall (WAF)** in front of a web application and checking whether it can stop a harmful web request before the request reaches the real application.

I used **SafeLine** as the WAF and an **Apache web server** as the application behind it. I then sent a SQL Injection test through the WAF and checked what happened.

The simple idea is:

```text
User / Attacker
      |
      | HTTP request
      v
  SafeLine WAF
      |
      | Safe request only
      v
 Apache Web App
```

If SafeLine decides that a request is harmful, it can stop the request instead of passing it to Apache.

SafeLine describes itself as a self-hosted WAF and reverse proxy. It is designed to inspect web traffic and protect applications from attacks such as SQL injection, XSS, path traversal and other common web attacks. citeturn0search0

---

## Why did I build this?

I wanted to understand WAFs in a practical way rather than only reading about them.

The main goal was simple:

> **Install the WAF, put it in front of a web application, send a known SQL Injection test, and see whether the WAF actually stops it.**

This also helped me understand an important security idea: installing a security tool is not enough. We should test whether it is doing the job we expect it to do.

---

## Lab setup

| Component | Used for |
|---|---|
| Ubuntu | SafeLine and web-server environment |
| SafeLine | WAF / reverse proxy |
| Apache | Web application server |
| Browser | Sending the web requests |
| SQL Injection test | Checking the WAF detection |

The Apache application was listening on port **81**, while SafeLine was placed in front of it.

### How the traffic moved

```text
SQL Injection test
       |
       v
  SafeLine WAF
       |
       | blocked
       X

or

Normal request
       |
       v
  SafeLine WAF
       |
       | allowed
       v
 Apache (port 81)
```

This is the main idea behind a reverse-proxy WAF: the user talks to the WAF first, and the WAF decides whether the request should continue to the backend application. citeturn0search0

---

## What I did

### 1. Installed SafeLine

SafeLine was installed on Ubuntu using its installation method.

The reason for installing it on the Ubuntu system was to create a local security lab where the WAF and the web application could be tested without exposing a real website.

SafeLine's current project documentation provides an installation guide and Docker-based deployment information. citeturn0search0

### 2. Opened the SafeLine management panel

After installation, I accessed the SafeLine management panel.

The management panel was available on port **9443** in this lab.

The management panel is where the protected website is added and where security events can be reviewed.

### 3. Added the web application

I added the Apache application to SafeLine as a protected website.

The lab used:

- Domain: `secure-lab`
- Local mapping: `192.168.1.99`
- SafeLine listening port: `80`
- Mode: **Reverse Proxy**
- Backend: `http://192.168.1.99:81`

The important part is the last line.

SafeLine was listening in front of Apache, while Apache continued running on port 81.

So the user did **not** directly access Apache.

Instead:

```text
Browser
   |
   | http://secure-lab
   v
SafeLine :80
   |
   | if allowed
   v
Apache :81
```

### 4. Tested a SQL Injection request

I then sent a SQL Injection test through the protected application:

```text
http://secure-lab/index.php?id=1' OR '1'='1
```

This was done only against the intentionally configured lab application.

The purpose was not to break a real website. The purpose was to see whether the WAF could recognise a suspicious request.

### 5. Checked the result

The request did not reach the application normally.

Instead, the browser showed an **Access Forbidden** response.

That gave the first important result:

> SafeLine identified the request as malicious and stopped it before it reached Apache.

### 6. Checked the SafeLine attack log

I then checked the attack log in the SafeLine dashboard.

The event was recorded as a **SQL Injection** attack and included information such as the source IP and time of the event.

This is useful because a WAF is not only about blocking traffic. The security team also needs to know **what was blocked and why**.

---

## What the test proved

The test gave us a simple before/after understanding:

| Request | Result |
|---|---|
| Normal web request | Can be passed to Apache |
| SQL Injection test | Blocked by SafeLine |
| Blocked request | Recorded in SafeLine attack log |

SafeLine's documented capabilities include blocking web attacks and recording security events, along with features such as rate limiting and access controls. citeturn0search0

---

## Why the reverse proxy matters

A common question is:

**"Why not just install the WAF on the web application itself?"**

A reverse-proxy setup gives us a separate layer in front of the application.

The application does not need to understand every incoming security threat itself. SafeLine can inspect the HTTP request first.

```text
Without WAF

Client ───────────────> Apache


With SafeLine

Client ─────> SafeLine ─────> Apache
                 |
                 └── suspicious request = BLOCK
```

This separation is one of the main things I wanted to understand from this project.

---

## What I learned

### 1. A WAF is not just a firewall

A traditional network firewall mainly controls network connections and ports.

A WAF looks much more closely at **web requests**.

For example, it can inspect things such as URLs, parameters and request content to identify suspicious web traffic.

### 2. Installing a security tool is not enough

A successful installation does not prove that a security tool is working.

The SQL Injection test gave me a way to check the actual protection.

### 3. Logs are important

The block itself tells us that something happened.

The attack log gives us more information about the event, which is important when investigating security incidents.

### 4. Security should be tested in a controlled environment

The SQL Injection test was performed against the lab application, not an unrelated public website.

That makes the test safe and repeatable.

---

## Skills demonstrated

- Web Application Security
- WAF Deployment
- Reverse Proxy Configuration
- SafeLine Configuration
- SQL Injection Testing in a Lab
- Security Event Analysis
- Basic Web Traffic Investigation

---

## Important note

This repository documents the lab and the results available from the original project. It does not claim that screenshots or raw terminal output are available when they are not included in the repository.

The goal is to explain the work clearly without adding fake evidence.

---

## References

- SafeLine official project: https://github.com/chaitin/SafeLine
- SafeLine documentation: https://docs.waf.chaitin.com/

---

*Cybersecurity lab project — SafeLine Web Application Firewall Deployment.*
