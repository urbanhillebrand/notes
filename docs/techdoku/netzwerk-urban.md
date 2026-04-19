# Netzwerk neu 02/23

## Netzwerkbereiche

|     |     |
| --- | --- |
| .1 | Gateway |
| .2 - .19 | Netzwerk (Switches, APs) |
| .20 - .127 | statische IPs oder DHCP-Resvg. |
| .128 - .254 | DHCP |

## Netzwerke, statische IPs

**LAN (urban.lan, 10.10.0.0/24)**

|     |     |
| --- | --- |
| .1 | opnsense |
| .2 | sw-ho |
| .3 | sw-wz |
| .4 | sw2-ho |
| .5 | sw-tisch |
| .20 | srv (alt) |
| .21 | nas1 |
| .22 | srv2 |
| .23 | pikvm |
| .30 | lap-en-dock |
| .31 | lap-wl-int |
| .32 | pi-hole |
| .33 | lexmark (alt) |
| .34 | pve |
| .40 | lexmark |
| .41 | Etikettendrucker |
| .36 | uc |
| .64 - .95 | reserviert (docker macvlan DHCP) |
| .80 | reserviert (OPNsense install) |

**IOT (iot.urban.lan, 10.10.64.0/24)**

|     |     |
| --- | --- |
| .1 | opnsense |
| .10 | Nvidia Shield |
| .20 | homeassistant |
| .21 | mqtt |
| .22 | zigbee2mqtt |
| .64 - .95 | reserviert (docker macvlan DHCP) |

**Media (media.urban.lan, 10.10.128.0/24)**

|     |     |
| --- | --- |
| .1 | opnsense |
| .11 | eversolo |
| .12 | nas1 |
| .13 | mediatablet |
| .14 | sofabaton |
| .20 | emby |
| .21 | jellyfin |
| .22 | sonarr |
| .23 | lidarr |
| .24 | sabnzbd |
| .25 | radarr |
| .26 | prowlarr |
| .27 | vuultimo |
| .64 - .95 | reserviert (docker macvlan DHCP) |
| .254 | anon |

**Extern (extern.urban.lan, 10.10.192.0/24)**

|     |     |
| --- | --- |
| .1 | opnsense |

## Switchports WZ

| Port# | Gerät |
| --- | --- |
| 1 | Verbindung Büro |
| 2 | AP WZ |
| 3 | Shield |
| 4 | Raspberry Pi |
| 5 | (frei) |
| 6 | XBOX |
| 7 | BluRay |
| 8 | TV-Receiver |
