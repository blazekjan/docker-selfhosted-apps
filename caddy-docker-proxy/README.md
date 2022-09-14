![enter image description here](https://raw.githubusercontent.com/docker-library/docs/175a99d9d009afb887a921e35bfa892a01d7be77/caddy/logo.png)

# Introduction

Caddy is a powerful, enterprise-ready, **open source web server** with **automatic HTTPS** written in Go. **caddy-docker-proxy** is a plugin that enables Caddy to be used as a reverse proxy for Docker containers via **labels**.

# How does it work?

The plugin **scans Docker metadata, looking for labels** indicating that the service or container should be served by Caddy. Then, it generates an **in-memory Caddyfile** with site entries and proxies pointing to each Docker service by their DNS name or container IP.  
Every time a Docker object changes, the plugin updates the Caddyfile and triggers Caddy to gracefully reload, with **zero-downtime**.

## Docker compose 

**WARNING!** I am using latest **Compose v2** (currently version **2.11.0**) and **latest docker compose reference**, which is a **major overhaul**! Be careful when following my instructions! In case of trouble you should visit this site: [Compose specification | Docker Documentation](https://docs.docker.com/compose/compose-file/) and look what is new and / or changed.

Create a **new network**, name it for example **caddy**:

    $ docker network create caddy

Copy .env file, **compose.yaml** config and bring it up with:
	
    $ docker compose up -d
