# How to compile imx-yocto-thud on Fedora 32

How updates can break everything

## GCC 7.4

GCC 10, the default on new Fedora versions, creates error.
I switched to gcc 7.4.0 to match the Ubuntu 18.04 version.
The gcc-builder repository by Bob Steagall helped me here
```bash
git clone https://github.com/BobSteagall/gcc-builder
```
You just have to specify the version in gcc-build-vars.sh
```bash
export GCC_VERSION=7.4.0
```
And the number of job for Build and Test which match your hardware.
For me :
```bash
export GCC_BUILD_JOBS_ARG='-j4'
export GCC_TEST_JOBS_ARG='-j4'
```
Optionally, you can change the installation path with GCC_INSTALL_PREFIX.
Finally, you run build-gcc.sh and watch, it can be quite long !
Once finished, add this gcc version in your PATH variable:
```bash
PATH=/${GCC_INSTALL_PREFIX}/bin:$PATH
```

## Repo installation

https://source.android.com/setup/develop#installing-repo

## Getting the imx yocto

```bash
mkdir imx-yocto-bsp
cd imx-yocto-bsp
# init the repository from the imx-linux-thud branch
repo init -u https://source.codeaurora.org/external/imx/imx-manifest -b imx-linux-thud -m imx-4.19.35-1.0.0.xml
# get sources
repo sync
cd imx-yocto-bsp
# set up open-embedded environnement
DISTRO=fsl-imx-wayland MACHINE=imx8mqevk source fsl-setup-release.sh -b build-wayland
```

## Patches

### Qemu
* Bad glibc version, gettid must be replaced by sys_gettid. cf https://patchwork.openembedded.org/patch/165581/
* stime has been removed from glibc, you must use clock_settime(CLOCK_REALTIME, ...) instead. cf https://git.openembedded.org/openembedded-core/commit/meta/recipes-devtools/qemu/?id=2cca75155baec8358939e2aae822e256bed4cfe0
* The SIOCGSTAMP symbol has been moved in this linux kernel, you must include linux/sockios.h. cf https://patchwork.kernel.org/patch/10998911/

### Libgpg
TODO In Gawk 5.0, regexp routines are replaced by Gnulib implementation, which only allows escaping specific characters.

