# Pfsense_Captive_Portal_Guest_Network_Project-


##  Project Overview

This project demonstrates the design and implementation of a **secure guest network using pfSense** with a fully functional **Captive Portal authentication system**.

The goal was to:

* Isolate guest users from the main network
* Enforce authentication before internet access
* Apply bandwidth control
* Restrict traffic using firewall rules
* Customize the user login experience
* Troubleshoot real-world networking issues

---

##  Network Architecture

###  Virtual Environment Setup

The project was implemented using a virtualized environment with:

* **pfSense Firewall VM (2 interfaces initially, later 3)**
* **Windows Client VM (for testing)**
* **VMware Virtual Network Editor**

###  Network Design

| Network      | VMnet              | Purpose                | IP Range         |
| ------------ | ------------------ | ---------------------- | ---------------- |
| WAN          | VMnet3 (NAT)       | Internet access        | 192.168.146.0/24 |
| LAN          | VMnet1 (Host-only) | Internal network       | 192.168.30.0/24  |
| Guest (OPT1) | VMnet2 (Host-only) | Captive Portal network | 192.168.10.0/24  |

---

##  Step 1: pfSense Initial Configuration

###  Assign Interfaces

* WAN → VMnet3
* LAN → VMnet1
* OPT1 → VMnet2

###  Configure Interfaces

#### WAN

* DHCP (automatic)

#### LAN

* IP: `192.168.30.1/24`

#### OPT1 (Guest Network)

* Enabled
* IP: `192.168.10.1/24`

---

##  Step 2: DHCP Configuration (Guest Network)

Navigate to:

```
Services → DHCP Server → OPT1
```

Configure:

* Enable DHCP
* Range: `192.168.10.10 – 192.168.10.100`

 Result: Guest devices receive IP automatically

---

##  Step 3: Connectivity Testing

From Windows VM:

```bash
ipconfig /renew
ping 192.168.10.1
```

✔ Confirmed:

* IP assigned from guest network
* pfSense reachable

---

##  Step 4: Captive Portal Setup

Navigate to:

```
Services → Captive Portal
```

###  Create Zone

* Name: `guest_portal`
* Interface: `OPT1`
* Enable Captive Portal 

---

##  Step 5: Authentication Configuration

###  Selected:

* **Use an authentication backend**
* Authentication Server: **Local Database**

---

##  Step 6: User Creation

Navigate to:

```
System → User Manager
```

Created user:

* Username: `testuser`
* Password: `Test123`

---

##  Step 7: Captive Portal Testing

Steps:

1. Connect Windows VM to **VMnet2**
2. Open browser
3. Visit:

```
http://neverssl.com
```

✔ Result:

* Redirected to login page

---

##  Initial Problem Encountered

Users were getting:

> **“Access Denied”**

###  Investigation

Checked logs:

```
Status → System Logs → Captive Portal
```

Observed:

* Authentication failures
* No active sessions

---

##  Troubleshooting Process

###  Issue 1: Custom Login Page Missing Required Fields

Problem:

* HTML form missing required pfSense parameters

Fix:
Added:

```html
<input type="hidden" name="redirurl" value="$PORTAL_REDIRURL$">
<input type="hidden" name="zone" value="$PORTAL_ZONE$">
```

---

###  Issue 2: Firewall Blocking DNS

Logs showed:

```
Default deny → port 53 (DNS)
```

Fix:
Navigate:

```
Firewall → Rules → OPT1
```

Add rules:

1. Allow DNS (UDP 53)
2. Allow DNS (TCP 53)

---

###  Issue 3: Firewall Over-Restriction

Temporary fix:

* Added **Allow Any rule** for testing

---

###  Issue 4: Session Reset

Cleared sessions:

```
Status → Captive Portal → Disconnect All
```

---

##  Step 8: Successful Authentication

After fixes:

 Login successful
 Internet access granted
 User visible under:

```
Status → Captive Portal
```

---

##  Step 9: Custom Styled Login Page

Implemented custom HTML + CSS:

### Features:

* Dark modern UI
* Centered login card
* Styled input fields
* Responsive layout

---

##  Step 10: Firewall Security Rules

After testing, tightened rules:

Allowed only:

* DNS (53)
* HTTP (80)
* HTTPS (443)

Blocked:

* All other traffic

---

##  Step 11: Bandwidth Limiting

Configured per-user limits:

* Download: **2 Mbps**
* Upload: **1 Mbps**

---

## Step 12: Redirect Verification

Tested:

* Unauthenticated user tries browsing
   Automatically redirected to login page

---

##  Final Testing Checklist

| Test                  | Result |
| --------------------- | ------ |
| DHCP assignment       | ✅      |
| Login page redirect   | ✅      |
| Authentication        | ✅      |
| Internet after login  | ✅      |
| Session tracking      | ✅      |
| Firewall restrictions | ✅      |
| Bandwidth control     | ✅      |

---

##  Key Lessons Learned

* Captive Portal depends heavily on **DNS availability**
* Missing hidden fields in HTML breaks authentication
* Firewall rules must allow essential services before restrictions
* Logs are critical for troubleshooting:

  * Captive Portal logs
  * Firewall logs
* Always isolate variables during debugging

---

##  Project Outcome

A fully functional:

✔ Secure Guest Network
✔ Captive Portal Authentication System
✔ Custom Login Interface
✔ Traffic-Controlled Environment
✔ Real-world Troubleshooting Implementation

---




---

##  Conclusion

This project demonstrates practical skills in:

* Network segmentation
* Firewall configuration
* Authentication systems
* Troubleshooting real-world network issues
* Secure system design

It reflects hands-on experience applicable to:

* Network Engineering
* Cybersecurity
* SOC Operations

---


---
