---
layout: default
---

# Rough notes on setting up SpotifyD on Raspbperry Pi with Arch Linux ARM

[Arch Linux ARM](https://archlinuxarm.org/) is an Arch Linux port to ARM CPU.

[spotifyd](https://github.com/Spotifyd/spotifyd) is a Spotify Daemon that can stream music like the official client. It also supports Spotify Connect protocol, which makes it show up as a device that can be controlled from the official clients.

This is rough notes on how to run spotifyd daemon on a Raspberry PI with Arch Linux ARM, using pulseaudio.

spotifyd is not included in the Arch, but available via [aur](https://aur.archlinux.org/packages/spotifyd/). Consult [Arch Wiki](https://wiki.archlinux.org/index.php/Arch_User_Repository) for instructions on how to build it.

spotifyd comes with systemd unit file for user session. To run spotifyd as a system session copy the `/usr/lib/systemd/user/spotifyd.service` into `/etc/systemd/system` direcotry and modify it to look like this:

```
Wants=sound.target
After=sound.target
Wants=network-online.target
After=network-online.target

[Service]
User=alarm
Group=alarm
Environment="DBUS_SESSION_BUS_ADDRESS=unix:path=/run/dbus/system_bus_socket"
ExecStart=/usr/bin/spotifyd --no-daemon --config-path /etc/spotifyd/spotify.conf
Restart=on-failure
RestartSec=12

[Install]
WantedBy=default.target
```

Specify devicename and backend in `/etc/spotify/spotifyd.conf`:

```
[global]
device_name=alarmpi
backend=pulseaudio
```

Configure permissions on the system bus for spotifyd and pulseaudio by creating two files in `/etc/dbus-1/system.d`:

`pulseaudio.conf`:
```
<!DOCTYPE busconfig PUBLIC
 "-//freedesktop//DTD D-BUS Bus Configuration 1.0//EN"
 "http://www.freedesktop.org/standards/dbus/1.0/busconfig.dtd">
<busconfig>
  <policy user="alarm">
    <allow own="org.PulseAudio1"/>
    <allow send_destination="org.PulseAudio1"/>
    <allow receive_sender="org.PulseAudio1"/>
  </policy>
  <policy user="alarm">
    <allow own="org.pulseaudio.Server"/>
    <allow send_destination="org.pulseaudio.Server"/>
    <allow receive_sender="org.pulseaudio.Server"/>
  </policy>
</busconfig>
```

`spotifyd.conf`:
```
<!DOCTYPE busconfig PUBLIC
 "-//freedesktop//DTD D-BUS Bus Configuration 1.0//EN"
 "http://www.freedesktop.org/standards/dbus/1.0/busconfig.dtd">
<busconfig>
  <policy user="alarm">
    <allow own="org.mpris.MediaPlayer2.spotifyd"/>
    <allow send_destination="org.mpris.MediaPlayer2.spotifyd"/>
    <allow receive_sender="org.mpris.MediaPlayer2.spotifyd"/>
  </policy>
</busconfig>
```
