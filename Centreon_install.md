## Prérequis

Après avoir installé votre serveur, mettez à jour votre système d'exploitation à l'aide de la commande suivante :

- Debian 11

```
apt update && apt upgrade
```

> Acceptez toutes les clés GPG proposées et redémarrez votre serveur si une mise à jour du noyau est proposée.

## Étape 1 : Pré-installation

#### Installer les dépendances

Installez les dépendances suivantes :

```
apt update && apt install lsb-release ca-certificates apt-transport-https software-properties-common wget gnupg2 curl
```

#### Installer le dépôt Sury APT pour PHP 8.1

Pour installer le dépôt Sury, exécutez la commande suivante :

```
echo "deb https://packages.sury.org/php/ bullseye main" | tee /etc/apt/sources.list.d/sury-php.list
```

Ensuite, importez la clé du dépôt :

```
wget -O- https://packages.sury.org/php/apt.gpg | gpg --dearmor | tee /etc/apt/trusted.gpg.d/php.gpg  > /dev/null 2>&1apt update
```

#### Dépôt MariaDB

- Debian 11

```
curl -LsS https://r.mariadb.com/downloads/mariadb_repo_setup | bash -s -- --os-type=debian --os-version=11 --mariadb-server-version="mariadb-10.5"
```

#### Dépôt Centreon

Pour installer le logiciel Centreon, vous devez d'abord installer le dépôt Centreon.

Installez le dépôt Centreon à l'aide de la commande suivante :

```
echo "deb https://packages.centreon.com/apt-standard-23.10-stable/ $(lsb_release -sc) main" | tee /etc/apt/sources.list.d/centreon.listecho "deb https://packages.centreon.com/apt-plugins-stable/ $(lsb_release -sc) main" | tee /etc/apt/sources.list.d/centreon-plugins.list
```

Ensuite, importez la clé du dépôt :

```
wget -O- https://apt-key.centreon.com | gpg --dearmor | tee /etc/apt/trusted.gpg.d/centreon.gpg > /dev/null 2>&1apt update
```

### Étape 2 : Installation

Cette section décrit comment installer un serveur central Centreon.

Vous pouvez installer ce serveur avec une base de données locale au serveur, ou une base de données distante sur un serveur dédié.

- Avec base de données locale
- Avec une base de données déportée

```
apt updateapt install -y --no-install-recommends centreonsystemctl daemon-reloadsystemctl restart mariadb
```

### Étape 3 : Configuration

### Nom du serveur

Si vous le souhaitez, vous pouvez changer le nom d'hôte du serveur en utilisant la commande suivante :

```
hostnamectl set-hostname new-server-name
```

Remplacez **new-server-name** par le nom que vous souhaitez. Exemple :

```
hostnamectl set-hostname central
```

### Démarrage des services au démarrage du système

Pour que les services démarrent automatiquement au démarrage du système, exécutez les commandes suivantes sur le serveur central :

```
systemctl enable php8.1-fpm apache2 centreon cbd centengine gorgoned centreontrapd snmpd snmptrapd
```

Exécutez ensuite la commande suivante (sur le serveur central si vous utilisez une base de données locale, ou sur votre serveur de base de données distant) :

```
systemctl enable mariadbsystemctl restart mariadb
```

### Sécuriser la base de données

Depuis MariaDB 10.5, il est obligatoire de sécuriser l'accès root de la base de données avant d'installer Centreon. Si vous utilisez une base de données locale, exécutez la commande suivante sur le serveur central :

```
mysql_secure_installation
```

- Répondez **oui** à toutes les questions, sauf à "Disallow root login remotely ?".
- Il est obligatoire de définir un mot de passe pour l'utilisateur **root** de la base de données. Ce mot de passe vous sera demandé pendant l'installation web

## Étape 4 : Installation web

1. Démarrez le serveur Apache avec la commande suivante :

```
systemctl start apache2
```

2. Pour terminer l'installation, suivez la procédure d'installation web
