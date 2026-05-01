# Home Assistant Setup Notes

## Purpose

This document provides step-by-step operational notes for installing, bringing online, and troubleshooting Home Assistant OS on a Raspberry Pi in the IoT lab environment. It is written to support repeatable setup by students and instructors and to help quickly diagnose first-boot issues.

## Environment and Source Documentation

- Official Home Assistant Raspberry Pi installation guide: https://www.home-assistant.io/installation/raspberrypi/
- Imaging tool used: Raspberry Pi Imager - https://www.raspberrypi.com/software/
- Target platform: Raspberry Pi running Home Assistant OS
- Lab context: portable IoT / classroom deployment where Wi-Fi or segmented networking may affect first-boot provisioning

## Installation Summary

### High-Level Installation Steps

1. Download and review the official Home Assistant Raspberry Pi installation guide.
2. Open Raspberry Pi Imager.
3. Select the appropriate Raspberry Pi hardware target.
4. Choose the Home Assistant OS image.
5. Select the target microSD card.
6. Write the image to the card.
7. Insert the microSD card into the Raspberry Pi.
8. Power on the Raspberry Pi.
9. Wait for the system to boot to the Home Assistant CLI prompt:

```text
ha >
```

### Expected First Boot Behavior

On first boot, Home Assistant OS initializes system components and begins preparing the Home Assistant application stack.

Expected behavior may include:

- a delay while the operating system finishes initialization
- a message similar to:

```text
Preparing Home Assistant, this may take 20 minutes or more
```

- delayed availability of the web interface during initial setup

This first-boot process can take a while, but it should eventually complete if networking, DNS, and outbound internet access are functioning correctly.

## Connecting to Wi-Fi from Console

If Wi-Fi is not preconfigured, it can be configured manually from the console using `nmcli`.

### Step 1: Log in to the console

At the Raspberry Pi display or attached console, enter:

```bash
login
```

### Step 2: List available wireless networks

```bash
nmcli device wifi list
```

Use this to verify:

- the wireless adapter is detected
- nearby SSIDs are visible
- the intended lab or setup SSID is available

### Step 3: Connect to the Wi-Fi network

```bash
nmcli device wifi connect "YOUR_SSID" password "YOUR_PASSWORD"
```

Replace:

- `YOUR_SSID` with the wireless network name
- `YOUR_PASSWORD` with the wireless network password

### Step 4: Verify the connection

Use the following commands to confirm the Raspberry Pi is connected and has network information assigned:

```bash
nmcli device status
ip route
cat /etc/resolv.conf
```

Interpretation:

- `nmcli device status`
  - confirms whether the Wi-Fi interface is connected
  - shows which connection profile is active
- `ip route`
  - confirms whether a default gateway is present
  - if no default route exists, outbound traffic will usually fail
- `cat /etc/resolv.conf`
  - shows which DNS servers the system is using
  - if DNS servers are missing or incorrect, name resolution may fail even if internet routing works

## Accessing the Web UI

Once the system completes initial provisioning and networking is working, the Home Assistant web interface should be available at:

```text
http://homeassistant.local:8123
```

### Fallback Access Method

If local hostname resolution does not work, access the UI using the Raspberry Pi IP address:

```text
http://<device-ip>:8123
```

You can identify the IP address from the console using:

```bash
ip addr
```

## Common First Boot Issue: Supervisor Installation Loop

### Exact Symptom

A common first-boot issue is that the system appears stuck during setup with behavior such as:

```text
Preparing Home Assistant, this may take 20 minutes or more
```

and repeated Supervisor log activity indicating installation retries, for example:

```text
Error on Home Assistant installation, retrying in 30 seconds
```

### Most Likely Root Cause

This usually indicates a network dependency problem rather than a failed OS image.

Most common causes:

- DNS resolution failure
- missing default gateway
- outbound internet access blocked by network policy, VLAN rules, or upstream firewall restrictions
- image download failures from container/image registries such as `ghcr.io`
- TLS or timeout failures caused by restricted or unstable connectivity

In practice, this issue is typically caused by either:

- no working route to the internet, or
- DNS not resolving external hostnames

## Network Troubleshooting Checklist

Run the following checks from the console.

### 1. Check device and connection state

```bash
nmcli device status
```

Look for:

- the Wi-Fi adapter listed as connected
- an active connection profile associated with the intended SSID

If the interface is disconnected, reconnect to Wi-Fi before continuing.

### 2. Check the routing table

```bash
ip route
```

Look for a default route similar to:

```text
default via 192.168.1.1 dev wlan0
```

If no default route is present:

- the Raspberry Pi may be connected to Wi-Fi but unable to reach the internet
- Home Assistant may fail to download required components during first boot

### 3. Check DNS configuration

```bash
cat /etc/resolv.conf
```

Look for valid nameserver entries, for example:

```text
nameserver 1.1.1.1
nameserver 8.8.8.8
```

If DNS servers are missing, unreachable, or point to a resolver that cannot answer external queries, setup may stall.

### 4. Test raw IP connectivity

```bash
ping 8.8.8.8
```

Interpretation:

