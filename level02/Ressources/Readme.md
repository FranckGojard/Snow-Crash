# Rapport de Vulnérabilité : Manipulation de Valeur dans un Formulaire

## Nom de la Faille
**Manipulation de Valeur dans un Formulaire**
## OWASP
**A01:2021 – Contrôles d'accès défaillants**

## Description
Cette faille permet à un attaquant de manipuler la valeur soumise via le formulaire de la page `survey`. Le formulaire contient un menu déroulant (`<select>`) avec des options allant de 1 à 10. En modifiant la valeur d'une option pour une valeur supérieure à 10 (par exemple, 11), l'attaquant peut déclencher une réponse inattendue du serveur, comme l'affichage d'un flag.

## Comment Exploiter la Faille
1. **Étape 1** : Accédez à la page `survey` contenant le formulaire.
2. **Étape 2** : Inspectez le formulaire à l'aide des outils de développement du navigateur (clic droit > Inspecter).
3. **Étape 3** : Modifiez la valeur d'une des options du menu déroulant pour une valeur supérieure à 10. Par exemple, changez :
   ```html
   <option value="10">10</option>
   ```
   en :
   ```html
   <option value="11">10</option>
   ```
4. **Étape 4** : Soumettez le formulaire en sélectionnant la valeur modifiée.
5. **Étape 5** : Le serveur retourne le flag :
   ```
   Flag : 03a944b434d5baff05f46c4bede5792551a2595574bcafc9a6e25f67c382ccaa
   ```

## Comment Résoudre la Faille
Pour corriger cette vulnérabilité, suivez les étapes suivantes :

- **Validation côté serveur** : Vérifiez toujours les données soumises par l'utilisateur côté serveur. Ne faites pas confiance aux données côté client.
- **Limitation des valeurs acceptées** : Utilisez une liste blanche de valeurs autorisées pour le paramètre `valeur`.
- **Gestion des erreurs** : Redirigez vers une page d'erreur ou affichez un message approprié si une valeur non valide est soumise.
- **Journalisation** : Journalisez les tentatives de soumission de valeurs non valides pour détecter les activités suspectes.

## Conclusion
Cette vulnérabilité met en évidence l'importance de valider les données côté serveur et de ne pas faire confiance aux données côté client. En implémentant les corrections ci-dessus, vous pouvez sécuriser votre application contre ce type de manipulation.

---