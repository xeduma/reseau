# CACTI

```nano /var/www/html/cacti/include/config.php```

```
$database_username = 'cacti';
$database_password = 'cacti';
```

```nano /etc/systemd/cactid```

```
User=www-data
Group=www-data
EnvironmentFile=/etc/default/cactid
```

config mysql   heure, cache ...
```nano /etc/mysql/my.cnf```


```sudo apt install libapache2-mod-php```

```
chown -r www-data:www-data /var/www/html/cacti/plugin/weathermap
chown -r www-data:www-data /var/www/html/cacti/plugin/sources
```

## WEATHERMAP

installer le plugin wheathermap

### ajouté un device snmp cisco generic 

Device > add > template cisco router

create graph for this device 

set interface    set   In/Out Bits (64-bit, BW)        > create


