## Personal VPN
> a docker [wireguard](https://www.wireguard.com/) vpn hosted on a digitalocean droplet for $5 a month

This serves as a setup guide to create your own VPN for personal use.

### Requirements

> please note due to a current(13-08-2020) issue the docker container does not work on older versions of Ubuntu.

1. A server with a public IP that you have access to. (For my case I created a digitalocean droplet). Remember the location of the server determines the location of [you](https://whatismyipaddress.com/).
2. This server needs a linux(AFAIK) OS installed. (I went with Ubuntu 20.04). Please see the docker image [docs](https://hub.docker.com/r/linuxserver/wireguard) for more detail as well on server limitations.
3. This server needs docker and docker-compose installed. Please use the docker [docs](https://docs.docker.com/engine/install/) for this.

### Setup

> Now the easy part

We will split this in two. Server and peer where server is the vpn server that you connect to and peer is the user device that you connect from.

#### Server
Once you have the server setup like above you can use the commands given on [docker hub](https://hub.docker.com/r/linuxserver/wireguard) or the below docker-compose file to setup your server. For timezone see [this](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones).

```docker
version: "3"
services:
  wireguard:
    image: linuxserver/wireguard
    container_name: wireguard
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=<your-timezone>
      - SERVERURL=<your-server-ip-address>
      - SERVERPORT=51820
      - PEERS=1 # change to add more PEERS
    volumes:
      - ./config:/config
      - /lib/modules:/lib/modules
    ports:
      - 51820:51820/udp
    restart: unless-stopped
```

1. Create a file called `docker-compose.yml` on the server.
2. Paste the above code and replace the relevant values.
3. Run `docker-compose up -d` and wait for the container to startup. It should take less than a minute.
4. Run `docker container logs wireguard` to confirm a successful startup. If you see a QR code it's probably all good.

#### Peers

> all the clients I mean peers or is it devices

The peer can be any device that is [supported](https://www.wireguard.com/install/) by wireguard and any device further than that which can run docker(this is within reason do not take this as an absolute please). I do not know how to configure this for all peers so please refer to the wireguard documentation for connection. I will give an example of Mac/Windows as these are similar.

1. Your first peer/peer config can be found on your server under `config/peer1/peer1.conf` and this should be the only file you need for connection.
2. From the [link](https://www.wireguard.com/install/) provided follow the instructions to download wireguard peer.
3. On mac you can then import your new tunnel from `peer1.conf` within the peer GUI.
4. On mobile devices you can use the QR code saved on your server at `config/peer1/peer1.png`

