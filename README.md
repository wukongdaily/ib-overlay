# ib-overlay
Custom overlay files and scripts for OpenWrt ImageBuilder 24.10.2
<br>
此项目 只记录了我修改的文件和脚本，ImageBuilder工具包并不在其中.你可以把它当做模板,适当修改,就能定制属于自己的固件了<br>
### 目前本项目有关集成第三方插件的逻辑 也同步到了云端的ImageBuilder项目 希望对各位有所启发
https://github.com/wukongdaily/AutoBuildImmortalWrt
### 图文地址一 https://wkdaily.cpolar.top/archives/40
### 图文地址二 https://wkdaily.cpolar.cn/archives/40
### 视频教程 https://www.youtube.com/watch?v=7i6BQeitUtE
### 视频教程(B站) https://www.bilibili.com/video/BV18bGGzNEyN
## 1、下载ImageBuilder
```
# 下载
wget https://mirrors.aliyun.com/openwrt/releases/24.10.2/targets/x86/64/openwrt-imagebuilder-24.10.2-x86-64.Linux-x86_64.tar.zst
# 解压
tar --use-compress-program=unzstd -xvf openwrt-imagebuilder-24.10.2-x86-64.Linux-x86_64.tar.zst
# 进入ImageBuilder目录
cd openwrt-imagebuilder-24.10.2-x86-64.Linux-x86_64/
```

## 定位到ImageBuilder的根目录下

## 2、下载整个项目的 master.zip

```
wget -c -O master.zip.tmp https://github.com/mashkvultr/openwrt-image-builder/archive/refs/heads/dev.zip && mv -f master.zip.tmp master.zip

```
[![Github](https://img.shields.io/badge/如果下载不动,可套用加速前缀,点这里前往-d6acef?logo=github&logoColor=fff&labelColor=000&style=for-the-badge)](https://wkdaily.cpolar.top/archives/1) 
``` 
wget -c -O master.zip.tmp https://github.com/mashkvultr/openwrt-image-builder/archive/refs/heads/dev.zip && mv -f master.zip.tmp master.zip

```
## 3、解压 master.zip 到当前目录(ImageBuilder的根目录)，去除无关文件
```
unzip master.zip -d tempdir
rm -f tempdir/*/.gitignore tempdir/*/README.md tempdir/*/LICENSE
mv tempdir/*/* ./
mv tempdir/*/.config ./ 2>/dev/null
rm -rf tempdir
ls -lah
```
> ### ⚠️ 如果你觉得`extra-packages` 目录太大了，你也可以先【fork】本项目
> ### 将项目同步到自己的空间后，做一些修改、删减。然后在下载整个项目的zip 这样就比较小了

## 4、安装必要依赖 飞牛NAS系统为例 需要安装gawk
> 其他系统请参考官网文档 https://openwrt.org/zh/docs/guide-user/additional-software/imagebuilder
```bash
sudo apt update -y
sudo apt install gawk -y
```
## ⚠️ 关键修改说明（必读）
> `.config` 此文件是全部配置，是.开头的隐藏文件,一般不需要动。我这边去掉了ext4格式 如下 如果你需要也可以自由修改为y<br>
```bash
CONFIG_USES_EXT4=n
CONFIG_TARGET_ROOTFS_EXT4FS=n
```
> `repositories.conf`  这是仓库地址 你可以修改它 将ImageBuilder工具中的仓库地址替换为阿里云仓库 加快构建时的下载速度<br>
> `extra-packages` 这是我新建的目录 可以存放run文件。也可以存放自定义ipk 但最好是run 因为这样比较整齐 是一个整体<br>

> `files` 这也是我新建的目录 它对应的就是openwrt的根目录，比如files/etc  可覆盖openwrt系统里的/etc 这里存放的文件 会原样注入系统<br>
>> `files/etc/opkg/distfeeds.conf` 此文件是为了覆盖系统中 软件包的默认仓库 换成阿里云<br>
>> `files/etc/uci-defaults/99-init.sh` 此脚本是固件首次启动时候运行的 用于修改一些必要的配置<br>
>> `files/etc/banner` 此文件是终端的banner信息 可自己自由发挥<br>
>> `files/mnt/shell/istore.sh` 个性化脚本 用于在openwrt中安装istore 属于非必须的 如不需要 也可以删除<br>
>> `files/usr/bin` 此目录默认是空的 若用户集成adguardhome 则自动将内核拷贝到此目录<br>

> `prepare-packages.sh` 此脚本用于 make image之前的准备工作，用于将`extra-packages`中的所有run解压后的ipk 或者原本的ipk复制到`packages`中<br>
> `packages` 用于存放自定义ipk的，但是我为了整齐 可读性强，我采用上述脚本方式 自动将`extra-packages`中的所有ipk拷贝过来<br>
> `check.sh` 此脚本用于检测`packages`目录下所有ipk的包名 并将包名列表写入到 `packages_names.txt` 此文件在构建环节会用到 判断用户是否集成了错误的包名<br>
> `build.sh` 此脚本用于最关键一步:构建镜像 make image<br>

# 相关项目
### Run是如何生成的 如何找run文件？如何制作run文件
https://github.com/wukongdaily/RunFilesBuilder

### 云端的ImageBuilder工作流(Github Action)
https://github.com/wukongdaily/AutoBuildImmortalWrt

### OpenWrt ImageBuilder 官网下载地址
https://downloads.openwrt.org/releases/24.10.2/targets/x86/64/


## 默认脚本build.sh 构建的固件特点
>> 该固件处理了docker的防火墙设置 用户无需操心容器端口无法访问等问题<br>
>> 该固件刷入单网口设备或虚拟机时候，默认是自动获取ip模式。多网口时，默认eth0 是wan，其余自动帮你桥接为br-lan<br>
>> 192.168.99.1 root 无密<br>



