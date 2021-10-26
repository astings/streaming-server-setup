# How to setup a home media server using OMV, Jellyfin, and Servarr services on a RPI4


### Step 0: Setting up the Raspberry Pi

Set up your rasspberry as you would like, but I advise setting up ssh for easier installation, and setting up an ssh key and disabling password login for maximum security. Doing so will allow you to safely open port 22 on your internet box, in order to access the rpi from anywhere safely. I also recommend to set up ethernet connection (if not by default), ensure this is your main connection, and do a full update before starting.


### Step 1: Installing Open Media Vault (OMV) and setting up the main disk

OMV provides disk management, and easy installation of Docker and Portainer. There are a lot of resources online, I won't cover full installation here. The main steps to look for are :
 1. Installation of OMV
 2. Setting up a new disk
 3. Setting up Docker and Portainer

### Step 2: Setting up local server

Using portainer, run a stack with the attached docker compose file. Here is a list of the services deployed and their uses.

**NordVPN**: basic VPN protection when torrenting

**Transmission**: lightweight torrent client

**Jellyfin**: Stream server, one of 3 commonly found client. The other 2 are Plex and Emby, both of which have paid subscriptioN. Jellyfin is the only one totally free

**Servarr services**: Radarr (movies), Sonarr (TV), Bazarr (subtitles) all manages your library. They are connected to your torrent client, to a movie database (The Movie Database by default) and your torrent indexer if you have one. They handle finding the torrents that match your criterias, lauching torrent download, cleaning the files names, moving the files in a pattern recognized by jellyfin and even automatically downloading the subtitles you need. Configuration is very easy but there are some errors you should watch for (detailed later).

**Optional**

**Jackett**: Jacket is a torrent indexer, meaning it will be used by your Servarr services to get a list of torrents for a request. It handles thousands of torrent providers, private and public, and they are categorised. Very practical.

**Caddy**: Caddy is the reverse proxy of this setup. If you do not need access from the outside (only local network usage), it is not necessary to use it. It will be used to serve the right services to the right URLs, and handles all the complicated stuffs such as HTTPS Certificates.

**Ombi**: Ombi handles user requests. Basically, users can log into Ombi, and ask for specific movies that are missing in your libraries. It can notify you (the admin), but it can also automatically add the movies to Servarrs for download, and it does user management. Pretty useful if you have a lot of people using your server


##### Useful links for the setup

**Docker volumes** A lot of attention should be paid to the files hierarchy and volumes declaration in docker compose. A very comprehensive guide can be found [here](https://trash-guides.info/Hardlinks/How-to-setup-for/Docker/), and you can reuse my own paths.

**Linux server** As you can see, a lot of my docker images come from the team at Linuxserver.io. They do an amazing job at delivering very easy to use images, and maintain them very often.


### Step 3: Setting up remote access

Remote access consists in 3 steps:
- Buying a domain name
- Add it to Cloudflare for proxiing your services and hiding your ip (beware: do not proxy jellyfin as the high amount of packets will likely get you banned from cloudflare)
- Add the A* adresses to point subdomains you want to use (ex: subservice.hostadress.xxx) to your public IP adress
- Set Caddyfile to point each subdomain to the right local ip and port combination
- Setup loggin on all exposed services
- In jellyfin, in dashboard -> advanced -> networking, allow remote connections with the right ports
- (Optional) setup Fail2Ban to protect your Setup
- Open up port 80 and 443 on your internet Box
