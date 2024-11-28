# LAN

## Général

```dart
#mode configuration
enable                                   en                          execution privilégié
configure terminal                      conf t                       mode configuration
hostname "Switch1"

#DNS
no ip domain-lookup                                                  pas de recherche DNS
ipdomain-name "RT-lab.lry"                                           adresse DNS
enable secret "class"                                                MDP Execution privilégié (crypté)

#MDP
line console 0                                                      MDP console                       
password cisco
login

line VTY 0 15                                                       MDP VTY (telnet) | SSH
password cisco
login

crypto key generate rsa 1024                                        Générer clef RSA sur 1024 bits    SSH

service password-encryption                                         Chiffrer MDP dans running config
login                                                               Active connexion

#Sauvegarde | redemarrage 
write                                                               Sauvegarder
clock set "h:mm:ss"                                                 Set horloge

erease startup-config                                               supprime la config enregistée
copy running-config startup-config                                  Copy conf temporaire dans la conf stoquée local
reload                                                              Rechargé la config

banner motd" accès non autorié par cet utilisateur"

#IP   addresse      Interfaces
interface GigabyteEthernet 0/0               int GiE0/0             configurer interface Gi0/0
interface FastEthernet 0/0                   int FaE0/0             configurer interface Gi0/0

ip addressse 172.16.32.254 255.255.255.0
description "Réseau Utilisateur"
no shutdown                                 no shut                Active l'interface

#Interface série 0/0/0
int S0/0/0
clock rate 125000                                                   Horloge

#priorité interface série
ip routing
standby 1 priority 100      (default)
standby 1 priority 50        (prioritaire)

#route
ip route 192.168.3.0 255.255.255.255.0 192.168.1.1 100            route         Route statique
#       <réseau dest>    <masque>    <ip interface distante>  <métrique>
```

## Show

```dart
show running config                         sh run                  ffiche la configuration
show ip route                                                       affiche les routes
show mac-address-table                                              affiche la table MAC
show mac-address-table static 00e0.2917.1884 vlan 3 fa0/4           affiche la table MAC de l'addresse MAC sur le vlan

show interface fa 0/1 switchport                                    affiche les parametres de l'interface

show ip nat translation

show ip dhcp binding 

show cdp neighbors detail                                           affiche la config des voisins de couche 1 et 2
```

## Vlan

```dart
vlan 2                                                             créer vlan 2
name vlan 2                                                        nome vlan 2
exit

int range fa0/4-6
switchport mode access
switchport acces vlan2

int gi0/8
switchport trunk encapsulation dot1q                              Mode trunk aggregation de port
switchport mode trunk                                                                                           Mode trunk
switchport trun allowed vlan all                                  allouéer touts les vlans

interface vlan 3                         int vlan 3                configurer vlan 3
ip address 192.168.1.4 255.255.255.0
no shutdown

show vlan id 2

no vlan3                                           ,               supprimez le vlan 3

spanning-tree vlan3 priority 4096                                  STP 4096 > 32768 par default

**Routeurs :** 
int GI 0/0.1                                                       Interface 0/0 division 1

encapsulation dot1q 10                                             lien aggréé sur le vlan 10
ip address 192.168.20.254 255.255.255.0                            ip passerelle du VLAN
no shut
```

## Ping | Tracert

```bash
tracert 192.168.1.25                                               affiche les sauts routeur de l'ip
ping -I <ip source> <ipdest>
```

### ACL

```dart
#le réseau 192.168.1.0 (fa0/0)   ne doit pas pouvoir accéder au réseau 192.168.2.0 (fa0/1)
access-list 1 deny 192.168.1.0 0.0.0.192
access-list 1 permit any   
int fa 0/1
ip access-group 1 out
#refuser l'acces au reseau source 192.168.1.0 sur l'interface de sortie fa0/1

#supprimer access-list
ip access-list standard 1
no 20
15 permit 192.168.1.1 255.255.255.0

#ETENDUS
acces-list 101 permit tcp 172.16.0.1 0.0.0.0 192.168.1.2 0.0.255.255 eq ftp
#     acl 101 permet le tcp depuis <src ip> vers <dest ip> sur le port ftp (21)
deny ip any any log

```

### NAT

```bash

NAT static : 
(config)"#"int fa 0/0
(config-if)"#"ip nat inside source static 10.6.1.20 171.68.68.10
# nat sur la source <ip> qui deviendra <ip>

NAT dynamic : 
(config-if)"#"ip nat pool nat-pool1 179.9.8.70 179.9.8.95 netmask 255.255.255.240
(config-if)"#"ip nat inside source list 1 pool nat-pool1
(config-if)"#"access-list 1 permit 10.0.0.0 0.0.0.255

PAT : 
(config-if)"#"ip address 192.168.3.1 255.255.255.0
(config-if)"#"ip nat inside

REDIRECTION DE PORT : 
(config-if)"#"ip nat inside source static tcp 192.168.1.1 80 200.200.200.200 88
#redirige le port source <ip:port> vers le port destination <ip:port>

(config)"#"clear ip nat translation *                        vider les tables NAT
```

### DHCP

```dart
sh ip dhcp binding
debug ip dhcp server events
```

### MAC

```jsx
int Fa 0/1
switchchport port-security maximum 1                              max 1 adr mac sur le port
switchport mode access
switchport port-security mac-address sticky 
```

### VTP

```dart
vtp mode client
vtp server
vtp domain a2sr

```

