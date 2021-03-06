In case you want to connect your phone manually, maybe because you don't have GUI or RNDIS does not work over USB or simply for test via the commandline, you can still use Network-Manager.

Since Network Manager is driven by config files, first step is to create a new config file, probably for a WiFi connection:

    [connection]
    id=<xxx>
    uuid=<uuid>
    type=802-11-wireless
    autoconnect=true
    timestamp=0

    [ipv4]
    method=auto
    ignore-auto-routes=false
    ignore-auto-dns=false
    never-default=false

    [802-11-wireless]
    ssid=<ssid>
    mode=infrastructure
    channel=0
    rate=0
    tx-power=0
    mtu=0
    seen-bssids=
    security=802-11-wireless-security
    mac-address=<mac>

    [802-11-wireless-security]
    key-mgmt=wpa-psk
    wep-tx-keyidx=0
    psk=<psk>

The following fields need to be filled up:
 
1. `<xxx>` - Name of the connection, best to be the same as the SSID
2. `<uuid>` - Create a new one by calling uuid on your desktop
3. `<ssid>` - Needs to be in ascii bytes, for this use the following python snippet: `';'.join(str(ord(c)) for c in 'SSID')+';'`
4. `<mac>` - Paste MAC address of interface from ifconfig -a
5. `<psk>` - Output from wpa_passphrase <ssid> <passphrase>

Call this file after the SSID of the network, lets say xxx.

We will place this config file in the folder /etc/NetworkManager/system-connections/ and change the permissions to 0600 - this is important, files with other modes will not be accepted.

*Note: After placing the connection file in this folder the connection will be automatically started whenever its possible, i.e. the interface is accessible and the AP is nearby. Probably you want to move this file out again if no longer needed.*

Now we use the cmdline tool nmcli to tell Network-Manager what we want:

`nmcli con reload`

this will re-read the config directories

`nmcli con up id xxx`

This should bring up the interface, attach wpa_supplicant to it and handle the association and key exchange, and get dhclient informed. Pretty neat!