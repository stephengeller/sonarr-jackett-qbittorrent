# Sonarr Jackett qBitTorrent in Docker

## Setup 

1. Install Docker and docker-compose
2. (optional) [mount a network drive if using network storage](https://linuxtect.com/how-to-mount-cifs-windows-share-in-linux/)

## Start

1. Run `script/start` to spin up sonarr, jackett and qbittorrent in docker containers.

`./script/start <PATH_TO_PLEX_STORAGE> <PATH_TO_QBITTORRENT_DOWNLOADS>`

e.g. 

`./script/start /Downloads/Plex /Downloads/qbittorrent`

If everything works as intended, Sonarr will download TV to the qbittorrent path, then, once complete, import the finished download to your Plex path as configured in Sonarr.

You can reach the servers by navigating to:

* http://localhost:8989 (sonarr)
* http://localhost:9117 (jackett)
* http://localhost:8080 (qbittorrent)

All of your personal configuration should be created automatically and stored within this repository, ignored by git.

## Configuration

Once everything is spun up successfully, you can go ahead and configure Sonarr to use Jackett as an indexer and qBitTorrent as a downloader.

When configuring these, it's important to remember that the URL for each app is the name of the app itself (e.g. `http://jackett:8989`) rather than localhost, since that's how docker-compose configures the network.

### QBitTorrent

Default username and password is:
username: **admin**
password: **adminadmin**

### Jackett

Set this up via Torznap in Sonarr

## Optional extras

### Start up on boot (linux)

You can use `systemd` to get these services spun up on boot. 

1. First, copy `sonarr.service` to `/etc/systemd/system/sonarr.service`.
2. Then, run `systemctl enable sonarr.service` to enable it at boot
3. Restart your device and check that it is running on reboot (you can use `systemctl status sonarr`)

### Mount network drive on boot (linux)

You can mount a network drive using `/etc/fstab`.

```
//<IP_ADDRESS_OF_NETWORK_STORAGE>/<DRIVE_NAME> /local/path/to/mount/to cifs username=<NETWORK_USER_NAME>,password=<NETWORK_USER_PASS>,nofail,uid=1000,gid=1000 0 0
```

e.g. 

```
//192.169.0.111/westerndigital2tb /media/network-drive cifs username=stephen,password=hunter12,nofail,uid=1000,gid=1000 0 0
```

Before you add this, you need to make sure that:

1. the folder being mounted to (e.g. `/media/network-drive` in the above example) already exists (`mkdir /media/network-drive`)