# Let's Encrypt with nginx
This repository is the result of folling the guide: https://linuxhandbook.com/nginx-reverse-proxy-docker/

# Create Bridge Network
Create the bridge network that will be used for communication between the ngix reverse proxy and the proxied containers.
```
docker network create net
```

# Building Docker Image
```
docker-compose up -d
```

# Running another container behind the nginx proxy.
Replace sub.domain.com with the public FQDN of the HTTPS endpoint.
```
docker run --rm --name nginx-dummy -e VIRTUAL_HOST=sub.domain.com -e LETSENCRYPT_HOST=sub.domain.com -e VIRTUAL_PORT=80 --network net -d PROXIED_CONTAINER_NAME
```

## Proxied Container Notes
### Do not bind to any port
The container can leave out the port that serves the frontend. The reverse proxy container will automatically detect that.

### (OPTIONAL) Define VIRTUAL_PORT
If the reverse proxy container fails to detect the port, you can define another environment variable named VIRTUAL_PORT with the port serving the frontend or whichever service you want to get proxied, like "80" or "7765".

### Set Let's Encrypt email specific to a container
You can override the DEFAULT_EMAIL variable and set a specific email address for a specific container/web service's domain/subdomain certificate(s), by setting the email id to the environment variable LETSENCRYPT_EMAIL. This works on a per-container basis.
