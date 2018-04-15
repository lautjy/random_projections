# Linux from zero

## Install Linux
Two paths here: One for VM and another for "real/metal" installation.
The real one will need real drivers for your machine. On the VM everything
just works via VirtualBox virtual hardware drivers.

### Install Debian in a VirtualBox
Create a VM and just install it. Deselect all the desktop choices (using arrow keys and spacebar). You should have only the lowest item selected when installing things
= minimal install. Rest will come later.

## Install programs for a nice dev setup

```
su
apt-get install build-essential sudo vim xorg slim i3 curl wget tmux python python-dev python-pip ipython git-core python3 python3-pip virtualenv virtualenvwrapper rxvt-unicode-256color xsel htop ncdu
# Maybe not so much needed
apt-get install software-properties-common dkms meld
apt-get install p7zip-full p7zip-rar

# THIS?
## My choice of terminal with i3 is urxvt. Let’s install it:
rxvt-unicode rxvt-unicode-terminfo

# Update few Python items:
sudo pip install -U pip
sudo pip install wheel twine
```


### What were all those?
+ build-essential - has make, gcc, and the rest for building
+ xorg - X11 windowing system
+ slim - login manager
+ i3 - window manager
+ rxvt-unicode-256color - good terminal we'll use with i3
+ xsel - will be used by some of the perl-script plugins of rxvt(-u)
+ dkms - needed to build kernel modules for VirtualBox guest additions
+ python* - your pythons
+ tmux - nice way to save shell sessions
+ software-properties-common - provides `add-apt-repository`
+ meld - visual diffing
+ ncdu - disk space investigation
+ `terminfo` is just for some compatibility issues with sshing and screen.

**CONSIDER**
  * picture viewers:
      - gpicview
      - feh
  * console webbrowser lynx
  * console based file manager ranger
    - vim like bindings, tabs, written in python and fast file manager!


```bash
# Add to vboxsf group and developers group:
sudo groupadd devs
sudo groupadd docker
# User creation
##-m, --create-home: Create user home directory
##-p, --password: Specify user password
##-s, --shell: Default shell for logon user
#adduser --disabled-password --gecos "" $USERNAME;
su

export USERNAME=itsame
useradd -m -s /bin/bash $USERNAME \
    & mkdir -p /home/$USERNAME/.ssh \
    & chown -R $USERNAME:$USERNAME /home/$USERNAME/.ssh \
    & chmod -R 500 /home/$USERNAME/.ssh \
    & touch /etc/sudoers.d/98_$USERNAME \
    & echo '$USERNAME ALL=(ALL) NOPASSWD:ALL' >/etc/sudoers.d/98_$USERNAME \
    & chmod 440 /etc/sudoers.d/98_$USERNAME \
    & usermod -aG docker,vboxsf,devs $USERNAME \
    & chown root:devs /opt
```


```
# Give your user sudo rights
visudo
<user_name> ALL=(ALL:ALL) ALL
```


#### Config Xorg to skip hardware detection at every boot
To speedup the booting process one can give Xorg a predefined config that describes
the hardware. It speeds up booting by making it NOT search for every darn device at every startup.
Booted into "special (recovery mode)"(?), and ran the following command:

    Xorg -configure

That creates `/root/xorg.conf.new`, review that and copy it to `/etc/X11/xorg.conf`.



### Other things to install if needed
**Chrome** with flash (for Twitch):

```
apt-get install chromium libexif12
apt-get install pepperflash-plugin-nonfree
```

**Docker**:
This keeps changing a bit too - best google it:
https://docs.docker.com/engine/installation/linux/debian/

Docker is just one file (Golang static compilation FTW), which is easy to build from sources if needed.

docker-compose: See latest version number from here:
https://github.com/docker/compose/releases
Install:

```
sudo curl -L https://github.com/docker/compose/releases/download/1.18.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version
```


**NodeJS** and **npm**:
The way to install npm changes quickly. Best google this one too.
Something that worked in 2015Q4:

```
    curl -sL https://deb.nodesource.com/setup_4.x | sudo bash -
    sudo apt-get install -y nodejs
    sudo npm install -g npm
```


