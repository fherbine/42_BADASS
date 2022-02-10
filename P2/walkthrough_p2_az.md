# STATIC 

### host 1

```sh
$ ip addr add < ip/mask > dev eth1 # 30.1.1.1/24

````

### host 2 

```sh
$ ip addr add < ip/mask > dev eth1 # 30.1.1.2/24
```

### router 1

```sh
$ ip addr add < ip/mask > dev eth0 # 10.1.1.1/24
$ ip addr add < ip/mask > dev eth1 # 30.1.1.3/24

$ ip link add vxlan10 type vxlan id 10 remote < ip eth0 router 2 > dstport 4789 dev eth0 # 10.1.1.2
$ ip link set vxlan10 up # activation de l'interface 
$ ip addr add < ip/mask eth1 > dev vxlan10 # 30.1.1.3/24

$ ip link add br0 type bridge # creation du bridge
$ ip link set dev br0 up # activation du bridge
$ brctl addif br0 vxlan10 # ajout interface vxlan10 au bridge
$ brctl addif br0 eth1 # ajout interface eth1 au bridge
```
### router 2

```sh
$ ip addr add < ip/mask > dev eth0 # 10.1.1.2/24
$ ip addr add < ip/mask > dev eth1 # 30.1.1.3/24

$ ip link add vxlan10 type vxlan id 10 remote < ip eth0 router 1 > dstport 4789 dev eth0 # 10.1.1.1
$ ip link set vxlan10 up # activation de l'interface 
$ ip addr add < ip/mask eth1 > dev vxlan10 # 30.1.1.3/24

$ ip link add br0 type bridge # creation du bridge
$ ip link set dev br0 up # activation du bridge
$ brctl addif br0 vxlan10 # ajout interface vxlan10 au bridge
$ brctl addif br0 eth1 # ajout interface eth1 au bridge
```
# MULTICAST DYNAMIC

### host 1

```sh
$ ip addr add < ip/mask > dev eth1 # 30.1.1.1/24

````

### host 2 

```sh
$ ip addr add < ip/mask > dev eth1 # 30.1.1.2/24
```

### router 1

```sh
$ ip link add vxlan10 type vxlan id 10 dstport 4789 group < ip > dev eth0 ttl auto # 239.1.1.1 dans le sujet

$ brctl addbr br0
$ brctl addif br0 vxlan10
$ brctl addif br0 eth0
$ brctl addif br0 eth1
$ brctl stp br0 off

$ ip link set up dev br0
$ ip link set up dev vxlan10
```

### router 2

```sh
$ ip link add vxlan10 type vxlan id 10 dstport 4789 group < ip > dev eth0 ttl auto # 239.1.1.1 dans le sujet

$ brctl addbr br0
$ brctl addif br0 vxlan10
$ brctl addif br0 eth0
$ brctl addif br0 eth1
$ brctl stp br0 off

$ ip link set up dev br0
$ ip link set up dev vxlan10
```
