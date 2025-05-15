# Rapport de Vulnérabilité : Brute Force sur Formulaire de Connexion

## Nom de la Faille

Brute Force sur Formulaire de Connexion
## OWASP
**A07:2021 – Identification et authentification de mauvaise qualité**

## Description

Cette faille permet à un attaquant de tester automatiquement un grand nombre de mots de passe pour un utilisateur spécifique via un formulaire de connexion. Dans ce cas précis, le formulaire de connexion présente l'URL suivante :

```
http://localhost:8080/?page=signin&username={user}&password={pass}&Login=Login#
```

L'attaquant a remarqué que le mot de passe est directement dans l'URL, ce qui permet de le remplacer facilement pour tenter de multiples combinaisons.

## Comment Exploiter la Faille

1. Accéder à la page de connexion "Sign in".
2. Observer que l'URL contient directement les informations d'identification.
3. Utiliser un dictionnaire de mots de passe pour tester les mots de passe les plus courants.
4. Automatiser le processus de test avec un script Bash.

### Script d'Exploitation

Le script `brute_force.sh` utilise les 500 pires mots de passe issus du projet GitHub [SecLists](https://github.com/danielmiessler/SecLists) pour tenter de se connecter automatiquement via la commande curl.

### Flag Trouvé

Une fois le bon mot de passe découvert, nous pouvons le tester sur le site :

```
b3a6e43ddf8b4bbb4125e5e7d23040433827759d4de1c04ea63907479a80a6b2
```

## Comment Corriger la Faille

Pour éviter cette vulnérabilité, il est essentiel de suivre ces bonnes pratiques :

1. **Limiter les tentatives de connexion** : Mettre en place un verrouillage temporaire après plusieurs échecs.
2. **Utiliser un CAPTCHA** : Pour prévenir les attaques automatisées.
3. **Stocker les mots de passe de manière sécurisée** : Utiliser un hachage sécurisé comme bcrypt.
4. **Désactiver la transmission des informations sensibles dans l'URL** : Utiliser des requêtes POST au lieu de GET.
5. **Surveiller les logs de connexion** : Détecter les tentatives suspectes et les bloquer.

## Conclusion

Cette vulnérabilité montre à quel point il est crucial de sécuriser les formulaires de connexion en empêchant les attaques par force brute. Des mesures de sécurité supplémentaires peuvent empêcher ce type d'attaque et protéger les données des utilisateurs.


