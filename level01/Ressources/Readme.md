# Rapport de Vulnérabilité : Extraction de mot de passe depuis `/etc/passwd` et bruteforce avec John The Ripper

## Description

Cette faille repose sur la présence d’un mot de passe hashé directement dans le fichier `/etc/passwd`. En affichant son contenu, on peut repérer une ligne contenant un hash appartenant à l’utilisateur `flag01`. Ce hash peut ensuite être cracké à l’aide de l’outil **John The Ripper** pour obtenir le mot de passe en clair.

## Comment Exploiter la Faille

### Étape 1 : Lecture du fichier `/etc/passwd`

```bash
cat /etc/passwd
```

**Extrait pertinent :**

```
...
flag00:x:3000:3000::/home/flag/flag00:/bin/bash  
flag01:42hDRfypTqqnw:3001:3001::/home/flag/flag01:/bin/bash  
flag02:x:3002:3002::/home/flag/flag02:/bin/bash 
... 
```

On récupère le hash suivant :

```
42hDRfypTqqnw
```

### Étape 2 : Installation de John The Ripper

Télécharger la version officielle depuis :
[https://www.openwall.com/john/](https://www.openwall.com/john/)

Fichier utilisé :
**1.9.0 core sources in tar.xz**

Une fois extrait, compiler les sources en suivant la procédure fournie dans le fichier `INSTALL` :

```bash
make clean generic
```

L’exécutable `john` est maintenant disponible dans le dossier `run`.

### Étape 3 : Création du fichier contenant le hash

Créer un fichier `file.txt` contenant :

```
42hDRfypTqqnw
```

### Étape 4 : Cracker le mot de passe

Lancer John sur le fichier :

```bash
run/john file.txt
```

Une fois terminé, afficher le mot de passe trouvé :

```bash
run/john --show file.txt
```

**Mot de passe obtenu :**

```
?:abcdefg
```

### Étape 5 : Connexion au compte `flag01`

```bash
su flag01
```

**Mot de passe :**

```
abcdefg
```

### Étape 6 : Récupération du flag

```bash
getflag
```

**Flag obtenu :**

```
f2av5il02puano7naaf6adaaf
```

### Étape 6 : Passage au niveau suivant

```bash
su level02
```

**Mot de passe :**

```
f2av5il02puano7naaf6adaaf
```

---

## Comment Résoudre la Faille

Pour corriger cette vulnérabilité :

* **Ne jamais stocker les mots de passe dans `/etc/passwd`** : Ils doivent être stockés dans `/etc/shadow`, qui est lisible uniquement par root.
* **Utiliser des algorithmes de hachage robustes** : Le format de hash utilisé ici est trop faible et facilement bruteforcable.
* **Restreindre les accès aux fichiers système** : Assurez-vous que les utilisateurs ne peuvent lire que ce qui leur est strictement nécessaire.

## Conclusion

Cette vulnérabilité montre qu’un simple accès en lecture à un fichier système mal configuré peut exposer des mots de passe utilisateurs. Une séparation claire des fichiers sensibles, une politique de hachage robuste et un contrôle des permissions sont essentiels pour sécuriser un système Unix.

---