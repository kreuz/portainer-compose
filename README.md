# Portainer compose setup

A simple setup to deploy Portainer with custom templates and HTTP authentication.

This setup also comes with [watchtower](https://hub.docker.com/r/centurylink/watchtower/) to automatically upgrade Portainer version :)

Default credentials are: `demo`/`demo`.

# Requirements

1. Install [Docker](http://docker.io).
2. Install [Docker-compose](http://docs.docker.com/compose/install/).
3. Clone this repository

# Usage

The default configuration will connect Portainer against the local Docker host.

Run it:

```
$ docker-compose up -d
```

And then access Portainer by hitting [http://localhost](http://localhost) with a web browser.

# Configuration

## How can I specify which Docker host I want to manage?

You'll need to pass the IP/hostname of your Docker host to the portainer binary.

Update the `command` field of the **portainer** service in the `docker-compose.yml` file:

```yml
portainer:
  image: portainer/portainer
  container_name: "portainer-app"
  command: --templates http://templates/templates.json -d /data -H tcp://<DOCKER_HOST>:<DOCKER_PORT>
  networks:
    - local
```

## How can I change the default credentials?

Use the following command to generate new credentials:

```shell
$ docker run --rm httpd htpasswd -nbB <USERNAME> <PASSWORD>
```

Then replace the content of the file `nginx/config/auth.htpasswd` and rebuild/restart the stack:

```shell
$ docker-compose build
$ docker-compose down && docker-compose up -d
```

## How can I specify my own templates?

Create the file `templates/templates.json` and insert your template definitions in it.

For more information about the template definition format, see: https://github.com/portainer/templates

Then, bind mount the file for the **templates** service in the `docker-compose.yml` file:

```yml
templates:
  image: portainer/templates
  container_name: "portainer-templates"
  networks:
    - local
  volumes:
    - ./templates:/usr/share/nginx/html
```
