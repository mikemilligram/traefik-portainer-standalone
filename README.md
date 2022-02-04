# Standalone traefik-portainer

## Quick Start
Before deploying the stack, make sure the required volumes and networks exist by executing these commands on the manager node:

Next be sure to add the variables in the `docker-compose.yml` file into a .env file in the same directory.

    # .env
    DOMAIN=your-domain.com
    EMAIL=your@email.com

The following command spins up the containers makes traefik available for all your services to use:
    
    docker-compose up -d

Now you are good to go!

## Using other services with traefik
To make any service use traefik, add it to the `traefik-proxy` network and add the following to your service's configuration under the `labels` tag:

    - traefik.enable=true
    - traefik.docker.network=traefik-proxy
    - traefik.http.routers.my-service-https.entrypoints=http
    - traefik.http.routers.my-service-http.rule=Host(`service.your-domain.com`)
    - traefik.http.services.my-service.loadbalancer.server.port=your-port-here
      
Change `service.your-domain.com` to the FQDN you want your service to be accessible under.

`your-port-here` has to match the port your service is available at.

Additionally, if you want to use https, add:

    - traefik.http.routers.my-service-https.entrypoints=https
    - traefik.http.routers.my-service-https.rule=Host(`service.your-domain.com`)
    - traefik.http.routers.portainer-https.tls=true
    - traefik.http.routers.my-service-https.tls.certresolver=le
    - traefik.http.routers.my-service-http.entrypoints=https-redirect

If you need http authentication, add:

    - traefik.http.routers.my-service-http.middlewares=auth 

<u>OR</u>, if you are using https, add:

    - traefik.http.routers.my-service-https.middlewares=auth
      
