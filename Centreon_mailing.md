# 1 Installation des prérequis : 

Installation de 
 - Postfix : Package principal
 - mailutils : gestion du courrier par console (pour des tests principalement)
 - libsasl2-2 : Interface de programmation de Cyrus SASL
 -  libsasl2-modules : Modules supplémentaires pour libsasl2
 - ca-certificates : Contient des fichiers PEM

>	apt-get install postfix mailutils libsasl2-2 ca-certificates libsasl2-modules

Choisir l'option 'site internet' puis laisser le reste par défaut

>	systemctl enable postfix

# 2 Configuration

Dans le fichier de configuration par défaut :

>	nano /etc/postfix/main.cf

Modifier la ligne 'relayhost =' de sorte que : 

>	relayhost = [smtp.gmail.com]:587

Puis en fin de fichier ajouter les éléments suivants :

_Activation de l'authentification_
>	smtp_sasl_auth_enable = yes

_Pointer vers le fichier sasl_passwd_
>	smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd

_interdire le mode anonyme_
>	smtp_sasl_security_options = noanonymous

_Indiquer ou se trouve le certificat_
>	smtp_tls_CAfile = /etc/postfix/cacert.pem

_Forcer l'utilisation du TLS_
>	smtp_use_tls = yes

# 3 Authentification Google
## 3.1 Sur le compte gmail

Avec le compte de service, se rendre sur "[**https://myaccount.google.com/security**](https://myaccount.google.com/security)"


![[Pasted image 20231106113840.png]]

Créer un mot de passe d'application et sauvegarder le mot de passe à 16 caractère (Exemple sous Passbolt)

## 3.2 Sur le serveur Centreon/Postfix

Créer le fichier : 

>	nano /etc/postfix/sasl_passwd

Ajouter les informations du compte Google de service :

>	[smtp.gmail.com]:587 USERNAME@gmail.com:MDP

Ajouter les droits sur le fichier

>	chmod 400 /etc/postfix/sasl_passwd

Executer un postmap pour la prise en compte du fichier 'sasl_passwd'

>	postmap /etc/postfix/sasl_passwd

On va ensuite générer un certificat '.pem' puis le rediriger vers '/etc/postfix/cacert.pem'

>	cd /etc/ssl/certs
> 
>	openssl req -newkey rsa:2048 -new -nodes -x509 -days 3650 -keyout key-for-smtp-gmail.pem -out cert-for-smtp-gmail.pem
> 
>	cat /etc/ssl/certs/cert-for-smtp-gmail.pem | sudo tee -a /etc/postfix/cacert.pem

Relancer le service postfix et vérifier son status

>	/etc/init.d/postfix reload

>	/etc/init.d/postfix status

# 4 Test 

Pour tester le fonctionnement, utiliser la commande suivante :

>	echo "Test mail from postfix" | mail -s "Test Postfix" adresse@mail.com

Le destinataire devrait recevoir un mail avec "Test Postfix" en objet et "Test mail from postfix" en contenu. 

A noter que le mail apparaît dans les "messages envoyés" du compte de service (attention aux saturations !!!)
