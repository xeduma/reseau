# sommaire
[Configuration](#configuration)

[Ajouter un hote](#Ajouter-un-hôte)

[Ajouter une Template](#Ajouter-une-Template)

[imprimante](#imprimantes)

[Graphique custom](#Graphique-custom)

[monitoring une base SQL](#ODBC---sql)



# configuration
```bash
/etc/zabbix/zabbix_server.conf
```

# Ajouter un hôte
Dans zabbix : Surveillance > Hôtes

## ping 
nom

groupe

(interface) Ajouter > Agent > IP

## SNMP




# Ajouter une Template
télécharger une template depuis https://www.zabbix.com/integrations

Dans zabbix : Collecte de données > Modèles

importé

choisir l'ensemble des règles, pour ajouter l'intégratlité de la template

ensuite en peut ajouter notre hôte  spécial

## Ajouter une Mibs
si la template n'est pas dispo, il faut ajouter une mib depuis le site du constructeur
emplacement de la mib
```bash
/usr/share/snmp/mibs 
```

# imprimantes
import template richo
collecte de donnée > modèle > snmp ricoh > graphique > créer
elements > ajouté 
nom
cle : A_TonerLevel[Cyan Toner]
numérique flottant
oid : .1.3.6.1.4.1.367.3.2.1.2.24.1.1.5.2


# Graphique custom
emplacement
```bash
/usr/share/zabbix/modules 
```




# ODBC - SQL
Zabbix va contacter le SQL avec le pilote ODBC

https://www.zabbix.com/integrations/mssql#mssql_odbc
https://docs.microsoft.com/en-us/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server?view=sql-server-ver16
https://medium.com/@amit61508/microsoft-sql-ms-sql-database-monitoring-with-zabbix-dffd1f5b0f0a

### Créer le user sur le serveur SQL
sql instance   microsoft : Sécurité > connexions > Nouvelle connexion ....
mappage d'utilisateur > accès MSDB >user   dbo_datareader

### Donner accès au user    commmande dans le SQL machine
```
use master
go
grant view server state to user
```
vérifier le port      > gestionnaire de config SQL Server
config réseau > protocols pour <INSTANCE>   propriété  > port ouvert 

### Installer le pilote ODBC windows 
https://docs.microsoft.com/en-us/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server?view=sql-server-ver16


## Config odbc

```odbcinst -j```

```nano /etc/odbc.ini```

```
[coucou]
Description = MSSQL test database
Driver      = ODBC Driver 18 for SQL Server
Server      = coucou.local
User        = zabbix_monitor
#Password    =
Port        = 1433
#Database    =
TrustServerCertificate = yes
#ErrorMessagesPath=/tmp/ODBC_zabbix/
LogPath=/tmp/ODBC_zabbix
```

```nano /etc/odbcinst.ini```

```
nano /etc/odbcinst.ini
[ODBC Driver 18 for SQL Server]
Description = Microsoft ODBC Driver 18 for SQL Server
Driver      = /opt/microsoft/msodbcsql18/lib64/libmsodbcsql-18.3.so.3.1
UsageCount  = 1
TrustServerCertificate = False
```
Vérifier que le libmsodbcsql-18.3 est la bonne version (18.x ?)
TrustCertificat a false car pas de SSL


### Tester la connexion : 
```isql -v coucou zabbix_monitor "mdp"```


### Activer odbc sur zabbix :
```nano /etc/zabbix/zabbi_server.config```

ctr+ + w : rechercher  « ODBCpollers »
```StartODBCPollers=5```                #décommenter cette ligne

```Systemctl restart zabbix_server```

### Ajouter le host dans zabbix : 
ajoute modèle host > MSSQL by ODBC
dans macro > Ajouté : 
Macro | Valeur
 ---: | :---
{$MSSQL.DSN} | coucou
{$MSSQL.PASSWORD} | MDP_secret
{$MSSQL.USER} | zabbix_monitor


