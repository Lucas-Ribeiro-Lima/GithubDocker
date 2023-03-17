# Containered micro-services
**Docker containers configurations**

This is a reposity to my personal use container's and configuration files, to expose my personal project of deploying and configurating a personal ubuntu server with containered micro services with free licenses, these services can be potentially escalated to a enterprise use.

All services are deployed with free licenses without cost's

**Project**

1. Ubuntu Server LTS instalattion and configuration
2. Basic linux management
3. Installation of docker
4. Network management

**Services and Integrations:**

1. Portainer
2. Wake on Lan
3. Private VPN - Wireguard
4. Free DNS Server solution - Pi hole
5. Free web page to redirect my ip's easily - Heimdall
6. My own cloud service - Nextcloud
8. Onlyoffice services integrated with Nextcloud
9. Reverse proxy for management of public internet acess and management of SSL - Nginx Proxy Manager
10. Auto duckdns external ip and ipv6 uptade of domains - Duckdns
11. Backup service with disaster control - Duplicati

**Directory Structure**

1. GithubDocker  // Where we going to store all volumes

   - containers-folders // Here we going to create one folder to integrated micro-services
   
      - app data config // Some containers have dependencies, like databases, internal-proxies, data storage and here we going to divide that to facilitate manutention of configs. Here i'm going to store the docker-compose.yml o each service that can be used to deploy without the portainer management stack.

**Keep in mind, rename this foldes need's changes in all docker-composes.yml files to indicate the new correct path**

**Docker-compose.yml Services**

[Portainer](/containers/portainer)

https://github.com/Thyfas/GithubDocker/blob/12fc961a20590737c9a1d5e07b6ecc7091ad63e4/containers/portainer/docker-compose.yml#L1-L12

[Duckdns](/containers/duckdns)

[Heimdall](/containers/heimdall)

[Onlyoffice](/containers/onlyoffice)

[Wireguard](/containers/wireguard)

[Nextcloud](/containers/nextcloud)

[Pi-hole](/containers/pihole)

[Nginx Proxy Manager](/containers/proxy)

[Wake on Lan](/containers/wol)

[Duplicati](/containers/duplicati)
