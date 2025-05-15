# Rapport de Vulnérabilité : XSS via Paramètre `src` sur la Page Media

## Nom de la Faille

**XSS via le paramètre *`src`* sur la page media**

## OWASP
**A03:2021 – Injection**


## Description

Cette faille permet l'exécution de code JavaScript malveillant via le paramètre `src` sur la page `media`. En injectant un payload encodé en Base64, un attaquant peut exécuter du JavaScript dans le navigateur de la victime, ce qui peut mener à du vol de cookies, du détournement de session ou d'autres attaques XSS.

## Comment Exploiter la Faille

1. **Accéder à la page vulnérable** :
2. **Injecter un script malveillant encodé en Base64** :
- `data:text/html;base64,PHNjcmlwdD5hbGVydCg0Mik8L3NjcmlwdD4=`

## Comment Résoudre la Faille

- **Filtrage et validation des entrées** : Ne pas permettre l'utilisation de `data:` comme source d'image.
- **Encodage du contenu** : Utiliser `htmlspecialchars()` ou `Content-Security-Policy (CSP)` pour empêcher l'exécution de scripts.
- **Utilisation d'une liste blanche** : Restreindre les valeurs autorisées pour `src` aux fichiers locaux prédéfinis.

## Conclusion

Cette vulnérabilité permet d'exécuter du JavaScript arbitraire en manipulant le paramètre `src` de la page media. Une correction adéquate est essentielle pour empêcher l'exploitation XSS et protéger les utilisateurs contre des attaques potentielles.

