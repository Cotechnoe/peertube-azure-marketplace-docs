# Prérequis

Avant de déployer la VM PeerTube depuis Azure Marketplace, assurez-vous que les exigences suivantes sont satisfaites.

## Exigences Azure

- Un **abonnement Azure actif** avec les permissions de créer des machines virtuelles et les ressources associées (IP publique, groupe de sécurité réseau, disque).
- Taille de VM recommandée : **Standard_B2s** (2 vCPU, 4 Go RAM) ou supérieure.
- Régions supportées : East US et autres — vérifiez la disponibilité au moment du déploiement.

## Exigences réseau

- **Port 80 (HTTP)** et **port 443 (HTTPS)** doivent être accessibles depuis Internet pour que PeerTube soit joignable par les utilisateurs et pour l'émission du certificat TLS (si vous utilisez Let's Encrypt).
- **Port 22 (SSH)** est recommandé pour accéder aux journaux et récupérer le mot de passe administrateur, mais n'est pas requis pour la finalisation (celle-ci est automatique).

> Le déploiement Azure Marketplace crée un groupe de sécurité réseau (NSG) avec ces ports ouverts par défaut. Vous pouvez restreindre le port 22 à votre IP après la configuration initiale.

## DNS (recommandé)

Un **nom de domaine publiquement résolvable** pointant vers l'IP publique de la VM est fortement recommandé :

- Requis pour obtenir un certificat TLS valide via Let's Encrypt.
- Sans nom de domaine, l'instance utilisera un certificat auto-signé, que les navigateurs signaleront comme non approuvé.

Les labels DNS Azure Public IP (`<label>.eastus.cloudapp.azure.com`) sont supportés et suffisants pour les tests.

## Clé SSH (recommandée)

- Une **paire de clés SSH** est recommandée pour accéder aux journaux et récupérer le mot de passe administrateur après le déploiement.
- Si vous ne fournissez pas de clé SSH, le mot de passe administrateur PeerTube sera inaccessible.

## Prérequis en termes de compétences

- Aucune commande à exécuter pour déployer : la VM se configure seule.
- Une utilisation basique de SSH est utile pour récupérer le mot de passe administrateur.
- Aucune connaissance préalable de PeerTube n'est requise.
