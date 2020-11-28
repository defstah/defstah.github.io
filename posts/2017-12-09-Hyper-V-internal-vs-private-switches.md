---
layout: post
title: "Hyper-V: Internal vs. Private Switches"
date: 2017-12-09
---

Today I wasn't paying attention when setting up my lab in Hyper V. Took me about 20 minutes of scratching my head to work out why my Hyper-V host machine was getting DHCP configuration from my virtual lab's DHCP server (DC1). The reason this was happening is because I configured my virtual router and DC to connect to an internal switch, which my host is also connected to. The virtual router, which I had installed to contain broadcast traffic, was clearly not going to be effective if my host had a direct connection to the virtual DHCP server. I needed to remove my virtual router and DC1 from the internal switch (which is shared with my host), and connect them to a new private switch. 

How NOT to set up a lab to contain broadcast traffic:
```
+-------------+LAPTOP+---------------------+
|                                +------+  |
|   +-------+     +----------+   | Virt |  |
|   |Host   |     | Internal | <-+Router|  |
|   |DHCP   +---> | Switch   |   +------+  |
|   |Client |     +----+-----+             |
|   +---+---+          ^         +------+  |
|       |              +---------+DC1   |  |
|       |                        |DHCP S|  |
|       |                        +------+  |
|       |        +--------+                |
|       +------> |External| +--------+     |
|                |Switch  |          v     |
|                +--------+                |
|                              +---------+ |
|                              |         | |
+------------------------------+  WiFi   +-+
                               |         |
                               +----+----+
                +---------+         |
                | Wifi AP +^--------+
                | DHCP S  |
                +---------+
```

What it should look like (I hope):
```
+--------------+LAPTOP+---------------------+
|                                 +------+  |
|                                 |DC1   |  |
|                       +-------+ |DHCP S|  |
|                       v         +------+  |
|                                           |
|    +-------+     +----------+             |
|    |Host   |     | Private  | <-----+     |
|    |DHCP   |     | Switch   |   +-------+ |
|    |Client |     +----------+   | Virt. | |
|    +---+---+                    | Router| |
|        |             +--------+ +-------+ |
|        |             v                    |
|        |                                  |
|        |        +--------+                |
|        +------> |External| +--------+     |
|                 |Switch  |          v     |
|                 +--------+                |
|                               +---------+ |
|                               |         | |
+-------------------------------+  WiFi   +-+
                                |         |
                                +----+----+
                 +---------+         |
                 | Wifi AP +^--------+
                 | DHCP S  |
                 +---------+


```
