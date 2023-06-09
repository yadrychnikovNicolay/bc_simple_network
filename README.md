# bc_simple_network
BeCode Network Module - One Simple Network

## Components

### For the local network

 - 1 router
 - 1 switch
 - 3 PCs

### To simulate internet access

 - 1 external router
 - 1 web server
 
### To simulate website access

 - 1 DNS server

## Step-by-step configuration

### Link the PCs together

The 3 PCs are linked to the switch's first 3 ports (**FastEthernet 0/1 0/2 0/3**) through their **FastEthernet0** port. I've attributed the ips/subnets masks to the PCs' interfaces as instructed and made sure the interfaces were up on both them and the switch.
| Devices | LAN | IP | Mask |
|---------|-----|----|------|
| PC-Robert | Eth | 192.168.1.10 | 255.255.255.0 | 
| PC-Camille | Eth | 192.168.1.11 | 255.255.255.0 |
| PC-Renaud | Eth | 192.168.1.12 | 255.255.255.0 |

At this point the PCs can already communicate between themselves. *The ip addresses on the switch are not configured by the way, so pings between the switch and other devices won't work. I'm doing things plug-and-play style here. I also haven't configured the passwords anywhere nor the message banner.*

### Add the router through which internet would be provided

The local router's **GigabitEthernet 0/0/0** port is linked to the switch's **GigabitEthernet 0/1** port. 

The router's interface is configured with the following ip/subnet mask : **192.168.1.1** /**255.255.255.0**. 

Again I made sure the interfaces are up and running. 

**The router's ip can now be set as the default gateway for the 3 PCs.**

With this, pings between the local router and the PCs are possible.

### Simulate access to internet by creating another network with a webserver we'll need to reach
The external router's **GigabitEthernet 0/0/0** port is linked to the webserver's **FastEthernet0** port. 

The router's interface is configured with the following ip/subnet mask : **192.140.1.1** / **255.255.255.0**. 

The server's interface is configured with the following ip/subnet mask : **192.140.1.10** / **255.255.255.0** and it's default gateway is **192.140.1.1**. 

Again I made sure the interfaces are up and running.

The external router can now ping the webserver.

### Enable communication between the networks so we can reach the webserver

The routers are linked together through their **GigabitEthernet 0/0/1** port. 

The local router's interface is configured with the following ip/subnet mask : **192.168.2.1** / **255.255.255.0**. 

The external router's interface is configured with the following ip/subnet mask : **192.168.2.2** / **255.255.255.0**. 

Again I made sure the interfaces are up and running.

The routers can now ping each other. But a this point the PCs can't communicate with the webserver or even the external router so adding routes is necessary.

First on the local router with a route to reach network **192.140.1.0** with the mask **255.255.255.0** through next hop which is **192.168.2.2**.

Then on the external router with a route to reach network **192.168.1.0** with the mask **255.255.255.0** through next hop which is **192.168.2.1**.

The PCs can now reach the webserver on another network. If we use a browser on a PC and try to reach 192.140.1.10, the default cisco packet tracer webpage will be displayed. 

But users mostly use domain names. Let's say the domain name of this site is www.cisco.com. If we try to reach it we get a "Host Name Unresolved" error. It's because our network doesn't know which ip is behind this domain name. **So we're gonna add a local DNS server to help us.**

### Setup the DNS server

The DNS server will be linked through its **FastEthernet0** port to the switch's **FastEthernet 0/4** port. 

It is configured with the following ip/subnet mask : **192.168.1.254** / **255.255.255.0**. It's default gateway is **192.168.1.1**. 

Again I made sure the interfaces are up and running.

The DNS server can now be reached by other end devices.

I've **enabled the DNS service** on the DNS server and I've **added an entry** with the name *www.cisco.com* which has the address **192.140.1.10**. 

Then I've configured all the end devices with **192.168.1.254** as the DNS server address.

**That's it ! *www.cisco.com* can now be reached through the browser of the PCs.**
[Link to the PKT file](https://github.com/alexandrentougas/building_a_simple_network/blob/main/simpleNetworkFinal.pkt)
