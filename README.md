# Azure Virtual Machine Setup and Remote Connection Lab

## 1. Project Overview
This repository documents the secure configuration, deployment, and remote system management of Windows and Linux cloud infrastructure using Microsoft Azure.

## 2. Infrastructure Configuration Summary
* **Resource Group:** RemoteLearning-RG
* **Windows VM Name:** WinAdminVM
* **Linux VM Name:** LinuxAdminVM

### Network Security Group (NSG) Rules List

#### Windows Inbound Security Rule
* **Rule Name:** Allow_RDP_From_MyIP
* **Destination Port:** 3389
* **Protocol:** TCP
* **Source IP Restriction:** My Local Public IP
* **Action:** Allow
* **Priority:** 100

#### Linux Inbound Security Rule
* **Rule Name:** Allow_SSH_From_MyIP
* **Destination Port:** 22
* **Protocol:** TCP
* **Source IP Restriction:** My Local Public IP
* **Action:** Allow
* **Priority:** 100

## 3. Remote Connection Process & Verification

### Windows Connection (RDP)
* **Client Used:** Native Windows Remote Desktop Connection (mstsc)
* **Verification Activity:** Opened PowerShell and executed system hostname validations.


### Linux Connection (SSH)
* **Client Used:** Local terminal via OpenSSH client
* **Key Command Used:** `ssh -i LinuxAdminVM_key.pem azureuser@<IP>`
* **Verification Activity:** Evaluated storage architecture via `df -h` and system kernel via `uname -a`.

## 4. Troubleshooting Steps
* **Issue encountered:** None. Connections were successful on the first attempt due to precise IP matching in the Network Security Group configuration.
* **Resolution:** Ensured local public IP was appended with a `/32` CIDR mask to guarantee exact matching on the incoming Azure firewall rules.