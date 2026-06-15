# Access the VM

After the VM is deployed from Azure Marketplace, connect to it via SSH to complete the finalization.

## Prerequisites

- The VM is running (check in the Azure Portal under **Virtual machines**).
- You have the private SSH key corresponding to the public key you provided at deployment.
- Port 22 is open in the Network Security Group (default after Marketplace deployment).

## Find the public IP address

1. Open the [Azure Portal](https://portal.azure.com).
2. Navigate to **Virtual machines** and select your VM.
3. The **Public IP address** is displayed on the Overview page.

Alternatively, using Azure CLI:

```bash
az vm show -d -g <resource-group> -n <vm-name> --query publicIps -o tsv
```

## Connect via SSH

```bash
ssh -i /path/to/your/private-key azureuser@<public-ip>
```

Replace:
- `/path/to/your/private-key` with the path to your SSH private key.
- `<public-ip>` with the VM's public IP address or DNS label.

The default admin username is **`azureuser`** (set by Azure at deployment).

## Verify the connection

Once connected, confirm the OS and that the PeerTube stack is present:

```bash
lsb_release -d
# Expected: Ubuntu 22.04.x LTS

systemctl list-units --type=service | grep -E "nginx|postgresql|redis"
# nginx.service, postgresql.service, redis-server.service should be listed
```

> At this stage, `peertube.service` is **not yet running** — this is expected. PeerTube requires finalization before it can start. See [Finalize PeerTube](finalize.md).

## Troubleshooting

| Problem | Cause | Solution |
|---------|-------|----------|
| Connection timeout | Port 22 blocked | Check NSG inbound rules in the Azure Portal |
| Permission denied (publickey) | Wrong key or user | Verify the key path and that you are connecting as `azureuser` |
| VM unreachable | VM stopped or deallocated | Start the VM from the Azure Portal |
