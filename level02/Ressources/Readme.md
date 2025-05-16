# Rapport de Vulnérabilité : Analyse de trafic réseau avec un fichier `.pcap`

## Description

Cette faille repose sur l’analyse d’un fichier réseau au format `.pcap` contenant un mot de passe transmis via le protocole TCP. Ce fichier peut être transféré sur la machine locale puis ouvert dans **Wireshark**, un outil d’analyse de paquets réseau. En suivant un flux TCP, on peut observer le mot de passe tapé et le reconstituer, malgré certains caractères masqués.

## Comment Exploiter la Faille

### Étape 1 : Récupération du fichier `.pcap`

Un fichier `level02.pcap` est présent sur la machine distante.
Un fichier `.pcap` (Packet Capture) est une capture brute de paquets réseau. Il contient toutes les données échangées sur un réseau à un instant donné.

Utiliser `scp` pour le transférer sur la machine locale :

```bash
scp -P 4242 level02@192.168.56.101:/home/user/level02/level02.pcap .
```

### Étape 2 : Ouverture dans Wireshark

Changer les droits du fichier si nécessaire :

```bash
chmod 644 level02.pcap
```

L’ouvrir dans **Wireshark**, un logiciel graphique permettant d’inspecter et d’analyser des paquets réseau de manière interactive.

### Étape 3 : Suivre le flux TCP

Faire un clic droit sur n’importe quelle ligne dans la liste des paquets →
**Follow** → **TCP Stream**

On obtient :

```
Password: ft_wandr...NDRel.L0L
```

### Étape 4 : Afficher les données en hexadécimal

Changer le format d’affichage : **Show data as → Hex Dump**

Extrait obtenu :

```
000000B9  66                                                 f
000000BA  74                                                 t
000000BB  5f                                                 _
000000BC  77                                                 w
000000BD  61                                                 a
000000BE  6e                                                 n
000000BF  64                                                 d
000000C0  72                                                 r
000000C1  7f                                                 .
000000C2  7f                                                 .
000000C3  7f                                                 .
000000C4  4e                                                 N
000000C5  44                                                 D
000000C6  52                                                 R
000000C7  65                                                 e
000000C8  6c                                                 l
000000C9  7f                                                 .
000000CA  4c                                                 L
000000CB  30                                                 0
000000CC  4c                                                 L
000000CD  0d                                                 .
```

Les caractères `7f` correspondent au caractère ASCII **DEL**.
Il s'agit d'une touche de suppression, utilisée pour effacer les caractères précédents.

### Étape 5 : Reconstitution du mot de passe

En tenant compte des caractères effacés, on reconstitue le mot de passe final :

```
ft_waNDReL0L
```

### Étape 6 : Connexion au compte `flag02`

```bash
su flag02
```

**Mot de passe :**

```
ft_waNDReL0L
```

### Étape 7 : Récupération du flag

```bash
getflag
```

**Flag obtenu :**

```
kooda2puivaav1idi4f57q8iq
```

---

## Comment Résoudre la Faille

Pour corriger cette vulnérabilité :

* **Ne jamais transmettre de mot de passe en clair sur un réseau** : Utiliser des protocoles chiffrés comme SSH, TLS, etc.
* **Éviter les captures de trafic stockées sans protection** : Les fichiers `.pcap` peuvent contenir des données extrêmement sensibles.
* **Mettre en place une détection d’intrusion réseau (IDS)** : Pour alerter en cas de transmission suspecte.

## Conclusion

Cette vulnérabilité montre qu’un simple enregistrement de trafic réseau peut suffire à intercepter un mot de passe s’il est transmis sans chiffrement. L’analyse avec Wireshark révèle clairement les frappes clavier, y compris les suppressions, permettant à un attaquant de reconstituer le mot de passe complet.

---