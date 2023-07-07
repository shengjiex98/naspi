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
A bug in Raspberry Pi OS causes UFW to try to start before all of the network interfaces were up. 
To enable UFW upon reboot, edit `/lib/systemd/system/ufw.service` and add the following line to the 
end of the first section (`[Unit]`). [Link to discussion](https://askubuntu.com/a/1040584)
```
After=network-pre.target
```

## Services

### Non-Docker
Samba (for file sharing)
```bash
sudo apt install samba samba-common
```
Then add sharing to the data hard drive and disable home sharing.
The configuration file is `/etc/samba/smb.conf`

### Docker
Use the `docker-compose.yml` file for the following services. 
All containers are maintained by [linuxserver.io](https://fleet.linuxserver.io/)
- WireGuard
- qBittorrent
- Plex
- Radarr

In qBittorrent, go to `Options` -> `Advanced` and select `wg0` for `Network interface`.
This prevents any IP leaks when used in conjuction with the WireGuard VPN.

For WireGuard, set up the kill-switch as specified in [this doc](./wg-killswitch.md).
This should be placed in a pre-configured `wg0.conf` file containing credentials for the VPN service.

### Optional Services
Use the Pi as a VPN server: [PiVPN](https://www.pivpn.io/)
