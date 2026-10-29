# Scenario 02: DHCP Connectivity Troubleshooting

**Environment:** Windows Server 2025, Windows 11 Pro, Hyper-V  
**Domain:** ad.findlayinc.test  
**Server:** FIN-DC01  
**Category:** Network Troubleshooting  
**Status:** Resolved

## 1. Problem Description

While configuring my Windows Server and Active Directory homelab, I encountered a networking issue where my Windows 11 virtual workstation was unable to obtain a valid IP address from the network.

Instead of receiving an address from the expected `192.168.50.0/24` network, the workstation automatically assigned itself the following IP address:

`169.254.119.72`

This prevented the workstation from communicating with the domain controller through the intended network configuration.

**Objective:** Identify why the workstation was not receiving an appropriate IP address, configure DHCP services, and restore network connectivity.

## 2. Initial Investigation

I began by examining the workstation's network configuration.

Using Command Prompt, I checked the IP address assigned to the workstation:

```cmd
ipconfig /all
```

The workstation had received an address in the `169.254.x.x` range.

I learned that this type of address is called an **Automatic Private IP Address (APIPA)**.

APIPA is a Windows feature that automatically assigns a link-local IP address when a network interface configured for automatic addressing cannot obtain an address from DHCP.

This suggested that the workstation had not successfully received a DHCP response.

## 3. Understanding the Root Cause

DHCP stands for **Dynamic Host Configuration Protocol**.

Its purpose is to automatically provide devices with network configuration information, including:

- IP address
- Subnet mask
- Default gateway, when applicable
- DNS server information

In my homelab, the Windows Server domain controller had not yet been configured to provide DHCP addresses.

As a result, the Windows 11 workstation was unable to obtain an address from the intended DHCP server.

**Root cause:** DHCP service was not yet configured and available to provide addresses to the workstation.

## 4. DHCP Server Configuration

To resolve the issue, I configured DHCP on my Windows Server 2025 virtual machine.

### Step 1: Install the DHCP Server Role

Using Windows Server Manager, I installed the DHCP Server role.

This added the functionality needed for Windows Server to automatically assign IP addresses to clients.

### Step 2: Authorize DHCP in Active Directory

After installation, I completed the DHCP post-installation configuration and authorized the DHCP server in Active Directory.

Authorization helps prevent unauthorized Windows DHCP servers from distributing addresses in an Active Directory environment.

### Step 3: Create a DHCP Scope

I created a DHCP scope with the following configuration:

| Setting | Configuration |
|---|---|
| Scope name | Findlay Lab Scope |
| Starting IP address | 192.168.50.100 |
| Ending IP address | 192.168.50.200 |
| Subnet mask | 255.255.255.0 |
| DNS server | 192.168.50.10 |
| DNS domain | ad.findlayinc.test |
| Default gateway | Not configured |

The default gateway was intentionally omitted because the lab network was isolated and did not require routing to an external network.

### Step 4: Activate the Scope

I activated the DHCP scope so it could begin distributing addresses to clients.

## 5. Client-Side Troubleshooting

After configuring DHCP, I returned to the Windows 11 workstation.

I renewed its network configuration so that it could request an address from the DHCP server.

Commands used for
