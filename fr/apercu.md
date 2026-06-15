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

| Composant | Version |
|-----------|---------|
| Ubuntu Server | 22.04 LTS (Gen2) |
| PeerTube | Dernière version stable à la date de build |
| Node.js | ≥ 18 LTS |
| PostgreSQL | Ubuntu 22.04 LTS (défaut) |
| Redis | Ubuntu 22.04 LTS (défaut) |
| nginx | Dernière version stable |
| ffmpeg | Paquet Ubuntu 22.04 |

L'image ne contient **pas** :

- de nom de domaine ou de certificat TLS pré-configurés (générés à la première exécution) ;
- de comptes utilisateurs ou de mots de passe existants (tous les identifiants sont générés à la finalisation).

## Fonctionnement

1. Déployez la VM depuis Azure Marketplace.
2. Connectez-vous via SSH.
3. Exécutez le script de finalisation — il configure le domaine, la base de données, le TLS et démarre PeerTube.
4. Ouvrez `https://<votre-domaine-ou-ip>` dans un navigateur.

Consultez [Finalisation de PeerTube](finalisation.md) pour la procédure complète.

## Public cible

Cette offre est destinée à :

- des administrateurs système déployant une plateforme vidéo auto-hébergée ;
- des organisations nécessitant la souveraineté des données pour leurs contenus vidéo ;
- des développeurs souhaitant évaluer PeerTube en environnement cloud.

Des connaissances de base en administration Linux sont requises.
