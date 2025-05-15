# Rapport de Vulnérabilité : Fuite de Mot de Passe via Fichier htpasswd

## Nom de la Faille
Fuite de Mot de Passe via Fichier htpasswd
**A07:2021 – Identification et authentification de mauvaise qualité**

## Description
Cette faille permet à un attaquant d'accéder à une page d'administration protégée en exploitant un fichier `.htpasswd` mal sécurisé. En inspectant des pages sensibles du site web telles que `/robots.txt` ou `/admin`, l'attaquant peut découvrir des informations cruciales ou des fichiers sensibles exposés.

## Étapes pour Exploiter la Faille
1. Visitez plusieurs pages sensibles à la racine du site web comme :
   - `/robots.txt`
   - `/admin`
   - `/whatever`

2. Lors de la navigation dans la page `/whatever`, repérez la présence d'un fichier `htpasswd`.

3. Ouvrez le fichier pour lire le contenu :
   ```
   root:437394baff5aa33daa618be47b75cb49
   ```

4. Décryptez la chaîne de caractères en utilisant un déchiffreur MD5, ce qui donne :
   ```
   qwerty123@
   ```

5. Allez sur la page d'administration `/admin` et entrez :
   - **Nom d'utilisateur** : `root`
   - **Mot de passe** : `qwerty123@`

6. Si l'authentification est réussie, le flag s'affiche :
   ```
   d19b4823e0d5600ceed56d5e896ef328d7a2b9e7ac7e80f4fcdb9b10bcb3e7ff
   ```

## Comment Corriger la Faille
Pour corriger cette vulnérabilité, suivez ces recommandations :
1. **Sécurisation des fichiers sensibles :** Ne laissez jamais un fichier `.htpasswd` accessible depuis le web. Stockez-le en dehors de la racine du serveur.
2. **Restrictions d'accès :** Configurez le serveur pour restreindre l'accès aux fichiers de configuration sensibles.
3. **Hash sécurisé :** Utilisez des algorithmes de hachage plus robustes que MD5, comme bcrypt ou Argon2.
4. **Surveillance et journalisation :** Détectez les tentatives d'accès aux pages administratives ou aux fichiers sensibles.

## Conclusion
Cette faille illustre l'importance de la protection des fichiers critiques dans une application web. L'utilisation de bonnes pratiques de sécurisation des fichiers, ainsi que de l'authentification renforcée, permet de réduire considérablement les risques d'intrusion.


