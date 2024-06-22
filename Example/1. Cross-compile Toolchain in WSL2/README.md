# VoCore v1 | Cross-compile Toolchain OpenWRT in WSL2

1. Make sure `wsl2` is [activated in your Windows 10 or Windows 11 computer](https://learn.microsoft.com/id-id/windows/wsl/install#prerequisites),
2. [Install Ubuntu 16.04 in wsl2](https://gist.github.com/xynova/87beae35688476efb2ee290d3926f5bb), Ubuntu 16 is mandatory,
3. [Connect to wsl2 Ubuntu16.04  via VS Code](https://code.visualstudio.com/docs/remote/wsl)
4. In VSCode terminal connected to wsl2 Ubuntu16.04, run command bellow to install build tool,
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
wget https://raw.githubusercontent.com/Muhammad-Yunus/VoCore-v1-Project/main/Example/1.%20Cross-compile%20Toolchain%20in%20WSL2/patch/011-fix-sigstksz.patch -p tools/m4/patches/
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
