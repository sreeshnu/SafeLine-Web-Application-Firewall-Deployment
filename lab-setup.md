# SafeLine Lab Setup

This file explains the lab in the order it was built.

## 1. Machines and services

The lab uses Ubuntu for the SafeLine and web-server environment.

The main services are:

- **SafeLine** — checks incoming web requests.
- **Apache** — hosts the web application.

Apache listens on port `81` in this lab.

SafeLine listens in front of Apache.

## 2. Why two ports?

Using separate ports makes the reverse-proxy idea easy to understand.

```text
SafeLine :80  --->  Apache :81
```

A visitor connects to SafeLine. If the request is allowed, SafeLine sends it to Apache.

## 3. Local name

The lab uses the name `secure-lab` for the application and maps it to the lab system through `/etc/hosts`.

This means the test does not need a public DNS name.

## 4. SafeLine management

SafeLine provides a web management panel where applications can be added and security events can be reviewed.

The lab used port `9443` for this panel.

## 5. Add the application

The application was added with these settings:

```text
Domain: secure-lab
Listening port: 80
Mode: Reverse Proxy
Upstream: http://192.168.1.99:81
```

The upstream address is the backend Apache server.

## 6. Test the connection first

Before testing an attack, the important thing is to make sure the normal application works through SafeLine.

The basic flow should be:

```text
Browser
  |
  v
SafeLine
  |
  v
Apache
  |
  v
Web page
```

If the normal page does not work, there is no point testing the WAF yet. First fix the proxy or application connection.

## 7. Then test detection

Once the normal request works, send a controlled SQL Injection test through SafeLine and check whether the request is blocked and recorded.

The purpose of this step is validation, not attacking an unrelated system.
