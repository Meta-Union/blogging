# <ins>How I upgraded to 2 physical servers</ins>

## Preamble

I bought a new physical server I'm calling MetaUnion3 (MetaUnion1 existed at some point but was replaced by MetaUnion2 which is why there is only MetaUnion2 and MetaUnion3 right now) a few days ago and though I had everything set up so that it would be easy to migrate some services to that new server… I haven't underestimated anything this much in quite some time. It took me several days to figure things out, test a lot, learn a lot and just read through a lot of documentation and other threads.

It was a wild ride and I thought I'd share it here. Beware though, it gets very technical and it requires a lot of knowledge on those topics.

I'm heavily using Docker containers for almost anything, so whenever I talk about networks or containers I refer to docker networks and containers unless stated otherwise.

## IPv4 is still necessary…

When I bought the server I had the option to buy an additional IPv4 for my server but it costs an extra 2€/month and I thought since the entire server is only about 35€/month I could just go without it… another big mistake of mine that I quickly came to realize.

If you want to use Docker and public images you will need an IPv4, there's no going without it! A lot of docker domains don't even have AAAA DNS entry which makes IPv6 communication impossible.

Docker networking **might** work with only IPv6 but I never got it to work and because I couldn't even download images I knew I could just not go without it. _You could consider setting up a VPN between the two servers, which would also make the encryption mentioned in “Networking” unnecessary and make some things easier but it would also make the VPN server a single point of failure which I wanted to avoid as well so for now I'm going without it. Maybe in the future I'll consider it again but currently I won't._

So I bought the IPv4, configured it on MetaUnion3 and things just started working, small success!

## Networking

For communication between containers across physical servers you need something called an overlay network, which for most cases I had already set up as I knew it would be required at some point.

