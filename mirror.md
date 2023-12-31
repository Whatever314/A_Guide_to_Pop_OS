# 配置软件源镜像加速

## 太长不看版

分别执行以下共3个命令：
``` bash
$ sudo cat <<'EOF' > /etc/apt/sources.list.d/pop-os-release.sources
X-Repolib-Name: Pop_OS Release Sources
Enabled: yes
Types: deb deb-src
URIs: http://apt.pop-os.org/release https://mirrors.tk/pop-os/release
Suites: jammy
Components: main
X-Repolib-Default-Mirror: https://mirrors.tk/pop-os/release
EOF

```

``` bash
$ sudo cat <<'EOF' > /etc/apt/sources.list.d/pop-os-apps.sources
X-Repolib-Name: Pop_OS Applications
Enabled: yes
Types: deb
URIs: http://apt.pop-os.org/proprietary https://mirrors.tk/pop-os/proprietary
Suites: jammy
Components: main
X-Repolib-Default-Mirror: https://mirrors.tk/pop-os/proprietary
EOF
```

以下命令设置来自`Ubuntu`上游的软件源，其中使用的是[清华大学](https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/)和[阿里云](https://developer.aliyun.com/article/704603)提供的镜像：
``` bash
$ sudo cat <<'EOF' > /etc/apt/sources.list.d/system.sources
X-Repolib-Name: Pop_OS System Sources
Enabled: yes
Types: deb deb-src
URIs: http://apt.pop-os.org/ubuntu https://mirrors.tuna.tsinghua.edu.cn/ubuntu https://mirrors.aliyun.com/ubuntu
Suites: jammy jammy-security jammy-backports
Components: main restricted universe multiverse
X-Repolib-ID: system
X-Repolib-Default-Mirror: https://mirrors.tuna.tsinghua.edu.cn/ubuntu
```


## `repoman`与`python3-repolib`：Pop_OS的软件源管理

与`Ubuntu`不同的是，`Pop_OS`的软件源配置文件不是`/etc/apt/sources.list`：
``` bash
$ cat /etc/apt/sources.list.d 
## See sources.list(5) for more information, especialy
# Remember that you can only use http, ftp or file URIs
# CDROMs are managed through the apt-cdrom tool.
# deb cdrom:[Pop_OS 22.04 _Jammy Jellyfish_ - Release amd64 (20231006)]/ jammy main restricted
```

这是因为`Pop_OS`使用系统内置组件[`python3-repolib`](https://repolib.readthedocs.io/en/latest/index.html)来管理软件源，并为其开发了带图形化界面的[`repoman`](https://github.com/pop-os/repoman) ，并把它集成到Pop!\_Shop里面，即你点击`Pop!\_Shop - 右上角菜单 -  System Software Sources...`时显示出的图形界面。我们查看一下`repoman`和`python3-repolib`的信息：
``` bash
$ dpkg -s repoman 
Package: repoman
Status: install ok installed
Priority: extra
Section: x11
Installed-Size: 208
Maintainer: Ian Santopietro <ian@system76.com>
Architecture: all
Version: 2.4.0~1692815132~22.04~47078a9
Depends: python3:any (>= 3.4~), gir1.2-gtk-3.0, python3-repolib (>= 2.0)
Recommends: gir1.2-flatpak-1.0
Description: Repoman, A Software Sources Manager
 "Hey man, let's go do crimes. Yeah, let's install software and not pay."
 .
 Repoman is a software sources manager for Debian-based linux distributions.
 It allows you to configure system repository information and other system
 settings, as well as add, remove, and modify third-party repositories on
 the system. Repoman can also configure Flatpak sources.
```

``` bash
$ dpkg -s python3-repolib
Package: python3-repolib
Status: install ok installed
Priority: optional
Section: python
Installed-Size: 224
Maintainer: Ian Santopietro <isantop@gmail.com>
Architecture: all
Source: repolib
Version: 2.2.1~1699647578~22.04~43c8b87
Depends: python3:any (>= 3.8~), apt, lsb-release, python3 (>= 3.8), python3-dbus, python3-debian, python3-distro (>= 1.4.0), python3-gnupg, python3-launchpadlib, python3-lazr.restfulclient
Conffiles:
 /etc/dbus-1/system.d/org.pop_os.repolib.conf 65f9d606c6acfd2b29d725a0012f6880
Description: Repository Management for APT in Python (Python 3)
 RepoLib is a Python library and CLI tool-set for managing your software
 system software repositories. It's currently set up to handle APT repositories
 on Debian-based linux distributions.
 .
 RepoLib is intended to operate on DEB822-format sources. It aims to provide
 feature parity with software-properties for most commonly used functions.
 .
 This package installs the library for Python 3.
Homepage: https://github.com/isantop/repolib
```


## 从配置文件修改软件源

`Pop_OS`软件源的配置文件在目录`/etc/apt/sources.list`下，分为以下多个文件：
``` bash
$ ls /etc/apt/sources.list.d               
pop-os-apps.sources  pop-os-release.sources  system.sources
```
其中`pop-os-apps.sources`和`pop-os-release.sources`为system76团队（Pop_OS开发者）打包的Pop_OS专用软件源，`system.sources`为上游`Ubuntu`的软件源。这些配置文件由`python3-repolib`提供的命令行工具[`apt-manage`](https://repolib.readthedocs.io/en/latest/aptmanage/aptmanage.html)管理，不过我们直接用文本编辑器修改这些配置文件的内容比较方便。

### 1. 设置Pop_OS专用软件源镜像

查看一下`pop-os-release.sources`的内容：
``` bash
$ cat /etc/apt/sources.list.d/pop-os-release.sources 
X-Repolib-Name: Pop_OS Release Sources
Enabled: yes
Types: deb deb-src
URIs: http://apt.pop-os.org/release
Suites: jammy
Components: main
```
其具体语法可以参考[`python3-repolib`的文档](https://repolib.readthedocs.io/en/latest/index.html)。

国内可以使用由[`mirrors.tk`](https://mirrors.tk/help/pop-os/)提供的`Pop_OS`专用软件源。我们在`URIs`后添加`mirrors.tk`提供的地址`https://mirrors.tk/pop-os/release`，不同链接之间用空格分隔，然后添加以下行把`mirrors.tk`源设为默认：
``` 
X-Repolib-Default-Mirror: https://mirrors.tk/pop-os/release
```

修改完后`pop-os-release.sources`文件的内容如下：
``` sources
X-Repolib-Name: Pop_OS Release Sources
Enabled: yes
Types: deb deb-src
URIs: http://apt.pop-os.org/release https://mirrors.tk/pop-os/release
Suites: jammy
Components: main
X-Repolib-Default-Mirror: https://mirrors.tk/pop-os/release
```

同理，将`pop-os-apps.sources`修改为：
``` sources
X-Repolib-Name: Pop_OS Applications
Enabled: yes
Types: deb
URIs: http://apt.pop-os.org/proprietary https://mirrors.tk/pop-os/proprietary
Suites: jammy
Components: main
X-Repolib-Default-Mirror: https://mirrors.tk/pop-os/proprietary
```


### 2. 设置`Ubuntu`上游软件源

查看一下`system.sources`的内容：
``` bash
$ cat /etc/apt/sources.list.d/system.sources 
X-Repolib-Name: Pop_OS System Sources
Enabled: yes
Types: deb deb-src
URIs: http://apt.pop-os.org/ubuntu 
Suites: jammy jammy-security jammy-backports
Components: main restricted universe multiverse
X-Repolib-ID: system
X-Repolib-Default-Mirror: http://apt.pop-os.org/ubuntu
```

以[清华大学](https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/)和[阿里云](https://developer.aliyun.com/article/704603)提供的`Ubuntu`镜像为例，并设清华源为默认，将`system.sources`修改为：
``` sources
X-Repolib-Name: Pop_OS System Sources
Enabled: yes
Types: deb deb-src
URIs: http://apt.pop-os.org/ubuntu https://mirrors.tuna.tsinghua.edu.cn/ubuntu https://mirrors.aliyun.com/ubuntu 
Suites: jammy jammy-security jammy-backports
Components: main restricted universe multiverse
X-Repolib-ID: system
X-Repolib-Default-Mirror: https://mirrors.tuna.tsinghua.edu.cn/ubuntu
```


以上步骤执行完毕后，使用`sudo apt update`更新`apt`配置即可


## 其他

除了直接修改`/etc/apt/sources.list.d`目录下的文件，也可以使用命令行工具`apt-manage`来实现，具体用法可参考[`apt-manage`的文档](https://repolib.readthedocs.io/en/latest/aptmanage/aptmanage.html)