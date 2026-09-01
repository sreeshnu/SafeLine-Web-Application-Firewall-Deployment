# Testing and Validation

The main point of this project was to check whether SafeLine actually protects the web application.

## Test 1 — Normal request

First, send a normal request through SafeLine.

Expected flow:

```text
Browser
  ↓
SafeLine
  ↓
Apache
  ↓
Response
```

This confirms that SafeLine can pass normal traffic to the backend.

## Test 2 — SQL Injection test

A controlled SQL Injection test was sent to the lab application:

```text
http://secure-lab/index.php?id=1' OR '1'='1
```

The test was performed against the local lab application.

## Expected security result

SafeLine should inspect the request before forwarding it to Apache.

In the documented lab result:

- SafeLine blocked the request.
- The browser received an **Access Forbidden** response.
- The event appeared in the SafeLine attack log.
- The event was identified as **SQL Injection**.

## Why this matters

There are two separate things to check:

### Did the WAF block it?

The Access Forbidden response showed that the request was stopped.

### Did the WAF record it?

The attack log showed that SafeLine recorded the event and identified the attack type.

Having both results is useful because a security tool should not only stop suspicious traffic; security teams also need useful information for investigation.

## What I would test next

If this lab were extended, I would test several other controlled request types, such as:

- XSS test requests
- Path traversal patterns
- Repeated requests for rate-limit testing
- Normal requests that should **not** be blocked

The important part would be to compare allowed traffic with blocked traffic so that false positives can also be noticed.

## Validation checklist

- [x] SafeLine installed
- [x] Management panel accessible
- [x] Apache application added
- [x] Reverse proxy configured
- [x] Normal application path established
- [x] SQL Injection test sent
- [x] Malicious request blocked
- [x] Attack event checked in the log

This is the difference between saying **"I installed a WAF"** and showing **"I installed it and tested whether it worked."**
