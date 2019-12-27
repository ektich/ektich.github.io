---
layout: default
---

# Publishing network services with ssytemd

Network services that are provided by a server can be made discoverable using DNS-SD protocol by publishing them on the network (terminology might be wrong here!). Before `systemd` it was the function of Avahi daemon to publish services, but the same can be achieved by `systemd-resolved` system (if it is configured to perform both lookups and answer for queries).

`systemd-resolved` searches for files with `.dnssd` extension in `dnssd` subdirectory of `systemd` configuration directory (`/usr/lib/systemd/dnssd`, `/usr/local/lib/systemd/dnssd`, `/run/systemd/dnssd`, or the most convenient `/etc/systemd/dnssd`).

The format of the file is documented in [systemd.dnssd man page](https://www.freedesktop.org/software/systemd/man/systemd.dnssd.html), but as an example following file, saved as `/etc/systemd/dnssd/sshd.dnssd` will register SSH service:

```
[Service]
Name=%H SSH Server
Type=_ssh._tcp
Port=22
```

(`%H` will be replaced with the name of the server).
