Rapport de Vulnérabilité : Contournement de Protection via gdb (Manipulation des fonctions ptrace et getuid)
Description
Cette vulnérabilité exploite la possibilité d’utiliser un débogueur (ici, GDB) pour manipuler le comportement interne du binaire /bin/getflag. Ce binaire utilise deux protections principales :

ptrace() pour détecter si le binaire est débogué, ce qui empêche son exécution sous débogueur.

getuid() pour vérifier que l’utilisateur exécutant le binaire possède l’UID attendu (ici 3014).

En interceptant ces appels via GDB, il est possible de contourner ces protections, modifiant directement les valeurs retournées par ces fonctions, et ainsi récupérer le token associé au niveau.

Comment Exploiter la Faille
Étape 1 : Lancement du binaire sous GDB
Exécuter le binaire sous débogueur :

shell
Copier
Modifier
gdb /bin/getflag
Définir deux points d’arrêt (breakpoints) sur les fonctions clés :

gdb
Copier
Modifier
break ptrace
break getuid
Étape 2 : Contournement de la protection anti-debug (ptrace)
Lancer le programme sous GDB :

gdb
Copier
Modifier
run
Le programme s’arrête sur l’appel à ptrace() (utilisé pour détecter la présence de GDB) :

gdb
Copier
Modifier
Breakpoint 1, ptrace () ...
Terminer l’exécution de la fonction ptrace() et modifier la valeur retournée ($eax) pour simuler qu’aucun débogueur n’est présent :

gdb
Copier
Modifier
finish
set $eax = 0
continue
Étape 3 : Modification du résultat de getuid()
Le programme s’arrête sur l’appel à getuid(), vérifiant si l’UID correspond à celui attendu (ici 3014) :

gdb
Copier
Modifier
Breakpoint 2, getuid () ...
Terminer l’exécution de la fonction getuid() et modifier la valeur retournée ($eax) pour correspondre à l’UID requis :

gdb
Copier
Modifier
finish
set $eax = 3014
continue
Le programme exécute désormais le chemin privilégié et affiche le token :

mathematica
Copier
Modifier
Check flag.Here is your token : 7QiHafiNa3HVozsaXkawuYrTstxbpABHD8CPnHJ
Étape 4 : Passage au niveau suivant
Utilisez ce token pour accéder à l'utilisateur suivant :

shell
Copier
Modifier
su level15
Mot de passe : 7QiHafiNa3HVozsaXkawuYrTstxbpABHD8CPnHJ
Comment Résoudre la Faille
Renforcer la protection anti-debug (ptrace) :

Ajouter des protections supplémentaires qui rendent le contournement via GDB plus difficile (plusieurs appels ptrace, vérifications croisées, anti-debugging avancé).

Éviter la confiance absolue envers les résultats des fonctions systèmes :

Ne pas baser la sécurité uniquement sur les résultats retournés par des appels systèmes pouvant être manipulés via débogueurs.

Utiliser des méthodes d'authentification robustes :

Mettre en place des mécanismes cryptographiques ou autres vérifications externes plutôt que de simples vérifications de l’UID ou de la présence de débogueur.

Conclusion
Cette vulnérabilité montre clairement les limites d’un mécanisme de sécurité basé uniquement sur des fonctions comme ptrace() ou getuid(). Les attaquants peuvent facilement intercepter et modifier ces résultats via des outils courants de debugging tels que GDB. Pour protéger efficacement un programme, il est nécessaire d’employer une stratégie de défense en profondeur, avec des mécanismes multiples et complémentaires.