**Atom editor** - though maybe better go with **VSCode**:
Quite an ok editor. Can still suffer from slowdowns though (2016Q2).
Download the .deb file from http://atom.io and then:

```
    dpkg -i atom.deb
    apt-get update
    apt-get install -f
    dpkg -i atom.deb
```

It starts with `atom` and has atom package manager to install packages
from terminal `apm`.

Settings:
  * Allow pending pane items


**Java**
On jessie you need to add backports, and update-alternatives

```
sudo nano /etc/apt/sources.list
# add there:
## Get backports for openjdk-8- amongst other things
deb http://httpredir.debian.org/debian jessie-backports main contrib non-free

# Install and set path:
sudo apt-get update && sudo apt-get install openjdk-8-jdk
sudo update-alternatives --config java
```



### **Sound**
Install basic Alsa utils:

```
    apt-get install alsa-utils
    #Unmute everything, and then set master volume:
    amixer sset Master unmute
    alsamixer   # then set the master volue
```

## Configure Everything
Remember to configure stuff. Some of the configs are self-evident, some need more instructions given below.

### **Disable PC speaker beep**
Disable and make it permanent:

```
sudo rmmod pcspkr
 echo "blacklist pcspkr" > /etc/modprobe.d/nobeep.conf
```

### **SLim** login configs:

```
vim /etc/slim.conf  # to set autologin, username, etc
```

### **i3** window manager
The default main "key", aka "Mod", is the Windows-key. They key bindings are defined in  `~/.i3/config`.

1) Change it to start *urxvt* terminal on "Mod+Return":

```
# start a terminal
#bindsym $mod+Return exec i3-sensible-terminal
bindsym $mod+Return exec urxvt
```

2) You can also comment out the existing `$mod+l` which gives vim-like movement across windows.

3) Add lockscreen at: `bindsym $mod+l exec slimlock`

Note: Useful i3 keyboard shortcuts:

```
    Mod + Enter             # new terminal window
    Mod + jklö              # move around, or use arrow keys (up,left,right,down)
    Mod + Shift + <number>  # move window to workspace
    Mod + d                 # open dmenu
```

### **urxvt** terminal and keymapping
So config-mess is put into `~/.Xresources`. It controls the *urxvt* console.
Use the example config-mess from here.

The way it works: Display manager (*i3* in this case) loads `.Xresources` (using `xrdb`) when it logs into *X*. To see the loaded configs `xrdb -query -all`.

These were needed to get `ctrl + right arrow` word skipping to work again:

```
URxvt.keysym.Control-Up:    \033[1;5A
URxvt.keysym.Control-Down:  \033[1;5B
URxvt.keysym.Control-Left:  \033[1;5D
URxvt.keysym.Control-Right: \033[1;5C
```

