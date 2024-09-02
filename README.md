# plex_and_wireguard
How to get remote Plex working with Wireguard (eg. ProtonVPN), and by working I mean bypass.

Assuming you have your Wireguard connected.

Get UID of user `plex`, 998 in my case. Set all traffic of `plex` to be marked:

```sudo iptables -t mangle -A OUTPUT -m owner --uid-owner 998 -d 0.0.0.0/0 -j MARK --set-mark 1```

Send all marked traffic to `plexroute`:

```sudo ip rule del fwmark 0x1 lookup plexroute```

Route all `plexroute` traffic through your physical ethernet interface:

```sudo ip route add default via 192.168.1.1 dev enp0 table plexroute```

Now, also rewrite all traffic from `plex` as originating from your host IP address:

```sudo iptables -t nat -A POSTROUTING -m owner --uid-owner 998 -o enp0 -j SNAT --to-source 192.168.1.78```

Make sure your external port is forwarded to your Plex port. And that's it.
