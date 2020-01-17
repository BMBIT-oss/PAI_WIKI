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
You have already installed Docker

Create required directories, configure and run PAI.

```
mkdir ~/docker/pai/config ~/docker/pai/log

wget https://raw.githubusercontent.com/ParadoxAlarmInterface/pai/master/config/pai.conf.example -O ~/docker/pai/config/pai.conf
```

Create config file in `~/docker/pai/config/pai.conf`
Edit as described in [Configuration](./Configuration)

```bash
docker run -it \
--name pai \
-v /etc/timezone:/etc/timezone:ro \
-v /etc/localtime:/etc/localtime:ro \
-v ~/docker/pai/config:/etc/pai:ro \
-v ~/docker/pai/log:/var/log/pai \
--user $(id -u):$(id -g) \
--device=/dev/tty.YOUR_SERIAL_PORT \
-P \
paradoxalarminterface/pai:latest
```

`--device` is only required if you use Serial connection.

See [docker documentation](https://docs.docker.com/engine/reference/run/) for explanations.

### Stop
```bash
docker stop pai
```

### Updating
```bash
docker stop pai
docker pull paradoxalarminterface/pai:latest
```

Then run as usual.

## Composer
You have already installed Docker and Docker Composer.

**All commands should be run as a regular user (non-root).**

Create folders in your home directory.
```bash
mkdir -p ~/docker/pai/config ~/docker/pai/log
id
> uid=1000(pi) gid=1000(pi)
```

Create config file in `~/docker/pai/config/pai.conf`
Edit as described in [Configuration](./Configuration)

Create file `~/docker/docker-compose.yml`
```yaml
version: '3'

services:
  pai:
    container_name: pai
    restart: unless-stopped
    image: paradoxalarminterface/pai:latest
    volumes:
      - "~/docker/pai/config:/etc/pai:ro"
      - "~/docker/pai/log:/var/log/pai:rw"
      - "/etc/timezone:/etc/timezone:ro"
      - "/etc/localtime:/etc/localtime:ro"
    user: "1000:1000"
    devices:
      - "/dev/tty.YOUR_SERIAL_PORT"
    ports:
      - "10000:10000"
```

Populate `PUID` and `PGID` with ids you got from `id` command.

`devices:` section is only required if you use Serial port to connect to the Panel.

`ports:` section and port `10000` is required if you want to connect to Panel with Babyware through PAI (PAI will proxy requests. **IP Interface**).

### Running && Stopping
```bash
cd ~/docker
# Run
docker-compose up -d pai
# Stop
docker-compose stop pai
```
### Updating
Change version in `services->pai->image` if you did not used `latest` or `dev-latest`
```bash
cd ~/docker
docker-compose stop pai
docker-compose pull pai
docker-compose up -d pai
```

See [docker documentation](https://docs.docker.com/compose/compose-file/) for explanations.

### Q&A
> MQTT does not connect.

Make sure you specify `MQTT_HOST` properly. Host's IP address or docker interface IP might be required there.