# apcupsd scripts for Proxmox HA clusters

The primary changes to the default scripts are the use of the [Pushover API](https://pushover.net/api) instead of "mail" for notifications and the inclusion of a ha-manager "disable" call prior shutdown. 

The former is for simplicity and reliability. I previously relied on apcupsd to send an email to a separate server which would then forward messages to Pushover, but here I've replaced the email lines with direct calls to the Pushover REST API. 

The latter change is to prevent the "HA Shuffle". When an HA node receives a shutdown command, it will usually try to offload it's HA guests onto another node. In the case of a power failure and an unavoidable shutdown of the entire cluster, there's no reason to shuffle guests around; they should simply stop all guests in-place and continue with the shutdown. The script checks for running HA guests and then calls ha-manager to disable them all, before then sending a `shutdown now` command to the remaining nodes and finally to itself. 

There is also an additional script, called "restart-ha-vms" which can be called when the system comes back online, either via systemd service or cron, to restore the VMs and re-enable HA. 

## TODO: 
Automatically gather IP addresses of the other nodes to prevent hardcoding the node IPs. 