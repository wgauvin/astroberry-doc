# Joys of Astroberry

## Personal extra packages

Installing the preferred extras, just to feel at home
compared to other POSIX environments.

```
$ sudo apt install silversearcher-ag
```

## Change defaults

### Device name

The default name for the Astroberry device is `astroberry`. This can be accessed on a network using `astroberry.local` but this is neither secure nor helpful if you're in range
of another device running Astroberry. As such use the
following to change the default name.

```
$ sudo nano /etc/devicename
```

Change the device name, save and exit via `^O` and `^X`.

A reboot is needed to take effect.

```
$ sudo reboot
```

After the reboot the device will be accessible via `<devicename>.local`. Where `<devicename>` is the new
device name.

### Change User Login

The default user is `astroberry`. It would be nice to change
this username but the name of `astroberry` is in a lot of
configuration. Until a safe way of changing this is found
it is not recommended to change this.

### Passwords

By default `astroberry` comes with default passwords for the
users `pi` and `astroberry`. Theses passwords need to be reset so the Pi is more secure.

```
$ sudo passwd pi
$ sudo passwd astroberry
```

#### VNC Password

The VNC password is **not** linked to the user password. As
such the password of this needs to change separately. To 
change the password run:

```
$ sudo vncpasswd -service
```

Not that for clients, like `noVnc` that only the first 8
characters will be used.

## GPSD

I have a **USGlobalsat BU-353-S** USB GPS dongle. This 
should have worked out of the box, however, it didn't. I 
needed to do some searching but the best result I have is 
from following the second answer [Ask Ubuntu - How can I get the GPS receiver BU-353 working on Ubuntu?](https://askubuntu.com/a/952242).

Basically you need to change the `/etc/default/gpsd` to:

* disable `gpsd` from starting at boot time. This could be because the USB hasn't loaded before the daemon has run
* use hot plugging for the USB device
* mention what USB device it should be, multiple USB devices will cause and issue. Will deal with that when it happens.
* provide some `gpsd` options.

My config file is:

```
# Default settings for the gpsd init script and the hotplug wrapper.

# Start the gpsd daemon automatically at boot time
START_DAEMON="false"

# Use USB hotplugging to add new USB devices automatically to the daemon
USBAUTO="true"

# Devices gpsd should collect to at boot time.
# They need to be read/writeable, either by user gpsd or the group dialout.
DEVICES="/dev/ttyUSB0"

# Other options you want to pass to gpsd
GPSD_OPTIONS="-n -G -b"
```

## Wi-Fi

### Wi-Fi HotSpot

The default Wi-Fi HotSpot is `astroberry` and the password
to that hotspot is `astroberry`. This again is not a secure
setup. The following is done to change the hotspot SSID and
password.

#### Change via command line

```
$ sudo nano /etc/NetworkManager/system-connections/HotSpot.nmconnection
```

Change at least the following fields: `ssid`, `psk`

The `psk` is the password and is in cleartext. This is a
limitation of the Ubuntu's NetworkManager. What is needed
is to make sure the default value is changed. Also changing
the `ssid` needs to be changed as the way WiFi works is that
multiple networks with the same name act as the same so
change this!

To save and exit use `^O` and `^X`.

#### Change via GUI

TODO - write this up

### Connect to Wi-Fi network

TODO - use GUI to set up 

## Sudo access

Default `sudo` access for the `astroberry` user is to put
the password in. To make life easier the user can be put
into the no password group for `sudo`

```
$ sudo nano /etc/sudoers.d/010_astroberry-nopasswd
```

And add the following line:

```
astroberry ALL=(ALL:ALL) NOPASSWD:ALL
```

## SSH access

Raspberry Pi's enable `ssh` access but to allow for a
passwordless ssh a known public ssh key is need to be
provided to the user account.

```
$ mkdir ~/.ssh
```

Create an `~/.ssh/authorized_keys` and add public keys that 
will have access to the Pi.

Change the file permissions to be what is needed for OpenSSH.

```
$ chmod 700 ~/.ssh
$ chmod 600 ~/.ssh/authorized_keys
```
