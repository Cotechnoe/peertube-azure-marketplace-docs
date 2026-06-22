# Aperçu

**PeerTube sur Azure** est une offre de machine virtuelle publiée par Cotechnoe sur Microsoft Azure Marketplace. Elle vous permet de déployer une plateforme vidéo auto-hébergée et fédérée sur une VM Azure en quelques minutes.

## Qu'est-ce que PeerTube ?

[PeerTube](https://joinpeertube.org) est une plateforme vidéo open source auto-hébergée développée par Framasoft. Elle prend en charge :

- l'hébergement et la diffusion de vidéos (HLS, WebTorrent) ;
- la fédération avec d'autres instances PeerTube via ActivityPub ;
- la diffusion en direct (live streaming) ;
- les chaînes, playlists et abonnements utilisateurs.

## Ce que cette offre fournit

L'image Azure Marketplace contient un environnement pré-installé et pré-configuré :

| Composant | Version | Notes |
|-----------|---------|-------|
| Ubuntu Server | 22.04 LTS (Gen2) | |
| **PeerTube** | **8.2.0** | Compilé depuis les sources |
| Node.js | 24.x LTS | Dépôt NodeSource |
| pnpm | 10.x | Gestionnaire de paquets PeerTube |
| PostgreSQL | 14.x | Paquet Ubuntu 22.04 |
| Redis | 8.8.0 | Dépôt officiel packages.redis.io |
| nginx | 1.18.x | Paquet Ubuntu 22.04 |
| ffmpeg | 7.0.2 | Build statique johnvansickle.com |
| certbot | 1.21.0 | Renouvellement TLS automatique |

L'image ne contient **pas** :

- de nom de domaine ou de certificat TLS pré-configurés (générés à la première exécution) ;
- de comptes utilisateurs ou de mots de passe existants (tous les identifiants sont générés à la finalisation).

## Fonctionnement

1. Déployez la VM depuis Azure Marketplace.
2. La VM se configure entièrement seule au premier boot (3 à 8 minutes).
3. Connectez-vous en SSH pour récupérer le mot de passe administrateur.
4. Ouvrez `https://<ip-publique-ou-domaine>` dans un navigateur.

Vous pouvez optionnellement personnaliser le hostname, l'email et les mots de passe dans le champ **Custom data** (onglet Avancé) lors de la création de la VM.

Consultez [Finalisation de PeerTube](finalisation.md) pour les détails.

## Public cible

Cette offre est destinée à :

- des administrateurs système déployant une plateforme vidéo auto-hébergée ;
- des organisations nécessitant la souveraineté des données pour leurs contenus vidéo ;
- des développeurs souhaitant évaluer PeerTube en environnement cloud.

Des connaissances de base en administration Linux sont requises.
