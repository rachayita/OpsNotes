# rust
- `rustup doc`
- gnu debugger(gdb) for rust

# s/w
- TestDisk: recover deleted files
- gitbucket
- imcompressor: image compressor
- dumpcap with wireshark
- alsamixer: for vol

# ss: port check
- > `sudo watch ss -tulpn`

# set screen resolution
- arandr: graphical resolution
- > `xrandr` shows current screen resolution
- > `xrandr -q`
- > `xrandr -d :0 --output <OUTPUT> --mode 1360x768`

# arch graphical driver
> xf86-video-vmware 

# enable tap click
- https://www.reddit.com/r/archlinux/comments/86g4ef/how_to_enable_tap_click/
- 40-libinput.conf or 30-touchpad.conf
 
```
Section "InputClass"
	Identifier "libinput touchpad catchall"
	MatchIsTouchpad "on"
	MatchDevicePath "/dev/input/event*"
	Option "Tapping" "True"
	Option "TappingDrag" "True"
	Option "ScrollMethod" "Edge"
	Option "AccelProfile" "linear"
	Option "AccelSpeed" "1"
	Option "DisableWhileTyping" "False"
	Driver "libinput"
EndSection
```

# commands
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

# 5 sec lag problem with intel gpu
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

# rsync
> `rsync -avr source destination`
- no slash at path end

# mount android phone
- > `simple-mtpfs -l`
- > `simple-mtpfs --device 1 cell/`
- to unmount:
- > `fusermount -u cell/`

# install font manually:
- https://ehellman.github.io/blog/2013/04/16/manually-install-a-font-in-arch-linux/
- Unpack the file and move it to the shared fonts folder
> `mv font.ttf /usr/share/fonts/  or  ~/.local/share/fonts`
- Or if you want to install the font for a single user, move it to the .fonts directory in the home folder.
> `mv font.ttf ~/.fonts/`
- You might also have to update /etc/X11/xorg.conf or /etc/xorg.conf with the new directory
- Search for FontPath to find the correct location within the file to add your new path.
- Now, reload the font cache:
> `fc-cache -vf`

# freelance contract
- https://www.ashedryden.com/blog/contract

# connect AnnePro2 P1 via bluetooth
- press F2 + 1 for 5 sec
- > $ bt-device -l
- > $ bt-device -c <mac>

# ISO 8601: the better date format
- >   yyyy-mm-dd hh:mm:ss
- >   2021-12-22 23:44:56

# Try and redefine the ssh url for remote origin:
> `git remote set-url origin git@github.com:<url-repo>.git`
- And try again
> `ssh -T git@github.com`

# terminal background color
> #1e1e1e

# vim
- macro recording 
  - qd 	start recording to register d, or any
  - ... 	your complex series of commands
  - q 	stop recording
  - @d 	execute your macro at reg d
  - @@ 	execute your macro again 
  - #@d to execute number of times
- `set wildignore+=**/node_modules/**` ignore files or folders 
- `:w !sudo tee "%"` root priviledge for saving doc in vim 
- `:5,17s/^/#/` comment line 5-17
- `:%s/^/#/` comment all lines

# nvimdiff
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

# pass store format
```
YwrZSNH35j164ns9pI
URL: *.example.com/*
Username: myuser@example.com
Secret Question 1: secret question here
Phone Support PIN #: 00000 
```

# mutt-wizard
- add mail account:
> `mw -a user@gmail.com -u user -n user -i imap.gmail.com -I 993 -s smtp.gmail.com -S 465`

# gen ssh keys
- mkdir ~/.ssh
- chmod 700 ~/.ssh
- ssh-keygen -t rsa

# base64
- echo -n 'data' | base64 c2NvdHRsaW51eC5jb20gcm9ja3MK
- echo -n c2NvdHRsaW51eC5jb20gcm9ja3MK | base64 -d data

# .ssh/config format for multiple keys
```
Host ANY_NAME
HostName HOST or IP
User USER_NAME
IdentityFile "PATH TO id_rsa FILE"
```
