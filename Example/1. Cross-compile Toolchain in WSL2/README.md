# VoCore v1 | Cross-compile Toolchain OpenWRT in WSL2

1. a. Make sure `wsl2` is [activated in your Windows 10 or Windows 11 computer](https://learn.microsoft.com/id-id/windows/wsl/install#prerequisites),
1. b. Enable wsl2 `networking mode mirrored` by creating a file `.wslconfig` in folder `C:\Users\{Your User Name}` with the following config,
```
[wsl2]
networkingMode=mirrored
```
2. [Install Ubuntu 16.04 in wsl2](https://gist.github.com/xynova/87beae35688476efb2ee290d3926f5bb), Ubuntu 16 is mandatory,
3. [Connect to wsl2 Ubuntu16.04  via VS Code](https://code.visualstudio.com/docs/remote/wsl)
4. In VS Code terminal connected to wsl2 Ubuntu16.04, run command bellow to install build tool,
```
sudo apt-get install -y build-essential git subversion sharutils vim asciidoc binutils bison flex texinfo gawk help2man intltool libelf-dev zlib1g-dev libncurses5-dev ncurses-term libssl-dev python2.7-dev unzip wget gettext xsltproc && sudo apt-get clean
```
5. Clone OpenWRT 15.05 Chaos Calmer,
```
git clone --branch chaos_calmer https://github.com/openwrt/chaos_calmer.git openwrt
cd openwrt/
```
6. Download patch file for OpenWRT 15.05,
```
wget https://raw.githubusercontent.com/Muhammad-Yunus/VoCore-v1-Project/main/Example/1.%20Cross-compile%20Toolchain%20in%20WSL2/patch/011-fix-sigstksz.patch -P tools/m4/patches/
```
7. Run OpenWRT build system script,
```
./scripts/feeds update -a && ./scripts/feeds install -a
```
8. Convert `git://` protocol to `https://`,
```
git config --global url.https://github.com/.insteadOf git://github.com/
```
9. Setup target systems,
```
make menuconfig
```
- it will open OpenWRT build configuration, make sure select like bellow,
![](img/build-condif.png)
10. Compile OpenWRT using `make`
```
make -j1 V=s
```
11. Add environment variable needed for builds in `~/.bashrc`at the end of the file, 
```
export STAGING_DIR=~/openwrt/staging_dir
export TOOLCHAIN_DIR=$STAGING_DIR/toolchain-mips_34kc_gcc-4.8-linaro_uClibc-0.9.33.2
export LDCFLAGS=$TOOLCHAIN_DIR/usr/lib
export LD_LIBRARY_PATH=$TOOLCHAIN_DIR/usr/lib
export PATH=$TOOLCHAIN_DIR/bin:$PATH
export CC=mips-openwrt-linux-uclibc-gcc
```
13. Reload `~/.bashrc`,
```
source ~/.bashrc
```
14. Test toolchain by creating `Hello World` program, create a new file name it `main.c` and paste bellow code,
```
#include <stdio.h>

int main(void)
{
    return printf("hello world\n");
}
```
15. Compile the code using `gcc`,
```
`find ~/openwrt -name mipsel-openwrt-linux-gcc` -g main.c -o hello
```
16. Connect to VoCore WiFi with SSID `VoCore`
17. Copy compiled binary `hello` to VoCore over SSH using `scp`,
```
scp ./hello root@192.168.61.1
```
18. SSH to VoCore,  
```
ssh root@192.168.61.1
```
19. Run the compiled code inside VoCore,
```
./hello
``` 
- it's should printing `Hello World`


### Source
- [https://vonger.cn/?p=1585](https://vonger.cn/?p=1585)
- [https://medium.com/meseta-robots/set-up-cross-compile-toolchains-in-docker-to-save-time-openwrt-build-system-for-ar9331-1744629164c8](https://medium.com/meseta-robots/set-up-cross-compile-toolchains-in-docker-to-save-time-openwrt-build-system-for-ar9331-1744629164c8)
- [https://learn.microsoft.com/en-us/windows/wsl/networking#accessing-a-wsl-2-distribution-from-your-local-area-network-lan](https://learn.microsoft.com/en-us/windows/wsl/networking#accessing-a-wsl-2-distribution-from-your-local-area-network-lan)
