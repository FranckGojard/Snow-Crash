# Rapport de Vulnérabilité : Faille User-Agent et Referer

## Nom de la Faille

Manipulation des en-têtes HTTP : User-Agent et Referer
## OWASP
**A01:2021 – Contrôles d'accès défaillants**
## Description

Cette faille permet à un attaquant de contourner les vérifications côté serveur en manipulant les en-têtes HTTP, notamment le champ "User-Agent" pour simuler un navigateur spécifique et le champ "Referer" pour indiquer l'origine de la requête.

Dans ce cas précis, l'application web vérifie si la requête provient de la page "[https://www.nsa.gov/](https://www.nsa.gov/)" et si le navigateur utilisé est "ft\_bornToSec". En modifiant les en-têtes HTTP via un outil comme curl ou un plugin de navigateur tel que "Modify Header Value", il est possible de tromper le serveur et d'obtenir le flag.

## Commande utilisée

```
curl -H "Referer:https://www.nsa.gov/" -H "User-Agent:ft_bornToSec" http://localhost:8080/\?page\=b7e44c7a40c5f80139f0a50f3650fb2bd8d00b0d24667c4c2ca32c88e13b758f | grep "flag"
```

Cette commande envoie une requête avec les en-têtes User-Agent et Referer modifiés.

## Limiter la Faille

Pour prévenir ce type de faille, il est essentiel de :

- Ne pas faire confiance aux en-têtes HTTP pour la gestion des autorisations ou des accès.
- Utiliser des jetons d'authentification sécurisés et signés pour valider les requêtes.
- Vérifier l'identité de l'utilisateur via des mécanismes d'authentification robustes, comme les sessions ou les cookies sécurisés.
- Mettre en place des règles de filtrage côté serveur pour détecter les en-têtes suspects.

## Conclusion

Cette vulnérabilité montre l'importance de ne pas se fier aux informations des en-têtes HTTP pour prendre des décisions d'autorisation. La validation côté serveur et l'utilisation de mécanismes d'authentification sécurisés sont indispensables pour éviter les attaques par manipulation d'en-têtes.


