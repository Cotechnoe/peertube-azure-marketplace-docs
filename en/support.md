# Support and FAQ

## Getting support

For issues related to this Azure Marketplace offer (deployment, finalization, VM configuration):

- **GitHub Issues**: [github.com/Cotechnoe/peertube-azure-marketplace-docs/issues](https://github.com/Cotechnoe/peertube-azure-marketplace-docs/issues)
- **Publisher**: Cotechnoe

For issues related to PeerTube itself (features, bugs, federation):

- **PeerTube documentation**: [docs.joinpeertube.org](https://docs.joinpeertube.org)
- **PeerTube community forum**: [framacolibri.org/c/peertube](https://framacolibri.org/c/peertube)

> This offer is packaged and maintained by Cotechnoe. Cotechnoe does not provide support for the PeerTube application itself beyond the deployment and initial configuration covered in this documentation.

---

## FAQ

### The VM is running but I cannot access PeerTube in my browser

1. Check that `peertube.service` is running: `sudo systemctl status peertube.service`.
2. Check that nginx is running: `sudo systemctl status nginx`.
3. Verify that ports 80 and 443 are open in the Azure NSG (Azure Portal → your VM → Networking).
4. If you are using a domain name, confirm it resolves to the VM's public IP: `dig +short <your-domain>`.

### The browser shows a security warning

This is expected if you are using the self-signed TLS certificate generated during finalization (when accessed via IP address or without a valid domain). Either:

- Click "Advanced" and proceed (for testing only).
- Configure a public domain name and use Let's Encrypt for a browser-trusted certificate.

### I forgot the admin password

Retrieve it from the system journal:

```bash
sudo journalctl -u peertube.service --no-pager | grep "User password"
```

If the password has been changed since initial setup, reset it via the PeerTube CLI:

```bash
cd /var/www/peertube/versions/peertube
sudo -u peertube NODE_CONFIG_DIR=/var/www/peertube/versions/peertube/config \
  NODE_ENV=production node dist/server/tools/peertube.js reset-password -u root
```

### PeerTube is running but video transcoding is very slow

Transcoding is CPU-intensive. The **Standard_B2s** size (2 vCPUs) is sufficient for light use. For production workloads with frequent uploads, consider resizing to a compute-optimized VM (e.g., **Standard_F4s_v2**) in the Azure Portal.

### How do I add a custom domain and a Let's Encrypt certificate?

1. Point your domain's DNS A record to the VM's public IP.
2. Stop nginx: `sudo systemctl stop nginx`.
3. Run certbot: `sudo certbot certonly --standalone -d <your-domain> --non-interactive --agree-tos --register-unsafely-without-email`.
4. Update the nginx configuration to reference the new certificate paths (`/etc/letsencrypt/live/<your-domain>/`).
5. Update `hostname` in `/var/www/peertube/versions/peertube/config/production.yaml`.
6. Restart nginx and PeerTube: `sudo systemctl start nginx && sudo systemctl restart peertube.service`.

### How do I stop the VM to save costs when not in use?

Deallocate the VM from the Azure Portal or CLI:

```bash
az vm deallocate -g <resource-group> -n <vm-name>
```

Note: the public IP address may change after reallocation unless you configure a static IP or a DNS label in Azure.

### Where is PeerTube data stored?

| Data | Location |
|------|----------|
| Videos and thumbnails | `/var/www/peertube/storage/` |
| Database | PostgreSQL — `peertube_prod` database |
| Configuration | `/var/www/peertube/versions/peertube/config/production.yaml` |
| Logs | `sudo journalctl -u peertube.service` |
