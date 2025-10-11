---
title: "VLAN Hopping Attack: EVE-NG Lab"
author: radouane
date: 2025-10-11 00:15:00 +0100
categories: [Networking, Attacks]
tags: [Networking, CLI, Commands, Wireshark]
image:
  path: /assets/img/posts/2025/VLAN_hopping/eve-ng_lab.PNG
  alt: VLAN Hopping Attack
description: VLAN Hopping attack. How to escape your VLAN and get into any other VLAN in the network.
pin: false
toc: true
comments: true
---

there are a lot of network attacks. but here's a fun one. 

# what is VLAN hopping?
in brief, it is the act of escaping from your VLAN to access another VLAN that you shouldn't be allowed to.

## does it always work?
no, because it depends on the switch model and vendor. but it is a good attack that all network engineers should simulate to learn how it works.

i used the following image:
i86bi_linux_l2-ipbasek9-ms.may8-2013-team_track.bin

# what you should know about this attack?
for the attacker to be able to escape from its VLAN to another VLAN. the attacker MUST be in the same VLAN as the native VLAN.

because the attack is very simply, as follows:
1. attacker will prepare a packet with two dot1q tags, the first tag is its VLAN which also needs to be the native VLAN in the trunk link. 
2. second tag will be the VLAN that our target belongs to. the order is important.
3. attacker will send the packet.

then here's what will happen next. 
![VLAN Hopping EVE-NG Lab](/assets/img/posts/2025/VLAN_hopping/eve-ng_lab.PNG)

the first switch which the attacker is connected to, will receive the packet and will remove the first tag from the packet because it is a tag with the same VLAN as native VLAN. and as we all know, native VLAN traffic is untagged.
![VLAN Hopping Wireshark](/assets/img/posts/2025/VLAN_hopping/VLAN_Hopping_Screenshot.png)

not all switches operate like that, but most of them do. they play it smart and removes the tag as it is just native VLAN, but do not inspect if there is any other tag, that is why the order of tags is important.

the second switch will receive the packet with only one dot1q tag, which is the second tag which the first switch didn't see, so second switch just forwards it to that VLAN in that tag. 

and that's the VLAN hopping attack, very simple and fun.

here's the command i used:
sendp(Ether(dst='50:00:00:11:00:00', src='50:00:00:0e:00:00')/Dot1Q(vlan=1)/Dot1Q(vlan=10)/IP(dst='10.10.10.1', src='10.10.0.1')/ICMP(), iface='eth0')

you must have scapy installed.
