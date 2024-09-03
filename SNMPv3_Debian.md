# 1. Prérequis

Installation des prérequis : 

```
apt update && apt-get -y install snmp snmpd libsnmp-dev
```

Arrêter le service (Indispensable pour que l'ajout du *2.2* soit pris en compte):

```
/etc/init.d/snmpd stop
```

# 2. Sur l'hôte à superviser

## 2.1 
Ajouter le futur utilisateur dans le fichier `nano /etc/snmp/snmpd.conf` et commenter les lignes correspondantes aux communautés snmpv2c (si elles existaient préalablement)

![[Pasted image 20240104142006.png]]

## 2.2

Dans le fichier `/var/lib/snmp/snmpd.conf` créer le nouvel utilisateur :

```
createUser usersnmpv3 SHA MOTDEPASSE AES PASSPHRASE
```


#### Attention !!! Ne pas utiliser le caractère ";" dans la passphrase, même entre 'quotes' centreon passe à l'argument suivant sans lire la fin de la chaine de caractères

Exemple : 

![[Pasted image 20240104142342.png]]

## 2.3 

redémarrer le service :

```
/etc/init.d/snmpd start
```

Vérification du bon fonctionnement en snmpv3 (penser aux 'quotes' pour les mots de passe et passphrase) : 

```
snmpwalk -v 3 -u usersnmpv3 -a SHA -A 'MOTDEPASSE' -x AES -X 'PASSPHRASE' -l authPriv localhost
```


# 3. Paramétrage sur Centreon : 

Installer le connecteur de supervision Linux SNMP v3

![[Pasted image 20231219142146.png]]

Ensuite lors de l'ajout des hôtes, laisser le nom de communauté vide et sélectionner la version **3**

Puis le Modèle "OS-Linux-SNMPv3-custom"

Enfin renseigner les différentes valeurs correspondantes aux éléments saisis dans le fichier `/var/lib/snmp/snmpd.conf`

![[Pasted image 20240104140646.png]]
