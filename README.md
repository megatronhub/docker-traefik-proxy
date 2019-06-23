# Docker powered reverse proxy using traefik

[![N|Traefik](https://img.shields.io/badge/Traefik-latest-orange.svg)](https://traefik.io/)

##### A reverse proxy thats dynamic, automatic with acme TLS support for my use with my home servers

## Prerequisites
- A computer with [openmediavault](https://www.openmediavault.org/) installed onto its own harddrive, preferrably a fresh install of omv (I do not care much if boot drive fails as I don't mind if there would be downtime to reinstall os as I have backups for omv config just install os and restore, if zero downtime is a necessity you likely dont need to use my repo.)
    - It is possible to use one harddrive/ssd for omv & the download location, omv by default does not allow you through the web panel create a filesystem on the operating system partition, for this to work you have to run a rescue instance ie: Gparted live to shrink the omv os partition and create a seperate partition for your download/docker filesystem (this is how I started I eventually bought a seperate smaller ssd for boot drive, just google a how-to if this is the route you choose)

- [Docker](https://docs.docker.com/install/linux/docker-ce/debian/) & [docker-compose](https://linuxize.com/post/how-to-install-and-use-docker-compose-on-debian-9/) installed on the machine running openmediavault

- Setup and mount your raid array and filesystems (obviously), again google an omv raid tutorial
    - To share some insite I have one ssd BOOT drive for omv itself, another larger ssd for docker install & download location (i also have my home dir mounted to a location on this ssd, I wanted to keep my omv boot drive as clean as possible) and a RAID 6 ext4 filesystem for the media content to be stored on, it currently consists of 5 4TB WD RED's, gives me 12TB storage plus double parity for 2 disk failure redundancy (FYI they are dirt cheap on amazon $129CAD-$149CAD (at the time of writing this) with prime shipping https://www.amazon.ca/gp/product/B00EHBERSE/ref=ppx_yo_dt_b_asin_title_o01_s01?ie=UTF8&psc=1) 

- For letsencrypt to work you will need to own your own domain ie: example.com, I am with godaddy and ive forwarded my domain to cloudflare to take advantage of ddclient
    - Please note from this point forward it is assumed your domain is controlled using cloudflare, its fast, free & feature rich so why not

- You will want to change your IP address for your omv host to a static IP, I am not going to go over how to do this, but you should add a dhcp reservation for the IP address of your choice in your router aswell

- In your router settings you will need to forward ports TCP 80, TCP 443 to your newly assigned static ip address from your omv host

- Confirm if openssh-server is installed & enabled on your omv host by going to your omv webpanel Diagnostics/Dashboard there will be a services box with SSH service and 2 green dots for enabled/running
    - If memory serves ssh should be installed and enabled by default I could be wrong its been a long time
    - If you do not see both green dots then go to Services/SSH and enable and configure ssh settings

### Getting Started
##### You will need to ssh to your omv host to run the following commands
- Clone this repository to the omv host
    ```sh
    $ git clone https://github.com/shanehughes1990/docker-traefik-proxy.git ~/docker-traefik-proxy && cd ~/docker-traefik-proxy
    ```

- Make a copy of sample.env in the repository directory
    ```sh
    $ cp sample.env .env
    ```
- Change the environment variables in .env to fit your needs
    ```sh
    $ nano .env
    ```
    - Once your done changing the variables press ctrl+o enter ctrl+x to save and exit file

- Make a copy of sample.traefik.toml in the repository directory
    ```sh
    $ cp sample.traefik.toml traefik.toml
    ```
- Change the environment variables in traefik.toml to fit your needs
    ```sh
    $ nano traefik.toml
    ```
    - Once your done changing the variables press ctrl+o enter ctrl+x to save and exit file
- You will need to change to port the omv runs from as port 80 will be occupied by your reverse proxy
    - Open your omv web panel and go to System/General Settings and change port from 80 to something else I am using 8443, Press save and a reload notification will popup, press that aswell, from the time being you can access your omv instance at http://hostip:8443, we will be routing this through our reverse proxy later

- This should be everything you need to get started all thats left is to run command
    ```sh
    $ docker-compose up -d
    ```
