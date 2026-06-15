# Prérequis

Avant de déployer la VM PeerTube depuis Azure Marketplace, assurez-vous que les exigences suivantes sont satisfaites.

## Exigences Azure

- Un **abonnement Azure actif** avec les permissions de créer des machines virtuelles et les ressources associées (IP publique, groupe de sécurité réseau, disque).
- Taille de VM recommandée : **Standard_B2s** (2 vCPU, 4 Go RAM) ou supérieure.
- Régions supportées : East US et autres — vérifiez la disponibilité au moment du déploiement.

## Exigences réseau

- **Port 22 (SSH)** doit être accessible depuis votre poste de travail pour effectuer l'étape de finalisation.
- **Port 80 (HTTP)** et **port 443 (HTTPS)** doivent être accessibles depuis Internet pour que PeerTube soit joignable par les utilisateurs et pour l'émission du certificat TLS (si vous utilisez Let's Encrypt).

> Le déploiement Azure Marketplace crée un groupe de sécurité réseau (NSG) avec ces ports ouverts par défaut. Vous pouvez restreindre le port 22 à votre IP après la finalisation.

## DNS (recommandé)

Un **nom de domaine publiquement résolvable** pointant vers l'IP publique de la VM est fortement recommandé :

- Requis pour obtenir un certificat TLS valide via Let's Encrypt.
- Sans nom de domaine, l'instance utilisera un certificat auto-signé, que les navigateurs signaleront comme non approuvé.

Les labels DNS Azure Public IP (`<label>.eastus.cloudapp.azure.com`) sont supportés et suffisants pour les tests.

## Clé SSH

- Une **paire de clés SSH** est requise au moment de la création de la VM (Azure injecte la clé publique).
- Vous devez disposer de la clé privée correspondante en local pour vous connecter après le déploiement.

## Prérequis en termes de compétences

- Utilisation basique de la ligne de commande Linux (connexion SSH, exécution de commandes en `sudo`).
- Aucune connaissance préalable de PeerTube n'est requise pour réaliser le déploiement.
