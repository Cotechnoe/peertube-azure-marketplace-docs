# Overview

**PeerTube on Azure** is a virtual machine offer published by Cotechnoe on the Microsoft Azure Marketplace. It lets you deploy a self-hosted, federated video platform on an Azure VM in a few minutes.

## What is PeerTube?

[PeerTube](https://joinpeertube.org) is an open-source, self-hosted video platform developed by Framasoft. It supports:

- video hosting and streaming (HLS, WebTorrent);
- federation with other PeerTube instances via ActivityPub;
- live streaming;
- user channels, playlists, and subscriptions.

## What this offer provides

The Azure Marketplace image ships a pre-installed, pre-configured environment:

| Component | Version | Notes |
|-----------|---------|-------|
| Ubuntu Server | 22.04 LTS (Gen2) | |
| **PeerTube** | **8.2.0** | Built from source |
| Node.js | 24.x LTS | NodeSource repository |
| pnpm | 10.x | PeerTube package manager |
| PostgreSQL | 14.x | Ubuntu 22.04 package |
| Redis | 8.8.0 | Official packages.redis.io repository |
| nginx | 1.18.x | Ubuntu 22.04 package |
| ffmpeg | 7.0.2 | Static build from johnvansickle.com |
| certbot | 1.21.0 | Automatic TLS renewal |

The image does **not** include:

- a pre-configured domain name or TLS certificate (generated at first run);
- pre-existing user accounts or passwords (all credentials are generated at finalization).

## How it works

1. Deploy the VM from Azure Marketplace.
2. The VM configures itself automatically on first boot (3 to 8 minutes).
3. Connect via SSH to retrieve the administrator password.
4. Open `https://<public-ip-or-domain>` in a browser.

You can optionally customize the hostname, email, and passwords in the **Custom data** field (Advanced tab) when creating the VM.

See [Finalize PeerTube](finalize.md) for details.

## Intended audience

This offer is designed for:

- system administrators deploying a self-hosted video platform;
- organizations requiring data sovereignty for video content;
- developers evaluating PeerTube in a cloud environment.

Basic Linux administration knowledge is expected.