---

## RIPv2    Routing Information Protocol

```dart
routeur rip                                                   Active RIP
version 2                                                     Rip v2
network 172.16.3.0                                                                                              
passive-interface fa 0/1                                      désactive ripv2 pour l'interface
```

# **OSPF   Open Shortest Path First**

![Screenshot_1.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/71d247b5-b64f-46ad-8829-2c7bc74d1a72/Screenshot_1.png)

### IPv4   OSPF v2                                   |                    IPv6          OSPF v3              |                     Description

```dart
conf t                                              
                                                        |     ipv6 unicast-routing                      |       active routage ipv6
router ospf 1                                           |     ipv6 ospf 1                               |       active le protocole OSPF
router id                                                                                               |       indique l'ID d'un routeur
network 172.16.1.0 0.0.0.252 area 0                     |     ipv6 ospf 172.16.1.0 0.0.0.252 area 0     |       active la zone aire 0 sur un réseau (!masque inversé)
network 10.0.1.0 0.0.0.255 area 1                       |     ipv6 ospf 10.0.1.0 0.0.0.255 area 1       |       active la zone aire 1 sur un réseau (!masque inversé)

ip ospf cost 11                                               defini le coût directement sur une interface                                                  cout d'un Routeur

int se0/0/0                                             |
no passive interface                                                                                    |       interface passive a OSPF     (passive sur interface bout de réseau)

//coût des liens 
ip ospf cost 10                                         |                                               |       Coût du lien  10   //par default série  (100 000 000 / 1 544 000) = 64
auto-cost reference-bandwidth 10 000      =>    BP de référence = 10 000    =>    10GiEth 10 Gbits/S  cost 1    =>    GiEth 1 Gbits/S  cost 10     =>      FastEth  100Mbit/S  cost 100
//coût des liens automatique   sur 10 000             (plus pertinent pour la priorité des liens)

show ip ospf interface                                  |                                               |       Affiche les parametres OSPF d'une interface      
show ip ospf neighbors                                  |                                               |       Affiche les voisins      ?????
show ip protocols                                       |                                               |       Affiche les networks area     protocole de routage     interface activées                 
show ip route ospf                                      |                                               |       Affiche les routes créer par OSPF
show ip ospf database router

show ip route 192.168.1.0                               |     sh ipv6 route 192.168.1.0                 |       Affiche les routes pour l'interface

ip ospf authentification-key <Passord>                                           sécurité mot de passe
ip ospf message-digest-key 1 md5 7 secret
area 0 authentification message-digest

int lookback 0                                                                            interface de loobback (utile car lien en priorité (côut id le + fort))
ip address 192.168.31.33 255.255.255.255

redistrubute connected subnets                                         redistribution des réseau (pour addresses lookback, ou bout de réseau)
network 192.168.1.0 0.0.0.255 area 0

default-information originate                           pour rédistribuer la default gaeway      E1= Métrique de route incrémenté   E2 métrique de 20 par def           

auto-cost reference-bandwidth 10 000                    pour prendre en compte le gigabyte
```

Id cisco : mmwwgv

```bash
nslookup
rtproxy
```

1460 Octet > charge utile d’une trame TCP/ip

/30  masque inversé    0.0.0.3

/25                               0.0.0.127

### Switch niveau 3                  Cisco 3560

```dart
#interface SVI                              interface logique / virtuel d'un vlan

ip routing                                  active la couche 3 (routage)
int vlan 10
ip address 10.10.1.1 255.0.0.0
no shut
  
sh ip route                                 les deux réseau sont directement connecté 

no switchport                               pas de port niv 2    (comme un port d'un routeur)
ip address 10.10.1.1 255.0.0.0              on peut donc mettre une Adress a un port

```

### agrégation lien - 2 liens - Etherchannel

```dart
#LACP           agrégation de lien (2 liens)
int range gi0/23-24
channel-group 2 mode active                             active = LACP    désirable = Cisco

int port-channel 2
switchport mode trunk
switchport trunk native vlan 99
swiitchport trunk allowed vlan 2,3,4,5

int port-channel 2
no switchport                                      ajoute une ip sur la double liaison (donc int niveau 3)
ip address 10.10.0.1 255.0.0.0
no shut

int range fa 0/11-12
no switchport
channel-group 2 mode actif
no ip naddress
```

### vlan privé

```dart
vlan 101
private-vlan isolated                  vlan isolé / privé

```

## HSRP           Pool de Routeur actif passif (Master / Slave)             Cisco

```dart
ip routing
int GI0/0 
no switchport                                          active l'interface comme un routeur (pas de couche 2)
ip address 10.1.10.1 255.255.255.0                     ip réel
standby version 2
standby 1
standby 1 ip 10.1.10.2                                 ip virtuel
standby 1 priority 90                                  priority default = 100       le plus grand qui répond (104 actif   94 passif)
standby 1 preempt

standby 1 track serial 0/0 20                          tracker l'interface de sortie d'un routeur, la liaison source va donc passer par un autre lien. Priorité de 20 (10 default)

show standby brief
```

## Virtual Routeur Redundancy Protocol            VRRP                       Standard

```dart

int GI0/0 ip address 10.1.10.1 255.255.255.0           ip réel
vrrp 1 ip 10.1.10.2                                    ip virtuel
vrrp 1 priority 90                                     priority default = 100       le plus grand qui répond
vrrp 1 preempt

RSTP     rapid-pvst     spanning-tree
```
