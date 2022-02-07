# BADASS - partie 2
## statique

### Lancement images docker

Comme pour la partie 1, nous allons construire nos images docker (si ce n'est pas déjà le cas), afin de les utiliser comme routeurs/end devices dans _GNS3_.

```sh
$ cd P1
$ sudo docker build -t host_fherbine -f ./_fherbine_host-1 .
$ sudo docker build -t routeur_aabelque -f ./_aabelque_routeur-1 .
```

On va ensuite ajouter ces images dans _GNS3_:
- On va sur `edit`>`Preferences` (Ctrl+Maj+P)
- On va ensuite dans `Docker` > `Docker containers`
- Pour les "end devices" (host_fherbine)
    - `New` puis on selectionnne l'image docker `host_fherbine`
    - On appuie sur `Next` jusqu'à la fermeture du dialogue
- Pour les "routeurs" (routeur_aabelque)
    - `New` puis on selectionnne l'image docker `routeur_aabelque`
    - Le nom par défaut devrait être celui de l'image, `routeur_aabelque`, on peut donc appuyer sur `Next` à cette étape.
    - A l'etape suivante on modifie notre nombre d'interfaces réseau physique (Adapters) de 1 (par défaut), à 2 (`eth0` et `eth1`).
    - On peut appuyer sur `Next` jusqu'à la fin du dialogue.

A cette étape, on peut se permettre d'apporter deux modifications falcultatives sur notre image docker routeur dans GNS3:
(dans `edit`>`Preferences` > `Docker` > `Docker containers` )

On selectionne `routeur_aabelque` et on clique sur `Edit`, on peut changer ici le picto (Symbol), ainsi que la categorie de notre  équipement.

On peut ensuite construire notre topologie VxLAN, conformément avec le sujet, en faisant du Drag&Drop depuis l'interface de GNS3 et en ajoutant nos liaison.

### configuration statique des interfaces réseau

Après avoir réaliser notre topologie réseau et nos liaisons entre nos équipements sur GNS3, on va réaliser la configuration de nos équipements.

**Sans les avoir démarrés au préalables**, on pourra double-cliquer sur nos equipements pour editer leurs fichiers `/etc/network/interfaces` (Dans `Network configuration`, puis le bouton `edit`).

GNS3 nous fournit des configurations par défaut (ip statiques et dynamiques), pour toutes nos interfaces `eth0`, et `eth1`.

D'après le sujet voici les configurations choisit:

|      hostname      | interfaces |     IP      | gateway  |
|--------------------|------------|-------------|----------|
|   host_fherbine-1  |    eth0    | 30.1.1.1/24 | 30.1.1.3 |
|   host_fherbine-2  |    eth0    | 30.1.1.2/24 | 30.1.1.3 |
| routeur_aabelque-1 |    eth0    | 10.1.1.1/24 |          |
| routeur_aabelque-1 |    eth1    | 30.1.1.3/24 |          |
| routeur_aabelque-2 |    eth0    | 10.1.1.2/24 |          |
| routeur_aabelque-2 |    eth1    | 30.1.1.3/24 |          |


### Configuration des bridges et VxLAN

Il faudra ensuite démarrer les équipements et se connecter successivement aux deux routeurs pour configurer notre VxLAN.

Pour se connecter, il nous suffit de double-cliquer sur l'équipement.

#### Configuration d'une interface VxLAN

```sh
$ ip link add vxlan10 type vxlan id 10 remote <addresse  IP de eth0 du routeur opposé> dstport 4789 dev eth0 # Ajout d'une interface VxLAN avec pour id 10, transitant par `eth0`, vers l'adresse IP précisé.
$ ip link set vxlan10 up # on active l'interface
$ ip addr add <IP/mask de eth1> dev vxlan10 # on lui donne la même adresse IP que eth1
```

Le VxLAN, ajoutant une couche d'abstraction pour que nos hosts de chaque réseau pensent être sur le même LAN, il nous faudra confondre sur nos routeurs leurs interfaces vxlan et eth1 avec un bridge.

#### Configuration d'un bridge

```sh
$ ip link add name br0 type bridge # création du bridge br0
$ ip link set br0 up # on active le bridge
$ ip link set vxlan10 master br0 # on ajoute l'interface vxlan10 au bridge
$ ip link set eth1 master br0 # on ajoute l'interface eth1 au bridge
```

> les fichiers de configuration statique pour les interfaces eth0/1 sont dispos dans ce repo.