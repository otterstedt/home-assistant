# Enable Tellstick access to hass in docker

## Host machine (raspberry pi)
### Install telldus-core 
Add apt repo: 
```
https://s3.eu-central-1.amazonaws.com/download.telldus.com unstable main
```

Install packages:
```
telldus-core
socat
```

Configure Tellstick Devices
```
/etc/tellstick.conf
```

Add systemd services:
tellstick-clients.service
```
[Unit]
Description=Expose Telldus sockets over TCP
After=network.target

[Service]
ExecStart=/usr/bin/socat TCP-LISTEN:50800,reuseaddr,fork,bind=<<Host bind IP>> UNIX-CONNECT:/tmp/TelldusClient
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

tellstick-events.service
```
[Unit]
Description=Expose Telldus sockets over TCP
After=network.target

[Service]
ExecStart=/usr/bin/socat TCP-LISTEN:50801,reuseaddr,fork,bind=<<Host bind IP>> UNIX-CONNECT:/tmp/TelldusEvents
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

Host Bind IP is the IP for the host machine that is reachable from the home assistant docker container.

## Home Assistant Config

```
tellstick:
  host: <<Host bind IP>>
  port: [50800, 50801]

```