After setting up security things on the server I like to set up the monitoring service called Netdata (you can find that on https://netdata3.meta-union.com) and so I did - but quickly realized that for some reason I couldn't get my reverse web proxy to forward any traffic to the new Netdata container. I knew I would have to do some troubleshooting and quickly realized I couldn't even ping or netcat from one container to another, really strange.

Then I started changing some configuration regarding the network settings and at some point noticed that if I disabled the application data encryption it would work fine. I thought maybe that's some publicly known bug so I started investigating. I searched on Google for hours but to no avail.

Since I wanted to move the builders Minecraft server over to the MetaUnion3 quickly I thought I'd just disable the beforementioned application data encryption but it turned out doing that basically killed the whole Minecraft network which kind of made me panic. I had to restart all containers to make everything work again without the encryption which I didn't exactly plan on. At least I had everyone informed before so it wasn't a complete disaster.

Another break was had at some point and I thought that maybe the problem was caused because Docker handled swarm managers and workers differently and didn't allow them to communicate the same way so I wanted to change MetaUnion3 from a worker to a manager.

I promoted MetaUnion3 from worker to manager without any issues but things still didn't get any better so I thought a reboot might help, a really bad idea… a swarm apparently expects at least 50% of managers to be alive to declare a leader which now was no longer the case.

_I mentioned the extra IPv4 before… I hadn't set it up right and the reboot made the server lose the IPv4 again and I thought it was completely unavailable, it'll take me basically a day to notice this._

## A broken Docker swarm

So now I am stuck with a non working Docker swarm but also couldn't reach the new server and once again kind of panicked as the Minecraft builders server was now unavailable but people had already built new stuff on it! I set the new server into recovery mode which at least allowed me to copy files from it again so that the builders wouldn't lose all their progress they've made in the meantime. I copied everything back to MetaUnion2 - back to where I came from.

_I was able to make the swarm work again by re-initializing it at some point by the way._

## Another day

After a long night of recovering data and making the old server work again as it used to I made the call of getting a new, cheap server for testing purposes. Crashing the entire Minecraft network two times within such a short time was too much and I felt bad for all the users.

So I bought a new, temporary server for around 7€/month (which I'll later cancel when I'm done with testing and everything's running smoothly) for testing purposes. It must have also been around this time I noticed that the new server wasn't fully broken but all that was missing was the IPv4 address. So I fixed the IPv4 and started working with those 2 new servers (the temporary and MetaUnion3).

The problem with no communication persisted though, even when both servers were managers and I was at a loss… so I started searching on Google again. After about another hour I finally found a GitHub issue that described exactly the problem that I was having. It was closed and followed up with another issue which basically concluded it was an issue with NATing. Even though I'm not doing any external NATing which they blamed, I am claiming/guessing it's the internal Docker NAT (translating from the public IPv4 to the private one within the docker network). According to this GitHub issue the problem lies in recalculation of the checksum when NAT is done. I admit it wouldn't even make sense to have the encryption option if the docker internal NATing was an issue but I've given up hope on this problem.

Alright I thought, I'll find another solution for encryption as I also read about other networking drivers and also potentially considered a VPN solution. The problem with the alternative driver which also did encryption was that it was way too much trouble to administer and the VPN one once again had a single point of failure and required quite some extra work.

I settled with the default Docker overlay network, no application data encryption but at least it works easily and reliably! Sometimes you have to cut corners, especially when you're a single administrator.

## Reverse proxy issue

I've been using a solution like this until now: https://github.com/FunkLid/docker-compose-letsencrypt-nginx-proxy-companion

The problem with it is, it relies on noticing if a Docker container went up which only works on a single physical server and not across servers, so yet another issue I hadn't considered and only now realized.

It took me another round of digging on the Internet but found a really nice solution called https-portal: https://github.com/SteveLTN/https-portal

I know what it does on a higher level but no idea what's happening inside (I'm certain it's magic)… all that matters is that I give it super simple instructions and it works even across physical servers! Another problem solved quite quickly but I had to recreate some networks that made things take a little longer but I'll spare you the details ;-)

## Logging

Up until now I've been using Splunk for logging and just read the local files that I had mounted into the Splunk container, that obviously no longer worked.

“No problem!” I thought, I just create an overlay network and send container syslogs across it… but that just wouldn't work. I still don't know why exactly as ports were certainly open to the overlay network but I found something that worked. Setting up an extra universal forwarder on MetaUnion3 and making it forward the logs to MetaUnion2 worked, so I went with that! Some extra networking and static IP assigning thoughts and documentation had to be spent to make it scale easier later but those were also dealt with rather quickly.

## Syncing files

I had set up a somewhat clever way of syncing plugins across the Minecraft servers which basically made me do the following things:

Download plugin files from websites
Upload plugin files to MetaUnion2
Run script that distributes the new plugins onto the Minecraft servers

But as you can maybe already see, this won't scale once again. If we imagine I have 100 physical servers at some point, I'd then have to upload the same files to 100 servers and also run the script on those 100 servers, so this problem had to be dealt with in some smart way.

I knew of a solution that I could use already, something called Syncthing - open source, encrypts your data, is easy to use. So I started setting it up, made some mistakes as expected and tried some other stuff that didn't work. After a bit I got it to work as I wanted and files started syncing across both servers. Now all I'd have to do is make the script run every now and then so it would update the Minecraft servers at all times.

## Backups

That one was actually the easiest part, I'm using borg (https://borgbackup.readthedocs.io/en/stable/) for this and just had it set up the same as I had on the old server. It's a brilliant little tool that does things quickly, efficiently and safely - everything you could ever ask for!

## The final upgrade/change

I was finally ready to get the Minecraft builders server onto MetaUnion3 and so I did, just some final touches on the container so it would send logs to the forwarder and change some of its network settings and it was done.

Now I have a backup, I have logs and the Minecraft server can communicate with the proxy, yay!! :D

And to top it off, here's an overview of the current situation:

![image](https://github.com/user-attachments/assets/d8603918-a91a-424d-b77f-039d2113572d)

