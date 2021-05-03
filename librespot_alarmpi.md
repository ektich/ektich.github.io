# RPi as Spotify Speaker with librespot

## WIP/Idea: Socket activation
Instead of running librespot at startup, configure systemd for socket activation.

Spotify client detects spotify speakers by sending out mDNS PTR query for `_spotify-connect._tcp.local`

librespot (started with `librespot --name RPix`) responds with the following:
```
Answers
    _spotify-connect._tcp.local: type PTR, class IN, RPi._spotify-connect._tcp.local
        Name: _spotify-connect._tcp.local
        Type: PTR (domain name PoinTeR) (12)
        .000 0000 0000 0001 = Class: IN (0x0001)
        0... .... .... .... = Cache flush: False
        Time to live: 60 (1 minute)
        Data length: 33
        Domain Name: RPi._spotify-connect._tcp.local
    RPi._spotify-connect._tcp.local: type SRV, class IN, priority 0, weight 0, port 42521, target alarmpi.local
        Service: RPi
        Protocol: _spotify-connect
        Name: _tcp.local
        Type: SRV (Server Selection) (33)
        .000 0000 0000 0001 = Class: IN (0x0001)
        0... .... .... .... = Cache flush: False
        Time to live: 60 (1 minute)
        Data length: 21
        Priority: 0
        Weight: 0
        Port: 42521
        Target: alarmpi.local
    RPi._spotify-connect._tcp.local: type TXT, class IN
        Name: RPi._spotify-connect._tcp.local
        Type: TXT (Text strings) (16)
        .000 0000 0000 0001 = Class: IN (0x0001)
        0... .... .... .... = Cache flush: False
        Time to live: 60 (1 minute)
        Data length: 20
        TXT Length: 11
        TXT: VERSION=1.0
        TXT Length: 7
        TXT: CPath=/
    alarmpi.local: type A, class IN, addr 192.168.0.185
        Name: alarmpi.local
        Type: A (Host Address) (1)
        .000 0000 0000 0001 = Class: IN (0x0001)
        0... .... .... .... = Cache flush: False
        Time to live: 60 (1 minute)
        Data length: 4
        Address: 192.168.0.185
```
