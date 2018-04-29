# Yubo Dotfiles

These are all my dotfiles and now a lot more. Want to be able to pacstrap, clone the repo and make install *all* the things.

## Order of things
- Set up partitions (`fdisk` or through native OS)
- Mount `/mnt` and `/mnt/boot`
- `pacstrap -i /mnt base base-devel`
- `arch-chroot /mnt`
- `pacman -S git`
- `git clone https://yubo56@github.com/yubo56/dotFiles.git ~dotfiles`
    - use personal access token from Gmail Tasks
- `make root`
- Reboot, login to new user
- Re-encode keys, after new keybase user is added
- `make linux`

## misc notes
- Screensaver on suspend uses 00xscreensaver on home, xscreensaver.service on Mac
- `karabiner.json` belongs in `~/.config/karabiner/karabiner.json`
    - Rules are applied in reverse precedence
    - Convention: map from only left modifiers, map only to right modifiers
- On Mac, need `hid-apple-patched-git-dkms`, join all configuration options onto
  one line
- ibus needs to be 1.5.14-2 to support unicode input

## fonts
- Under chrome: advanced font settings [DEPRECATED??]:
    - [Script]: [font size], [standard], [serif], [sans], [monospace]
    - Default: 20, Sans, Serif, Sans, Monospace
    - Hangul: 20, Baekmuk Batang, Baekmuk Batang, Baekmuk Batang, Default
    - Simplified Han: 20, UKai CN, UKai CN, UKai CN, Default
- DarkReader: (anything ends in either `.pdf` or `/pdf`)
```
[a-z]*/.*[\.,/]pdf$
```

## Setup on Mac
- `yaourt -S hid-apple-patched-git`, use config file from `.setup/config_manual`

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
    - `ffmpeg -framerate 8 -i '*uz*.png' test.mp4`
- track git-lfs
    - `git lfs track "*.psd"`
- `exec screen -R -s ~yubosu/anaconda3/bin/zsh` in `~/.bash_profile`
    - profile to avoid running on ssh, shell to specify alt shell
- `~/.ssh/authorized_keys`: put public key,
    - `chmod 700 ~/.ssh && chmod 600 ~/.ssh/authorized_keys`
- `/^1?$|^(11+)\1+$/` matches non-prime numbers of 1s
