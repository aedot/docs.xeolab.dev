#<b> Influxdb</b>

## Docker-Compose Template

Let's create the folder to add this compose file too, you might save your app's data in another location so just add your path to this command.

    mkdir influxdb && cd influxdb

Now let's create the docker-compose file with the `nano` text editor

    nano docker-compose.yml

### docker-compose.yml

    version: "3"

    services:

      influxdb:
        image: "influxdb:1.8-alpine"
        container_name: influxdb
        ports:
          - "2003:2003"
          - "8086:8086"
        environment:
          TZ: "America/Los_Angeles"
          INFLUXDB_ADMIN_USER: "admin"
          INFLUXDB_ADMIN_PASSWORD: "adminpassword"
        volumes:
          - "/var/lib/docker/volumes/influxdb:/var/lib/docker/volumes/influxdb"
        logging:
          driver: "json-file"
        networks:
          - backend
        restart: unless-stopped

    networks:
      backend:
        driver: bridge
        external: true

## Basic Command

- User management
    - Admin user
    ```bash
    CREATE USER admin WITH PASSWORD "<password>" WITH ALL PRIVILEGES
    ```
    - Create non-admin user
    ```bash
    CREATE USER <username> WITH PASSWORD "<password>"
    ```    
    - Grant admin privileges to an existing user
    ```bash
    GRANT ALL PRIVILEGES TO <username>
    ```
    - GRANT READ, WRITTE or ALL database privileges to an existing user
    ```bash
    GRANT [READ,WRITE,ALL] ON <database_name> TO <username>
    ```
    - REVOKE READ, WRITE, or ALL databases privileges from an existing user
    ```bash
    REVOKE [READ,WRITE,ALL] ON <database_name> FROM <username>
    ```
    - Revoke admin privileges from an admin user
    ```bash
    REVOKE ALL PRIVILEGES FROM <username>
    ```
    - Show all existing users & their admin status
    ```bash
    SHOW USERS
    ```
    - SHOW a user's database privileges
    ```bash
    SHOW CRANTS FOR <user_name>
    ```
    - RESET a user's password
    ```bash
    SET PASSWORD FOR <username> = "<password>"
    ```
    DROP a user
    ```bash
    DROP USER <username>
    ```
    - CREATE database
    ```bash
    CREATE DATABASE <database_name>
    ```