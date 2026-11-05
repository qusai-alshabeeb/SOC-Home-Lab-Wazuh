# SOC Home Lab – Installation Guide

## 1. Overview

This guide documents the installation and initial configuration of the SOC Home Lab infrastructure using VMware Workstation, Ubuntu Server, and Wazuh SIEM.

The goal is to deploy a centralized security monitoring platform that collects and analyzes security events from Windows endpoints.

## 2. System Requirements

### Host Machine

| Component | Specification |
|---|---|
| Operating System | Windows 11 |
| Processor | Intel Core Ultra 5 |
| RAM | 16 GB |
| Storage | 512 GB SSD |
| Virtualization Software | VMware Workstation 17.6.0 |

### Wazuh Server Virtual Machine

| Component | Configuration |
|---|---|
| Operating System | Ubuntu Server 24.04.5 LTS |
| RAM | 8 GB |
| CPU | 4 vCPUs |
| Disk | 80 GB |
| Network Adapter | NAT |
| Hostname | wazuh-server |
| Username | socadmin |
| IP Address | 192.168.73.132 |

## 3. Ubuntu Server Installation

1. Create a new virtual machine in VMware Workstation.
2. Select the Ubuntu Server 24.04.5 LTS ISO image.
3. Configure the virtual machine with 8 GB RAM, 4 CPU cores, and an 80 GB virtual disk.
4. Set the network adapter to NAT.
5. Complete the Ubuntu Server installation.
6. Configure the hostname as `wazuh-server`.
7. Create the administrative user `socadmin`.
8. Complete the installation and reboot the virtual machine.

## 4. Disk Configuration

After installation, the root logical volume was expanded to use the available free space.

The following command was used:

```bash
sudo lvextend -l +100%FREE -r /dev/ubuntu-vg/ubuntu-lv
```

This expanded the root filesystem using the remaining free space in the volume group.

## 5. Wazuh SIEM Installation

Wazuh was installed using the official all-in-one installation script.

### Step 1: Download the Installation Script

```bash
curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh
```

### Step 2: Run the Installer

```bash
sudo bash ./wazuh-install.sh -a
```

The `-a` option performs an all-in-one installation, deploying the main Wazuh components:

- Wazuh Manager
- Wazuh Indexer
- Wazuh Dashboard

### Step 3: Save Installation Credentials

After installation, the script provides the initial dashboard credentials.

The administrator password should be stored securely and must not be published in the GitHub repository.

## 6. Accessing the Wazuh Dashboard

After installation, the dashboard can be accessed through a web browser using the server IP address:

```text
https://192.168.73.132
```

Log in using the administrator credentials generated during installation.

A browser security warning may appear because the dashboard uses a self-signed certificate in the default setup.

## 7. Verify Wazuh Services

The following commands can be used to check the status of the main Wazuh services:

```bash
sudo systemctl status wazuh-manager
sudo systemctl status wazuh-indexer
sudo systemctl status wazuh-dashboard
```

All three services should be running successfully.

## 8. Installation Results

The installation was completed successfully.

The Wazuh server is accessible through its dashboard, and the environment is ready for Windows endpoint integration.

| Component | Status |
|---|---|
| Ubuntu Server | Installed |
| Wazuh Manager | Running |
| Wazuh Indexer | Running |
| Wazuh Dashboard | Accessible |
| Network Configuration | NAT |
| Initial Deployment | Completed |

## 9. Security Considerations

- Never upload administrator passwords or API keys to GitHub.
- Use secure credentials for all virtual machines.
- Keep the Wazuh server and its components updated.
- Restrict access to the management dashboard.
- Perform security testing only in authorized lab environments.

## 10. Next Steps

The next stage involves deploying Wazuh agents on Windows endpoints and verifying that security events are successfully collected by the central server.
