# Docker: nginx Load Balancing Reverse Proxy with Let's Encrypt

This repository is the result of following the guide: https://linuxhandbook.com/nginx-reverse-proxy-docker/ See the guide for a more detailed explanation of the docker-compose.yml file.

The docker-compose.yml file creates two docker containers, nginx and letsencrypt. A  virtual network, for communication between the proxied containers and nginx, must be created manually. Proxied containers can be added to the virtual network by specifying their connection options in the 'docker run' command. 


# Deployment
## Create Docker Bridge Network
Create the bridge network that will be used for communication between the nginx reverse proxy and the proxied containers.
```
docker network create net
```

## Build and Run Docker Image
- The nginx container must be publicly accessible at sub.domain.com for let's encrypt to function.
```
docker-compose up -d
```
## Spawning Proxied Containers
| Option                 | Description                            |
|------------------------|----------------------------------------|
| sub.domain.com         | The public FQDN of the HTTPS endpoint. |
| proxied_image_name     | The name of the image you're running.  |
| proxied_container_name | The name of the spawned container.     |
Subtitute the options listed in the above table before running the command below.
```
docker run --rm --name proxied_container_name -e VIRTUAL_HOST=sub.domain.com -e LETSENCRYPT_HOST=sub.domain.com -e VIRTUAL_PORT=80 --network net -d proxied_image_name
```

## Container Options
### Do not bind to any port
- The container can leave out the port that serves the frontend. The reverse proxy container will automatically detect that.

### (OPTIONAL) Define VIRTUAL_PORT
- If the reverse proxy container fails to detect the port, you can define another environment variable named VIRTUAL_PORT with the port serving the frontend or whichever service you want to get proxied, like "80" or "7765".

### Container Specific Let's Encrypt email
- You can override the DEFAULT_EMAIL variable and set a specific email address for a specific container/web service's domain/subdomain certificate(s), by setting the email id to the environment variable LETSENCRYPT_EMAIL. This works on a per-container basis.