### **Installing a font**
Example of trying to get inconsolata font to work (it didn't):

```
    apt-get install fonts-inconsolata
    sudo fc-cache -fv
```
And inconsolata doesn't work. Outputs boxes only - dammit :(

### **Terminal Colors** (TODO)
This is still a slight mystery to me... Just some tidbits here now.

List all terminal and their supported colors:

    for T in `find /usr/share/terminfo -type f -printf '%f '`;do echo "$T `tput -T $T colors`";done|sort -nk2

Print all colors:

    (x=`tput op` y=`printf %76s`;for i in {0..256};do o=00$i;echo -e ${o:${#o}-3:3} `tput setaf $i;tput setab $i`${y// /=}$x;done)


### Get CTRL+Shift+v/c to work in rxvt-unicode-256color
This is done using `rxvt-unicode` terminal:

```
# sudo apt-get install rxvt-unicode-256color xsel
cd ~
mkdir scripts_term
cd scripts_term/
# Copy-Paste scripts from the internet. Feeling secure yet?
# These must be enabled in the config later.
# Clipboard connection
wget https://raw.githubusercontent.com/muennich/urxvt-perls/master/clipboard
# CTRL + "uparrow"/"downarrow" to change font-size
wget https://raw.githubusercontent.com/majutsushi/urxvt-font-size/master/font-size
```


### VIM - configure
Confused person tries to configure VIM.

#### Colors
Download a new Vim color scheme. Extracts and move the downloaded `*.vim` file to this folder `~/.vim/colors/`.

Useful keybindings (TODO):

```
    CTRL +w +w  = change windows
    :qa         = exit whole thing
```

#### VIM additions (powerline, fugitive, extradite, vundle)
Well, the official documentation mentions somewhere on the second last page (of a lot of pages) that you should `set laststatus=2` in vim, otherwise there is no status bar and nothing interesting to see...

I found one blog that explained that I should install the Vim *fugitive* and *syntastic* plugins to get more out of the status line. Both these plugins are a definite recommend BTW regardless of *powerline*.

  * vim-fugitive comes with a lovely description: "A Git wrapper so awesome, it should be illegal"
  * And Extradite, a commit browser built on top of fugitive, seems rather good as well: http://int3.github.io/vim-extradite/
  * vundle = package management




# Installing and configuring other useful things
Bit more involved installations and configurations.

## SSH server
Some idea on how to allow SSH access to your machine.
*OpenSSH* gives use the SSH server, and *fail2ban* protects against brute-force
SSH hacking attempts. *pwgen* is for random password generation, might be useful.

    apt-get install openssh fail2ban pwgen

Configuration in `/etc/sshd/sshd_config`.
Here are some configuration tips to increase security of your SSH server:

```
    # Allow only certain users:
    AllowUsers  <YOUR-USERNAME>

    # Disable root login over SSH:
    PermitRootLogin no

    # Disable password login, allows keys only
    PasswordAuthentication no
    ChallengeResponseAuthentication no

    # By changing the default port you will make SSH server more secure.
    # By changing the default port you will reduce the amount of brute force attacks
    Port 22333

    # Change SSH login grace time
    # By changing this you will have control on your unauthenticated connections left open.
    #In Debian, by default this is set to 120 seconds.
    # Authentication:
    LoginGraceTime 30


    # To add a nice welcome message edit the file /etc/issue and change the Banner line into this:
    Banner /etc/issue
```

Starting, restarting, and enabling SSHD using SystemD

```
    systemctl start sshd.service
    systemctl enable sshd.service
    systemctl restart sshd.service
```


## rTorrent

https://wiki.archlinux.org/index.php/Rtorrent

Before running rTorrent, find the example configuration file `/usr/share/doc/rtorrent/rtorrent.rc` and copy it to `~/.rtorrent.rc`:

```
# cp /usr/share/doc/rtorrent/rtorrent.rc ~/.rtorrent.rc
min_peers = 40
max_peers = 52
min_peers_seed = 10
max_peers_seed = 52
max_uploads = 8
download_rate = 200
upload_rate = 28
check_hash = yes
```

*Check_hash* yes/no controls if torrent data is checked at startup.

*Download rate* (in kB/s)
Use the following formula to determine your optimal speeds:

  * 80% of your maximum upload speed
  * 95% of your maximum download speed

*Maximum connected peers per torrent*
Yet another setting that you don’t want to max out. I experimented quite a lot with the max connected peers settings and came to the conclusion that both high and low number hurt the download speed of a torrent. The following setting worked best for me:

  * upload speed * 1.3

so if your maximum upload speed is 40 kB/s, the optimal amount of connected peers per torrent is `40 * 1.3 = 52`

*Maximum upload slots* `1 + (upload speed / 6)`

The directory option will determine where your torrent data will be saved. Be sure to enter the absolute path, as rTorrent may not follow relative paths:

    directory = /home/[user]/torrents/

The session option allows rTorrent to save the progess of your torrents. It is recommended to create a directory called .session (e.g. mkdir ~/.session).

    session = /home/user/.session/

The schedule option has rTorrent watch a particular directory for new torrent files. Saving a torrent file to this directory will automatically start the download. Remember to create the directory that will be watched (e.g. mkdir ~/watch). Also, be careful when using this option as rTorrent will move the torrent file to your session folder and rename it to its hash value.

```
    schedule = watch_directory,5,5,load_start=/home/user/watch/*.torrent
    schedule = untied_directory,5,5,stop_untied=
    schedule = tied_directory,5,5,start_tied=
```

The following schedule option is intended to stop rTorrent from downloading data when disk space is low.

    schedule = low_diskspace,5,60,close_low_diskspace=100M

Although, rTorrent allows a range of ports, a single port is recommended.

    port_range = 49164-49164

The encryption option enables or disables encryption. It is very important to enable this option, not only for yourself, but also for your peers in the torrent swarm. Some users need to obscure their bandwidth usage from their ISP. And it does not hurt to enable it even if you do not need the added security.

    encryption = allow_incoming,try_outgoing,enable_retry

It is also possible to force all connections to use encryption. However, be aware that this stricter rule will reduce your client's availability:

    encryption = require,require_RC4,allow_incoming,try_outgoing

This final dht option enables DHT support. DHT is common among public trackers and will allow the client to acquire more peers.

    dht = auto
    dht_port = 6881
    peer_exchange = yes

If you want to use rtorrent with some web interfaces (e.g. rutorrent) you need to add the following line to the configuration file:

    scgi_port = localhost:6666

To set it to be managed via systemd see Arch Linux page.

Some rtorrent keys:

```
    Ctrl-q  Quit application
    Ctrl-s  Start download. Runs hash first unless already done.
    Ctrl-d  Stop an active download or remove a stopped download
    Ctrl-k  Stop and close the files of an active download.
    Ctrl-r  Initiate hash check of torrent. Without starting to download/upload.
    Left    Returns to the previous screen
    Right   Goes to the next screen
    Backspace/Return    Adds the specified *.torrent
    a|s|d   Increase global upload throttle about 1|5|50 KB/s
    A|S|D   Increase global download throttle about 1|5|50 KB/s
    z|x|c   Decrease global upload throttle about 1|5|50 KB/s
    Z|X|C   Decrease global download throttle about 1|5|50 KB/s
```


## urxvt config instructions from Arch install guide

Now configure it by opening ~/.Xdefaults and add this:

```
! urxvt

URxvt*geometry:                115x40
!URxvt*font: xft:Liberation Mono:pixelsize=14:antialias=false:hinting=true
URxvt*font: xft:Inconsolata:pixelsize=17:antialias=true:hinting=true
URxvt*boldFont: xft:Inconsolata:bold:pixelsize=17:antialias=false:hinting=true
!URxvt*boldFont: xft:Liberation Mono:bold:pixelsize=14:antialias=false:hinting=true
URxvt*depth:                24
URxvt*borderless: 1
URxvt*scrollBar:            false
URxvt*saveLines:  2000
URxvt.transparent:      true
URxvt*.shading: 10

! Meta modifier for keybindings
!URxvt.modifier: super

!! perl extensions
URxvt.perl-ext:             default,url-select,clipboard

! url-select (part of urxvt-perls package)
URxvt.keysym.M-u:           perl:url-select:select_next
URxvt.url-select.autocopy:  true
URxvt.url-select.button:    2
URxvt.url-select.launcher:  chromium
URxvt.url-select.underline: true

! Nastavuje kopirovani
URxvt.keysym.Shift-Control-V: perl:clipboard:paste
URxvt.keysym.Shift-Control-C:   perl:clipboard:copy

! disable the stupid ctrl+shift 'feature'
URxvt.iso14755: false
URxvt.iso14755_52: false

!urxvt color scheme:

URxvt*background: #2B2B2B
URxvt*foreground: #DEDEDE

URxvt*colorUL: #86a2b0

! black
URxvt*color0  : #2E3436
URxvt*color8  : #555753
! red
URxvt*color1  : #CC0000
URxvt*color9  : #EF2929
! green
URxvt*color2  : #4E9A06
URxvt*color10 : #8AE234
! yellow
URxvt*color3  : #C4A000
URxvt*color11 : #FCE94F
! blue
URxvt*color4  : #3465A4
URxvt*color12 : #729FCF
! magenta
URxvt*color5  : #75507B
URxvt*color13 : #AD7FA8
! cyan
URxvt*color6  : #06989A
URxvt*color14 : #34E2E2
! white
URxvt*color7  : #D3D7CF
URxvt*color15 : #EEEEEC
```
