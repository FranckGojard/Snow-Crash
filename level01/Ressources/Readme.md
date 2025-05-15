# Rapport de Vulnérabilité : Manipulation de Cookie pour Élévation de Privilèges

## Nom de la Faille
**Manipulation de Cookie pour Élévation de Privilèges**
## OWASP
**A01:2021 – Contrôles d'accès défaillants**

## Description
Cette faille permet à un attaquant de modifier la valeur du cookie `I_am_admin` pour obtenir des privilèges administratifs. Par défaut, le cookie est défini avec la valeur `false` (hashée en MD5). En modifiant cette valeur pour `true` (hashée en MD5), l'attaquant peut accéder à des fonctionnalités ou des informations sensibles, comme un flag.

## Comment Exploiter la Faille
1. **Étape 1** : Identifiez le cookie `I_am_admin` dans les outils de développement du navigateur (onglet "Application" ou "Stockage").
2. **Étape 2** : La valeur par défaut du cookie est le hash MD5 de `false` :
   ```
   I_am_admin = 4b9971fcdc9a797a1b6678d6a7b5e5d5
   ```
3. **Étape 3** : Remplacez la valeur du cookie par le hash MD5 de `true` :
   ```
   I_am_admin = b326b5062b2f0e69046810717534cb09
   ```
4. **Étape 4** : Rafraîchissez la page ou soumettez une requête. Si la vérification du cookie est incorrectement implémentée, vous obtiendrez un accès administratif et le flag :
   ```
   Flag : df2eb4ba34ed059a1e3e89ff4dfc13445f104a1a52295214def1c4fb1693a5c3
   ```

## Comment Résoudre la Faille
Pour corriger cette vulnérabilité, suivez les étapes suivantes :

- **Ne pas se fier aux cookies côté client** : Les cookies ne doivent jamais être utilisés pour stocker des informations sensibles ou critiques comme les privilèges d'accès.
- **Validation côté serveur** : Vérifiez toujours les privilèges de l'utilisateur côté serveur, en utilisant des sessions sécurisées.
- **Utiliser des tokens sécurisés** : Si des cookies sont nécessaires, utilisez des tokens signés et chiffrés pour empêcher toute manipulation.
- **Hachage sécurisé** : Si vous devez stocker des valeurs dans des cookies, utilisez des algorithmes de hachage sécurisés et salés (comme bcrypt ou Argon2).

## Conclusion
Cette vulnérabilité démontre les dangers de la confiance excessive envers les données côté client, comme les cookies. En implémentant des vérifications côté serveur et en utilisant des méthodes sécurisées pour gérer les privilèges, vous pouvez protéger votre application contre ce type d'attaque.

### Remarque
- Le hash MD5 de `false` est `4b9971fcdc9a797a1b6678d6a7b5e5d5`.
- Le hash MD5 de `true` est `b326b5062b2f0e69046810717534cb09`.