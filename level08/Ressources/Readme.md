# Rapport de Vulnérabilité : Faille de Répertoire Caché (Hidden Directory)

## Nom de la Faille

Répertoires cachés exposés (Hidden Directory)
## OWASP
**A01:2021 – Contrôles d'accès défaillants**

## Description

Cette faille consiste à rendre accessibles des répertoires cachés ou non répertoriés sur le serveur, souvent référencés dans le fichier `robots.txt`. Ces répertoires peuvent contenir des informations sensibles, des fichiers de configuration ou même des indices pour des CTF.

### Exploitation de la Faille

Pour exploiter cette faille, nous avons procédé comme suit :

1. Nous avons d'abord visité le fichier `robots.txt` pour détecter d'éventuels chemins cachés. Cela nous a permis de découvrir la route suivante : `/.hidden`
2. En accédant à cette route, nous avons découvert une multitude de liens symboliques menant vers d'autres répertoires et fichiers README.
3. Nous avons alors créé un script `flag.sh` capable de parcourir ce labyrinthe de répertoires et d'analyser chaque fichier README pour détecter le mot "flag".
4. Grâce à ce script, nous avons pu identifier le chemin correct et découvrir le flag final.

### Comment Limiter la Faille

Pour se protéger contre ce type de faille, il est important de :

1. Ne pas exposer de répertoires ou de fichiers sensibles dans le fichier `robots.txt`.
2. Configurer le serveur pour restreindre l'accès aux répertoires internes et sensibles.
3. Utiliser des permissions d'accès appropriées pour les répertoires privés.
4. Désactiver la liste des répertoires sur le serveur (Directory Listing).

## Conclusion

Cette vulnérabilité démontre l'importance de vérifier soigneusement les répertoires exposés sur un serveur. En appliquant des mesures de sécurité basiques, on peut réduire significativement les risques d'exploitation liés aux répertoires cachés.


