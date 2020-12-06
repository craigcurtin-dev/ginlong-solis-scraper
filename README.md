# ginlong-solis-scraper

Dockerized version of [ginlong-scraper](https://github.com/dkruyt/ginlong-scraper)

For Docker newbies i would suggest running this with Portainer https://www.portainer.io/installation/

You can also move the environment variables into an ENV file and invoke that on the commandline when 
invoking the docker image.

In the case of two inverters (see note below) once you have the deviceid you can set up two seperate docker containers
and just vary the deviceId in the environment variables.

Scrapes PV statistics from the Ginlong monitor pages and outputs it to influxdb, pvoutput or mqtt.

There is a possibility it also works with the following inverters: Omnik Solar, Solarman and Trannergy Inverters

## Configuration

### Environment variables

| Environment variable      | Required | Description                                                                                          | Default value   |
|---------------------------|----------|------------------------------------------------------------------------------------------------------|-----------------|
| LOG_LEVEL                 | No       | Logging level (ERROR, INFO, DEBUG)                                                                   | `INFO`          |
| GINLONG_USERNAME          | Yes      | Ginlong Solis username                                                                               | *empty*         |
| GINLONG_PASSWORD          | Yes      | Ginlong Solis password                                                                               | *empty*         |
| GINLONG_DOMAIN            | No       | Ginlong Solis domain                                                                                 | `m.ginlong.com` |
| GINLONG_LANG              | No       | Ginlong Solis language                                                                               | `2` *(English)* |
| GINLONG_DEVICE_ID         | No       | Ginlong Solis device ID<br/>(only required if auto-detect fails or if you have more than one device) | *empty*         |
| USE_INFLUX                | No       | Set to true if you want to use InfluxDB as output                                                    | `false`         |
| INFLUX_DATABASE           | No       | InfluxDB DB name                                                                                     | `influxdb`      |
| INFLUX_SERVER             | No       | InfluxDB server                                                                                      | `localhost`     |
| INFLUX_PORT               | No       | InfluxDB server port                                                                                 | `8086`          |
| INFLUX_MEASUREMENT        | No       | InfluxDB measurement type                                                                            | `PV`            |
| USE_PVOUTPUT              | No       | Set to true if you want to use PvOutput as output                                                    | `false`         |
| PVOUTPUT_API_KEY          | No       | PvOutput API key                                                                                     | *empty*         |
| PVOUTPUT_SYSTEM_ID        | No       | PvOutput system ID                                                                                   | *empty*         |
| USE_MQTT                  | No       | Set to true if you want to use MQTT as output                                                        | `false`         |
| MQTT_CLIENT_ID            | No       | MQTT client ID                                                                                       | `pv`            |
| MQTT_SERVER               | No       | MQTT server                                                                                          | `localhost`     |
| MQTT_USERNAME             | No       | MQTT username                                                                                        | *empty*         |
| MQTT_PASSWORD             | No       | MQTT password                                                                                        | *empty*         |
| TZ                        | No       | TimeZone e.g Australia/Sydney                                                                        | *empty*         |

Note that if you have more than 1 device - then it is not readily apparent where to get the Device ID
In that case - setup the script, and set LOG_LEVEL to DEBUG, then view the logs and search for deviceId - 
this will list the IDs of each inverter.

Note that the TZData package will not be installed in the container - do the following from the sh shell prompt in the container

apk add tzdata
ls /usr/share/zoneinfo

Suppose you want to use Sydney - Australia 

First copy the proper zone to localtime

cp /usr/share/zoneinfo/Australia/Sydney /etc/localtime
Now specify your timezone

echo "Australia/Sydney" >  /etc/timezone
date
Result: Wed Mar 8 00:46:05 CET 2006

You can now remove the other timezones

apk del tzdata



## Bonus

The grafana-dashboard-example.json file you could import in to Grafana if you use the influx database. Then you can make a dashboard similar to this.

![grafana](https://github.com/dkruyt/resources/raw/master/grafana-dashboard-ginlong-small.png)
