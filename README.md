# naspi
A repo for my cute but mighty pi home server.

## OS
[Raspberry Pi OS Lite (64-bit)](https://www.raspberrypi.com/software/operating-systems/)

## Configurations

### Automatic Updates
For system security, install `unattended-upgrades` package after installign the OS:
```bash
sudo apt install unattended-upgrades
```

### SSH
Set up SSH keys and disable SSH root login/password login. Optionally, change the SSH port.
The configuration file is `/etc/ssh/sshd_config`.

### UFW
UFW in Raspberry Pi OS may not start automatically on reboot because of incorrect loading during system boot.
To enable UFW upon reboot, edit `/lib/systemd/system/ufw.service` and replace the following line
```
Before=network.target
```
with
```
Before=network-pre.target
Wants=network-pre.target local-fs.target
After=local-fs.target
```
See the [discussions](https://askubuntu.com/a/1040584) regarding this issue and fixes
([1](https://git.launchpad.net/ufw/commit/?id=b3b831af27e7325085d91f42688620c13640f8f9), 
 [2](https://git.launchpad.net/ufw/commit/?id=66457982870852f59a498039f2131764851226d3)) from the ufw repo.

## Services

### Non-Docker
Samba (for file sharing)
```bash
sudo apt install samba samba-common
```
Then add sharing to the data hard drive and disable home sharing.
The configuration file is `/etc/samba/smb.conf`



### Docker
Use the `docker-compose.yml` file.
Most containers are maintained by [linuxserver.io](https://fleet.linuxserver.io/)

NGINX is used for redirects so that each services' port number doesn't need to be explicitly used.
<!-- See [this doc](./nginx.md) for more details about configuring NGINX. -->

In qBittorrent, go to `Options` -> `Advanced` and select `wg0` for `Network interface`.
This prevents any IP leaks when used in conjuction with the WireGuard VPN.

For WireGuard, set up the kill-switch as specified in [this doc](./wg-killswitch.md).
This should be placed in a pre-configured `wg0.conf` file containing credentials for the VPN service.

### Optional Services
Use the Pi as a VPN server: [PiVPN](https://www.pivpn.io/)
