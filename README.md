# dokku-portainer

Credits : https://gist.github.com/woudsma/03c69260715327ee8453f73b121f416c

This project contains Portainer that will be deployed as an app on a Dokku host.

## Run [Portainer](https://github.com/portainer/portainer) on Dokku host
Running Portainer as a container management tool for other Dokku hosts.  

### 1. Create portainer app

```sh
# Create app
dokku apps:create portainer

# Create persistent storage folder
sudo mkdir -p /var/lib/dokku/data/storage/portainer

```
Edit app docker-options:  
```sh
dokku docker-options:add portainer deploy,run "-v /var/lib/dokku/data/storage/portainer:/data -v /var/run/docker.sock:/var/run/docker.sock"
```

### 2. Deploy portainer app

```sh
dokku git:sync --build portainer https://github.com/davmrtl/dokku-portainer.git main
```

### 3. Configure networking on portainer app

Map ports and add SSL using [dokku-letsencrypt](https://github.com/dokku/dokku-letsencrypt):
```sh
# Map ports
dokku ps:stop portainer
dokku config:set portainer DOKKU_PROXY_PORT_MAP="http:80:9000"

# Add SSL
dokku config:set --no-restart portainer DOKKU_LETSENCRYPT_EMAIL=your@email.tld
dokku letsencrypt portainer

# Check if ports are mapped correctly
dokku config:get portainer DOKKU_PROXY_PORT_MAP

# Should output: "http:80:9000 https:443:9000"
```

To re-deploy Portainer, first stop the Portainer container (it's using the Docker socket)

```sh
dokku ps:stop portainer
dokku git:sync --build portainer https://github.com/davmrtl/dokku-portainer.git main
```
