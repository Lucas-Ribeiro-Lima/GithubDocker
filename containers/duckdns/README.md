Here is the basic duckdns docker composed utilized

```yaml
---
version: "3"
services:
  duckdns:
    image: lscr.io/linuxserver/duckdns:latest
    container_name: duckdns
    environment:
      - PUID=1000 #optional
      - PGID=1000 #optional
      - TZ=America/Sao_Paulo
      - SUBDOMAINS={firstdomain,seconddomain,thirddomain}
      - TOKEN={yourtoken}
    volumes:
      - /GithubDocker/containers/duckdns:/config #optional
    network_mode: bridge
    restart: unless-stopped
```

Some configurations gonna be in hand, you gonna need to specify the domains do you wanna actualize and your token to authorize.

Everything you will need can be found on official duckdns site
> [DuckdDNS Official Site](https://www.duckdns.org/domains)

> Create a free acount and create some domains, then after configuring the correct domains and token all are set to start the container,

> **Some advice for multiple domains, dont use comma space to separate them**.

At that time the container going to auto-update your public ip to the domains specified on DDNS.
