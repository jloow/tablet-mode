# tablet-mode

Allow users to toggle a convertible laptop between laptop and tablet mode, now adapted for Fedora.

The adaption to Fedora mostly consists in adapting the paths in the `.service` and `.desktop` files as well as specifying the steps needed for installation (which have been adapted from the `PKGBUILD` file in AUR).

## Installation

Clone this repo and `cd` into it. Build and install `tablet-mode`:

```sh
python setup.py build
sudo python setup.py install
```

Copy files to relevant locations and set permissions:

```sh
install -dm 755 "/etc"
install -dm 750 "/etc/sudoers.d"
install -m 640 tablet-mode.sudoers "/etc/sudoers.d/tablet-mode"
install -Dm 644 tablet-mode.service "/usr/lib/systemd/system/tablet-mode.service"
install -Dm 644 laptop-mode.service "/usr/lib/systemd/system/laptop-mode.service"
install -Dm 644 tablet-mode.group "/usr/lib/sysusers.d/tablet-mode.conf"
install -Dm 644 tablet-mode.desktop "/usr/share/applications/tablet-mode.desktop"
```

Add the `tablet` group (unsure if this step is required) and add users to this group:

```sh
sudo groupadd tablet
sudo usermod -G tablet -a USER
```

We also need to install the `evtest` dependency.


## Configuration

The devices to be deactivated in either *tablet* or *laptop* mode must be specified in `/etc/tablet-mode.json`.  
You can specify whether desktop notifications shall be send when changing the mode using the `notify` flag.  
If specified, you can override `sudo` to provide another program that accepts commands to be run as root without a password
by the current user to elevate privileges, such as *doas*.

```json
{
    "tablet": [
        "/dev/input/by-path/platform-i8042-serio-0-event-kbd",
        "/dev/input/by-path/platform-i8042-serio-1-event-mouse"
    ],
    "notify": false,
    "sudo": "/usr/bin/doas"
}
```

## Usage

You must be a member of the group `tablet` to toggle between tablet and laptop mode.  
You can toggle between tablet and laptop mode by running `setsysmode toggle` or use the desktop icon provided with this package.
