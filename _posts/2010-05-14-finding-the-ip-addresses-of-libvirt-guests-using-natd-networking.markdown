---
layout: post
---
Libvirt uses dnsmasq to provide a DHCP service for the guests that are
using NAT'd networking. I have one guest running with a hostname
of "kraken" and to find it's IP address.

    cat /var/lib/misc/dnsmasq.leases 

gives

    1273825151 52:54:00:76:a9:d7 192.168.122.84 kraken *

with the second entry being the MAC address and the third being the IP addr and
the fourth being the hostname. However I find it strange that this information
is not available via the libvirt commands or at least I cannot find it.

