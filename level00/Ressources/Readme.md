# Rapport de Vulnérabilité : Recherche de Fichiers et Chiffrement César

## Description

Cette faille repose sur la présence d’un fichier appartenant à l’utilisateur flag00, accessible en lecture. En listant les fichiers possédés par cet utilisateur, on découvre un fichier situé dans /usr/sbin/john. En affichant son contenu avec la commande cat, on obtient un message chiffré en César. Une fois déchiffré, ce message donne accès au mot de passe de l’utilisateur flag00.

## Comment Exploiter la Faille

### Étape 1 : Recherche des fichiers appartenant à `flag00`

```bash
find / -user flag00 2>/dev/null
```

* `find /` : cherche à partir de la racine du système.
* `-user flag00` : filtre les fichiers appartenant à l'utilisateur `flag00`.
* `2>/dev/null` : redirige les erreurs (comme les "Permission denied") vers null pour ne garder que les résultats accessibles.

**Résultat :**

```
/usr/sbin/john
/rofs/usr/sbin/john
```

### Étape 2 : Analyse du fichier trouvé

On utilise `cat` pour afficher le contenu du fichier :

```bash
cat /usr/sbin/john
```

**Sortie :**

```
cdiiddwpgswtgt
```

### Étape 3 : Déchiffrement

Ce texte est chiffré avec un **chiffrement César**.
Après décryptage, on obtient :

```
nottoohardhere
```

### Étape 4 : Connexion en tant que `flag00`

```bash
su flag00
```

**Mot de passe :**

```
nottoohardhere
```

### Étape 5 : Récupération du flag

```bash
getflag
```

**Flag obtenu :**

```
x24ti5gi3x0ol2eh4esiuxias
```

### Étape 6 : Passage au niveau suivant

```bash
su level01
```

**Mot de passe :**

```
x24ti5gi3x0ol2eh4esiuxias
```

## Comment Résoudre la Faille

Pour corriger cette vulnérabilité :

* **Ne pas stocker de données sensibles en clair ou en chiffré simple** : Même un chiffrement comme César est insuffisant.
* **Restreindre les accès** : Limiter les droits en lecture sur les fichiers sensibles.

## Conclusion

Cette vulnérabilité montre comment une mauvaise configuration système et un chiffrement trop faible peuvent permettre à un attaquant de prendre le contrôle d’un compte et d’accéder à des ressources sensibles.

---