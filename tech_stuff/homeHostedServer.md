# <ins>Making a home-hosted server available through a public-hosted server</ins>

## The problem

Once again starting by explaining the problem… you have a server at home but you don't want to port forward to it and perhaps because you have a dynamic IP you also don't want to set up something like DynDNS to somehow connect to it through the internet but you do have a public server that you are renting from a provider somewhere.

I've had WireGuard set up as my VPN software already and thought… how could I possibly make this server at my home available through the power of technology without having to set up a lot of other stuff that I have no control over (DynDNS) and also sacrificing security (by port forwarding)?

The problem was clear, the goal was set so I got to work.

## A high level overview

As always, I want to first demonstrate what the solution should look like in the end because I already had a good idea of what it would be like in the end, just didn't know how exactly I would get there.
![image](https://github.com/user-attachments/assets/9aa26881-6c91-4001-aa72-1aed395ccef3)


From here it hopefully becomes somewhat clearer what the actual goal was/is. I have wireguard1 as a server that all other nodes connect to, I have hooked up the homeServer1 by installing wireguard on it and connecting with something like “wg-quick up <myconfig\>.conf”, there is going to be a projectzomboid1 server running in a container on homeServer1 and portforwarder1 will forward all traffic coming to udp port 16261 through the vpn to projectzomboid1.

### Some other things that I considered that did not work

As some might know I have already used the swarm ability of Docker but unfortunately in this use case that can't be done as homeServer1 which is behind a NAT firewall just won't communicate properly with publicServer1 and thus they never form a swarm that I could use. I've tried several things but nothing satisfied (especially my security) demands so I considered this approach.

## The details

If this is your first time setting something like this up, here is the docker-compose.yml you'd probably want to do this:

```
version: "2.1"
services:
  wireguard1:
    image: ghcr.io/linuxserver/wireguard:latest
    container_name: wireguard1
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Berlin
      - SERVERURL=mydomain.com #change this
      - SERVERPORT=51820
      - PEERS=homeServer1
      - ALLOWEDIPS=10.0.0.0/8
    volumes:
      - ./config:/config
      - ./lib/modules:/lib/modules
    ports:
      - 51820:51820/udp
      - 16261:16261/udp
    sysctls:
      - net.ipv4.conf.all.src_valid_mark=1
    restart: unless-stopped
  portforward1:
    image: codejamninja/tcp-udp-proxy
    restart: unless-stopped
    container_name: portforward1
    depends_on:
      - vpn1-wireguard1
    network_mode: "service:wireguard1"
    environment:
      - LISTEN=16261
      - PROTOCOL=udp
      - UPSTREAM=<homeServerWireguardIp>:16261

# It's certainly possible you need one network here to have the two connected, sorry if I missed that.
```

But what does all of this even do? After bringing up the two services wireguard1 and portforward1 you should pretty much already have what you need on your publicServer1. Locate the homeServer1.conf file in the ./config folder and copy it somewhere on homeServer1 and install this (with "wg-quick up ./homeServer1.conf")

The portforward1 is using the network interface of wireguard1 (thanks to the network_mode parameter) and thus should share the IP 10.13.13.1 with wireguard1 which means that now when someone were to send a packet to the public IP of publicServer1 on port 16261 it would be processed by portforward1 and be forwarded to homeServerWireguardIp (you gotta check in the ./homeServer1.conf which one that is) on udp port 16261 which is exactly what we wanted.

Now all that is left to do is set up the docker container for projectzomboid1 on homeServer1 and make sure that you have the following lines in your docker-compose.yml on homeServer1:

```
service:
  projectzomboid1:
    [...]
    ports:
      - 16261:16261/udp
    [...]
```

And you should be good to go and able to access your projectzomboid1 through the public IP of your publicServer1.

This can obviously be used for any server (just beware of using double tcp connections as that could lead to drastic slow downs) to achieve this goal.
