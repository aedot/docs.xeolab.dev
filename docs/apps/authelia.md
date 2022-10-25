#<b> Authelia</b>

## Docker-Compose Template

Let's create the folder to add this compose file too, you might save your app's data in another location so just add your path to this command.

    mkdir authelia && cd authelia

Now let's create the docker-compose file with the `nano` text editor

    nano docker-compose.yml

### docker-compose.yml

    version: '3'
    services:
    auth:
        container_name: auth    
        image: authelia/authelia:latest
        ports:
        - 9091:9091
        volumes:
        - /var/lib/docker/volumes/authelia:/config
        labels:
        traefik.enable: true
        traefik.http.routers.authelia.entryPoints: https
        networks:
        - proxy
        restart: unless-stopped

    networks:
    proxy:
        driver: bridge
        external: true

## Configuration

### Configuration File
In your `/var/lib/docker/volumes/authelia` folder you will find `configuration.yml`. 

File **must** be edit to suit environment

### Creating Users

There are two options when deciding how you want users to exist for Authelia. 

??? tip inline end

    Use option is there are less than 10 user, as used in this docs

1. Using a simple YML file with the user's encrypted credentials that Authelia can read.
2. Allow Authelia to read from an LDAP database such as FreeIPA or Active Directory.

### users_database.yml

    users:
    john:
        displayname: "John Doe"
        password: "$argon2id$v=19$m=65536,t=3,p=2$BpLnfgDsc2WD8F2q$o/vzA4myCqZZ36bUGsDY//8mKUYNZZaR0t4MFFSs+iM"
        email: john.doe@authelia.com
        groups:
        - admins
        - dev

Option 1 - Using a Users Database File

- Copy the file content into /var/lib/docker/volumes/authelia/users_database.yml. 
- Adjust the file to the user you would like to sign in as.
    - Changes include the username and display name, for example.
- To generate the hashed password, open the terminal
    - Type in the following (replacing 'yourpassword' with the password you want for the user):

            docker run --rm authelia/authelia:latest authelia hash-password 'yourpassword'

- Copy the hashed password that is generated and paste it into the users_database.yml file as replacing the one in the template we provide.

!!! warning

    Remember to comment out the 'ldap' section in your `configuration.yml` since you are not using it.

#### Starting Up
At this point, you should start the Authelia container and read the logs. 

Test that you can reach the WebUI of Authelia {++(http://SERVERIP:9091)++} and can log in or set up 2FA
