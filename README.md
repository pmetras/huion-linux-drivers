# User Space Linux Drivers for Huion Tablets


## Table of Contents

- [Features](#features-)
- [Usage](#usage-)
- [Requirements](#requirements-)
  - [Dependencies](#dependencies-)
  - [Xorg extra code](#xorg-extra-code-)
- [Troubleshooting](#troubleshooting-)
- [Config Examples](#config-examples-)
  - [Multi-Monitor](#multi-monitor-)
  - [Shortcuts](#shortcuts-)
- [Ubuntu Example for Huion Kamvas Pro 12"](#ubuntu-example-for-gt-116-)
- [Help Welcomed](#help-welcomed-)
  - [To do](#to-do-)

## Features [↑](#table-of-contents "Back to TOC")

 * Supports multiple tablet models
 * Precise cursor positioning
 * Full pressure sensitivity
 * Both stylus buttons
 * Compatible with multi-monitor setups
 * Customizable buttons and scrollbar shortcuts
 * Multiple sets of shortcuts
 * Optional desktop notifications
 * Versatile configuration file


## Usage [↑](#table-of-contents "Back to TOC")

 * Follow the requirements: Install the dependencies and the xorg extra code.
 * Download this repository (You only need `huion-tablet-driver.py` and `config.ini`).
 * Edit `config.ini` to match your tablet, multi-monitor setup and desired shortcuts.
 * Run `sudo ./huion-tablet-driver.py` (needs superuser privileges)


## Requirements [↑](#table-of-contents "Back to TOC")

### Dependencies [↑](#table-of-contents "Back to TOC")

 * [python](https://www.python.org/) version 3.5 or greater
 * [uclogic-tools](https://github.com/DIGImend/uclogic-tools) ([read why][2])

    ```
    # Installation from source: install dependencies, clone, compile & install

    $ sudo apt install make automake gcc pkg-config libusb-1.0-0-dev  # For ubuntu

    git clone https://github.com/DIGImend/uclogic-tools
    cd uclogic-tools
    autoreconf -i -f && ./configure --prefix=/usr/local/ && make
    sudo make install
    ```

 * [xinput](https://wiki.archlinux.org/index.php/Xinput)
 * [evdev](https://wiki.gentoo.org/wiki/Evdev)
 * [python-evdev](https://github.com/gvalkov/python-evdev)
 * [pyusb](https://walac.github.io/pyusb/)
 * [numexpr](https://github.com/pydata/numexpr)
 * [xdotool][7] (optional, for button shorcuts)
 * [notify-send][8] (optional, for desktop notifications)
 * [xrandr][9] (optional, for monitor configuration) (and [arandr][10])

[2]: https://github.com/benthor/HuionKamvasGT191LinuxDriver/issues/1#issuecomment-351207116
[7]: http://www.semicomplete.com/projects/xdotool/
[8]: https://wiki.archlinux.org/index.php/Desktop_notifications
[9]: https://wiki.archlinux.org/index.php/xrandr
[10]: https://christian.amsuess.com/tools/arandr/


Install packages in Archlinux:

```
$  pacman -S xorg-xinput xf86-input-evdev python-evdev python-pyusb xdotool \
libnotify xorg-xrandr arandr python-numexpr
```

Install packages in Ubuntu:
```
$ sudo apt install xinput xserver-xorg-input-evdev python3-evdev python3-usb \
xdotool libnotify-bin arandr python3-numexpr
```

### Xorg Extra Code [↑](#table-of-contents "Back to TOC")

You will likely also need to add some code to the Xorg server.
Create a new file in` /etc/X11/xorg.conf.d/evdev-tablet.conf` with the following content:

```
Section "InputClass"
	Identifier "evdev tablet catchall"
	MatchIsTablet "on"
	MatchDevicePath "/dev/input/event*"
	Driver "evdev"
EndSection
```

## Troubleshooting [↑](#table-of-contents "Back to TOC")

### My tablet doesn't provide any inputs despite all my debugging efforts

Maybe you're connecting the table through a USV hub, or USB docking station? This is known to have caused problems in the past. Try plugging the tablet directly to the computer.

### /usr/local/bin/uclogic-probe: not found

You eiher need to compile uclogic binaries (see [Dependencies](#dependencies-)), or they are installed in a different location. For example Debian 10 automatically installs them under `/usr/bin/`. Try updating the path in the `config.ini` file, which by default is: `uclogic_bins = /usr/local/bin`.


## Config Examples [↑](#table-of-contents "Back to TOC")

### Multi-Monitor [↑](#table-of-contents "Back to TOC")

If you have a multi-monitor setup, edit your copy of `config.ini`
with the correct values for your particular setup.

```
# Multi Monitor Configuration
enable_multi_monitor  = true
enable_xrandr         = false
current_monitor_setup = [monitor_3]
```

You'll have to customize your current monitor setup, by modifying one of the
existing examples in the section 3 of the `config.ini` file.

[More information about multiple monitors in the wiki](https://github.com/joseluis/huion-linux-drivers/wiki/Multi-Monitor)


### Shortcuts [↑](#table-of-contents "Back to TOC")

To customize the shortcuts associated with the buttons and the scrollbar,
edit the file `config.ini`, and use the xdotool syntax for the buttons actions.

First, assign the menu you're going to use as the starting menu.

### Example with a Single Buttons Menu

```
start_menu = [menu_simple_10b]

[menu_simple_10b]

# upper buttons
b0 = key Tab           # hide interface
b1 = key r             # rect select (gimp) & pick layer (krita)
b2 = key ctrl+x        # cut
b3 = key ctrl+c        # copy
b4 = key ctrl+v        # paste

# scrollbar
su = click 4           # mouse wheel up
sd = click 5           # mouse wheel down

# lower buttons
b5 = key ctrl+z        # undo
b6 = key ctrl+y        # redo (gimp)
b7 = key ctrl+shift+z  # redo (krita)
b8 = key 4             # turn left (krita)
b9 = key 6             # turn right (krita)
```

[See an example with multiple menus in the wiki](https://github.com/joseluis/huion-linux-drivers/wiki/Buttons-Shortcuts#12-example-with-multiple-menus)

## Ubuntu Example for GT-116 [↑](#table-of-contents "Back to TOC")
*Tested on Ubuntu LTS 16.04*

First, adapt the parameters in `config.ini` to your tablet model. In case of a Huion Kamvas Pro 12" (GT-116), you could change the following options to use the 5 buttons menu for Gimp and Krita.
```ini
current_tablet = [tablet_gt116]
...
enable_multi_pointer = true
...
start_menu = [menu_5b_multi]
```

After doing all the pre-requisites listed above, copy the 3 files `TabletDriver`, `config.ini` and `python-tablet-driver.py` into `/usr/local/huion-linux-driver`.
```bash
$ sudo mkdir /usr/local/huion-linux-driver
$ sudo cp config.ini /usr/local/huion-linux-driver
$ sudo cp python-table-driver.py /usr/local/huion-linux-driver
$ sudo cp TabletDriver /usr/local/huion-linux-driver
```

Create a symbolic link to the launcher file `TabletDriver`:
```bash
$ sudo ln -s /usr/local/huion-linux-driver/TabletDriver /usr/local/bin/TabletDriver
```

Then you can launch the driver:
```bash
$ sudo TableDriver
```
The console screen lists the configuration options and then displays the current active menu. You can change the active menu using the lower left button. This configuration creates 2 pointers: one for the mouse that you can keep on your primary screen and one for the tablet pencil that you use to draw on the tablet. The mouse can move on both screens: if you loose it, it could be on the tablet screen.

## Help Welcomed [↑](#table-of-contents "Back to TOC")

Please take a look to the open issues to see if you can help with something. Thanks.

### To do [↑](#table-of-contents "Back to TOC")

  - [ ] make tilt work [#33]
  - [ ] use udev rules instead of sudo [#31]