- if this fails, the issue is usually gateway, routing, upstream internet, or firewall related
- if this succeeds, basic outbound connectivity is working

### 5. Test DNS name resolution

```bash
ping google.com
```

Interpretation:

- if `ping 8.8.8.8` works but `ping google.com` fails, the issue is DNS
- if both fail, the issue is likely routing, gateway access, or blocked outbound connectivity

## Gateway Failure vs. DNS Failure

### Gateway / Routing Failure

Symptoms:

- no default route in `ip route`
- `ping 8.8.8.8` fails
- `ping google.com` also fails

Meaning:

- the device cannot properly reach the internet
- Home Assistant cannot retrieve images or dependencies required during setup

### DNS Failure

Symptoms:

- `ping 8.8.8.8` succeeds
- `ping google.com` fails
- Supervisor logs may reference name resolution, repository access, or download errors

Meaning:

- network connectivity exists, but the device cannot resolve external hostnames
- Home Assistant may fail to reach online services even though Wi-Fi appears connected

## Fix: Manually Set DNS

If DNS appears to be the problem, manually configure public DNS servers for the active connection.

### Step 1: Identify the active connection name

```bash
nmcli connection show
```

Locate the active Wi-Fi connection profile name.

### Step 2: Disable automatic DNS and set DNS servers manually

Replace `YOUR_CONNECTION_NAME` with the active Wi-Fi connection name:

```bash
nmcli connection modify "YOUR_CONNECTION_NAME" ipv4.ignore-auto-dns yes
nmcli connection modify "YOUR_CONNECTION_NAME" ipv4.dns "1.1.1.1 8.8.8.8"
```

### Step 3: Restart the connection

```bash
nmcli connection down "YOUR_CONNECTION_NAME"
nmcli connection up "YOUR_CONNECTION_NAME"
```

### Step 4: Verify DNS after reconnection

```bash
cat /etc/resolv.conf
ping google.com
```

If DNS now works, give Home Assistant time to retry installation.

## Supervisor Log Inspection

Use the following command to inspect Supervisor logs:

```bash
ha supervisor logs
```

### Errors to Look For

Pay particular attention to messages involving:

- DNS resolution failures
- inability to resolve external hostnames
- image pull failures involving `ghcr.io`
- timeout errors
- TLS or certificate-related connection failures
- repeated retry behavior during Home Assistant installation

These messages help determine whether the issue is:

- DNS-related
- blocked outbound internet access
- unstable connectivity
- upstream filtering or registry access restrictions

## Firewall / Network Considerations in the IoT Lab

In most deployments, no local firewall changes are needed on the Home Assistant OS device itself.

However, in a lab environment using VLANs, segmented SSIDs, or restrictive upstream policies, verify the following:

- outbound internet access is allowed for the Raspberry Pi during first boot and updates
- HTTPS access to external registries and services is not blocked
- TCP port `8123` is allowed from the instructor or management network to the Home Assistant host for web UI access
- DNS is allowed to approved resolvers
- mDNS traffic may be required if using `homeassistant.local`

### mDNS Considerations

The hostname:

```text
homeassistant.local
```

relies on multicast DNS (mDNS). In segmented environments:

- mDNS may not cross VLAN boundaries by default
- `homeassistant.local` may work only from the same local segment
- using the device IP address may be more reliable for administrative access

## Best Practices for Classroom / Camp Use

For reproducible setup in a classroom or camp environment, prefer the following:

- use the same SSID and credential pattern for setup devices when possible
- if segmented networking is required, document firewall and routing rules in advance
- verify outbound internet access before beginning first-boot provisioning
- verify DNS functionality before assuming the Home Assistant image is damaged
- keep an Ethernet connection available as a fallback for initial provisioning and troubleshooting
- record which SSID, IP range, gateway, and DNS servers were used during successful setup
- document observed errors and resolutions immediately so future deployments are easier to repeat

## Suggested Deployment Workflow

1. Image the microSD card with Raspberry Pi Imager.
2. Boot the Raspberry Pi into Home Assistant OS.
3. Wait for the CLI prompt.
4. Connect to Wi-Fi using `nmcli` if not already connected.
5. Verify:
   - interface status
   - default route
   - DNS configuration
   - raw internet connectivity
   - hostname resolution
6. Wait for Home Assistant provisioning to complete.
7. Open the web UI at `http://homeassistant.local:8123` or by IP address.
8. If the system appears stuck, inspect Supervisor logs and correct gateway or DNS issues first.
9. Record any lab-specific deviations, issues, and successful fixes in this document.

## Lab-Specific Notes Template

Use this section to record actual deployment details during setup.

### Documentation Sources Used

- Official guide URL:
- Additional notes or references:

### Network Used During Setup

- SSID:
- IP address:
- Gateway:
- DNS servers:

### Commands Used

```bash
# Add the exact commands used during setup here
```

### Issues Encountered

- Issue:
- Symptom:
- Resolution attempt:
- Final outcome:

### Final Verified State

- CLI reachable: Yes / No
- Wi-Fi connected: Yes / No
- Internet reachable by IP: Yes / No
- DNS working: Yes / No
- Web UI reachable: Yes / No
- Access URL:
