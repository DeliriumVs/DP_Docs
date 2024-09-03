Commencer par désinstaller l'agent de la machine "client". Inutile s'il s'agit d'une VM révoquée.

Pour supprimer des machines de la surveillance Wazuh, il faut se connecter sur le docker `wazuh-manager`.

Tout d'abords identifier l'ID du docker concerné

```
docker ps -a
```

Puis ouvrir une console dans le docker `wazuh-manager`

```
docker exec -it wazuh/wazuh-manager /bin/bash
```

une fois connecté, on execute une commande qui va permettre de gérer les agents connecté au manager : 

```
/var/ossec/bin/manage_agents
```

En sélectionnant R on peut ensuite saisir l'ID de l'agent que l'on souhaite retirer de la surveillance. La liste est disponible dans le Dashboard web, ou dans le CLI en utilisant `L` pour lister l'ensemble des agents.
