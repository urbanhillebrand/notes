# influxdb

## cmdline einrichten

influx config create --config-name default --org myort --token mytoken --active --host-url http://localhost:8086

## Sample-Queries

### von Command-Line (Quoting!)

```
sudo influx -precision rfc3339 -format=json -database collectd_current -execute \
    "select * from \"statsd_value\" where \"host\" = 'bms_1917_0006_CleanCapital' \
    and time >= '2020-03-17 22:59:00' and time <= '2020-03-17 23:00:00' \
    and \"type_instance\" = 'bms.bat.current'"
```

format: json, csv, column (default)
