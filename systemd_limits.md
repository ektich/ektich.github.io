---
layout: default
---

# Limiting resources using systemd

Tested on Arch Linux with systemd version 243.51-1

To limit resources available to an application (for example Chromium), create a `chromium.slice` file in `/etc/systemd/system/` directory with the following content:
```
[Slice]
MemoryHigh = 2G
MemoryMax = 6G
MemoryAccounting = true
```

The configuration above will set the high limit to 2Gb, and the absolute limit to 6G. According to [systemd documentation](https://www.freedesktop.org/software/systemd/man/systemd.resource-control.html) "Memory usage may go above the (high) limit if unavoidable, but the processes are heavily slowed down and memory is taken away aggressively in such cases". "If memory usage cannot be contained under the (absolute) limit, out-of-memory killer is invoked inside the unit. It is recommended to use MemoryHigh= as the main control mechanism and use MemoryMax= as the last line of defence".

The slice should be started as a root user with `systemctl start chromium.slice`. On Arch Linux, there is no need to use sudo as PolicyKit will ask current user to authenticate, in order to perform privileged tasks.

To start Chromium within the slice run `systemd-run --scope --slice chromium.slice chromium`. Again, run this without using `sudo`, PolicyKit will authenticate current user to perform any privileged tasks needed.

To check the status of the slice run `systemctl status chromium.slice`. It will show all processes running within the slice, and will show total memory usage within the slice:
```
● chromium.slice
   Loaded: loaded (/etc/systemd/system/chromium.slice; static; vendor preset: disabled)
   Active: active since Wed 2019-10-16 16:09:22 IST; 1h 2min ago
    Tasks: 137
   Memory: 623.9M (high: 2.0G max: 6.0G)
   CGroup: /chromium.slice
           └─run-u22782.scope
```


## Running as user with --user
It is possible to create slice within user's session by placing the file into ~/.config/systemd/user directory, but it looks like memory accounting/limiting does not work for user-based slices. It feels like (but requires more testing), that user-based slices get linked with the `user.slice` cgroup, instead of a dedicated cgroup being created for it.
