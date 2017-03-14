# Physical network interface monitoring

This monitoring template is done using discovery feature in Zabbix and detects if physical interface on server has carrier. 

* If physical interface is up and has carrier - returns 1.
* If physical interface is has no carrier or is down - returns 0. 

## Deployment

1. Create or use already created template.
2. In administration->general menu go to *Regular Expressions* and do following.
  1. Add new regular expression **Physical network interface discovery**.
  2. Use regular expression **^(em[0-9]{1,}|p[0-9]{1,}p[0-9]{1,}|eth[0-9]{1,}|ens[0-9]{1,})$**.
3. Add discovery rule to your template with key **net.phys.if.discovery** .
  1. *Filter* is **{#PHYSNET}**.
  2. *Regular expression* is **@Physical network interface discovery**.
4. Add item prototype with key **net.if.carrier.status[{#PHYSNET}]**.
5. Add trigger with expression **{Template OS Linux:net.if.carrier.status[{#PHYSNET}].last()}=0**.
6. Deploy script **netif_discovery.sh** from this repository to all hosts (preferably with Salt/Ansible) into path **/usr/local/sbin/netif_discovery.sh**.

## User Parameter

Use following user parameters:

```bash
UserParameter=net.phys.if.discovery,/usr/local/sbin/netif_discovery.sh
UserParameter=net.if.carrier.status[*], if [ $(cat /sys/class/net/$1/operstate) = "up" ]; then cat /sys/class/net/$1/carrier; else echo "0"; fi;
```

## Useful links

http://stackoverflow.com/questions/808560/how-to-detect-the-physical-connected-state-of-a-network-cable-connector
https://www.zabbix.com/documentation/3.0/manual/discovery/low_level_discovery
