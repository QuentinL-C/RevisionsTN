Bootcamp Administration de services réseau
==========================================

### Partie 1: Configuration réseau des machines

#### Question 1

Le fichier /etc/hostname contient le nom de la machine: Optiplex
-9020

On peut retrouver son adresse IP en tapant la commande:

```bash
$ ifconfig
```

On peut retrouver l'adresse IP de la passerelle avec la commande

```bash
$ route -n
```

#### Question 2

On peut retrouver le nom du driver(eth1) en tapant la commande:

```bash
$ ethtool -i eth1
driver: e1000e
version: 2.3.2-k
firmware-version: 0.13-3
bus-info: 0000:00:19.0
supports-statistics: yes
supports-test: yes
supports-eeprom-access: yes
supports-register-dump: yes
supports-priv-flags: no
```

Puis:

```bash
$ locate e1000e
/lib/modules/3.16-2-amd64/kernel/drivers/net/ethernet/intel/e1000e
/lib/modules/3.16-2-amd64/kernel/drivers/net/ethernet/intel/e1000e/e1000e.ko
/lib/modules/3.16-3-amd64/kernel/drivers/net/ethernet/intel/e1000e
/lib/modules/3.16-3-amd64/kernel/drivers/net/ethernet/intel/e1000e/e1000e.ko
/lib/modules/3.16.0-4-amd64/kernel/drivers/net/ethernet/intel/e1000e
/lib/modules/3.16.0-4-amd64/kernel/drivers/net/ethernet/intel/e1000e/e1000e.ko
```

La dernière version serait: /lib/modules/3.16-3-amd64/kernel/drivers/net/ethernet/intel/e1000

Le nom donné à l'alias est eth1 pour Ethernet 1 (numéro 1).

#### Question 3

Avec la commande ifconfig on récupère l'adresse IP de eth1 ainsi
son adresse de broadcast et son masque.
La commande route nous permet de récupérer l'adresse de la passerelle

Pour paramétrer l'adresse IP on utilise la commande:

```bash
$ ifconfig <interface> <adresseip> <netmask>
```

Exemple:

```bash
$ ifconfig eth1 192.168.169.221 255.255.255.0
```

Pour paramétrer l'adresse IP de la passerelle on utilise:

```bash
$ route add default gw <adresseip> <interface>
```

Exemple:

```bash
$ route add default gw 192.168.197.254 eth1
```

#### Question 4

Pour pérenniser ces différents paramètres, il suffit de les rentrer
dans le fichier /etc/network/interfaces

On utilise alors plus DHCP.

```text
# /etc/network/interfaces
# auto lo
# iface lo inet loopback

iface eth1 inet static
address 192.168.197.221
netmask 255.255.255.0
network 192.168.197.0
broadcast 192.168.197.255
gateway 192.168.197.254
```

#### Question 7

Si vous vous êtes connecté en IP statique, il vous faut rajouter manuellement
la route par défaut avec la commande route:

```bash
route add default gw 192.168.197.254 eth1
```

Description des colonnes:

* La destination(**Réseau**) : c'est une adresse IP qui indique quels sont les paquets de données qui vont suivre cette route selon leur destination.
* La passerelle(**Gateway**) : c'est une adresse IP qui indique par où les paquets vont passer pour arriver à destination. Ils seront envoyés à cette adresse.
* Le masque de sous-réseau(**Genmask**) : c'est une suite de 4 octets qui permet d'indiquer quelle est la taille de chaque partie de l'adresse IP (partie réseau et partie hôte).
* Les indicateurs(**Indic**) : Ils correspondent à l'état de la route (    U signifie que la route est active (Up) et G que la route est une passerelle .
* Les sauts(**Metric**) : C'est un nombre qui indique combien d'intermédiaires il reste avant d'arriver à la destination.
* Les références(**Ref**) : C'est un nombre qui indique le nombre de références associées à cette route.
* L'utilisation(**Use**) : C'est un compteur d'utilisation de la route.
* L'interface réseau(**Iface**) : C'est le nom de l'interface réseau qui sera utilisée pour cette route.


#### Question 8

Le fichier /etc/hosts permet de faire correspondre des noms de domaines avec des
adresses IP. Cela peut constituer une sorte de faille de sécurité. Si une tierce
personne a accès à notre fichier il peut changer certaines correspondance et
redirigé certain nom de domaine vers une autre adresse IP (www.bing.com amène vers
google.fr par exemple)


Liste des autres bases de données dans nsswitch.conf:

* **/etc/passwd**: informations à propos des utilisateurs ;
* **/etc/group**: informations à propos des groupes ;
* **/etc/shadow**: mots de passe cryptés des utilisateurs ;
* **/etc/ethers**: contient les adresses Ethernet sur 48 bit et leur adresse IP ;
correspondante, une ligne par adresse IP ;
* **/etc/protocols**: contient la liste des protocoles de l'Internet. Noms, numéro et alias ;
* **/etc/services**: contient la liste des services réseau de l'Internet. Noms, port/protocol, alias ;
* **/etc/rpc**: contient des noms lisibles par les humains qui peuvent être utilisés
à la place des numéros de programmes RPC.

#### Question 9

* **ping**: ICMP(Internet Control Message Protocol) echo envoyé vers destinataire (peut-être réponde) ;
* **traceroute**: Time To Live croissant des messages ICMP envoyés. Permet de découvrir
le chemin ;
* **nmap(Network Mapper)**: fingerprinting des hôtes du réseau(OS, version, services, ...) ;
* **nslookup**: déprécié, remplacé par dig et host ;
* **host**: envoi d'une requête DNS ;
* **dig**: interroge les entrées d'un serveur DNS(A, NS, MX, ...);
* **netstat**: informations sur les connexions réseaux actives (TCP+UDP) ;
* **whois**: interroge l'annuaire inversé(reverse DNS). Usage: whois @IP ;
* **iptables**:
