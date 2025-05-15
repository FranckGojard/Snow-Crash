# Rapport de Vulnérabilité : Inclusion de Fichier Local (LFI)

## Nom de la Faille

**Inclusion de Fichier Local (LFI)**
## OWASP
**A01:2021 – Contrôles d'accès défaillants**
**CWE-35 Path Traversal: '.../...//'**
**A10:2021 – A10 Falsification de requête côté serveur (SSRF)**

## Description

Cette faille permet à un attaquant de lire des fichiers sensibles du serveur en manipulant un paramètre GET mal sécurisé. En exploitant cette vulnérabilité, il est possible d'accéder à des fichiers critiques tels que `/etc/passwd` sur un système Linux.

## Comment Exploiter la Faille

1. **Étape 1** : Identifiez un paramètre vulnérable acceptant des chemins de fichiers dans l'URL.
2. **Étape 2** : Manipulez ce paramètre en utilisant `../` pour remonter dans l'arborescence des fichiers et atteindre des fichiers sensibles.
3. **Exemple d'exploitation** : `http://localhost:8080page=../../../../../../../../..etc/passwd`
4. **Résultat attendu** : Affichage du contenu du fichier `/etc/passwd`, révélant la liste des utilisateurs du système.

## Comment Résoudre la Faille

Pour corriger cette vulnérabilité, appliquez les mesures suivantes :

- **Validation stricte des entrées utilisateur** : Vérifiez et restreignez les valeurs acceptées par le paramètre concerné.
- **Utilisation de chemins absolus** : N’autorisez pas la concaténation de chemins de fichiers dynamiques en fonction des entrées utilisateur.
- **Désactivation des wrappers dangereux** : Restreignez l'accès aux flux comme `php://` et `data://` si possible.
- **Configuration du serveur** : Appliquez des restrictions sur les accès aux fichiers sensibles via les permissions système et la configuration du serveur web.

## Conclusion

L'inclusion de fichiers locaux est une faille critique qui peut compromettre un serveur entier. Une validation stricte des entrées utilisateur et des configurations de sécurité renforcées sont essentielles pour protéger l'application contre ces attaques.

