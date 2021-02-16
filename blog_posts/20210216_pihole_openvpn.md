# Personal VPN server on AWS with ad-blocking

- VPN server: OpenVPN
- Ad-blocking: Pi-hole

## Why I did this project
Monitoring my own internet footprint is a cheap way of generating my own real dataset to play with. Plus it's a running service, and if interested there's room for ex. Kibana monitoring, anomaly detection for myself, and stuff like that. Adding a pi-hole adds immediate benefit and if the server is down the cost is getting to see ads. Like I can feel the pain, but not too much. Also I was curious about how much information a VPN server can actually see.

## How I did this
Started with this [medium article](https://medium.com/fillory/openvpn-pihole-ad-blocking-on-aws-lightsail-for-3-50-mo-7e814eafff84). Got stuck at pi-hole installation with error message "dns resolution is currently unavailable". Googled around and this solution worked for me: changing the `nameserver` ip address in `/etc/resolv.conf` from `127.0.0.53` to `8.8.8.8`, then reconfigure pi-hole from command line with `pihole -r`. Then all is good.

The installation should leave an OpenVPN client config file at the home directory. I connected to it using OpenVPN Android client from my phone.

## How did things go
With half a day of normal usage with my phone, I have made 1802 queries to the pi-hole, among that 483 queries were blocked. Pi-hole stores query data in sqlite and they have [a nice documentation](https://docs.pi-hole.net/database/#supported-status-types). They have a good web UI as well, available at http://10.8.0.1/admin. I had to reset the admin password from server command line with `sudo pihole -a -p` before able to log in.

YouTube Android app in-app ads were not blocked, as also shown by many posts online. Pi-hole is configurable with a black list, and checking the query log may give some information.

When I opened Bitwarden Androind app, I can see from DNS it queried bitwarden domain names.

And many, many google-related domain names I've never heard off, like googleapis.com. It's like a new world for me.

Traffic were jammed a few times when I watch YouTube video.

## What's next
The traffic jam might be due to the scale of the server (1 vCPU, 512 MB RAM) but needs experimentation by first logging the hardware resource usage over time to see if it maxed out.

Shut down server for now, but interesting experience to see how much data a VPN+DNS server can see and how the ad blocking at DNS level was done.
