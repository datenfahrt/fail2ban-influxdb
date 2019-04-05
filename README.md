# fail2ban-influxdb

if you have influxdb exposed via http, you can send metrics via curl to it. In this case from Fail2ban when a banaction is triggered. 

Use an existing Database or create a new with

```
$ curl http://localhost:8086/query -XPOST --data-urlencode "q=CREATE DATABASE fail2ban"
```

Copy ```influxdb-writer.conf``` to ```/etc/fail2ban/action.d/``` and adjust influxdb_url + influxdb_db in the [Init] Section to fit your Environment:


```
...

[Init]

init = 'Write Point to InfluxDB'

# make sure, the database is available
influxdb_url = http://127.0.0.1:8086
influxdb_db = fail2ban

...
```

Edit your ```/etc/fail2ban/jail.local``` and add, for example:


```
...
# ban & send an e-mail with whois report to the destemail & write metrics to influxdb
action_mw_influxdb = %(banaction)s[name=%(__name__)s, bantime="%(bantime)s", port="%(port)s", protocol="%(protocol)s", chain="%(chain)s"]
                     %(mta)s-whois[name=%(__name__)s, dest="%(destemail)s", protocol="%(protocol)s", chain="%(chain)s"
                     influxdb-writer[name=%(__name__)s]

...

# default action
action = %(action_mw_influxdb)s

...

```

* Restart fail2ban ```sudo systemctl restart fail2ban```
* TEST your Setup & make sure fail2ban works correctly
* Make a nice Dashboard 



