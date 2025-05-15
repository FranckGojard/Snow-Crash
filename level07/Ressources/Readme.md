# Rapport de Vulnérabilité : Faille de Récupération de Mot de Passe

## Nom de la Faille
**Faible Contrôle des Champs Cachés dans le Formulaire de Récupération de Mot de Passe**
## OWASP
**A07:2021 – Identification et authentification de mauvaise qualité**

## Description
Cette faille permet à un attaquant de manipuler le formulaire de récupération de mot de passe en modifiant ou supprimant un champ `hidden` contenant une adresse e-mail pré-remplie. En supprimant ce champ et en soumettant le formulaire, l'attaquant obtient directement un accès à des informations sensibles, comme un flag.

## Comment Exploiter la Faille
1. **Étape 1** : Accédez à la page de récupération de mot de passe et ouvrez les outils de développement du navigateur (`F12` ou `Clic droit > Inspecter`).
2. **Étape 2** : Repérez le formulaire suivant dans le code HTML :
   ```html
   <form action="#" method="POST">
       <input type="hidden" name="mail" value="webmaster@borntosec.com" maxlength="15">
       <input type="submit" name="Submit" value="Submit">
   </form>
   ```
3. **Étape 3** : Modifiez le code en supprimant la ligne contenant l'input `hidden` :
   ```html
   <form action="#" method="POST">
       <input type="submit" name="Submit" value="Submit">
   </form>
   ```
4. **Étape 4** : Soumettez le formulaire modifié.
5. **Étape 5** : Si la vérification du champ `mail` est mal implémentée côté serveur, le flag s'affiche sur la page.

## Comment Résoudre la Faille
Pour corriger cette vulnérabilité, appliquez les bonnes pratiques suivantes :

- **Ne pas faire confiance aux champs cachés** : Un champ `hidden` peut être facilement modifié par un utilisateur.
- **Vérification stricte côté serveur** : Assurez-vous que la requête inclut bien un e-mail valide enregistré dans la base de données.
- **Utiliser des jetons sécurisés** : Remplacez l'identification par e-mail par un jeton unique envoyé par e-mail à l'utilisateur.
- **Implémenter un contrôle d'authentification** : Exiger une authentification préalable avant d'afficher un formulaire de récupération de mot de passe.

## Conclusion
Cette vulnérabilité démontre les risques liés à une validation insuffisante des entrées utilisateur. En mettant en place des contrôles stricts côté serveur et en évitant d'exposer des données sensibles dans des champs cachés, vous pouvez sécuriser efficacement votre application contre ce type d'attaque.

