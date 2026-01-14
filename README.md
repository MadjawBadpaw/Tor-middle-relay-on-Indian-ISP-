# Tor-middle-relay-on-Indian-ISP
# Tor Relay on Home Internet (India)
# IPv6 Works, IPv4 Fails — A Practical Case Study with Jio Fiber

# TL;DR

Tor relay can run and pass self-tests over IPv6

IPv4 fails due to ISP router limitations, not Tor misconfiguration

Jio Fiber router does not support real port forwarding, only predefined services

# Result: IPv6-only reachability confirmed, IPv4 blocked upstream

# NO CGNAT

## This repository documents the exact torrc setup, reachability tests, and the root cause.

# 1. System Setup

OS: Debian / Parrot

Tor version: system package (tor@default.service
)

# Network:

curl -4 ifconfig.me  ----> Gives public IPv4
Double check from internet.

Public IPv4: 67.68.x.x, 152.58.x.x (confirmed, not CGNAT) 
### CGNAT range is 100.64.0.0/10 
### 100.64.0.0 – 100.127.255.255

Global IPv6: 2409:40d7:1069:...

ISP: Jio Fiber (India)

# 2. Tor Configuration (torrc)

Minimal and correct relay configuration:

Nickname nakedsnakeeeeeeeee
ContactInfo experimentalwork6@gmail.com

ORPort 443 or 9001
ORPort [IPv6]:443 or 9001

AccountingMax 30 GB
AccountingStart month 1 00:00

ExitRelay 0
ExitPolicy reject *:*
SocksPort 0


# Notes:

Port 443 used to bypass common ISP filtering

Exit relay disabled (middle/guard only)

Accounting enabled → directory service intentionally disabled

# 3. Verification: Tor Is Listening Correctly
ss -tulpen | grep 443


## Output confirms dual-stack listening:

LISTEN 0 4096 0.0.0.0:443
LISTEN 0 4096 [::]:443


So:

Tor is bound to IPv4 ✅

Tor is bound to IPv6 ✅

No local firewall issue ❌

# 4. Tor Self-Test Results (Logs)

Successful IPv6 reachability:

Self-testing indicates your ORPort [2409:40d7:...]:443 is reachable from the outside. Excellent.


IPv4 test never succeeds.


# Key point:

IPv6 confirmed reachable by directory authorities

IPv4 never confirmed, despite public IPv4 and correct torrc

# 5. Root Cause: Jio Fiber Router Port Forwarding Jio Router Behavior

The Jio Fiber router UI only allows:

## Predefined services:

HTTP (80)

HTTPS (443)

FTP, DNS, etc.

No true NAT port mapping

“HTTPS Allow” does not forward arbitrary TCP traffic

## This means:

Inbound IPv4 traffic never reaches the host

This is not a Tor issue and not a Linux firewall issue.


# 6. Is the Relay “Working”?
Aspect	Status
Tor daemon running	✅
Bootstrap	✅ 100%
IPv6 ORPort reachable	✅
IPv4 ORPort reachable	❌
Fully usable relay	❌ (per current Tor relay requirements)

# Conclusion:
Tor is running correctly, but the relay is not fully usable because IPv4 inbound connectivity doesn't work.



# 7.Only real solutions:

router with better config

Use a VPS



# 8. Takeaway

This repo documents a real-world constraint when running Tor relays on Indian home ISPs:

Even with a public IPv4, ISP-controlled routers can silently block inbound connectivity, making proper Tor relay operation impossible without additional hardware or infrastructure changes.

# 9. Why This Repo Exists

To save others hours of debugging.
