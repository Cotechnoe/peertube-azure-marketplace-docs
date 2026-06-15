# Support et FAQ

## Obtenir de l'aide

Pour les problèmes liés à cette offre Azure Marketplace (déploiement, finalisation, configuration de la VM) :

- **GitHub Issues** : [github.com/Cotechnoe/peertube-azure-marketplace-docs/issues](https://github.com/Cotechnoe/peertube-azure-marketplace-docs/issues)
- **Éditeur** : Cotechnoe

Pour les problèmes liés à PeerTube lui-même (fonctionnalités, bugs, fédération) :

- **Documentation PeerTube** : [docs.joinpeertube.org](https://docs.joinpeertube.org)
- **Forum communautaire PeerTube** : [framacolibri.org/c/peertube](https://framacolibri.org/c/peertube)

> Cette offre est packagée et maintenue par Cotechnoe. Cotechnoe ne fournit pas de support pour l'application PeerTube elle-même au-delà du déploiement et de la configuration initiale couverts dans cette documentation.

---

## FAQ

### La VM fonctionne mais je ne peux pas accéder à PeerTube dans mon navigateur

1. Vérifiez que `peertube.service` est en cours d'exécution : `sudo systemctl status peertube.service`.
2. Vérifiez que nginx est en cours d'exécution : `sudo systemctl status nginx`.
3. Vérifiez que les ports 80 et 443 sont ouverts dans le NSG Azure (portail Azure → votre VM → Réseau).
4. Si vous utilisez un nom de domaine, confirmez qu'il résout vers l'IP publique de la VM : `dig +short <votre-domaine>`.

### Le navigateur affiche un avertissement de sécurité

C'est attendu si vous utilisez le certificat TLS auto-signé généré lors de la finalisation (lors d'un accès via IP ou sans domaine valide). Vous pouvez soit :

- Cliquer sur « Avancé » et continuer (pour les tests uniquement).
- Configurer un nom de domaine public et utiliser Let's Encrypt pour un certificat approuvé par les navigateurs.

### J'ai oublié le mot de passe administrateur

Récupérez-le depuis le journal système :

```bash
sudo journalctl -u peertube.service --no-pager | grep "User password"
```

Si le mot de passe a été changé depuis l'installation initiale, réinitialisez-le via la CLI PeerTube :

```bash
cd /var/www/peertube/versions/peertube
sudo -u peertube NODE_CONFIG_DIR=/var/www/peertube/versions/peertube/config \
  NODE_ENV=production node dist/server/tools/peertube.js reset-password -u root
```

### PeerTube fonctionne mais le transcodage vidéo est très lent

Le transcodage est intensif en CPU. La taille **Standard_B2s** (2 vCPU) est suffisante pour un usage léger. Pour des charges de production avec des uploads fréquents, envisagez de redimensionner vers une VM optimisée pour le calcul (ex. **Standard_F4s_v2**) dans le portail Azure.

### Comment ajouter un domaine personnalisé et un certificat Let's Encrypt ?

1. Pointez l'enregistrement DNS A de votre domaine vers l'IP publique de la VM.
2. Arrêtez nginx : `sudo systemctl stop nginx`.
3. Exécutez certbot : `sudo certbot certonly --standalone -d <votre-domaine> --non-interactive --agree-tos --register-unsafely-without-email`.
4. Mettez à jour la configuration nginx pour référencer les nouveaux chemins de certificat (`/etc/letsencrypt/live/<votre-domaine>/`).
5. Mettez à jour `hostname` dans `/var/www/peertube/versions/peertube/config/production.yaml`.
6. Redémarrez nginx et PeerTube : `sudo systemctl start nginx && sudo systemctl restart peertube.service`.

### Comment arrêter la VM pour économiser des coûts quand elle n'est pas utilisée ?

Désallouez la VM depuis le portail Azure ou la CLI :

```bash
az vm deallocate -g <groupe-de-ressources> -n <nom-vm>
```

Note : l'adresse IP publique peut changer après réallocation, sauf si vous configurez une IP statique ou un label DNS Azure.

### Où sont stockées les données PeerTube ?

| Donnée | Emplacement |
|--------|------------|
| Vidéos et miniatures | `/var/www/peertube/storage/` |
| Base de données | PostgreSQL — base `peertube_prod` |
| Configuration | `/var/www/peertube/versions/peertube/config/production.yaml` |
| Logs | `sudo journalctl -u peertube.service` |
