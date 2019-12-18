# Docker
Our project on [docker hub](https://hub.docker.com/r/paradoxalarminterface/pai).

We support `amd64`, `arm64`, `armv7`. So you can run on your linux server or a raspberry PI.

Branches:

* `master` branch ```paradoxalarminterface/pai:latest```
* `dev`(less stable) branch with new features ```paradoxalarminterface/pai:dev-latest```

Docker images are tagged, so if you want to use specific version you can specify that ```paradoxalarminterface/pai:1.1.0```

For configuration see [Configuration](./Configuration) page.

**Note:** The docker images do not have support for Signal.

## Plain
Create required directories, configure and run PAI.

```
mkdir pai/config pai/log

wget https://raw.githubusercontent.com/ParadoxAlarmInterface/pai/master/config/pai.conf.example -O pai/config/pai.conf

edit pai/config/pai.conf as needed

docker run -it \
-v /etc/timezone:/etc/timezone:ro
-v /etc/localtime:/etc/localtime:ro
-v pai/config:/etc/pai:ro \
-v pai/log:/var/log/pai \
--user $(id -u):$(id -g) \
--device=/dev/tty.YOUR_SERIAL_PORT \
-P \
paradoxalarminterface/pai:latest
```

See [docker documentation](https://docs.docker.com/engine/reference/run/) for explanations.

## Composer
You have already installed Docker and Docker Composer.
Create folders in your home directory.
```
cd ~
mkdir -p docker/pai/config docker/pai/logs
cd docker
id
> uid=1000(pi) gid=1000(pi)
```

Create file `docker-compose.yml`
```
version: '3'

services:
  pai:
    container_name: pai
    restart: unless-stopped
    image: paradoxalarminterface/pai:latest
    volumes:
      - "./pai/config:/etc/pai:ro"
      - "./pai/log:/var/log/pai:rw"
      - "/etc/timezone:/etc/timezone:ro"
      - "/etc/localtime:/etc/localtime:ro"
    environment:
      - PUID=1000
      - PGID=1000
    devices:
      - "/dev/tty.YOUR_SERIAL_PORT"
    ports:
      - "10000:10000"
```
Port `10000` is required if you want to connect to PAI with Babyware.

`devices:` section is only required if you use Serial port to connect to the Panel.

Populate `PUID` and `PGID` with ids you got from `id` command.

See [docker documentation](https://docs.docker.com/compose/compose-file/) for explanations.