# Finalisation de l'instance PeerTube

Après vous être connecté à la VM, exécutez le script de finalisation pour transformer l'image déployée en une instance PeerTube fonctionnelle.

## Ce que fait la finalisation

Le script de finalisation s'exécute une seule fois sur la VM et effectue ces étapes :

1. Génère le fichier de configuration PeerTube (`production.yaml`) avec votre nom d'hôte, un mot de passe de base de données aléatoire et un secret applicatif aléatoire.
2. Crée la base de données PostgreSQL et le rôle pour PeerTube.
3. Génère un certificat TLS pour HTTPS (auto-signé, ou Let's Encrypt si un nom de domaine est fourni).
4. Configure et active le reverse proxy nginx.
5. Installe et démarre `peertube.service`.
6. Attend que l'application réponde sur le port 443.

Tous les identifiants (mot de passe de base de données, secret applicatif) sont générés sur la VM à l'exécution — ils ne sont jamais stockés dans l'image.

## Exécuter le script de finalisation

Une fois connecté via SSH :

```bash
sudo /var/www/peertube/scripts/finalize-peertube-vm.sh
```

> Le script est idempotent : s'il a déjà été exécuté, il ignorera les étapes déjà complétées, sauf si vous forcez une nouvelle exécution.

Il vous sera demandé de fournir (ou de confirmer) :

- **Nom d'hôte** : le nom de domaine ou l'adresse IP publique que les utilisateurs utiliseront pour accéder à PeerTube.
- **Email administrateur** : utilisé comme adresse email du super-administrateur PeerTube.

## Attendre le démarrage

Le script attend que PeerTube soit accessible. Cela prend environ **60 à 120 secondes** lors du premier démarrage, car PeerTube initialise son schéma de base de données et crée le compte administrateur.

## Récupérer le mot de passe administrateur

Le mot de passe administrateur initial est généré automatiquement. Après le démarrage, récupérez-le depuis le journal système :

```bash
sudo journalctl -u peertube.service --no-pager | grep "User password"
```

Exemple de sortie :
```
Jun 14 20:12:57 ma-vm node[1910]: User password: <mot-de-passe-généré>
```

Conservez ce mot de passe — vous l'utiliserez pour vous connecter à l'interface web PeerTube en tant que `root`.

## Vérifier l'instance

Ouvrez un navigateur et accédez à `https://<votre-nom-dhôte>`.

- Si vous avez utilisé une IP publique ou un certificat auto-signé, votre navigateur affichera un avertissement de sécurité. C'est attendu — poursuivez vers le site.
- Si vous avez configuré un nom de domaine avec Let's Encrypt, le certificat sera valide.

Vérifiez que vous pouvez :

1. Voir la page d'accueil PeerTube.
2. Vous connecter en tant que `root` avec le mot de passe récupéré ci-dessus.
3. Naviguer vers **Administration > Vue d'ensemble** pour confirmer que le serveur fonctionne.

## Dépannage

| Problème | Cause | Solution |
|----------|-------|----------|
| Le script se termine avec une erreur | Dépendance manquante | Vérifiez la sortie — le script indique le composant manquant |
| La page d'accueil PeerTube ne se charge pas après 3 minutes | Problème de démarrage du service | Exécutez `sudo systemctl status peertube.service` et consultez les logs avec `sudo journalctl -u peertube.service -n 50` |
| Erreur de certificat dans le navigateur | Certificat auto-signé | C'est attendu — cliquez sur « Avancé » et continuez, ou configurez un domaine valide avec Let's Encrypt |
| Impossible de récupérer le mot de passe admin | Logs pas encore écrits | Attendez 30 secondes et relancez la commande `journalctl` |
