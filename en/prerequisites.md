# Prerequisites

Before deploying the PeerTube VM from Azure Marketplace, ensure the following requirements are met.

## Azure requirements

- An active **Azure subscription** with permission to create virtual machines and associated resources (public IP, network security group, disk).
- Recommended VM size: **Standard_B2s** (2 vCPUs, 4 GB RAM) or larger.
- Supported regions: East US and others — check availability at deploy time.

## Networking requirements

- **Port 80 (HTTP)** and **Port 443 (HTTPS)** must be accessible from the internet for PeerTube to be reachable by users and for TLS certificate issuance (if using Let's Encrypt).
- **Port 22 (SSH)** is recommended for accessing logs and retrieving the administrator password, but is not required for finalization (which is automatic).

> The Azure Marketplace deployment creates a Network Security Group (NSG) with these ports open by default. You may restrict port 22 to your IP after initial setup.

## DNS (recommended)

A **publicly resolvable domain name** pointing to the VM's public IP is strongly recommended:

- Required for a valid TLS certificate via Let's Encrypt.
- Without a domain name, the instance will use a self-signed certificate, which browsers will flag as untrusted.

Azure Public IP DNS labels (`<label>.eastus.cloudapp.azure.com`) are supported and sufficient for testing.

## SSH key (recommended)

- An **SSH key pair** is recommended so you can access logs and retrieve the administrator password after deployment.
- Without an SSH key, the PeerTube administrator password will be inaccessible.

## Knowledge prerequisites

- No commands to run for deployment: the VM configures itself automatically.
- Basic SSH usage is helpful for retrieving the administrator password.
- No prior PeerTube knowledge is required.
