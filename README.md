# Docker powered reverse proxy using traefik

[![N|Traefik](https://img.shields.io/badge/Traefik-latest-orange.svg)](https://traefik.io/)

##### A reverse proxy thats dynamic, automatic with acme TLS support for my use with my home servers

## Prerequisites
- A computer with Docker and docker-compose installed my current versions at the time of writing this are
-- Ubuntu 18.04.4 LTS
-- Docker version 19.03.8, build afacb8b7f0
-- Docker-compose version 1.25.4-rc2, build 36a5d4d4

- For letsencrypt to work you will need to own your own domain ie: example.com, I am with godaddy and ive forwarded my domain to cloudflare to take advantage of [ddclient](https://github.com/linuxserver/docker-ddclient) and [flare-dns](https://github.com/code5-lab/dns-flare)
    - Please note from this point forward it is assumed your domain is controlled using cloudflare, its fast, free & feature rich so why not

- You will want to change your IP address for your ubuntu host to a static IP, I am not going to go over how to do this, but you should add a dhcp reservation for the IP address of your choice in your router aswell

- In your router settings you will need to forward the following ports to your newly assigned static ip address from your ubuntu host
    ```sh
    HTTP port:80
    HTTPS port:443
    ```
- Confirm if openssh-server is installed & enabled on your ubuntu host by going to the terminal and typing the following
    ```sh 
    sudo systemctl status sshd
    ```
    - If memory serves ssh should be installed and enabled by default I could be wrong its been a long time
    - The account running the sudo command obviously will need sudo privelages

### Getting Started
##### You will need to ssh to your ubuntu host to run the following commands
- Clone this repository to the ubuntu host
    ```sh
    git clone https://github.com/shanehughes1990/docker-traefik-proxy.git ~/docker-traefik-proxy && cd ~/docker-traefik-proxy
    ```

- Make a copy of sample.env in the repository directory
    ```sh
    cp sample.env .env
    ```
- Change the environment variables in .env to fit your needs
    ```sh
    nano .env
    ```
    - Once your done changing the variables press ctrl+o enter ctrl+x to save and exit file (this is for nano text editor)

- You will need to to make sure there are no services running on that host from port 80 or 443 as they interfier with the traefik container run the following command and look for those ports being listed if they are you need to change the ports those services are running on
    ```sh
    sudo netstat -tulpn | grep LISTEN
    ```
- This should be everything you need to get started all thats left is to run the following command, make sure you are in the repository directory before running command
    ```sh
    cd ~/docker-traefik-proxy
    docker-compose up -d
    ```
