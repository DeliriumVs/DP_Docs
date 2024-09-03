Nettoyage du système des anciennes install 

```
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done
```


# Ajout des clés officielle GPG Docker :

```
sudo apt-get update
```

```
sudo apt-get install ca-certificates curl gnupg -y
```

```
sudo install -m 0755 -d /etc/apt/keyrings
```

```
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

```
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```


# Ajout des repertoires sources APT :

```
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

```
sudo apt-get update
```

```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose docker-compose-plugin -y
```

```
git clone https://github.com/wazuh/wazuh-docker.git -b v4.8.0
```

```
cd wazuh-docker/single-node/
```

```
docker-compose -f generate-indexer-certs.yml run --rm generator
```

```
docker-compose up -d
```


--> https://IP
username : admin
password : SecretPassword
