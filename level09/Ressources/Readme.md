# Rapport CTF SnowCrash - Level09 (Version Améliorée)

## Contexte  
**Objectif** : Exploiter le binaire `level09` (SUID) pour lire le fichier `token` protégé et obtenir le flag de `flag09`.  
**Éléments fournis** :  
- Binaire `level09` (exécuté avec les droits de `flag09`)  
- Fichier `token` (contenu binaire non lisible directement)  

---

## Reconnaissance Initiale  

### Permissions et Fichiers  
```bash
level09@SnowCrash:~$ ls -la
-rwsr-sr-x 1 flag09  level09 7640 Mar  5  2016 level09  # SUID activé
----r--r-- 1 flag09  level09   26 Mar  5  2016 token    # Lisible par level09
```

### Comportement du Binaire  
Tests avec différentes entrées :  
```bash
$ ./level09 "11111" → "12345"  
$ ./level09 "abcde" → "acegi"  
```

**Observation clé** : Chaque caractère de sortie = caractère d'entrée + position (index).  
Exemple avec "abcde" (ASCII 97,98,99,100,101) :  
- `a` (97) + 0 = 97 → `a`  
- `b` (98) + 1 = 99 → `c`  
- ... → Résultat : `acegi`  

---

## Analyse du Fichier `token`  

### Contenu Brut (Non Imprimable)  
```bash
$ cat token → f4kmm6p|=�p�n��DB�Du{��  
```

### Conversion Hexadécimale  
```bash
$ xxd -p token → 66346b6d6d36707c3d827f70826e838244428344757b7f8c890a  
```

**Pourquoi ?** Les caractères non imprimables (ex: `0x7f`, `0x82`) ne peuvent être affichés en ASCII standard. La conversion hexadécimale permet de traiter chaque octet individuellement.

---

## Solution : Inversion de l'Algorithme  

### Stratégie  
Pour décoder `token`, il faut soustraire l'index de chaque octet :  
`octet_décodé = octet_chiffré - position`  

### Script Python (Version Corrigée)  
```python
hex_token = "66346b6d6d36707c3d827f70826e838244428344757b7f8c890a"
token_bytes = bytes.fromhex(hex_token)

decoded = []
for i, byte in enumerate(token_bytes):
    decoded_byte = byte - i
    decoded.append(decoded_byte if 0 <= decoded_byte <= 255 else 0)  # Gestion des débordements

password = bytes(decoded).decode('ascii', errors='ignore').strip()
print("Mot de passe décodé:", password)  # Sortie : f3iji1ju5yuevaus41q1afiuq
```

**Échec initial** : Des valeurs négatives (ex: `0x0a - 25 = -15`) causaient une erreur `chr()`.  
**Correction** : Remplacement des valeurs hors plage ASCII par `0` ou suppression.

---

## Exploitation du Résultat  

### Connexion à `flag09`  
```bash
$ su flag09  
Password: f3iji1ju5yuevaus41q1afiuq  # Note : Le "?" final est un artefact et doit être ignoré
```

### Récupération du Flag  
```bash
$ getflag  
Check flag.Here is your token : s5cAJpM8ev6XHw998pRWG728z  
```

---

## Annexes Techniques  

| Position | Octet (Hex) | Décodage (Hex - Index) | Caractère |
|----------|-------------|------------------------|-----------|
| 0        | 0x66        | 0x66 - 0 = 0x66 (102) | f         |
| 1        | 0x34        | 0x34 - 1 = 0x33 (51)  | 3         |
| 2        | 0x6b        | 0x6b - 2 = 0x69 (105) | i         |
| ...      | ...         | ...                    | ...       |
| 24       | 0x0a        | 0x0a - 24 = -14        | (ignoré)  |

---

## Compétences Mobilisées  
- **Reverse Engineering** : Déduction d'un algorithme par tests black-box.  
- **Programmation** : Manipulation de bytes et gestion des erreurs en Python.  
- **Sécurité Unix** : Exploitation d'un binaire SUID pour escalade de privilèges.  

---

## Conclusion  
Ce niveau illustre deux failles critiques :  
1. **Sécurité par l'obscurité** : Un algorithme de chiffrement faible est facilement inversible.  
2. **Gestion des permissions** : Le SUID permet d'accéder à des ressources protégées via un binaire vulnérable.  

**Recommandations** :  
- Utiliser des algorithmes de chiffrement robustes (ex: AES).  
- Éviter les bits SUID sur des binaires traitant des données sensibles.  
- Supprimer les fichiers temporaires contenant des secrets.  