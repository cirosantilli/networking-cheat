# NetworkManager

GNOME project for simplifying network configuration. <https://wiki.gnome.org/Projects/NetworkManager>

Also has an Applet that shows on Ubuntu 12.04's taskbar, indicating connection status.

On Ubuntu it comes on the packages `network-manager` and `network-manager-gui` for the applet.

It corresponds to the upstart service `network-manager`, so for example to reload configurations it can be restarted with:

    sudo service network-manager restart

## nm-applet

The applet.

## nm-connection-editor

Opened from the applet "Edit Connections".

## nm-tool

Get NetworkManager status from the command line. Sample output:

    NetworkManager Tool

    State: connected (global)

    - Device: wlan0  [NUMERICABLE-B2BD] --------------------------------------------
    Type:              802.11 WiFi
    Driver:            rtl8192ce
    State:             connected
    Default:           no
    HW Address:        E0:06:E6:C7:97:8F

    Capabilities:
        Speed:           72 Mb/s

    Wireless Properties
        WEP Encryption:  yes
        WPA Encryption:  yes
        WPA2 Encryption: yes

    Wireless Access Points (* = current AP)
        NUMERICABLE-4D5F:Infra, C0:D9:62:C7:43:49, Freq 2412 MHz, Rate 54 Mb/s, Strength 26 WPA WPA2
        FREEBOX_BRAHIM_GH: Infra, F4:CA:E5:D9:B3:2C, Freq 2437 MHz, Rate 54 Mb/s, Strength 26 WPA
        Jordy:           Infra, 56:76:06:1D:11:14, Freq 2442 MHz, Rate 54 Mb/s, Strength 26 WPA
        *NUMERICABLE-B2BD: Infra, E0:AB:31:AC:30:6D, Freq 2412 MHz, Rate 54 Mb/s, Strength 84 WPA WPA2
        freephonie:      Infra, 56:76:06:1D:11:17, Freq 2442 MHz, Rate 54 Mb/s, Strength 26 WPA Enterprise
        NUMERICABLE-F1EC:Infra, 00:1A:2B:9B:0C:F4, Freq 2462 MHz, Rate 54 Mb/s, Strength 26 WPA WPA2
        [...]

    IPv4 Settings:
        Address:         192.168.0.10
        Prefix:          24 (255.255.255.0)
        Gateway:         192.168.0.1

        DNS:             89.2.0.1
        DNS:             89.2.0.2


    - Device: eth0  [Wired connection 1] -------------------------------------------
    Type:              Wired
    Driver:            e1000e
    State:             connected
    Default:           yes
    HW Address:        00:21:CC:CE:F2:C1

    Capabilities:
        Carrier Detect:  yes
        Speed:           1000 Mb/s

    Wired Properties
        Carrier:         on

    IPv4 Settings:
        Address:         192.168.0.11
        Prefix:          24 (255.255.255.0)
        Gateway:         192.168.0.1

        DNS:             89.2.0.1
        DNS:             89.2.0.2

## nmcli

Control NetworkManger from CLI.

Bring down wired connection:

    nmcli con down id 'Wired connection 1'
    nmcli dev disconnect iface eth0

Bring it back up:

    nmcli con up id 'Wired connection 1'

## /etc/NetworkManager/system-connections/ID

## configuration file

NetworkManager's configuration files:

    sudo vim /etc/NetworkManager/system-connections/ID

one per interface.

Modified through the applet.

## Related tools

### ifup

### ifdown

There are several levels of network management tools: <http://askubuntu.com/questions/1786/what-is-the-difference-between-network-manager-and-ifconfig-ifup-etc>.

From the lowest level to the highest:

- `ifconfig`
- `ifup` and `ifdown`
- NetworkManager

### /etc/network/interfaces

Configuration for `ifup` and `ifdown`.

    man interfaces

If you manually set configuration on `/etc/network/interfaces`, NetworkManager will now touch those interfaces and display them as "Not Managed".
