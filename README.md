# Dell XPS 15 9560 / 9570 Fedora 33 Install
Just a manual for setting up Fedora for Dell XPS 15, will be useful to this small demographic of XPS 15 users that want to try Fedora.

## Initial configuration

```shell
sudo dnf update

sudo systemctl reboot
```

## Get RPM fusion

```shell
sudo dnf install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm

sudo dnf update --refresh

sudo dnf groupupdate core

sudo dnf groupupdate multimedia --setop="install_weak_deps=False" --exclude=PackageKit-gstreamer-plugin

sudo dnf groupupdate sound-and-video
```


## Install Nvidia drivers from RPMFusion

* Install packages
```shell
sudo dnf install gcc kernel-headers kernel-devel akmod-nvidia xorg-x11-drv-nvidia xorg-x11-drv-nvidia-libs xorg-x11-drv-nvidia-libs.i686

sudo dnf install xorg-x11-drv-nvidia-cuda # optional

sudo dnf install vulkan # honestly shouldn't be optional

sudo dnf install vdpauinfo libva-vdpau-driver libva-utils # optional
```

* Wait up to 15 minutes
* Force the akmods to rebuild

```shell
sudo akmods --force

sudo dracut --force
```

* Wait another 5 minutes
* Reboot

```shell
sudo systemctl reboot
```

* Test whether the drivers have been installed correctly

```shell
lsmod | grep nvidia
modinfo -V nvidia
__NV_PRIME_RENDER_OFFLOAD=1 __GLX_VENDOR_LIBRARY_NAME=nvidia glxinfo | grep vendor
```

`lsmod` should say something like this:

```
nvidia_drm             65536  2
nvidia_modeset       1232896  2 nvidia_drm
nvidia_uvm           1150976  0
nvidia              34185216  73 nvidia_uvm,nvidia_modeset
drm_kms_helper        274432  2 nvidia_drm,i915
drm                   618496  12 drm_kms_helper,nvidia_drm,i915
```

* Last, but not the least, configure `/etc/default/grub` to have these params:

```shell
GRUB_CMDLINE_LINUX="rd.driver.blacklist=nouveau modprobe.blacklist=nouveau nvidia-drm.modeset=1 mem_sleep_default=deep rhgb quiet rd.driver.blacklist=nouveau modprobe.blacklist=nouveau nvidia-drm.modeset=1"

GRUB_CMDLINE_LINUX_DEFAULT="rd.driver.blacklist=nouveau modprobe.blacklist=nouveau nvidia-drm.modeset=1 mem_sleep_default=deep rhgb quiet rd.driver.blacklist=nouveau modprobe.blacklist=nouveau nvidia-drm.modeset=1"
```

`mem_sleep_default=deep` will fix a sleep state problem that's happening to XPS 15s, including, but not limited to, 9560s and 9570s.

## Add Flathub

```shell
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

Now you can install stuff from Flathub, e.g.
```shell
flatpak install flathub org.telegram.desktop

flatpak install flathub com.vscodium.codium
```

## Install Git

```shell
sudo dnf install git
```
Don't forget to configure it:

```shell
git config --global user.name "John Doe"

git config --global user.email whatever@wherever.com

git config --global core.editor nano # Or vi, whatever floats your boat
```

## Install ZSH

Come on, why wouldn't you?

```shell
sudo dnf install zsh
```

Change your shell to zsh:

```shell
chsh -s $(which zsh)
```

Then log out and log back in, opening a termnail emulator will run a setup script for ZSH.
Also, on F33 Konsole seems to be explicitly setting bash as a shell in the default profile.

### Install Oh My ZSH

```shell
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

This is optional, but I strongly recommend these settings and plugins:
```shell
HIST_STAMPS="mm/dd/yyyy"

plugins=(
  git
  sudo
  dnf
  colored-man-pages
  zsh-autosuggestions
  zsh-syntax-highlighting
)
```
The last two plungins you're gonna have to install by running this:

```shell
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

git clone https://github.com/zsh-users/zsh-syntax-highlighting ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

## Miscellaneous

### Install `ibus` to fix emoji picker (not relevant since Fedora 34)

[I'm for real here](https://www.reddit.com/r/Fedora/comments/fv4hh7/emoji_picker_for_kde_spin_f32beta/ "Reddit thread related to the emoji picker issue")

```shell
sudo dnf install ibus
```

### Install missing codecs

```shell
sudo dnf install gstreamer1-plugins-{bad-\*,good-\*,base} gstreamer1-plugin-openh264 gstreamer1-libav --exclude=gstreamer1-plugins-bad-free-devel
sudo dnf install lame\* --exclude=lame-devel
sudo dnf group upgrade --with-optional Multimedia
```

### Speaking of Codium...

`settings.json`:
```
{
    "editor.fontFamily": "Fira Code, 'monospace', monospace, 'Droid Sans Fallback'",
    "editor.fontLigatures": true,
    "editor.suggestSelection": "first",
    "editor.selectionClipboard": false,
    "editor.tabSize": 2,
    "editor.wordWrap": "on",
    "editor.linkedEditing": true,
    "editor.mouseWheelZoom": true,
    "files.autoSave": "afterDelay",
    "workbench.enableExperiments": false,
    "window.openFoldersInNewWindow": "on",
    "window.titleBarStyle": "custom",
    "git.autofetch": true,
    "git.confirmSync": false,
    "git.enableSmartCommit": true
}
```

### Install other stuff, go nuts

* [Fira Code (Monospaced font)](https://github.com/tonsky/FiraCode "Fira Code on GitHub")
  * `sudo dnf install fira-code-fonts`
* [Lightly (Breeze application style)](https://github.com/Luwx/Lightly "Lightly on GitHub")
* Steam (it's where money is wasted)
  * `sudo dnf install steam`

## Catch up on some reading

* [Arch Wiki article on XPS 15 9570](https://wiki.archlinux.org/index.php/Dell_XPS_15_9570 "Arch Wiki article on XPS 15 9570")
* [Arch Wiki article on XPS 15 9560](https://wiki.archlinux.org/index.php/Dell_XPS_15_9560 "Arch Wiki article on XPS 15 9560")


