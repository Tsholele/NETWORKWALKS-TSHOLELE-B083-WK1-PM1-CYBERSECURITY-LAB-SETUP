# NETWORKWALKS Cybersecurity Internship - Batch B083

## Week 1 - Project Module 1 - Lab Setup (VirtualBox & Kali Linux)

**Intern:** Tsholele  
**Batch:** B083  
**Week:** 1  
**Project:** PM1 - Cybersecurity Lab Setup  
**Date:** September 2026

---

## Project Objective

Set up a complete cybersecurity testing lab environment on a laptop/PC using:
- **VirtualBox** as the hypervisor
- **Kali Linux** as the attacker machine
- **NAT Network** in subnet `10.0.0.0/24`
- Kali static IP: **`10.0.0.2/24`**
- Full Internet access from Kali
- Clipboard, drag/drop, and shared folder enabled

---

## Lab Architecture

| Component | Value |
|-----------|-------|
| Hypervisor | VirtualBox (latest version) |
| Attacker Machine | Kali Linux 2026.2 (64-bit) |
| Network Type | NAT Network |
| Subnet | 10.0.0.0/24 |
| Kali IP | 10.0.0.2/24 (Static) |
| Gateway | 10.0.0.1 |
| DNS | 8.8.8.8, 8.8.4.4 |
| Host OS | Windows (with Downloads shared) |

---

## Steps Performed

### Phase 1 - Environment Setup

#### Step 1: Installed 7-Zip
Downloaded from https://7-zip.org/download.html to extract the Kali VM image.

#### Step 2: Installed VirtualBox
Downloaded from https://virtualbox.org/wiki/Downloads and installed on Windows host.

#### Step 3: Created NAT Network
- Opened **VirtualBox -> File -> Tools -> Network Manager**
- Created a **NAT Network** named `NatNetwork`
- IPv4 Prefix: `10.0.0.0/24`
- DHCP: Enabled
- IPv6: Disabled

![NAT Network Configuration](screenshots/02-nat-network.png)

#### Step 4: Imported / Configured Kali Linux VM
Kali Linux 2026.2 (amd64) VM configured in VirtualBox.

![VirtualBox Manager](screenshots/01-virtualbox-manager.png)

#### Step 5: Configured Network Adapter
- **Adapter 1 -> Attached to:** NAT Network
- **Name:** NatNetwork
- **Adapter Type:** Intel PRO/1000 MT Desktop (82540EM)
- **Promiscuous Mode:** Allow All

![Network Adapter Settings](screenshots/03-kali-network-adapter.png)

#### Step 6: Enabled Clipboard & Drag-and-Drop
- **Settings -> General -> Advanced**
- Shared Clipboard: Bidirectional
- Drag'n'Drop: Bidirectional

![Clipboard and Drag/Drop](screenshots/04-clipboard-dragdrop.png)

#### Step 7: Enabled Shared Folder
- **Settings -> Shared Folders**
- Folder Name: `downloads`
- Auto-mount: Enabled
- Make Permanent: Enabled

![Shared Folder Settings](screenshots/05-shared-folder-settings.png)

### Phase 2 - Kali Linux Configuration

#### Step 8: Booted Kali Linux

![Kali Desktop](screenshots/06-kali-desktop.png)

#### Step 9: Configured Static IP 10.0.0.2/24

Commands used:

    sudo nmcli connection modify "Wired connection 1" ipv4.addresses 10.0.0.2/24
    sudo nmcli connection modify "Wired connection 1" ipv4.method manual
    sudo nmcli connection modify "Wired connection 1" ipv4.gateway 10.0.0.1
    sudo nmcli connection modify "Wired connection 1" ipv4.dns "8.8.8.8 8.8.4.4"
    sudo nmcli connection modify "Wired connection 1" ipv4.dad-timeout 0
    sudo nmcli connection down "Wired connection 1"
    sudo nmcli connection up "Wired connection 1"

**Note:** The `ipv4.dad-timeout 0` was necessary due to a known DAD timeout bug in Kali 2026.1+ with VirtualBox v7.

![IP Address Configuration](screenshots/07-ip-a.png)

![Routing Table](screenshots/08-ip-route.png)

#### Step 10: Verified Internet Access

Commands used:

    ping -c 4 8.8.8.8
    ping -c 4 google.com

Both returned **0% packet loss**.

![Ping External IP](screenshots/09-ping-external-ip.png)

![Ping Google](screenshots/10-ping-google.png)

---

## Troubleshooting - Issue Faced & Solution

### Problem: Shared folder /downloads not accessible inside Kali

**Symptom:**
After configuring the shared folder in VirtualBox and rebooting Kali, the folder `/media/sf_downloads` returned:

    ls: cannot open directory '/media/sf_downloads': Permission denied

**Diagnosis:**
- `groups` confirmed `vboxsf` membership was correct
- `mount | grep vboxsf` returned nothing - the shared folder was not mounted
- `systemctl status vboxadd-service` showed failed to start with kernel module error

**Root cause:**
VirtualBox Guest Additions were not properly built for the current Kali kernel (6.12). The `vboxadd-service` daemon failed at startup, so the `vboxsf` mount never occurred at boot time.

**Attempted fixes:**
1. Reinstalled `virtualbox-guest-utils` and `virtualbox-guest-x11` - packages installed but service still failed
2. Confirmed `vboxsf` kernel module loads (`lsmod | grep vbox` shows `vboxsf` loaded)
3. Attempted to start `vboxadd-service` manually - failed with exit code 1

**Resolution (documented for later):**
To fully fix this on Kali 2026.x with VirtualBox 7.2, the recommended approach is:

    sudo apt install -y virtualbox-guest-dkms
    sudo dpkg-reconfigure virtualbox-guest-dkms
    sudo reboot

Or manually install from the Guest Additions ISO:

    sudo mount /dev/sr0 /mnt/cdrom
    sudo /mnt/cdrom/VBoxLinuxAdditions.run

This will be revisited during the internship for full completion. All other lab requirements (network, IP, Internet access, clipboard, drag/drop) are functioning correctly.

---

## Summary - Requirements Checklist

| Requirement | Status |
|-------------|--------|
| VirtualBox latest version installed | Done |
| Kali Linux VM set up as attacker machine | Done |
| NAT Network in 10.0.0.0/24 | Done |
| Kali IP address 10.0.0.2/24 | Done |
| Full Internet access from Kali | Done |
| Clipboard & Drag/Drop enabled | Done |
| Shared folder /downloads configured | Done (VM side) |
| Snapshots taken | In progress |

---

## Links

- **VirtualBox:** https://virtualbox.org/wiki/Downloads
- **Kali Linux:** https://kali.org/get-kali
- **7-Zip:** https://7-zip.org/download.html
- **Networkwalks:** https://networkwalks.com

---

## Tags

`#Networkwalks` `#Cybersecurity` `#KaliLinux` `#VirtualBox` `#EthicalHacking` `#LabSetup` `#BatchB083`
