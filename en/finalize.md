# Finalize the PeerTube Instance

After connecting to the VM, run the finalization script to turn the deployed image into a working PeerTube instance.

## What finalization does

The finalization script runs once on the VM and performs these steps:

1. Generates the PeerTube configuration file (`production.yaml`) with your hostname, a random database password, and a random application secret.
2. Creates the PostgreSQL database and role for PeerTube.
3. Generates a TLS certificate for HTTPS (self-signed, or Let's Encrypt if a domain name is provided).
4. Configures and enables the nginx reverse proxy.
5. Installs and starts `peertube.service`.
6. Waits for the application to respond on port 443.

All credentials (database password, application secret) are generated on the VM at runtime — they are never stored in the image.

## Run the finalization script

Once connected via SSH:

```bash
sudo /var/www/peertube/scripts/finalize-peertube-vm.sh
```

> The script is idempotent: if it has already been run, it will skip steps that are already complete unless you force a re-run.

You will be prompted to provide (or confirm):

- **Hostname**: the domain name or public IP address that users will use to access PeerTube.
- **Admin email**: used as the PeerTube super-administrator's email address.

## Wait for startup

The script waits until PeerTube is reachable. This takes approximately **60 to 120 seconds** on the first run because PeerTube initializes its database schema and creates the administrator account.

## Retrieve the admin password

The initial administrator password is generated automatically. After startup, retrieve it from the system journal:

```bash
sudo journalctl -u peertube.service --no-pager | grep "User password"
```

Example output:
```
Jun 14 20:12:57 my-vm node[1910]: User password: <generated-password>
```

Save this password — you will use it to log in to the PeerTube web interface as `root`.

## Verify the instance

Open a browser and navigate to `https://<your-hostname>`.

- If you used a public IP or a self-signed certificate, your browser will display a security warning. This is expected — proceed to the site.
- If you configured a domain name with Let's Encrypt, the certificate will be valid.

Check that you can:

1. See the PeerTube home page.
2. Log in as `root` with the password retrieved above.
3. Navigate to **Administration > Overview** to confirm the server is running.

## Troubleshooting

| Problem | Cause | Solution |
|---------|-------|----------|
| Script exits with an error | Missing dependency | Check the output — the script reports the missing component |
| PeerTube home page not loading after 3 minutes | Service startup issue | Run `sudo systemctl status peertube.service` and check logs with `sudo journalctl -u peertube.service -n 50` |
| Browser certificate error | Self-signed certificate | Expected — click "Advanced" and proceed, or configure a valid domain with Let's Encrypt |
| Cannot retrieve admin password | Logs not yet written | Wait 30 seconds and retry the `journalctl` command |
