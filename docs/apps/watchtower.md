#<b> Watchtower</b>

## Docker-Compose Template

Let's create the folder to add this compose file too, you might save your app's data in another location so just add your path to this command.

    mkdir watchtower && cd watchtower

Now let's create the docker-compose file with the `nano` text editor

    nano docker-compose.yml

### docker-compose.yml

    version: '3'

    services:
    watchtower:
        container_name: watchtower
        image: containrrr/watchtower
        restart: always
        volumes:
        - /var/run/docker.sock:/var/run/docker.sock
        environment:
        - WATCHTOWER_NOTIFICATIONS=email
        - WATCHTOWER_NOTIFICATION_EMAIL_FROM=${EMAIL_FROM}
        - WATCHTOWER_NOTIFICATION_EMAIL_TO=${WATCHTOWER_EMAIL_TO}
        - WATCHTOWER_NOTIFICATION_EMAIL_SERVER=smtp.gmail.com
        - WATCHTOWER_NOTIFICATION_EMAIL_SERVER_PORT=587
        - WATCHTOWER_NOTIFICATION_EMAIL_SERVER_USER=${SMTP_USER}
        - WATCHTOWER_NOTIFICATION_EMAIL_SERVER_PASSWORD=${SMTP_PASSWORD}
        - WATCHTOWER_NOTIFICATION_EMAIL_DELAY=2
        - WATCHTOWER_CLEANUP=true
        - WATCHTOWER_LABEL_ENABLE=true
        - WATCHTOWER_INCLUDE_RESTARTING=true
        - TZ=${TZ}
        labels:
        - "com.centurylinklabs.watchtower.enable=true"
        networks:
        - ${DOCKER_NETWORK}
        command: --interval 687

    networks:
    ${DOCKEER_NETWORK}:
        driver: bridge
        external: true

### Excluding containers

Sometimes you might have some containers that you don’t want to be updated automatically. Even though you control the behavior to a certain degree with the right version label some systems should just not automatically change. In this case I do not want HomeAssistant to randomly restart as it controls my smart home.
Watchtower supports two ways of implementing this. You can exclude all containers and specifically mark some for updates via the LABEL
`com.centurylinklabs.watchtower.enable: true` label and by setting the environment variable `WATCHTOWER_LABEL_ENABLE`.
By default Watchtower updates all containers instead and you can label those you do not want to have updated with `“com.centurylinklabs.watchtower.enable: false"`

### Cleanup

Watchtower will always pull the newest applicable images for your containers and then start new containers based on those updated images. This means that over time you will collect a huge amount of unused images. Of course you can clean those manually but the goal here is to automate the work. Fortunately we can add the `WATCHTOWER_CLEANUP=true` variable to watchtower to make it clean up after itself by deleting old unused images.
