# Troubleshooting

This page explains the easiest way to find where a SafeLine lab problem is happening.

## 1. SafeLine panel does not open

Check:

- Is the SafeLine service running?
- Is the management port reachable?
- Is a firewall blocking the port?

The management panel used in this lab was on port `9443`.

## 2. SafeLine opens but the website does not

Check the backend first.

```text
SafeLine
   ↓
Apache :81
```

Make sure Apache is running and listening on the expected port.

Then check the SafeLine upstream setting.

## 3. Normal website works but the attack is not blocked

Possible reasons include:

- The request is not reaching SafeLine.
- The application is being accessed directly instead of through the WAF.
- The protection settings are not enabled as expected.
- The test request does not match the detection logic.

The first thing to check is the traffic path.

## 4. The request is blocked but there is no useful log entry

Check the SafeLine attack log and confirm that the request is being processed by the protected application rather than another service.

## 5. Do not troubleshoot everything at once

A simple order is better:

```text
1. Is SafeLine running?
       ↓
2. Can I open the SafeLine panel?
       ↓
3. Can SafeLine reach Apache?
       ↓
4. Does a normal request work?
       ↓
5. Does the controlled attack test get blocked?
       ↓
6. Is the event visible in the log?
```

This order makes it much easier to find the actual problem.
