# Sonarr Jackett qBitTorrent in Docker

## Setup 

1. Install Docker and docker-compose
2. (optional) [mount a network drive if using network storage for your Plex files](https://linuxtect.com/how-to-mount-cifs-windows-share-in-linux/) if your Plex server/storage is on another device
3. Work out the directory for your Plex (to import to) and QBitTorrent (to download to first) files, and **make sure that any user can write to those folders (e.g. `chmod -R a+rw /path/to/dir`)**

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

Once everything is spun up successfully, you can go ahead and configure Sonarr to use Jackett as an indexer and qBitTorrent as a downloader. Configuration will persist across starts/stops, since the configuration is written and stored locally in this repository.

When configuring these, it's important to remember that the URL for each app is the name of the app itself (e.g. `http://jackett:8989`) rather than localhost, since that's how docker-compose configures the network.

### Media Management

Add `/Plex/<whatever you want>` as a root folder for Sonarr, as this maps to whatever you've set as your Plex folder.

### QBitTorrent

Default username and password is:
username: **admin**
password: **adminadmin**

Set this up as a Download Client in Sonarr, pointing to `qbittorrent:8080` as the base URL.

### Jackett

Set this up as an indexer via Torznap in Sonarr, pointing to `jackett:9117` as the base URL.

## Optional extras

### Start up on boot (linux)

You can use `systemd` to get these services spun up on boot. 

1. First, edit `sonarr.service` to use the correct paths to this repository on your device
2. Copy `sonarr.service` to `/etc/systemd/system/sonarr.service`.
3. Then, run `systemctl enable sonarr.service` to enable it at boot
4. Restart your device and check that it is running on reboot (you can use `systemctl status sonarr`)

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