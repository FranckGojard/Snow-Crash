
---

### 🧊 Rapport de réussite – Snow Crash Level 10

**📌 Objectif** :
Contourner les restrictions de lecture d’un fichier (`token`) en exploitant une vulnérabilité **TOCTOU** (Time-Of-Check To Time-Of-Use) via un binaire qui utilise les **sockets** pour transmettre le contenu d’un fichier.

---

**🔍 Analyse** :

* Le binaire cible ouvrait un fichier temporaire (`/tmp/playload`) avant de le transmettre via une socket.
* L’utilisateur n’avait pas les droits pour lire directement le fichier `token`.
* `/tmp` étant accessible en écriture, il était possible d’y manipuler des fichiers.

---

**🧠 Exploitation** :

* Mise en place d’un script Bash exploitant une **race condition** :

  ```bash
  while true; do
    echo "test" > /tmp/playload
    rm /tmp/playload
    ln -s ~/token /tmp/playload
  done
  ```
* But : remplacer `/tmp/playload` **juste après la vérification** par le programme, mais **juste avant son ouverture**, pour rediriger la lecture vers `~/token`.

---

**📬 Transmission** :

* Utilisation de `nc` pour capter le contenu envoyé par le programme sur le port ouvert.
* Exécution du binaire pendant que le script tourne.
* Le flag a été récupéré via la socket, comme prévu.

---

**✅ Résultat** :
Le flag a été intercepté avec succès, en contournant les permissions via une attaque TOCTOU bien synchronisée. 🫡

---