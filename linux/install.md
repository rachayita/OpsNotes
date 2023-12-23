# Install

## pacman
```shell
sudo pacman -S fd base-devel git meld neovim firefox mpv vifm youtube-dl feh stow \
alacritty chromium sxiv gdb vimb ueberzug xclip neomutt font-manager syncthing ufw gufw \
lxappearance cups netcat zathura unzip shotgun zbar tig pass  xdotool  ntfs-3g typst \
skim starship eza bat pass-otp nss-mdns system-config-printer jless htop neofetch \
tcpdump hyperfine zathura-pdf-poppler cronie dmenu gparted thunar xorg xorg-xinit \
bluez bluez-util blueman pulseaudio pavucontrol dunst networkmanager fzf nano \
arch-install-scripts wget
```
- lapce: rust based code editor
- start NetworkManager, Bluetooth, ufw and cronie services by systemctl
- browser addon: dark reader, https everywhere, vimium, ublock origin, firfox container


## paru
- paru-bin, stint(cmd line color picker), mutt-wizard-git, simple-mtpfs-git, obinskit
- brother-mfc-l2700dw, 7-zip-full (7z), vscodium, dmenufm, linux-wifi-hotspot

## cargo
powerline-rs du-dust racer  diskonaut paru nethoscope broot hmm fd-find

