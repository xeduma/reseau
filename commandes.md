# LAN

## Général

```bash
#mode configuration
enable                                                                   en                                    execution privilégié
configure terminal                                                       conf t                                mode configuration
hostname "Switch1"

#DNS
no ip domain-lookup                                                                                            pas de recherche DNS
ipdomain-name "RT-lab.lry"                                                                                     adresse DNS
enable secret "class"                                                                                          MDP Execution privilégié (crypté)

#MDP
line console 0                                                                                                 MDP console                       
password cisco
login

line VTY 0 15                                                                                                  MDP VTY (telnet)
password cisco
login

crypto key generate rsa 1024                                                                                  Générer clef RSA sur 1024 bits

service password-encryption                                                                                    Chiffrer MDP dans running config
login                                                                                                          Active connexion

#Sauvegarde | redemarrage 
write                                                                                                          Sauvegarder
clock set "h:mm:ss"                                                                                            Set horloge

erease startup-config                                                                                          supprime la config enregistée
copy running-config startup-config                                                                             Copy conf temporaire dans la conf stoquée local
reload                                                                                                         Redemarer

banner motd" accès non autorié par cet utilisateur"

#IP   addresse      Interfaces
interface GigabyteEthernet 0/0                                           int GiE0/0                            configurer interface Gi0/0
interface FastEthernet 0/0                                               int FaE0/0                            configurer interface Gi0/0

ip addressse 172.16.32.254 255.255.255.0
description "Réseau Utilisateur"
no shutdown                                                              no shut                               Active l'interface

ip route 192.168.3.0 255.255.255.255.0 192.168.1.1 100                      route                                 Route statique
#       <réseau dest>    <masque>    <ip interface distante>  <métrique>

```

## Show

```dart
show running config                                                      sh run                                 affiche la configuration
show ip route                                                                                                   affiche les routes
show mac-address-table                                                                                          affiche la table MAC
show mac-address-table static 00e0.2917.1884 vlan 3 fa0/4                                                       affiche la table MAC de l'addresse MAC sur le vlan

show interface fa 0/1 switchport                                                                                affiche les parametres de l'interface
```

## Vlan

```bash
#Vlan
interface vlan 3                                                         int vlan 3                             configurer vlan 3
ip address 192.168.1.4 255.255.255.0
no shutdown

vlan 2
name vlan 2
exit

int Fa 0/1
switchchport port-security maximum 1                                                                            Port secure max 1
switchport mode acces
switchport port-security
switchport port-security mac-address sticky                                              
switchport port-security maximum 1
switchport mode trunk                                                                                           Mode trunk aggregation de port

switchport acces vlan2

interface range fa 0/1 - 6

no vlan3

spanning-tree vlan3 priority 4096                                                                               STP 4096 > 32768 par default

**Routeurs :** 
int GI 0/0.1                                                                                                    Interface 0/0 division 1

encapsulation dot1q 10                                                                                          lien aggréé sur le vlan 10
ip address 192.168.20.254 255.255.255.0                                                                         ip passerelle
```

## Ping | Tracert

```bash
tracert 192.168.1.25                                                                                            affiche les routes de l'ip
```

## RIPv2    Routing Information Protocol

```bash
routeur rip                                                                                                     Active RIP
version 2                                                                                                       Rip v2
network 172.16.3.0                                                                                              
passive-interface fa 0/1                                                                                        désactive ripv2 pour l'interface
```

# **OSPF   Open Shortest Path First**

![Screenshot_1.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/71d247b5-b64f-46ad-8829-2c7bc74d1a72/Screenshot_1.png)

### IPv4   OSPF v2                                   |                    IPv6          OSPF v3              |                     Description

```dart
conf t                                              
                                                        |     ipv6 unicast-routing                      |       active routage ipv6
ospf 1                                                  |     ipv6 ospf 1                               |       active le protocole OSPF
router id                                                                                               |       indique l'ID d'un routeur
network 10.0.1.0 0.0.0.255 area 1                       |     ipv6 ospf 10.0.1.0 0.0.0.255 area 1       |       active la zone aire 1 sur un réseau (!masque inversé)
network 172.16.1.0 0.0.0.252 area 0                     |     ipv6 ospf 172.16.1.0 0.0.0.252 area 0     |       active la zone aire 1 sur un réseau (!masque inversé)

int se0/0/0                                             |
no passive interface                                                                                    |       interface passive a OSPF

//coût des liens 
ip ospf cost 10                                         |    ip ospf cost 10                            |       Coût du lien  10   //par default série  (100 000 000 / 1 544 000) = 64
auto-cost reference-bandwidth 10 000      =>    BP de référence = 10 000    =>    10GiEth 10 Gbits/S  cost 1    =>    GiEth 1 Gbits/S  cost 10     =>      FastEth  100Mbit/S  cost 100
//coût des liens automatique   sur 10 000             (plus pertinent pour la priorité des liens)

show ip ospf interface                                  |     show ipv6 ospf interfaces                 |       Affiche les parametres OSPF d'une interface      
show ip ospf neighbors                                  |     show ipv6 ospf neighbors                  |       Affiche les voisins      ?????
show ip protocols                                       |     show ipv6 protocols                       |       Affiche les networks area     protocole de routage     interface activées                 
show ip route ospf                                      |     show ipv6 route ospf                      |       Affiche les routes créer par OSPF

sh ip route 192.168.1.0                                 |     sh ipv6 route 192.168.1.0                 |       Affiche les routes pour l'interface

```

Id cisco : mmwwgv

```bash
ipconfig /all
nslookup

rtproxy
```

1460 Octet > charge utile d’une trame TCP/ip
