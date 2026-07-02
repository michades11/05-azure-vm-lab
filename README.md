# Secure Azure VM Architecture and Remote Management Lab

## 1. Project Overview
This repository documents the secure infrastructure deployment, disk architecture configuration, and migration from vulnerable public-facing remote connections to a zero-trust platform-managed perimeter using Azure Bastion.

## 2. Infrastructure Configuration Summary
* **Resource Group:** RemoteLearning-RG
* **Bastion Gateway Host Name:** Lab-Bastion-Host
* **Windows VM Name:** WinAdminVM (Private IP Only, Public IP Dissociated)
* **Linux VM Name:** LinuxAdminVM (Private IP Only, Public IP Dissociated)

### Storage Tier Architecture & Justification
For both the Windows and Linux computing nodes, **Standard SSD Managed Disks (Locally Redundant Storage - LRS)** were explicitly provisioned. 
* **Justification:** While production database clusters mandate Premium SSD or Ultra Disk tiers to survive intense IOPS bottlenecks, an administrative sandbox environment benefits most from Standard SSDs. They supply an optimized performance baseline ($\le 60 \text{ MB/s}$ throughput) and single-digit millisecond latency for system management commands, keeping daily operational costs contained within our laboratory budget while avoiding the severe latency performance drops found in legacy magnetic HDDs.

---

## 3. Network Security Group (NSG) Configuration

### Inbound Rule Profiles (Initial Phase)
* **Rule Name:** Allow_RDP_From_MyIP | Port: 3389 | Protocol: TCP | Source: Specific Local IP/32 | Action: Allow
* **Rule Name:** Allow_SSH_From_MyIP | Port: 22   | Protocol: TCP | Source: Specific Local IP/32 | Action: Allow

### Final Zero-Trust Outbound Profile & Bastion Security Analysis
After verifying connectivity, all Public IPs were dissociated from the network interfaces.
* **The Bastion Advantage:** By intercepting management traffic at the perimeter over TLS Port 443, Azure Bastion acts as an application layer proxy. Inbound network scanning threats, brute-force dictionary attacks against RDP/SSH ports, and zero-day protocol exploits are stopped entirely at the edge. The internal virtual machines are never exposed to the public internet routing table.

---

## 4. Azure Bastion Cost and Subnet Profile

### Subnet Topology Requirements
Azure Bastion mandates a highly strict, dedicated network topology framework:
* **Subnet Name:** `AzureBastionSubnet` (Exact matching required by Azure Resource Provider).
* **Address Space Prefix:** Minimum `/26` or `/27` block (e.g., `10.0.1.0/26`) to safely facilitate automated target instance scaling and host platform maintenance.

### Cost and SKU Analysis
* **Developer SKU Choice:** For this development and testing lifecycle, the **Azure Bastion Developer SKU** was prioritized. It routes traffic through shared cloud infrastructure to provide fully encrypted browser-based sessions for an architectural cost of **$0.00/hour** (completely free up to 5 GB outbound data transfer monthly). 
* **Production Alternative Comparison:** For an enterprise deployment, a upgrade to the **Basic** SKU (~$0.19/hour, approx. $138/month) or **Standard** SKU is necessary to unlock virtual network peering, concurrent session handling, native desktop client integration, and scale unit increments.

---

## 5. Multi-Method Access Verification

### Method A: Native IP-Restricted Connection
* **Verification Detail:** Successfully verified RDP and SSH tunnels by isolating matching source IP conditions within our initial NSG firewalls.


### Method B: Perimeter-Hardened Bastion Connection (Rubric Core)
* **Verification Detail:** Authenticated and managed our resources completely within a browser window wrapper using zero public IP points.

## 6. Development Lifecycle Troubleshooting
* **Symptom:** Azure Bastion deployment threw an instant validation failure during initialization setup.
* **Root Cause Analysis:** The core hosting Virtual Network subnet was named incorrectly or missed the needed subnet width bounds.
* **Resolution Engineering:** Reconstructed a clean, isolated subnet explicitly mapped to `AzureBastionSubnet` under an optimized `/26` configuration space to satisfy the resource provider requirements.