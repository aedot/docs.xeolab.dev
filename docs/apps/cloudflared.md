# <b> Cloudflare</b>

### Create App Folder
First we need to make sure we have the app folder ready with the correct permissions. 

    mkdir -p /var/lib/docker/volumes/cloudflared/ && chmod -R 777 /var/lib/docker/volumes/cloudflared/

### Authorize Cloudflared

In terminal, run the following command to authorize Cloudflared with the Cloudflare site you want to set up with a tunnel.

    docker run -it --rm -v /var/lib/docker/volumes/cloudflared:/home/nonroot/.cloudflared/ cloudflare/cloudflared:latest tunnel login

??? info

    It will print out a link to Cloudflare. Put this link in your web browser, and select which domain you want to use. Then, the daemon will automatically pull the certificate.

### Create a tunnel

Now we need to create a tunnel. To do this, we will run another command from the terminal:

    docker run -it --rm -v /var/lib/docker/volumes/cloudflared:/home/nonroot/.cloudflared/ cloudflare/cloudflared:latest tunnel create TUNNELNAME

This will create your tunnel's UUID.json file, which contains a secret used to authenticate your tunnelled connection with Cloudflare. The JSON file is only needed for running the tunnel, but any tunnel modifications require the cert.pem.

Make sure you copy your UUID, as this will be used in later steps. It can always be found later by the name of the JSON file.

### Create the config.yml

Now we need to create a `config.yml`to configure the tunnel

    nano /var/lib/docker/volumes/cloudflared/config.yml

Now paste in the following and amend your reverse proxy IP:PORT, tunnel UUID and domain name if applicable

- if you have an ssl certificate on your reverse proxy, you need to pass in your domain name that the SSL cert is under
- if you want to proxy to an http server, use the commended ingress rule
- if you want to disable ssl verification, add noTLSVerify under originRequest

        tunnel: UUID
        credentials-file: /home/nonroot/.cloudflared/UUID.json

        # NOTE: You should only have one ingress tag, so if you uncomment one block comment the others

        # forward all traffic to Reverse Proxy w/ SSL
        ingress:
        - service: https://REVERSEPROXYIP:PORT
            originRequest:
            originServerName: yourdomain.com

        #forward all traffic to Reverse Proxy w/ SSL and no TLS Verify
        #ingress:
        #  - service: https://REVERSEPROXYIP:PORT
        #    originRequest:
        #      noTLSVerify: true

        # forward all traffic to reverse proxy over http
        #ingress:
        #  - service: http://REVERSEPROXYIP:PORT

### Install cloudflared using docker-compose

        version: "3.8"
        services:
        cloudflared:
            image: cloudflare/cloudflared:latest #update the verion where necessary
            container_name: cloudflared
            restart: unless-stopped
            networks:
            - proxy
            command: tunnel --config /home/nonroot/.cloudflared/config.yml run UUID #Replace UUID with your actual UUID
            volumes:
            - /opt/appdata/cloudflared/data:/home/nonroot/.cloudflared/
        networks:
        proxy:
            driver: bridge
            external: true

???+ warning

    The TUNNEL UUID is put into this file AFTER you followed the steps to set up the tunnel and it's files etc.
    It also assumes you are using a custom docker network named 'proxy'. Otherwise, update it to reflect your Docker network or remove it entirely if you don't wish to use it. 

### Setting up DNS records

The next step will be to edit your domain DNS records.

- If you have an A record already, you can remove this as it is now not needed.
- Replace your A record with a CNAME record, that points to the domain root (@) and for the content, you need to add UUID.cfargotunnel.com (inserting your UUID that was copied earlier).

!!! example

    | Type | Name | Value | TTL | Proxy status |
    |------|------|-------|-----|--------|
    | CNAME | @ | UUID.cfargotunnel.com | Automatic | ![](/assets/cloudflare_status.png){width="36"} **Proxied**
    | CNAME | sonarr | @ | Automatic | ![](/assets/cloudflare_status.png){width="36"} **Proxied**
    | CNAME | plex | @ | Automatic | ![](/assets/cloudflare_status.png){width="36"} **Proxied**
    | CNAME | radarr | @ | Automatic | ![](/assets/cloudflare_status.png){width="36"} **Proxied**
    | CNAME | portainer | @ | Automatic | ![](/assets/cloudflare_status.png){width="36"} **Proxied**


## Troubleshooting
### Certificate not valid for any names

if below error occur

    Unable to reach the origin service. The service may be down or it may not be responding to traffic from cloudflared: x509: certificate is not valid for any names, but wanted to match youdomain.com

In your `config.yml` try changing `yourdomain.com` to `app.yourdomain.com`, where `app` is a valid subdomain that you have a DNS record for (configured in both cloudflare and your reverse proxy). Despite this being a specific hostname, cloudflared should be able to use this subdomain to verify certificates for your other subdomains as they pass through the tunnel.

#### Example config.yml

    tunnel: UUID
    credentials-file: /home/nonroot/.cloudflared/UUID.json

    ingress:
    - service: https://192.168.1.20:18443
        originRequest:
        originServerName: app.yourdomain.com

### Cannot determine default configuration path

f you are receiving an error like the following, it could be due to the config file being named incorrectly or is stored in the wrong location.

    Cannot determine default configuration path. No file [config.yml config.yaml] in [~/.cloudflared ~/.cloudflare-warp ~/cloudflare-warp /etc/cloudflared /usr/local/etc/cloudflared]bash

or

    error="Unable to reach the origin service. The service may be down or it may not be responding to traffic from cloudflared: dial tcp 127.0.0.1:8080: connect: connection refused" cfRay=XXXXXXXXXXXX-NRT originService=http://localhost:8080

As you can see, the logs are stating that it cannot access the config.yml file and so it uses the default configuration and points to the origin server 127.0.0.1:8080.

Make sure that your config file is named `config.yml` and is stored in the root directory of the appdata `/var/lib/docker/volumes/cloudflared/`.

### General troubleshooting

If you have finished your Argo Tunnel installation and the configuration process, but are still getting error messages, please look for the solution in one of the following links:

[https://support.cloudflare.com/hc/en-us/articles/360029779472-Troubleshooting-Cloudflare-1XXX-errors](https://support.cloudflare.com/hc/en-us/articles/360029779472-Troubleshooting-Cloudflare-1XXX-errors)

[https://support.cloudflare.com/hc/en-us/categories/200276217-Troubleshooting](https://support.cloudflare.com/hc/en-us/categories/200276217-Troubleshooting)