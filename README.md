
# reMarkable command-line utils

- [SSH setup](#SSH-setup)
- [Install remarkable_entware]()
- [Install rsync via opkg]()
- [Use rsync and crontab to run backups automatically]()

## SSH setup

1. On your reMarkable device, navigate to `Menu > Settings > About`, then under the `Copyrights and Licenses` tab, scroll down the `General Information` text. Right after the paragraph titled "GPLv3 Compliance", there will be the username (`root`), password and IP address needed for `SSH`.

2. Add the following lines to your `~/.ssh/config`

```
Host remarkable
  Hostname <IP_ADDRESS_YOU_FOUND_OUT>
  User root
  ForwardX11 no
  ForwardAgent no    
```

3. Have a public key ready at `~/.ssh/`

4. Run `ssh-copy-id remarkable`

5. Type your device password. You should see an output similar to:

```
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/Users/<USER>/.ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
root@192.168.7.77's password:

Number of key(s) added:        1.
```

6. Let's test it out. Type `ssh remarkable`:

```sh
ssh remarkable
  ｒｅＭａｒｋａｂｌｅ
  ╺━┓┏━╸┏━┓┏━┓   ┏━╸┏━┓┏━┓╻ ╻╻╺┳╸┏━┓┏━┓
  ┏━┛┣╸ ┣┳┛┃ ┃   ┃╺┓┣┳┛┣━┫┃┏┛┃ ┃ ┣━┫┗━┓
  ┗━╸┗━╸╹┗╸┗━┛   ┗━┛╹┗╸╹ ╹┗┛ ╹ ╹ ╹ ╹┗━┛
  reMarkable: ~/
```

Voilà! We're in.

### Tweak: auto sleep off

Turn off auto sleep to prevent the device from sleeping.

## Install remarkable_entware

Use the awesome [reMarkable Entware](https://github.com/Evidlo/remarkable_entware) scripts to install `rsync` and other utilities to your reMarkable device:

```sh
cd remarkable_entware

scp entware_install.sh remarkable:

ssh remarkable

./entware_install.sh
  [...]
  Configuring entware-upgrade.
  Upgrade operations are not required.
  Configuring opkg.
  Configuring zoneinfo-europe.
  Configuring zoneinfo-asia.
  Configuring libstdcpp.
  Configuring entware-release.
  Configuring findutils.
  Configuring entware-opt.

  Info: Congratulations! Entware has been installed.
  [...]

# Add /opt/bin & /opt/sbin to PATH
echo PATH=$PATH:/opt/bin:/opt/sbin >> ~/.bashrc

# Check
cat ~/.bashrc
  [...]
  PATH=/usr/local/bin:/usr/bin:/bin:/usr/local/sbin:/usr/sbin:/sbin:/opt/bin:/opt/sbin

# Double check
source ~/.bashrc
echo $PATH
  /usr/local/bin:/usr/bin:/bin:/usr/local/sbin:/usr/sbin:/sbin:/opt/bin:/opt/sbin
```

## Install rsync via opkg

```sh
ssh remarkable

opkg install rsync
  [...]

rsync --version
  rsync  version 3.1.3  protocol version 31
  Copyright (C) 1996-2018 by Andrew Tridgell, Wayne Davison, and others.
  Web site: http://rsync.samba.org/
  Capabilities:
      64-bit files, 64-bit inums, 32-bit timestamps, 64-bit long ints,
      no socketpairs, hardlinks, symlinks, IPv6, batchfiles, inplace,
      append, ACLs, xattrs, iconv, symtimes, prealloc

  rsync comes with ABSOLUTELY NO WARRANTY.  This is free software, and you
  are welcome to redistribute it under certain conditions.  See the GNU
  General Public Licence for details.
```

Check out what packages are available here: http://bin.entware.net/armv7sf-k3.2/Packages.html.

## Use rsync and crontab to run backups automatically

First, make sure you have `rsync` installed on your machine and that its protocol version is compatible with the one you have just installed in your reMarkable.

```sh
# edit rsync-remarkable.TEMPLATE.sh according to your needs

# save it as rsync-remarkable.sh

# test it out
source rsync-remarkable.sh
```

Then, add it to your `crontab`:

```sh
crontab -e

# add a new line, like
0 9 * * * source /PATH/TO/rsync-remarkable.sh >> /PATH/TO/rsync-remarkable.log 2>&1

# if you need a refresher: https://crontab.guru
```

# Credits and Acknowledgements

- The team who wrote and maintains [Entware](https://github.com/Entware/Entware)

- [Evan Widloski](http://evan.widloski.com) who wrote [reMarkable Entware](https://github.com/Evidlo/remarkable_entware)

- [JBB](https://jbbgameich.github.io) who put together [rsync-static](https://github.com/JBBgameich/rsync-static). He/she started the [reddit thread about rsync](https://www.reddit.com/r/RemarkableTablet/comments/atkrs7/rsync_on_remarkable/) that got me going

For more reMarkable resources, check out the great [awesome-reMarkable](https://github.com/reHackable/awesome-reMarkable) and [remarkablewiki.com](https://remarkablewiki.com/).

# Disclaimers

This is free open source software from hobby project of an enthusiastic reMarkable user.

There is no warranty whatsoever. Use it at your own risk.

> The author(s) and contributor(s) are not associated with reMarkable AS, Norway. reMarkable is a registered trademark of reMarkable AS in some countries. Please see https://remarkable.com for their products.