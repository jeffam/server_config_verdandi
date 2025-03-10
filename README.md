# verdandi server configuration

This is the Ansible playbook for the `verdandi` server, which primarily hosts the Uptime Kuma application.

The server is a small ec2 instance with the default user `ubuntu`. This user was created with the image and has sudo powers.

## Requirements

- Ansible
- A public ssh key added to the `ubuntu` user's `~/.ssh/authorized_keys` file.

## Usage

```
$ ansible-playbook main.yml
```

## Applications

All applications are run in containers. As of 2025-03, they are not started or stopped via Ansible, but are simply installed as docker compose projects in `/apps`.

To start an application:

```
$ ssh ubuntu@verdandi.ilr.cornell.edu
$ cd /apps/APPLICATION_NAME/  # E.g. /apps/whoami/
$ docker compose up -d
```

Or the shorter version:

```
$ ssh ubuntu@verdandi.ilr.cornell.edu "cd /apps/whoami/ && docker compose up -d"
```

Applications are generally configured to restart on server boot via `restart: unless-stopped` or similar.

### Caddy

Caddy is mainly used as a reverse proxy for containerized web apps on this server. This is done with the [caddy-docker-proxy](https://github.com/lucaslorentz/caddy-docker-proxy) module which uses container labels to dynamically configure Caddy. This is pretty similar to a feature in Traefik, although much simpler to configure here.

As of 2025-03, this playbook doesn't configure one requirement: the dedicated Docker network for Caddy and containers that it proxies. So this command was required before the containers could start:

```
docker network create caddy
```

### Uptime Kuma

https://uptime.ilr.cornell.edu/

This is a monitoring tool similar to Uptime Robot. It can be used to monitor web sites and report downtime to services like Slack and email.
