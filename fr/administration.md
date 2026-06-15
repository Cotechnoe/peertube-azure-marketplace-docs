# Administration de base

Cette page couvre les opérations essentielles pour gérer votre instance PeerTube sur Azure.

## Gestion du service

PeerTube fonctionne en tant que service systemd. Utilisez ces commandes pour le contrôler :

```bash
# Vérifier l'état
sudo systemctl status peertube.service

# Démarrer
sudo systemctl start peertube.service

# Arrêter
sudo systemctl stop peertube.service

# Redémarrer
sudo systemctl restart peertube.service

# Voir les logs (50 dernières lignes)
sudo journalctl -u peertube.service -n 50

# Suivre les logs en temps réel
sudo journalctl -u peertube.service -f
```

## Services associés

PeerTube dépend de trois services gérés indépendamment :

| Service | Vérifier l'état |
|---------|----------------|
| nginx (reverse proxy) | `sudo systemctl status nginx` |
| PostgreSQL (base de données) | `sudo systemctl status postgresql` |
| Redis (cache) | `sudo systemctl status redis-server` |

Les trois doivent être en cours d'exécution pour que PeerTube fonctionne correctement.

## Fichier de configuration

Le fichier de configuration principal de PeerTube se trouve à :

```
/var/www/peertube/versions/peertube/config/production.yaml
```

Après avoir modifié ce fichier, redémarrez PeerTube pour que les changements prennent effet :

```bash
sudo systemctl restart peertube.service
```

> Ne modifiez pas ce fichier pendant que PeerTube traite des uploads ou transcode des vidéos.

## Utilisation du disque

Le stockage vidéo se trouve sous :

```
/var/www/peertube/storage/
```

Surveillez l'utilisation du disque avec :

```bash
df -h /
du -sh /var/www/peertube/storage/
```

Le disque OS est provisionné à 30 Go par défaut. Si vous prévoyez d'héberger de grandes quantités de vidéos, attachez un disque de données séparé et configurez PeerTube pour l'utiliser via la section `storage` dans `production.yaml`.

## Mettre à jour PeerTube

La mise à jour de PeerTube nécessite des étapes manuelles. Suivez le guide de mise à jour officiel :

> [https://docs.joinpeertube.org/maintain/upgrade](https://docs.joinpeertube.org/maintain/upgrade)

Sauvegardez toujours votre base de données et votre stockage avant de mettre à jour.

## Sauvegarde

### Sauvegarde de la base de données

```bash
sudo -u postgres pg_dump peertube_prod > peertube_backup_$(date +%Y%m%d).sql
```

### Sauvegarde du stockage

```bash
sudo tar czf peertube_storage_$(date +%Y%m%d).tgz /var/www/peertube/storage/
```

Stockez les sauvegardes hors de la VM (Azure Blob Storage, poste de travail local, etc.).

## Renouvellement du certificat TLS nginx

Si vous avez utilisé **Let's Encrypt**, le renouvellement du certificat est géré automatiquement par le timer systemd certbot. Vérifiez qu'il est actif :

```bash
sudo systemctl status certbot.timer
```

Si vous avez utilisé le **certificat auto-signé** généré lors de la finalisation, il est valide 30 jours. Relancez le script de finalisation pour le régénérer, ou remplacez-le par un certificat Let's Encrypt en fournissant un nom de domaine.

## Pare-feu / NSG

Le groupe de sécurité réseau Azure (NSG) contrôle le trafic entrant. Règles recommandées après le déploiement :

| Port | Protocole | Utilisation | Restriction |
|------|-----------|------------|-------------|
| 22 | TCP | Administration SSH | Restreindre à votre IP |
| 80 | TCP | HTTP (redirection vers HTTPS) | Ouvert |
| 443 | TCP | HTTPS (PeerTube) | Ouvert |

Ajustez les règles NSG dans le portail Azure sous **Réseau** sur la page de votre VM.
