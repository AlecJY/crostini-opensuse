# openSUSE lxd images for Crostini

This project is trying to create an openSUSE LXD images for Crostini.
The image definition YAML file is based on
[lxc/lxc-ci](https://github.com/lxc/lxc-ci/blob/9e6ca78d1730817ef6e3bab0df84dcf8264da256/images/opensuse.yaml)
with adding some packages and configurations for supporting Crostini.

Most functions work, though there are some [bugs](#known-issues) and
some features are untested.

## Supported releases and architectures
The folloing releases is supported:
* openSUSE 15.4
* openSUSE 15.5
* openSUSE Tumbleweed

The folloing architectures is supported:
* amd64
* arm64 (Not Tested)

Arm64 version should work like amd64 one theoretically. However, I
don't have an arm64 Chromebook, so I'm not sure whether arm64 images
work or not.

## Prebuilt images
Both amd64 an arm64 prebuilt images are hosted on [https://crostini-images.alebit.com](https://crostini-images.alebit.com)

Currently there are three kind of images:
* opensuse/15.4/crostini
* opensuse/15.5/crostini
* opensuse/tumbleweed/crostini

## Install
### Install with remaining Debian (penguin) container
1. [Enable Crostini](https://support.google.com/chromebook/answer/9145439)
2. Open Chrome, paste `chrome://flags/#crostini-multi-container` into the address bar, then press Enter
3. Enable `Allow multiple Crostini containers` option, then press Restart
4. After restart, go to Settings > Advanced > Developers > Linux development environment > Manage extra containers
5. Press Create, then press Advanced. Type as the folloing
   * Container Name: opensuse (Or other name you like)
   * Image Server: https://crostini-images.alebit.com
   * Image Alias: Choose one of the followings
     * opensuse/15.4/crostini
     * opensuse/15.5/crostini
     * opensuse/tumbleweed/crostini
6. Press Create

Terminal will close or show some error messages. This is a [known issue](#terminal-closed-immediately-or-show-errors)
for this image. You can ignore the error.

7. Now you can open openSUSE from Terminal


### Install with replacing Debian (penguin) container
> [!WARNING]
> All data in Crostini will be removed. Please remember to backup files.

1. [Enable Crostini](https://support.google.com/chromebook/answer/9145439)
2. Open crosh (Press `Ctrl+Alt +T` in Chrome)
3. Run the following commands in crosh

```
vmc destroy termina
vmc start termina
exit
```

4. Run the following commands in crosh

```
vmc container termina penguin https://crostini-images.alebit.com opensuse/15.5/crostini
```

Some error messages may be shown. This is a [known issue](#terminal-closed-immediately-or-show-errors)
for this image. You can ignore the error.

5. Now you can open openSUSE from Terminal

## Build the images
Install [distrobuilder](https://github.com/lxc/distrobuilder) first. Then run

```bash
distrobuilder build-lxd opensuse.yaml -o image.architecture=$(arch) -o image.release=$RELEASE -o image.variant=crostini
```

Replace $RELEASE with one of the folloing values:
* 15.4
* 15.5
* tumbleweed

## Enable IME support
Just enable the two options in `chrome://flags`
* Crostini IME support (`chrome://flags#crostini-ime-support`)
* Crostini IME support for Qt applications (`chrome://flags/#crostini-qt-ime-support`)

## Known issues
### Terminal closed immediately or show errors
If you disable "Close window on exit" in Terminal setting. You
will see some errors like

```
ERROR vsh: [vsh.cc(237)] Poll timed out after waiting 5 seconds.
```

or

```
ERROR vsh: [vsh.cc(171)] Failed to launch vshd for termina:opensuse: requested container does not exist: opensuse
```

This is because openSUSE is still booting but Crostini thinks the OS is
ready. Wait a second and try again.

I'm still trying to figure out how to resolve the issue.

### App is stuck at loading
I guess the reason is the same as above, though it will be stuck at
loading forever. Open the app directly from commandline should make
the loading icon disappear.

Always open Terminal first after power on Chrome OS then open Linux
Apps later after make sure openSUSE is ready should prevent this
issue.

## Reference
* [Chrome OS devices/Crostini - ArchWiki](https://wiki.archlinux.org/title/Chrome_OS_devices/Crostini)
