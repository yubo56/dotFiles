# Yubo Dotfiles

These are all my dotfiles and now a lot more. Want to be able to pacstrap,
clone the repo and make install all the things.

## Order of things
- UEFI + set up partitions (`fdisk` or through native OS)
    - Note: May need to write GPT + create EFI partition
    - `mkfs.ext4 /dev/sdaX`
- Mount `/mnt`
- `pacstrap -i /mnt base base-devel linux linux-firmware`
- Mount `/mnt/boot`
- `genfstab -U /mnt > /mnt/etc/fstab`
- `bootctl --esp-path=/mnt/boot install`, then create entries + `loader.conf`
    - Sample entry
```
title Arch Linux Macbook
linux /vmlinuz-linux-macbook
initrd /initramfs-linux-macbook-fallback.img
options rw root=UUID=<...>
```
- `/mnt/etc/locale.conf` edits, `echo KEYMAP=dvorak > /mnt/etc/vconsole.conf`
- `arch-chroot /mnt`
- `locale-gen`
- `pacman -S git dhcpcd zsh gvim`
    - gvim since I'll want +X11 support later anyway, though it's much larger
- `git clone https://yubo56@github.com/yubo56/dotFiles.git ~/dotfiles`
    - can comment out `install_wpa_supplicant` if have lan connection
    - use personal access token from Gmail Tasks
- `make root`

- Reboot, login to new user
- `sudo dhcpcd` or connect to wifi
- clean `/etc/pacman.d/mirrorlist`
- `make linux`

## Setup on Mac (linux)
- `yaourt -S hid-apple-patched-git-dkms`, use config file from `.setup/config_manual`
- To get IGD:
    - `apple_set_os`, add bootloader entry
    - kernel param `mem_encrypt=off`
    - may need to compile with kernel params
```
CONFIG_EXTRA_FIRMWARE="radeon/verde_ce.bin radeon/verde_mc.bin radeon/verde_me.bin radeon/verde_pfp.bin radeon/verde_rlc.bin radeon/verde_smc.bin radeon/TAHITI_uvd.bin"
CONFIG_EXTRA_FIRMWARE_DIR="/lib/firmware"
```

## misc notes
- Screensaver on suspend uses 00xscreensaver on home, xscreensaver.service on Mac
- `karabiner.json` belongs in `~/.config/karabiner/karabiner.json`
    - Other than karabiner.json, just remap capslock -> control in system prefs
    - Rules are applied in reverse precedence
    - Convention: map from only left modifiers, map only to right modifiers
- ibus needs to be 1.5.14-2 to support unicode input
- `sudo sysctl kernel/unprivileged_userns_clone=1` for Brave namespaces
- Bluetooth headset: `bluez bluez-utils pulseaudio-bluetooth pulseaudio-alsa`
    - Pulseaudio asound.conf, `/etc/pulse/defaultpa`:
        `load-module module-switch-on-connect`
    - `bluetoothctl`:
        `power on; agent on; scan on; pair <...>; trust <...>; connect <...>`
- transparency: `sudo pacman -S xcompmgr transset-df`
    - `devilspie` used to be used to match windows on open, broken now
- droidcam `/etc/modprobe.d/droidcam.conf` resolution, `/opt/urserver/urserver`
  to start Unified Remote
    - `yaourt -S droidcam adb unified-remote-server`
- For audio, need `~/.asoundrc`, check which sound card via alsamixer, then
```
pcm.!default {
    type hw
    card 1
}
ctl.!default {
    type hw
    card 1
}
```
    - When bluetooth is connected, need to use pulseaudio, default is in
      `/etc/asound.conf`, e.g.
```
pcm.!default {
  type pulse
  fallback "sysdefault"
  hint {
    show on
    description "Default ALSA Output (currently PulseAudio Sound Server)"
  }
}

ctl.!default {
  type pulse
  fallback "sysdefault"
}
```
- if dmenu is randomly crashing, `strace dmenu_run`, but probably need to git
  clone and re-install (4.9-1 seems to have this problem)

# Misc commands to save
- resizing image while removing virtual canvas
    - `convert in.png -crop nxn+0+0 +repage out.png`
- resizing PDF with ghostscript
```
    ~/Downloads$ gs \
     -o c.pdf \
     -sDEVICE=pdfwrite \
     -dDEVICEWIDTHPOINTS=720 -dDEVICEHEIGHTPOINTS=1280 \
     -dFIXEDMEDIA \
     -dPDFFitPage \
     -dCompatibilityLevel=1.4 \
      b.pdf
```
- combining pdf pages
    - `pdftk a.pdf b.pdf cat output c.pdf`
- ffmpeg image -> video
    - `ffmpeg -framerate 8 -pattern_type glob -i '*uz*.png' test.mp4`
- `exec screen -R -s /usr/bin/zsh` in `~/.bash_profile`
    - profile to avoid running on ssh, shell to specify alt shell
- `~/.ssh/authorized_keys`: put public key,
    - `chmod 700 ~/.ssh && chmod 600 ~/.ssh/authorized_keys`
- `/^1?$|^(11+)\1+$/` matches non-prime numbers of 1s
- IBus errors:
    - `Config value [engine/hangul:on-keys] does not exist.` and for off-keys
        - `dconf write /desktop/ibus/engine/hangul/on-keys \'\'`
        - `dconf write /desktop/ibus/engine/hangul/off-keys \'\'`
    - IBus menus won't show, frozen
        - `git clone`, ensure have `gnome-common` installed, checkout 1.5.18
        - `./autogen.sh`
        - recompile w/ `ibusproperty.c:ibus_property_update()` line
          `set_visible` commented out
- To split/join files, `split -b 200M a.tar.gz "a.tar.gz.part"`, can just cat
  them all together to join
- On mac, set hostname: `sudo scutil --set HostName <...>`
