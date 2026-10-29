# Scenario 03: Windows Firewall and Ping Connectivity Troubleshooting

**Environment:** Windows Server 2025, Windows 11 Pro, Hyper-V  
**Server:** FIN-DC01  
**Domain:** ad.findlayinc.test  
**Category:** Network Connectivity and Firewall Troubleshooting   
**Status:** Resolved

## 1. Problem Description

While configuring my Windows Server 2025 homelab, I encountered a network connectivity issue between my host computer and my Windows Server virtual machine.

The server had a valid static IP address, but when I attempted to ping it from my host computer, the request timed out.

**Objective:** Determine why the server was not responding to ping requests and restore connectivity without unnecessarily changing the network configuration.

## 2. Network Environment

My homelab was configured using Microsoft's Hyper-V virtualization platform.

| Device | IP Address |
|---|---|
| Windows 11 host computer | 192.168.50.1 |
| Windows Server 2025 (FIN-DC01) | 192.168.50.10 |
| Subnet mask | 255.255.255.0 |
| Hyper-V virtual switch | FindlayLab |

Both devices were connected through the lab's internal virtual network.

## 3. Initial Troubleshooting

I began by testing connectivity from my host computer to the Windows Server virtual machine.

I opened Command Prompt and executed:

```cmd
ping 192.168.50.10
```

**Result:** The ping requests timed out.

This indicated that my host computer was not receiving ICMP Echo Replies from the server.

However, a failed ping does not automatically mean that a device is offline or disconnected.

## 4. Verifying IP Configuration

Before changing firewall settings, I checked the server's IP configuration.

I ran:

```cmd
ipconfig /all
```

I confirmed that the server was configured with the expected IP address:

`192.168.50.10`

This helped rule out an incorrect server IP address as the immediate cause.

I also reviewed the Hyper-V networking configuration to ensure that the virtual machines were connected to the intended network.

## 5. Investigating Windows Firewall

After verifying the network configuration, I investigated Windows Defender Firewall.

Windows Firewall controls which network traffic is permitted to enter or leave a computer.

I learned that Windows Firewall can block incoming ping requests even when a computer is otherwise reachable.

Ping uses **ICMP (Internet Control Message Protocol)**.

Specifically, it sends an ICMP Echo Request and waits for an ICMP Echo Reply.

### Opening Windows Firewall

On the Windows Server virtual machine, I opened the advanced firewall management console:

```cmd
wf.msc
```

I navigated to:

**Windows Defender Firewall with Advanced Security → Inbound Rules**

I located the rule:

`File and Printer Sharing (Echo Request - ICMPv4-In)`

This rule controls whether incoming IPv4 ping requests are permitted.

## 6. Resolution

I enabled the appropriate inbound ICMPv4 Echo Request rule for the server's active firewall profile.

This allowed the Windows Server virtual machine to respond to incoming ping requests from my host computer.

Rather than disabling Windows Firewall entirely, I enabled the specific rule required for the connectivity test.

This is an important security practice because it avoids unnecessarily allowing unrelated network traffic.

## 7. Verification

After updating the firewall rule, I returned to my host computer and executed:

```cmd
ping 192.168.50.10
```

**Result:** The server successfully responded to the ping requests.

This confirmed that the firewall configuration had been preventing incoming ICMP Echo Requests.

### Before and After

| Test | Before | After |
|---|---|---|
| Server IP configuration | Valid | Valid |
| Ping from host to server | Timed out | Successful |
| Incoming ICMPv4 Echo Requests | Blocked | Allowed |
| Connectivity test | Failed | Passed |

## 8. Root Cause

The issue was caused by a Windows Firewall inbound rule that did not permit the required ICMPv4 Echo Request traffic.

The server was configured with the correct IP address, but its firewall prevented it from responding to ping requests.

**Root cause:** Incoming ICMPv4 Echo Request traffic was blocked by Windows Firewall.

**Resolution:** Enabled the appropriate inbound firewall rule and verified successful ping responses.

## 9. Tools Used

- Windows Server 2025
- Windows 11 Pro
- Microsoft Hyper-V
- Windows Defender Firewall with Advanced Security
- Command Prompt
- `ping`
- `ipconfig /all`
- `wf.msc`

## 10. Skills Demonstrated

- Troubleshooting network connectivity
- Verifying static IP configurations
- Understanding ICMP and ping
- Navigating Windows Firewall
- Managing inbound firewall rules
- Distinguishing connectivity failures from firewall restrictions
- Testing and verifying troubleshooting solutions
- Applying targeted firewall changes instead of disabling security controls

## 11. Lessons Learned

This exercise taught me that a failed ping does not necessarily mean a device is offline.

A computer may have a valid IP address and an operational network connection while still refusing certain types of incoming traffic.

I also learned the importance of troubleshooting systematically.

Instead of immediately changing IP addresses or reinstalling network components, I verified the existing configuration and investigated possible causes.

Most importantly, I learned that disabling an entire firewall is not an appropriate first solution when a specific firewall rule can resolve the problem.

## 12. Future Improvements

- Practice creating custom inbound firewall rules.
- Compare inbound and outbound firewall rules.
- Learn how Windows Firewall profiles work: Domain, Private, and Public.
- Investigate blocked connections using firewall logs.
- Learn the difference between ICMP, TCP, and UDP.
- Practice troubleshooting a network service that fails even though ping succeeds.

---

**Final Outcome:** Successfully diagnosed and resolved an ICMP connectivity issue between a Windows 11 host and a Windows Server 2025 virtual machine by configuring the appropriate Windows Firewall inbound rule.
