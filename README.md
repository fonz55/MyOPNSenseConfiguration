# **MyOPNSenseConfiguration**
Virtual (Proxmox) OPNSense configuration on XS4All
This is the configuration for filtering packets from and to the Internet. It also has a seperated VLAN setup for my SetTopBox (STB)

What's already achieved:

- [x] Internet connection through a Vigor 130 in bridge mode
- [x] Configure Proxmox with two virtual NIC's
- [x] Install and basic setup for virtual OPNSense
- [x] Watching TV with the STB being on the same local LAN. This had some lag while zapping to other channels. Therefor I am trying to implement a separate VLAN.
- [x] Switch configuration for specific VLAN(25)
- [x] Getting IP from VLAN25 range with a laptop on the STB switch port
- [ ] Ping 213.75.112.1 with laptop
- [x] Ping router with laptop
- [x] Ping internet address (i.e xs4all.nl)
- [ ] Watch TV with STB on a separate VLN(25) and not having that irritating lag while zapping. 
- [x] Get help from experts ;-)

## **Overview**

First the overview which Interfaces

![OpnSense-Interfaces_Assignments](https://user-images.githubusercontent.com/59983726/153193421-1a3a78eb-3d5b-4b63-a15e-ec40272a2b52.jpg)

The VLAN's on the outside

![OpnSense-Interfaces_Other_VLANs](https://user-images.githubusercontent.com/59983726/153196675-9ca2bba0-e34a-4abe-ac5c-8e57db60cef8.jpg)

The WAN_Internet (VLAN6) settings

![OpnSense-Interfaces_WAN_INTERNET](https://user-images.githubusercontent.com/59983726/153194062-6fa7d591-f335-4182-99b0-e43757d4e0cd.jpg)
![OpnSense-Interfaces_WAN_INTERNET1](https://user-images.githubusercontent.com/59983726/153194060-2379b253-7844-435e-b061-41a6c3c4398a.jpg)

The WAN_IPTV (VLAN4) settings

![OpnSense-Interfaces_WAN_IPTV](https://user-images.githubusercontent.com/59983726/153194306-973bc8c2-9746-420e-94fb-aa823f0cc6db.jpg)
![OpnSense-Interfaces_WAN_IPTV1](https://user-images.githubusercontent.com/59983726/153194317-e4e622e8-9a99-4559-8d72-cfc1dfd76281.jpg)

And of course the local LAN settings

![OpnSense-Interfaces_LAN](https://user-images.githubusercontent.com/59983726/153196074-3c61d609-136e-47f0-9d5e-c5c53b42ccba.jpg)

That has a separated IPTV_STB (VLAN25) for the STB

![OpnSense-Interfaces_IPTV_STB](https://user-images.githubusercontent.com/59983726/153196255-55524d28-4150-48ba-8524-fc57ef53051d.jpg)

With this config I get this overview

![OpnSense-Interfaces_Overview_WAN_INTERNET](https://user-images.githubusercontent.com/59983726/153196748-a82faff8-f0e5-426f-b122-c469e338a26c.jpg)

Houston, we have liftoff!

Also IPTV has received an IP.

![OpnSense-Interfaces_Overview_WAN_IPTV](https://user-images.githubusercontent.com/59983726/153197099-ca5747b6-b9f8-4290-820e-34e39b73c4ca.jpg)

So the OPNSense router got following gateways and Routes.
Since KPN/XS4All doesn't send an default gateway for the WAN_IPTV interface (see the picture above) we do not have to set a lower priority on the WAN_IPTV Gateway.

![OpnSense-System_Gateways_Single](https://user-images.githubusercontent.com/59983726/153205974-865dc354-8f19-41c0-8ce0-a348da68304b.jpg)
![OpnSense-System_Routes_Status](https://user-images.githubusercontent.com/59983726/153205985-f13251a5-964a-4780-81bc-1a2dedaf3e8e.jpg)

With the DHCPv4 settings for LAN and IPTV_STB (VLAN25) we are able to connect with a laptop on the internet as well receive a different IP address from the VLAN25 DHCP range.

![OpnSense-Services-DHCPv4_LAN](https://user-images.githubusercontent.com/59983726/153205339-4a180808-988e-42e1-a30c-ef99bfb93299.jpg)
![OpnSense-Services-DHCPv4_IPTV_STB](https://user-images.githubusercontent.com/59983726/153205368-42b02c5a-8630-45f2-b2ca-b2b64bd9934b.jpg)
![OpnSense-Services-DHCPv4_IPTV_STB1](https://user-images.githubusercontent.com/59983726/153205386-36a2b049-a3cb-4d25-9119-4509a6b0e461.jpg)
![OpnSense-Services-DHCPv4_IPTV_STB2](https://user-images.githubusercontent.com/59983726/153205404-ace50984-9ebe-480c-9681-c8dda6e0c5a4.jpg)

The overview for LAN interface and IPTV_STB are successively:

![OpnSense-Interfaces_Overview_LAN](https://user-images.githubusercontent.com/59983726/153225069-80457535-6d70-4be5-8717-42355bbbda6c.jpg)
![OpnSense-Interfaces_Overview_IPTV_STB](https://user-images.githubusercontent.com/59983726/153225078-24523a56-8750-49c7-a726-588347123f5a.jpg)

## **Ruleset**

Now we will configure the filtering on the interfaces:
For WAN_INTERNET:

![OpnSense-Firewall-Rules-WAN_Internet](https://user-images.githubusercontent.com/59983726/153206730-896057e8-550b-485b-9f9f-4529bfa0ed3c.jpg)

For WAN_IPTV:

![OpnSense-Firewall-Rules-WAN_IPTV](https://user-images.githubusercontent.com/59983726/153206825-9a8f0aa9-e554-400f-a802-21bdfaea301a.jpg)

For LAN:

![OpnSense-Firewall-Rules-LAN](https://user-images.githubusercontent.com/59983726/153206874-461765dc-1cad-4922-982b-fde6a4be806c.jpg)

For IPTV_STB:

![OpnSense-Firewall-Rules-IPTV_STB](https://user-images.githubusercontent.com/59983726/153206960-74af4510-a2f5-48f2-afab-85a4115e48ea.jpg)

Unlike pfSense, within OPNSense an outbund IGMP options rule is needed. Otherwise the IPTV stream stops after 5 minutes.

_!! Pay attention: to enable multicast one has to set "allow options" !!_

The first and second rule are for enabling DNS and ICMP. 
Forgot to mention the aliases:

![OpnSense-Firewall-Alias](https://user-images.githubusercontent.com/59983726/153210456-904a3401-2eaa-4a7f-865f-0728d55f6807.jpg)
![OpnSense-Firewall-Alias1](https://user-images.githubusercontent.com/59983726/153210473-050b9086-3148-451d-8ea4-fa37d6b8b2d5.jpg)

Because of the classless routes DHCP setting on VLAN4 AND VLAN4 not having a default gateway eveything will be routed through the WAN_INTERNET interface. A full outbound NAT (without destination filter) over VLAN4 as well as a full NAT over VLAN6 will enable IPTV as well as Netflix, etcetera on the STB.
The resulting outbound NAT (change to hybrid):

## **NAT**

![OpnSense-Firewall-NAT_Outbound](https://user-images.githubusercontent.com/59983726/153213876-9b2a1e70-1dfe-426d-a36e-2826d198c461.jpg)

And as last we configure a IGMP Proxy
Because I want as little maintenance as possible to the future, the IGMP Proxy will provide a default route configuration. With a trick in pfSense and OPNSense the default route 0.0.0.0/0 will be split in two ranges: 0.0.0.0/1 & 128.0.0.0/1.

![OpnSense-Services-IGMPProxy](https://user-images.githubusercontent.com/59983726/153215546-03483072-62f9-4432-9ff2-271e3d392fe5.jpg)

Right now we are able to connect with an laptop on the VLAN25 switch port, receive an IP from the 172.16.25.10 range instead of the LNowAN range (i.e. 10.0.100.25), ping the gateway (172.16.25.1), ping 'the internet' (i.e. ping xs4all.nl) and subsequent ping one of the IPTV backend servers (ping 213.75.112.1)

# **Issue(!!)**

-=#$ GLITCH $#=-

The thing is that I am not able to get an answer from 213.75.112.1 @#$@$ Please advice what setting(s) to be changed.

-=#$$#=-

## **Physical setu**p
For completeness reasons I also attach both my TP Link (TL-SG108E) switch configurations for VLAN's. 
The first switch situated in the meter cupboard:

![TL-SG108E-Meterkast-VLANTags](https://user-images.githubusercontent.com/59983726/153216892-48164462-5480-4044-ab22-ff9572627a27.jpg)
![TL-SG108E-Meterkast-PVIDs](https://user-images.githubusercontent.com/59983726/153217024-59891cf7-d9b2-4f31-9066-2edfab66e544.jpg)

The second one in hour living room on the first floor.

![TL-SG108E-Woonkamer-VLANTags](https://user-images.githubusercontent.com/59983726/153217110-925338f3-b380-418f-b43e-e92288b06877.jpg)
![TL-SG108E-Woonkamer-PVIDs](https://user-images.githubusercontent.com/59983726/153217124-3fede997-68a8-40d4-b7af-5756ee0a6354.jpg)

The configuation of the Draytek Vigor 120 in bridge mode can be found on my favorite dutch website and forum [gathering.tweakers.net](https://gathering.tweakers.net/forum/list_messages/2076232).
