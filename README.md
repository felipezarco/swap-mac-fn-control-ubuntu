
### How

```bash
sudo apt install git dkms
```

```bash
Clone this repo and go into the source code directory:
```

```bash
git clone https://github.com/free5lot/hid-apple-patched
cd hid-apple-patched
```

Install module:
```bash
sudo dkms add .
sudo dkms build hid-apple/1.0
sudo dkms install hid-apple/1.0
```
Then, create file `/etc/modprobe.d/hid_apple.conf`. The following configuration emulates a standard PC layout:
```
options hid_apple fnmode=2
options hid_apple swap_fn_leftctrl=1
options hid_apple swap_opt_cmd=1
options hid_apple rightalt_as_rightctrl=1
options hid_apple ejectcd_as_delete=1
```
Finally, apply the new config file:
```bash
sudo update-initramfs -u
```
To (re-)load the module for immediate use, run
```bash
sudo modprobe -r hid_apple; sudo modprobe hid_apple
```
in one go (since the first command will disable your Apple keyboard). Alternatively, run `sudo reboot`, and the new module should be loaded on reboot.

### Configuration

Permanent configuration is done in file `/etc/modprobe.d/hid_apple.conf`. The format is one option-value pair per line, like `swap_fn_leftctrl=1`. After writing to the file, do `sudo update-initramfs -u` and reboot.
Temporary configuration (applies immediately but is lost after rebooting) is possible by writing to virtual files in `/sys/module/hid_apple/parameters/`, like `echo 1 | sudo tee /sys/module/hid_apple/parameters/swap_fn_leftctrl`.

These options are from the original mainstream `hid-apple` module:
- `fnmode` - Mode of top-row keys
  - `0` = disabled
  - `1` = normally media keys, switchable to function keys by holding Fn key (Default)
  - `2` = normally function keys, switchable to media keys by holding Fn key
- `swap_opt_cmd` - Swap the Option (\"Alt\") and Command (\"Flag\") keys
  - `0` = as silkscreened, Mac layout (Default)
  - `1` = swapped, PC layout
- `swap_fn_leftctrl` - Swap the Fn and left Control keys
  - `0` = as silkscreened, Mac layout (Default)
  - `1` = swapped, PC layout
- `iso_layout` - Enable/Disable hardcoded ISO-layout of the keyboard. Possibly relevant for international keyboard layouts
  - `0` = disabled, 
  - `1` = enabled (Default)

And these options were added in the patched version:
- `rightalt_as_rightctrl` - Use the right Alt key as a right Ctrl key
  - `0` = as silkscreened, Mac layout (Default)
  - `1` = swapped, PC layout
- `ejectcd_as_delete` - Use Eject-CD key as Delete key, if available
  - `0` = disabled (Default)
  - `1` = enabled
- `lock_as_delete` - Use Lock key as Delete key, if available
  - `0` = disabled (Default)
  - `1` = enabled
- `swap_fn_f13_insert` - Swap the Fn and f13 keys, making Fn Insert and f13 Fn. For people who need Insert
  - `0` = disabled (Default), 
  - `1` = enabled

Note: `swap_fn_f13_insert` will be ignored if `swap_fn_leftctrl` is activated.

### Warning regarding Secure Boot (on non-Apple computers)

Some distributions, including Ubuntu 16.04, require that all modules are signed if Secure Boot is enabled. This breaks all third-party modules. There are various work-arounds, the easiest of which is to disable secure boot. This is currently not an issue on Apple computers, because Apple firmware does not support Secure Boot. See [issue #23](https://github.com/free5lot/hid-apple-patched/issues/23).

### Links and mentions
- [**ArchWiki** (documentation wiki of **Arch Linux**) - Apple Keyboard - Swap the Fn key and Left Ctrl key](https://wiki.archlinux.org/index.php/Apple_Keyboard#Use_a_patch_to_hid-apple) (added by @Aetf)).
- [Official documentation of **openSUSE** - Installation on a Mac](https://en.opensuse.org/SDB:Installation_on_a_Mac#Other_things) (added by Cornertwist)
- [Official documentation of **Ubuntu** - AppleKeyboard - Mapping keys](https://help.ubuntu.com/community/AppleKeyboard#Mapping_keys_.28Insert.2C_Alt.2C_Cmd.2C_etc..29) (added by @Aetf).

