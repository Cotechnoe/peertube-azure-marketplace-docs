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

## Récupération des mots de passe

Tous les secrets sont générés sur la VM au premier boot — ils ne sont jamais stockés dans l'image.

### Mot de passe administrateur PeerTube (`root`)

Généré par PeerTube lui-même au premier démarrage. Récupérez-le depuis le journal système :

```bash
sudo journalctl -u peertube.service --no-pager | grep "User password"
```

Exemple de sortie :
```
Jun 22 08:00:01 ma-vm node[1234]: User password: AbCdEf123456
```

Ce mot de passe est utilisé pour se connecter à l'interface web en tant que `root`.

### Mot de passe PostgreSQL

Écrit dans le fichier de configuration de PeerTube :

```bash
sudo grep 'password' /var/www/peertube/versions/peertube/config/production.yaml
```

Vous pouvez également le lire directement depuis le champ `database.password` :

```bash
sudo python3 -c "import yaml; cfg=yaml.safe_load(open('/var/www/peertube/versions/peertube/config/production.yaml')); print(cfg['database']['password'])"
```

### Secret applicatif PeerTube

Écrit dans le même fichier sous `secrets.peertube` :

```bash
sudo python3 -c "import yaml; cfg=yaml.safe_load(open('/var/www/peertube/versions/peertube/config/production.yaml')); print(cfg['secrets']['peertube'])"
```

### Si les mots de passe ont été fournis via Custom data

Si vous avez renseigné `peertube_db_password` ou `peertube_secret` dans le champ **Custom data** lors de la création de la VM, ce sont ces valeurs qui ont été utilisées. Elles sont également consultables dans `production.yaml` comme indiqué ci-dessus.

### Réinitialiser le mot de passe administrateur PeerTube

Si vous avez perdu le mot de passe `root`, réinitialisez-le via la CLI PeerTube :

```bash
cd /var/www/peertube/versions/peertube
sudo -u peertube NODE_CONFIG_DIR=/var/www/peertube/versions/peertube/config \
  NODE_ENV=production node dist/server/tools/peertube-auth.js reset-password \
  -u root -p NouveauMotDePasse
```

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

## Liens utiles

### Documentation PeerTube

| Sujet | URL |
|-------|-----|
| Configuration via l'interface web | [docs.joinpeertube.org/admin/configuration](https://docs.joinpeertube.org/admin/configuration) |
| Configuration système (`production.yaml`) | [docs.joinpeertube.org/maintain/configuration](https://docs.joinpeertube.org/maintain/configuration) |
| Gestion des utilisateurs et authentification | [docs.joinpeertube.org/admin/managing-users](https://docs.joinpeertube.org/admin/managing-users) |
| Modération de l'instance | [docs.joinpeertube.org/admin/moderation](https://docs.joinpeertube.org/admin/moderation) |
| Journaux PeerTube | [docs.joinpeertube.org/admin/logs](https://docs.joinpeertube.org/admin/logs) |
| Outils CLI serveur | [docs.joinpeertube.org/maintain/tools](https://docs.joinpeertube.org/maintain/tools) |
| Mise à jour de PeerTube | [docs.joinpeertube.org/maintain/upgrade](https://docs.joinpeertube.org/maintain/upgrade) |
| Stockage distant S3 | [docs.joinpeertube.org/maintain/remote-storage](https://docs.joinpeertube.org/maintain/remote-storage) |
| Migration d'instance | [docs.joinpeertube.org/maintain/migration](https://docs.joinpeertube.org/maintain/migration) |
| Surveillance / Observabilité | [docs.joinpeertube.org/maintain/observability](https://docs.joinpeertube.org/maintain/observability) |
| Fédération et redondance | [docs.joinpeertube.org/admin/following-instances](https://docs.joinpeertube.org/admin/following-instances) |
| Personnaliser l'instance | [docs.joinpeertube.org/admin/customize-instance](https://docs.joinpeertube.org/admin/customize-instance) |
| Guide de confidentialité | [docs.joinpeertube.org/admin/privacy-guide](https://docs.joinpeertube.org/admin/privacy-guide) |

### Documentation Azure VM

| Sujet | URL |
|-------|-----|
| Vue d'ensemble des VMs Linux Azure | [learn.microsoft.com — Vue d'ensemble des VMs Linux](https://learn.microsoft.com/fr-fr/azure/virtual-machines/linux/overview) |
| Gérer les groupes de sécurité réseau (NSG) | [learn.microsoft.com — Groupes de sécurité réseau](https://learn.microsoft.com/fr-fr/azure/virtual-network/manage-network-security-group) |
| Attacher un disque de données à une VM Linux | [learn.microsoft.com — Attacher un disque de données](https://learn.microsoft.com/fr-fr/azure/virtual-machines/linux/attach-disk-portal) |
| Surveiller une VM Azure avec Azure Monitor | [learn.microsoft.com — Surveiller les VMs avec Azure Monitor](https://learn.microsoft.com/fr-fr/azure/virtual-machines/monitor-vm) |
| Sauvegarder une VM Azure avec Azure Backup | [learn.microsoft.com — Azure Backup pour VMs](https://learn.microsoft.com/fr-fr/azure/backup/backup-azure-vms-introduction) |
| Options de disponibilité des VMs Azure | [learn.microsoft.com — Disponibilité des VMs](https://learn.microsoft.com/fr-fr/azure/virtual-machines/availability) |
| Redimensionner une VM Azure | [learn.microsoft.com — Redimensionner une VM](https://learn.microsoft.com/fr-fr/azure/virtual-machines/resize-vm) |
| Custom data et cloud-init sur Azure | [learn.microsoft.com — Custom data et cloud-init](https://learn.microsoft.com/fr-fr/azure/virtual-machines/custom-data) |
