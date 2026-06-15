# Accès à la VM

Après le déploiement de la VM depuis Azure Marketplace, connectez-vous via SSH pour effectuer la finalisation.

## Prérequis

- La VM est démarrée (vérifiez dans le portail Azure sous **Machines virtuelles**).
- Vous disposez de la clé SSH privée correspondant à la clé publique fournie au moment du déploiement.
- Le port 22 est ouvert dans le groupe de sécurité réseau (par défaut après déploiement Marketplace).

## Trouver l'adresse IP publique

1. Ouvrez le [portail Azure](https://portal.azure.com).
2. Accédez à **Machines virtuelles** et sélectionnez votre VM.
3. L'**adresse IP publique** est affichée sur la page Vue d'ensemble.

Alternativement, avec Azure CLI :

```bash
az vm show -d -g <groupe-de-ressources> -n <nom-vm> --query publicIps -o tsv
```

## Se connecter via SSH

```bash
ssh -i /chemin/vers/votre/clé-privée azureuser@<ip-publique>
```

Remplacez :
- `/chemin/vers/votre/clé-privée` par le chemin vers votre clé SSH privée.
- `<ip-publique>` par l'adresse IP publique ou le label DNS de la VM.

Le nom d'utilisateur admin par défaut est **`azureuser`** (défini par Azure au déploiement).

## Vérifier la connexion

Une fois connecté, confirmez le système d'exploitation et la présence de la stack PeerTube :

```bash
lsb_release -d
# Attendu : Ubuntu 22.04.x LTS

systemctl list-units --type=service | grep -E "nginx|postgresql|redis"
# nginx.service, postgresql.service, redis-server.service doivent être listés
```

> À ce stade, `peertube.service` n'est **pas encore démarré** — c'est attendu. PeerTube nécessite une finalisation avant de pouvoir démarrer. Consultez [Finalisation de PeerTube](finalisation.md).

## Dépannage

| Problème | Cause | Solution |
|----------|-------|----------|
| Délai de connexion | Port 22 bloqué | Vérifiez les règles entrantes du NSG dans le portail Azure |
| Permission refusée (publickey) | Mauvaise clé ou mauvais utilisateur | Vérifiez le chemin de la clé et que vous vous connectez en tant qu'`azureuser` |
| VM inaccessible | VM arrêtée ou désallouée | Démarrez la VM depuis le portail Azure |
