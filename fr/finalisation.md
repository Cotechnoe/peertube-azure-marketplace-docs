# Finalisation de l'instance PeerTube

**Aucune intervention n'est requise.** La VM se configure entièrement toute seule au premier démarrage.

## Ce qui se passe automatiquement

Au premier boot, le service systemd `peertube-firstboot.service` s'exécute automatiquement et effectue ces étapes :

1. Détermine le nom d'hôte public de la VM (via Azure IMDS ou le champ Custom data).
2. Génère un mot de passe PostgreSQL et un secret applicatif aléatoires (sauf si fournis dans Custom data).
3. Génère le fichier de configuration PeerTube (`production.yaml`).
4. Crée la base de données PostgreSQL et le rôle pour PeerTube.
5. Génère un certificat TLS auto-signé pour HTTPS.
6. Configure et active le reverse proxy nginx.
7. Installe et démarre `peertube.service`.
8. Attend que l'application réponde sur le port 443.

Tous les identifiants sont générés sur la VM à l'exécution — ils ne sont jamais stockés dans l'image.

La finalisation prend environ **3 à 8 minutes** après le démarrage de la VM.

## Personnaliser le déploiement (optionnel)

Lors de la création de la VM, l'onglet **Avancé** du portail Azure propose un champ **Custom data**. Vous pouvez y saisir des paramètres au format `clé=valeur` pour surcharger les valeurs par défaut :

```
peertube_hostname=peertube.example.com
peertube_admin_email=admin@example.com
peertube_db_password=MonMotDePasseDB
peertube_secret=MonSecretApplicatif64chars
```

| Clé | Description | Défaut si absent |
|-----|-------------|-----------------|
| `peertube_hostname` | Nom de domaine ou IP publique pour accéder à PeerTube | IP publique détectée via Azure IMDS |
| `peertube_admin_email` | Email du super-administrateur PeerTube | `admin@<hostname>` |
| `peertube_db_password` | Mot de passe PostgreSQL | Généré aléatoirement (`openssl rand`) |
| `peertube_secret` | Secret applicatif PeerTube | Généré aléatoirement (`openssl rand`) |

> Tous ces paramètres sont **optionnels**. Si le champ Custom data est vide, la VM se configure automatiquement avec des valeurs sécurisées générées à la volée.

## Récupérer le mot de passe administrateur

Le mot de passe du compte `root` PeerTube est généré par PeerTube lui-même au premier démarrage. Connectez-vous en SSH à la VM et récupérez-le depuis le journal système :

```bash
sudo journalctl -u peertube.service --no-pager | grep "User password"
```

Exemple de sortie :
```
Jun 14 20:12:57 ma-vm node[1910]: User password: <mot-de-passe-généré>
```

Conservez ce mot de passe — vous l'utiliserez pour vous connecter à l'interface web PeerTube en tant que `root`.

## Vérifier l'instance

Ouvrez un navigateur et accédez à `https://<ip-publique-ou-domaine>`.

- Avec une IP publique ou un certificat auto-signé, votre navigateur affichera un avertissement de sécurité. C'est attendu — cliquez sur « Avancé » et poursuivez.
- Avec un nom de domaine et Let's Encrypt configuré, le certificat sera valide.

Vérifiez que vous pouvez :

1. Voir la page d'accueil PeerTube.
2. Vous connecter en tant que `root` avec le mot de passe récupéré ci-dessus.
3. Naviguer vers **Administration > Vue d'ensemble** pour confirmer que le serveur fonctionne.

## Suivre la progression (optionnel)

Pour consulter les journaux de la finalisation en temps réel :

```bash
sudo journalctl -u peertube-firstboot.service -f
```

## Dépannage

| Problème | Cause | Solution |
|----------|-------|----------|
| PeerTube non accessible 10 minutes après le démarrage | Échec du firstboot | Consultez `sudo journalctl -u peertube-firstboot.service -n 50` |
| PeerTube configuré avec une IP privée (ex. `10.x.x.x`) | IMDS n'a pas retourné l'IP publique | Connectez-vous en SSH et relancez manuellement : `sudo PEERTUBE_HOSTNAME=<ip-publique> PEERTUBE_FORCE=1 /usr/local/sbin/finalize-peertube-vm.sh` |
| La page d'accueil ne se charge pas | Problème de démarrage du service | `sudo systemctl status peertube.service` et `sudo journalctl -u peertube.service -n 50` |
| Erreur de certificat dans le navigateur | Certificat auto-signé | Attendu — cliquez sur « Avancé » et continuez, ou configurez un domaine avec Let's Encrypt |
| Impossible de récupérer le mot de passe admin | Logs pas encore écrits | Attendez 30 secondes et relancez la commande `journalctl` |
