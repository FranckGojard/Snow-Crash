# Rapport de Vulnérabilité : Téléversement de Fichiers Non Sécurisé

## Nom de la Faille
**Téléversement de Fichiers Non Sécurisé**
## OWASP
**A03:2021 – Injection**

## Description
Cette faille permet à un attaquant de téléverser un fichier malveillant sur le serveur via la fonctionnalité d'ajout d'image. En exploitant une mauvaise validation du type de fichier, l'attaquant peut exécuter du code arbitraire et obtenir un accès non autorisé.

## Comment Exploiter la Faille
1. **Étape 1** : Accédez à la page d'upload d'image (`add image`).
2. **Étape 2** : Préparez un fichier PHP malveillant (`bad.php`).
3. **Étape 3** : Utilisez la commande suivante pour envoyer le fichier :
   ```sh
   curl -X POST -F "Upload=Upload" -F "uploaded=@bad.php;type=image/jpeg" "http://localhost:8080/index.php?page=upload" | grep 'The flag is :'
   ```
4. **Étape 4** : Si l'upload est accepté, le fichier sera téléversé et accessible sur le serveur.
5. **Étape 5** : Exécutez le fichier téléversé pour obtenir un accès ou un flag.

## Impact de la Faille
- Exécution de code arbitraire sur le serveur.
- Possibilité de prendre le contrôle du serveur.
- Exfiltration de données sensibles.

## Recommandations pour Corriger la Faille
- **Restreindre les types de fichiers autorisés** : Vérifiez côté serveur que seuls les fichiers d'image valides (PNG, JPG, GIF) sont acceptés.
- **Vérification stricte du MIME-Type** : Ne pas se fier uniquement à l'extension du fichier.
- **Désactiver l'exécution des fichiers téléversés** : Configurer le serveur pour empêcher l'exécution de scripts dans le répertoire d'upload.
- **Renommer les fichiers uploadés** : Ajouter une extension sécurisée (.txt) ou un hash aléatoire pour éviter l'exécution.
- **Stockage sécurisé** : Placer les fichiers téléversés dans un répertoire non accessible depuis le web.

## Conclusion
Cette vulnérabilité met en évidence les risques liés aux téléversements de fichiers non sécurisés. Une validation stricte des fichiers et une configuration serveur appropriée sont essentielles pour prévenir les attaques.

