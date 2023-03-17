# Containered micro-services
**My docker containers configurations**

This is a reposity to my personal use container's and configuration files, to expose my personal project of deploying and configurating a personal ubuntu server with containered micro services with free licenses, these services can be potentially escalated to a enterprise use.

All services are deployed with free licenses without cost's

**Actually my project contain**

1. Ubuntu Server LTS instalattion and configuration
2. Basic linux management
3. Installation of docker
4. Network management

**In my docker i have that services and integrations:**

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

git-project  // Where we going to store all volumes

containers-folders // Here we going to create one folder to integrated micro-services

app data config // Some containers have dependencies, like databases, internal-proxies, data storage and here we going to divide that to facilitate manutention of configs

**Keep in mind, rename this foldes need's changes in all docker-composes.yml files to indicate the new correct path**
