# PeerTube on Azure Marketplace — Documentation

This repository contains the public user documentation for the **PeerTube VM offer** on Microsoft Azure Marketplace, published by **Cotechnoe**.

> **English is the canonical language.** French translations are maintained in parallel under `fr/`.

---

## Contents

| Page | English | Français |
|------|---------|----------|
| Overview | [en/overview.md](en/overview.md) | [fr/apercu.md](fr/apercu.md) |
| Prerequisites | [en/prerequisites.md](en/prerequisites.md) | [fr/prerequis.md](fr/prerequis.md) |
| Access the VM | [en/access-vm.md](en/access-vm.md) | [fr/acces-vm.md](fr/acces-vm.md) |
| Finalize PeerTube | [en/finalize.md](en/finalize.md) | [fr/finalisation.md](fr/finalisation.md) |
| Basic Administration | [en/administration.md](en/administration.md) | [fr/administration.md](fr/administration.md) |
| Support & FAQ | [en/support.md](en/support.md) | [fr/support.md](fr/support.md) |

---

## About this offer

- **Product**: [PeerTube](https://joinpeertube.org) — self-hosted, federated video platform
- **Publisher**: Cotechnoe
- **Base OS**: Ubuntu 22.04 LTS (Gen2)
- **Azure region**: East US (and others depending on availability)

## Software stack

Versions installed in the VM image:

| Component | Version | Notes |
|-----------|---------|-------|
| **PeerTube** | **8.2.0** | Built from source |
| Node.js | 24.x LTS | Via NodeSource repository |
| pnpm | 10.x | Package manager for PeerTube |
| PostgreSQL | 14.x | Ubuntu 22.04 package |
| Redis | 8.8.0 | Via official Redis repository (packages.redis.io) |
| nginx | 1.18.x | Ubuntu 22.04 package |
| ffmpeg | 7.0.2 | Static build from [johnvansickle.com](https://johnvansickle.com/ffmpeg/) |
| certbot | 1.21.0 | Let's Encrypt TLS automation |

## License

Documentation content is licensed under [CC BY 4.0](LICENSE).
