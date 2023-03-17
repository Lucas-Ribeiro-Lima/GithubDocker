#PORTAINER-CE

That is the portainer service, he is a web interface to manage yours container's.

Althought he is not mandatory, he facilitate the visualization of informations, logs, managing resources and costumizing YAML files.

The official website: https://www.portainer.io/

Navigate to the folder and use the following command:

docker-compose up -b

```yaml
---
version: "3"
services:
   app:
     image: 'portainer/portainer-ce:latest'
     restart: unless-stopped
     container_name: portainergit
     ports:
        - 8000:8000
        - 9443:9443
     volumes:
        - /var/run/docker.sock:/var/run/docker.sock
        - /GithubDocker/containers/portainer:/data portainer
```
He doenst need further configurations, but be sure of the <- /var/run/docker.sock:/var/run/docker.sock> volume, he is responsible to make the local enviromment works correctly.

After that the webUI will be acessible on the following URL:<localhost:9443>.
