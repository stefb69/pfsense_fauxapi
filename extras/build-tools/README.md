# Build Notes

NB: there does not seem to be any requirement for the FreeBSD host used to build 
packages with to be the same FreeBSD (or pfSense) version that you are 
targeting - I note this here because this was not clear to myself when starting
to build packages.

## Create a fresh FreeBSD host based on VirtualBox
 * Obtain a FreeBSD `VM-IMAGE` based release https://download.freebsd.org/ftp/releases/VM-IMAGES/
 * Recommended image type is VMDK which seems to work well with VirtualBox
 * Decompress the VMDK `xz`, create a new VirtualBox using this HDD image and power-on
 * Login to the instance at the console (root, no password)
 * Perform a package update `pkg update`
 * Install packages you will need `pkg install rsync`
 * Enable sshd at reboot by adding `sshd_enable="YES"` to the file `/etc/rc.conf`
 * Enable root login via ssh (if this approach suits you)
 * Consider adding your ssh key(s) to login user (again, if this approach suits you)
 * Powerdown instance and move VirtualBox (plus `vmdk`) file to a location that suits

## Create a VirtualBox clone to work with
 * Using the `instances-reclone.sh` script, edit the paths in this script (in the upper section)
 * Consider changing the MAC addresses in the (in the lower section) of the `instances-reclone.sh` script
 * Run the `instances-reclone.sh` to get a "nice" fresh clone - this tool will **CLOBBER** the target image if target is in powered-off state!

## Pull down the pfSense FreeBSD-ports locally to build against
 * Install git and pull down the latest pfSense FreeBSD-ports fork
```bash
pkg install git
cd /usr/
git clone https://github.com/pfsense/FreeBSD-ports ports
```
 * Consider creating a VirtualBox image based on the fresh FreeBSD + FreeBSD-ports to make a reclone easier/quicker later on.

## Build a package
 * Clean the build path if required before a build
```bash
cd /path/to/package
make clean
```

 * Change to the package directory and make
```bash
cd /path/to/package
make package
```

 * At end of the build process you should have a `.txz` package file ready for installation on a pfSense host

## Checking for errors
 * Before submitting a package you should check with `portlint`
```bash
pkg install portlint
echo DEVELOPER=yes >> /etc/make.conf
cd /path/to/package
portlint -CN
```

## Various pointers and other notes
 * https://forum.pfsense.org/index.php?topic=112807.0
 * https://gist.github.com/jdillard/3f44d06ba616fec60890488abfd7e5f5
