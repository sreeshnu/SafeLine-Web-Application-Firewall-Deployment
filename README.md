# SafeLine Web Application Firewall Deployment

## What is this project?

This project is about putting a **Web Application Firewall (WAF)** in front of a web application and testing whether it can identify and block a suspicious web request.

I used **SafeLine** as the WAF and **Apache** as the web server behind it. The main test was a SQL Injection request sent through the protected application.

The simple idea is:

```text
User / Attacker
      |
      | HTTP request
      v
  SafeLine WAF
      |
      | allowed request
      v
 Apache Web App
```

If SafeLine decides that a request is malicious, it can block the request instead of allowing it through to the backend.

SafeLine is a self-hosted WAF and reverse proxy. It inspects web traffic and provides protection against common web attacks.

## Why did I build this?

I wanted to understand how a WAF works in a real lab instead of only reading about it.

The goal was simple:

> **Put SafeLine in front of a web application, send a controlled SQL Injection test, and check whether the WAF detects and blocks it.**

The test also showed why installing a security tool is not enough. We need to generate traffic and check the result.

## Lab setup

| Component | Purpose |
|---|---|
| Ubuntu | Lab system running the WAF and web server |
| SafeLine | Web Application Firewall / reverse proxy |
| Apache | Backend web server |
| Browser | Sends normal and test web requests |
| SQL Injection test | Checks the WAF detection |

In the original lab, Apache listened on port **81** and SafeLine was placed in front of it.

### Traffic flow

```text
Normal request
     |
     v
 SafeLine
     |
     | allowed
     v
 Apache :81
```

For a suspicious request:

```text
SQL Injection test
     |
     v
 SafeLine
     |
     | blocked
     X
```

The important point is that SafeLine is the first layer receiving the web request.

## What I did

### 1. Installed SafeLine

I installed SafeLine in the Ubuntu lab environment so that I could test WAF behaviour against a local web application.

For a new installation, use the current SafeLine documentation because installation methods and requirements can change between releases.

### 2. Opened the management panel

After installation, I accessed the SafeLine management panel.

The original lab used port **9443** for the management panel.

The panel was used to configure the protected website and review security events.

### 3. Added the Apache application

I configured the Apache application as the backend protected by SafeLine.

The original lab configuration was:

- Domain: `secure-lab`
- Local mapping: `192.168.1.99`
- SafeLine listening port: `80`
- Mode: **Reverse Proxy**
- Backend: `http://192.168.1.99:81`

So the path was:

```text
Browser
   |
   | HTTP request
   v
SafeLine :80
   |
   | if allowed
   v
Apache :81
```

### 4. Tested a SQL Injection request

I sent this controlled test request to the lab application:

```text
http://secure-lab/index.php?id=1' OR '1'='1
```

This was performed only against the intentionally configured lab system.

The purpose was to check whether SafeLine would recognise the request as suspicious.

### 5. Checked the response

The browser returned an **Access Forbidden** response.

This showed that the request was blocked at the SafeLine layer.

I am not claiming from this response alone that the request definitely never reached Apache. That would require checking the backend access logs as additional evidence.

### 6. Checked the SafeLine attack log

The SafeLine dashboard recorded the event as a **SQL Injection** attack and showed information such as the source and event time.

This gave me two useful pieces of information:

1. The request was blocked.
2. The WAF recorded the security event for investigation.

## What the test proved

| Test | Result |
|---|---|
| Normal request | Intended to pass through SafeLine to Apache |
| SQL Injection test | Blocked by SafeLine |
| SQL Injection test | Recorded in the SafeLine attack log |

The result demonstrates the basic WAF workflow: inspect a web request, decide whether it matches a security rule, and block suspicious traffic.

## How I would make the validation stronger

A good WAF test should check both sides of the proxy.

For example:

```text
Send test request
       |
       v
Check SafeLine response
       |
       v
Check SafeLine attack log
       |
       v
Check Apache access log
       |
       v
Compare the results
```

Checking the Apache log is important when we want to make a stronger claim about whether a blocked request reached the backend.

## Why the reverse proxy matters

Without a WAF:

```text
Client ───────────────> Apache
```

With a WAF:

```text
Client ─────> SafeLine ─────> Apache
                 |
                 └── suspicious request = BLOCK
```

The reverse proxy gives us a separate security layer in front of the application.

## What I learned

### 1. A WAF is different from a normal network firewall

A network firewall mainly controls network connections and ports.

A WAF focuses on **web traffic** and can inspect parts of HTTP requests such as URLs, parameters and request content.

### 2. A security tool must be tested

Installing SafeLine successfully does not prove that the WAF is protecting the application correctly.

The controlled SQL Injection test gave me a simple way to check its behaviour.

### 3. Blocking and logging are both important

Blocking stops the suspicious request, while logging gives the analyst information that can be investigated later.

### 4. Evidence matters

A browser error page tells us that the request was rejected, but additional backend logs can give stronger evidence about where the request stopped.

### 5. Testing should be controlled

The SQL Injection test was performed against a local lab application that was intentionally configured for testing.

## Skills demonstrated

- Web Application Security
- WAF Deployment
- Reverse Proxy Configuration
- SafeLine Configuration
- Controlled SQL Injection Testing
- Security Event Analysis
- Basic Web Traffic Investigation

## Important note

This repository documents the original lab and the results available from it. It does not claim screenshots, raw terminal output, or backend-log evidence that is not stored in the repository.

The goal is to explain the work clearly without adding fake evidence.

## References

- [SafeLine official project](https://github.com/chaitin/SafeLine)
- [SafeLine documentation](https://docs.waf.chaitin.com/)

---

*Cybersecurity lab project — SafeLine Web Application Firewall Deployment.*
