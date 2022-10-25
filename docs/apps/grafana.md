#<b> Grafana</b>

## Docker-Compose Template

Let's create the folder to add this compose file too, you might save your app's data in another location so just add your path to this command.

    mkdir grafana && cd grafana

Now let's create the docker-compose file with the `nano` text editor

    nano docker-compose.yml

### docker-compose.yml

    version: "3"

    services:
        grafana:
        image: "grafana/grafana:latest"
        container_name: grafana 
        ports:
        - "3000:3000"
        environment:
        TZ: "America/Los_Angeles"
        GF_INSTALL_PLUGINS: "grafana-clock-panel,grafana-simple-json-datasource,grafana-piechart-panel,grafana-worldmap-panel"
        volumes:
        - "/var/lib/docker/volumes/grafana:/var/lib/docker/volumes/grafana"
        labels:
        - "com.centurylinklabs.watchtower.enable: true"
        logging:
        driver: "json-file"
        networks:
        - backend
        restart: unless-stopped

    networks:
    backend:
        driver: bridge
        external: true