## ~/.local/share/fonts
> ``` fc-cache -vf ```
- [JetBrains Mono](https://www.jetbrains.com/lp/mono/)
- [Font Awesome](https://fontawesome.com/download)
- [iosevka](https://typeof.net/Iosevka/)
- [iosevka nerd font: fonts for starship](https://www.nerdfonts.com/font-downloads)
- Bizcat:  an 8x16 bitmap font

## [PlugInstall](https://github.com/junegunn/vim-plug)
```shell
sh -c 'curl -fLo "${XDG_DATA_HOME:-$HOME/.local/share}"/nvim/site/autoload/plug.vim \
--create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim'
```

## printer setup
- install nss-mdns
- Start/restart avahi-daemon.service and cups.service
- Replace the hosts line in /etc/nsswitch.conf with
```
hosts: mymachines mdns_minimal [NOTFOUND=return] resolve [!UNAVAIL=return] files myhostname dns
```
- [port for cups network printing](https://www.cups.org/doc/firewalls.html)
   - 631/tcp  ipp/ipps
   - 161/udp  snmp
   - 443/tcp  ipps
- [localhost](http://localhost:631)

## qemu
- install qemu virt-manager dnsmasq vde2
- select qemu-full
- sudo systemctl enable --now libvirtd.service
- sudo usermod -aG libvirt $USER

### bluetooth settings
- [blutooth auto power on after boot/resume/before login](https://wiki.archlinux.org/title/Bluetooth)
- ``` /etc/bluetooth/main.conf ```
```
[Policy]
AutoEnable=true
```
### [rust analyzer](https://github.com/rust-lang/rust-analyzer/releases)
- download rust-analyzer in ~/.local/bin and make it executable

## optinal
- [vim sugar for the UNIX shell commands](https://github.com/tpope/vim-eunuch)
- [spacer](https://github.com/samwho/spacer): cli to insert spacers when cmd output stops
- https://github.com/polybar/polybar-scripts.git    bspwm/scripts
- https://github.com/cirala/vifmimg fir vifm image preview


# NOTES

## rust
- `rustup doc`
- gnu debugger(gdb) for rust

## s/w
- TestDisk: recover deleted files
- gitbucket
- imcompressor: image compressor
- dumpcap with wireshark
- alsamixer: for vol

## ss: port check
- > `sudo watch ss -tulpn`

## set screen resolution
- arandr: graphical resolution
- > `xrandr` shows current screen resolution
- > ``` xrandr -q ```
- > ``` xrandr -d :0 --output <OUTPUT> --mode 1360x768 ```
-> or ``` xrandr -s 1360x768 ```

## arch graphical driver
> xf86-video-vmware

## enable tap click
- https://www.reddit.com/r/archlinux/comments/86g4ef/how_to_enable_tap_click/
- 40-libinput.conf or 30-touchpad.conf

```
Section "InputClass"
        Identifier "libinput touchpad catchall"
        MatchIsTouchpad "on"
        MatchDevicePath "/dev/input/event*"
        Option "Tapping" "True"
        Option "TappingDrag" "True"
        # Option "ScrollMethod" "Edge"
        Option "ScrollMethod" "TwoFinger"
        Option "AccelProfile" "linear"
        Option "AccelSpeed" "1"
        Option "DisableWhileTyping" "False"
        Driver "libinput"
EndSection
```

## commands
- `inxi -F`sys info
- `ripgrep` rg for search in docs
- axel
- `entr` run arbitrary commands when files change
- `xev` to know key number
- `some-command | xclip -sel clip` copy to clipboard
- `curl wttr.in/city,country`
- `curl getnews.tech/<topic>`
- `find "4DOS" wikipedia.txt | tee 4DOS.txt | sort > 4DOSsorted.txt`
- `exec xmodmap -e "keysym Menu = Super_R" 2>/dev/null &` map menu key to super
- `cat ~/.ssh/id_rsa.pub | ssh admin@server "sudo tee -a /root/.ssh/authorized_keys2 > /dev/null"`
- `git config --local status.showUntrackedFiles no` donot show untracked files locally
- `lshw -c video` cmd to show video driver
- `netstat -lntpu` list open network ports and the processes
- `curl ifconfig.me` get your ip
- pacman - get list of packages installed by user>
  > `comm -23 <(pacman -Qqett | sort) <(pacman -Qqg base -g base-devel | sort | uniq)`
- `xrandr --output eDP-1 --brightness 0.5` adjust brightness
- `dd if=file.iso of=/dev/sdb bs=512k status=progress` create bootable usb
- `gtk-update-icon-cache -f -t ~/.icons/<theme_name>` run to update the icon cache
- `sudo mkfs.vfat /dev/sdb1`   to format usb drive with FAT32
- terminal background blue color #1e1e1e
- freelance contract: https://www.ashedryden.com/blog/contract
- `rsync -avr source destination`   no slash at path end
- `ln -s file link`
- if ~/.pki isn't present then $XDG_DATA_HOME/pki/nssdb will be used instead
  - no sources mentioned for environmental variable
- `journalctl -u cronie.service`

## 5 sec lag problem with intel gpu
- https://github.com/qutebrowser/qutebrowser/issues/4641
- https://github.com/qutebrowser/qutebrowser/issues/4809
- If not, create a new file in /etc/X11/xorg.conf.d/ called 20-modesetting.conf or whatever and add this:
```
Section "Device"
	Identifier 	"Intel Graphics"
	Driver		"modesetting"
	Option		"AccelMethod" "glamor"
	Option		"DRI"	"2"
EndSection
```

## mount android phone
- > `simple-mtpfs -l`
- > `simple-mtpfs --device 1 cell/`
- to unmount:
- > `fusermount -u cell/`

## install font manually:
- https://ehellman.github.io/blog/2013/04/16/manually-install-a-font-in-arch-linux/
- Unpack the file and move it to the shared fonts folder
> `mv font.ttf /usr/share/fonts/  or  ~/.local/share/fonts`
- Or if you want to install the font for a single user, move it to the .fonts directory in the home folder.
> `mv font.ttf ~/.fonts/`
- You might also have to update /etc/X11/xorg.conf or /etc/xorg.conf with the new directory
- Search for FontPath to find the correct location within the file to add your new path.
- Now, reload the font cache:
> `fc-cache -vf`

## connect AnnePro2 P1 via bluetooth
- press F2 + 1 for 5 sec
- > $ bt-device -l
- > $ bt-device -c <mac>

## Try and redefine the ssh url for remote origin:
> `git remote set-url origin ssh://git@github.com/username/<url-repo>.git`
> `ssh -T git@github.com`

## vim
- `set wildignore+=**/node_modules/**` ignore files or folders
- `:w !sudo tee "%"` root priviledge for saving doc in vim
- `:5,17s/^/#/` comment line 5-17
- `:%s/^/#/` comment all lines

## nvimdiff
- ]c          - next difference
- [c          - previous difference
- do          - diff obtain
- dp          - diff put
- zo          - open folded text
- zc          - close folded text
- zR          - unfold all lines
- zr          - open all folds
- zm          - close all folds
- :diffoff!   - to end diff mode
 :syn off     - to remove colors
- :diffupdate - to remove leftover spacing issues)
- :only       - once you’re done reviewing all conflicts, this shows only the middle/merged file
- :set diffopt+=iwhite      - to avoid whitespace comparison
- :set diffopt+=context:0   - you can influence how many identical lines are kept around changes (default: 6 lines above and below) via the context value of the diffopt option. So, to completely fold all identical lines
- :windo diffthis - to begin diffing on all visible windows which executes :diffthis on each window.
- default identifiers that can be selected using diffget
  - LO local master copy
  - RE remote master to be merged
  - BA common ancestor of remote and local changes.
- :diffupdate - re-scan the files for differences
  - choose which version you want to keep with :diffget //2 or :diffget //3
  - the //2 and //3 are unique identifiers for the target/master copy and the merge/branch copy file names
- If you were trying to do a git pull when you ran into merge conflicts, type `git rebase –continue`

## mutt-wizard
> `mw -a user@gmail.com -u user -n user -i imap.gmail.com -I 993 -s smtp.gmail.com -S 465`

## gen ssh keys
- mkdir ~/.ssh
- chmod 700 ~/.ssh
- ssh-keygen -t rsa

## base64
- echo -n 'data' | base64 c2NvdHRsaW51eC5jb20gcm9ja3MK
- echo -n c2NvdHRsaW51eC5jb20gcm9ja3MK | base64 -d data

## .ssh/config format for multiple keys
```
Host ANY_NAME
HostName HOST or IP
User USER_NAME
IdentityFile "PATH TO id_rsa FILE"
```
## change pinentry permanently
- `vim ~/.gnupg/gpg-agent.conf`
```
pinentry-program /usr/bin/pinentry-tty
```
## [autologin](https://wiki.archlinux.org/title/Getty#Prompt_only_the_password_for_a_default_user_in_virtual_console_login)
> `sudo mkdir /etc/systemd/system/getty@tty1.service.d`
> `sudoedit /etc/systemd/system/getty@tty1.service.d/autologin.conf`
- change it to your username
```
[Service]
ExecStart=
ExecStart=-/sbin/agetty -o '-p -f -- \\u' --noclear --autologin username %I $TERM
```

## move tabs at bottom in firefox
1. Find your Firefox profile directory
    From the Help menu, select More Troubleshooting Information. The Troubleshooting Information tab will open.
    Under the Application Basics section next to "Profile Folder", click "Show in Finder". A window will open that contains your profile folder.
2. Create a chrome/ directory in the profile directory.
3. Create userChrome.css custom css rules file in chrome/ directory
    Copy the contents of below file (from @jonhoo's configuration files on github) into a file called userChrome.css in the newly created chrome directory.
    https://raw.githubusercontent.com/jonhoo/configs/master/gui/.mozilla/firefox/chrome/userChrome.css
4. Enable custom css in firefox about:config settings
    type "about:config" into Firefox's URL bar
    Click "Accept the Risk and Continue"
    In the settings search bar, look for toolkit.legacyUserProfileCustomizations.stylesheets, and toggle the boolean to true
    Restart Firefox
