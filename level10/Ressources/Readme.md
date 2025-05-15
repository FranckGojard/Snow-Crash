# Rapport de Vulnérabilité : Faille XSS (Cross-Site Scripting)

## Nom de la Faille
Faille XSS basée sur les attributs (non persistante)
## OWASP
**A03:2021 – Injection**

## Description
Cette faille permet à un attaquant d’injecter du code malveillant directement dans un formulaire de feedback en utilisant des balises ou caractères spéciaux pour contourner la validation côté client. 

### Comment Exploiter la Faille
Pour exploiter cette faille, il suffit de renseigner des caractères ou des balises spécifiques dans le formulaire. Voici un exemple avec une balise non filtrée :

```
<script
```

Cette balise contourne la validation car la casse n’est pas vérifiée, permettant ainsi de passer le filtrage basique. Le serveur interprète alors cette balise comme un script valide, menant potentiellement à une exécution de code.

Lors de l'exécution, le flag est directement retourné dans la réponse du serveur.

### Comment Corriger la Faille
Pour corriger cette vulnérabilité, voici quelques bonnes pratiques :

1. **Validation côté serveur :** Toujours valider et filtrer les entrées côté serveur, même si une validation côté client est déjà mise en place.
2. **Échappement des caractères spéciaux :** Encoder les caractères spéciaux (comme `<`, `>`, et `&`) afin qu’ils ne soient pas interprétés comme du code.
3. **Utilisation d’en-têtes de sécurité :** Mettre en place les en-têtes HTTP appropriés (comme `Content-Security-Policy`) pour empêcher l'exécution de scripts non approuvés.
4. **Sanitisation des entrées :** Nettoyer les données saisies par l'utilisateur avant de les afficher ou de les traiter.

## Conclusion
Cette vulnérabilité met en évidence l'importance de bien filtrer et encoder toutes les données d'entrée côté serveur. L'absence de contrôles stricts permet aux attaquants d'exécuter du code malveillant et de compromettre la sécurité de l'application.


