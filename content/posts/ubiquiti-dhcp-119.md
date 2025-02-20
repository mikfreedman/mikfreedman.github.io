---
title: "Ubiquiti Dhcp 119"
date: 2025-02-20T10:12:15-05:00
---

If you'd like to specify multiple search domains for your UniFi Network you need to configure a custom DHCP option.

**Notes**

* These instructions work for UniFi *OS UCK G2 v4.1.11*
* UniFi uses [Vyatta](https://en.wikipedia.org/wiki/Vyatta) under the hood, you can check how your configuration is being deployed by looking at `/opt/vyatta/etc/dhcpd.conf`
* On macos you can see which options are coming back from the DHCP server using `ipconfig getpacket en0`

**Instructions**

* Enable the legacy interface on your controller
* Navigate to Settings > Services > DHCP > DHCP Options
* Add a new option called `domain-search` with code `119` of type `Text`
* For each of your configured VLANs
    * Remove any value in `Domain Name` -- This is super important! If this value is set it will add a conflicting `domain-search` key in `dhcpd.conf` that overrides the value you set in the controller
    * Under "Advanced DHCP Options" > "Custom DHCP Options" set the value of `domain-search` to the domain suffixes you'd liek
    * This format worked for me: `"a1.example.com", "a2.example.com"`
* Let USG reprovision

**Confirmation**

Renew the lease on your client machine and see that the domain search field is properly set:

```
[snip]
domain_search (dns_namelist): {a1.example.com, a2.example.com}
end (none):
```

If it's not there ssh to USG and check `/opt/vyatta/etc/dhcpd.conf` to ensure certain options aren't being set twice or similar.

You can manually configure vyatta like so:

```bash
configure

set service dhcp-server shared-network-name [network name] subnet [subnet cidr]  domain-name "a1.example.com a2.example.com"

commit; save

exit
```

Note that setting it this way is temporary and will not survive a reprovision / reboot

