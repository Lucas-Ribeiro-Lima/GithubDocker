```yaml
---
version: "3"
services:
  duckdns:
    image: lscr.io/linuxserver/duckdns:latest
    container_name: duckdnsgit
    environment:
      - PUID=1000 #optional
      - PGID=1000 #optional
      - TZ=America/Sao_Paulo
      - SUBDOMAINS=
      - TOKEN=
    volumes:
      - /GithubDocker/containers/duckdns:/config #optional
    network_mode: bridge
    restart: unless-stopped
```