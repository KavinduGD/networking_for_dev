# Address Resolution Protocol (ARP)

## If we don't know the mac address, we can use ARP to find it

## ARP table is a cache of IP to MAC address mappings

## If destination Ip in the same network, we can send a broadcast ARP request

## If destination IP is not in the same network, we send the packet to the default gateway. First Use ARP to get the MAC address of the gateway

## 🛑 ARP is not used between routers on the internet
