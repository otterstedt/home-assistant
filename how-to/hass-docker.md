# Home Assistant in Docker
## Make InfluxDB considered started when port 8086 is listening
Add the following to ```influxdb.service```
```
ExecStartPost=/bin/sh -c 'until nc -z <<influx bind IP>> 8086; do sleep 0.2; done'
```
This will ensure no services depending on InfluxDB start before the listener is up and running.

## SystemD Service
```hass.service```

This example will start home assistant after influxdb is started

Configuration files are located in: ```/home/pi/homeass/config```
### Raspberry PI
```
[Unit]
Description=Homeassistant Docker
Requires=docker.service
After=docker.service influxdb.service

[Service]
ExecStart=/usr/bin/docker run --rm --privileged --name=home-assistant -v=/home/pi/homeass/config:/config -v=/etc/localtime:/etc/localtime:ro --net=host homeassistant/raspberrypi3-homeassistant
ExecStop=/usr/bin/docker stop home-assistant
ExecReload=/usr/bin/docker restart home-assistant
User=pi
Group=docker
Environment=PATH=/bin:/usr/bin:/usr/local/bin:$PATH
WorkingDirectory=/home/pi/homeass
TimeoutSec=600

[Install]
Alias=hass.service
WantedBy=multi-user.target
```
Enable and start
```
sudo systemctl enable hass.service
sudo systemctl start hass.service
``` 
### CentOS
```
[Unit]
Description=Homeassistant Docker
Requires=docker.service
After=docker.service influxdb.service

[Service]
ExecStart=/usr/bin/docker run --rm --name=home-assistant -v=/home/centos/homeass/config:/config -v=/etc/localtime:/etc/localtime:ro --net=host homeassistant/home-assistant
ExecStop=/usr/bin/docker stop home-assistant
#ExecStopPost=/usr/bin/docker rm -f home-assistant
ExecReload=/usr/bin/docker restart home-assistant
User=centos
Group=docker
Environment=PATH=/bin:/usr/bin:/usr/local/bin:$PATH
WorkingDirectory=/home/centos/homeass
TimeoutSec=600

[Install]
Alias=hass.service
WantedBy=multi-user.target
```

