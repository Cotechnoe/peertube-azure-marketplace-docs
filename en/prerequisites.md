# Prerequisites

Before deploying the PeerTube VM from Azure Marketplace, ensure the following requirements are met.

## Azure requirements

- An active **Azure subscription** with permission to create virtual machines and associated resources (public IP, network security group, disk).
- Recommended VM size: **Standard_B2s** (2 vCPUs, 4 GB RAM) or larger.
- Supported regions: East US and others — check availability at deploy time.

## Networking requirements

- **Port 22 (SSH)** must be accessible from your workstation to complete the finalization step.
- **Port 80 (HTTP)** and **Port 443 (HTTPS)** must be accessible from the internet for PeerTube to be reachable by users and for TLS certificate issuance (if using Let's Encrypt).

> The Azure Marketplace deployment creates a Network Security Group (NSG) with these ports open by default. You may restrict port 22 to your IP after finalization.

## DNS (recommended)

A **publicly resolvable domain name** pointing to the VM's public IP is strongly recommended:

- Required for a valid TLS certificate via Let's Encrypt.
- Without a domain name, the instance will use a self-signed certificate, which browsers will flag as untrusted.

Azure Public IP DNS labels (`<label>.eastus.cloudapp.azure.com`) are supported and sufficient for testing.

## SSH key

- An **SSH key pair** is required at VM creation time (Azure injects the public key).
- You must have the corresponding private key available locally to connect after deployment.

## Knowledge prerequisites

- Basic Linux command-line usage (connecting via SSH, running commands as `sudo`).
- No prior PeerTube knowledge is required to complete the deployment.
