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

| Component | Version |
|-----------|---------|
| Ubuntu Server | 22.04 LTS (Gen2) |
| PeerTube | Latest stable at build time |
| Node.js | ≥ 18 LTS |
| PostgreSQL | 14 |
| Redis | 6 |
| nginx | Latest stable |
| ffmpeg | Ubuntu 22.04 package |

The image does **not** include:

- a pre-configured domain name or TLS certificate (generated at first run);
- pre-existing user accounts or passwords (all credentials are generated at finalization).

## How it works

1. Deploy the VM from Azure Marketplace.
2. Connect via SSH.
3. Run the finalization script — it configures the domain, database, TLS, and starts PeerTube.
4. Open `https://<your-domain-or-ip>` in a browser.

See [Finalize PeerTube](finalize.md) for the complete procedure.

## Intended audience

This offer is designed for:

- system administrators deploying a self-hosted video platform;
- organizations requiring data sovereignty for video content;
- developers evaluating PeerTube in a cloud environment.

Basic Linux administration knowledge is expected.
