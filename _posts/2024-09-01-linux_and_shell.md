---
layout: post
title:  "Linux and Shell"
date:   2024-09-01
categories: go_big
---

### Background
Linux 是基于 Unix 的开源操作系统，通常用于服务器、台式机和嵌入式系统。

Linux terminal 是一个用于和操作系统进行交互以及执行各种任务的命令行界面，可以用作浏览目录文件及文件中的内容，你也可以用它来安装和升级软件包，也能创建和编辑文件以及执行 shell 脚本。

### Package management
常用的包管理器有：
#### [apt](https://wiki.debian.org/Apt)
Advanced Packaging Tools, 是一款 [Debian](https://www.debian.org/)-based Linux 系统最常用的包管理器。

背景信息：

*Some of the most popular Debian-based Linux distributions: Linux Mint, Ubuntu, Kali Linux.*

*Meanwhile Red Hat-based distributions are: CentOS, Fedora.*

*Red Hat-based Linux distributions are often preferred for enterprise environments and servers, focusing on stability and security. Whereas Debian-based Linux distributions moreover focus on long-term support and stability.*
Most Used Commands:
- install package: `sudo apt-get install [package_name]`;
- remove the package: `sudo apt-get remove [package_name]`;
- list details of installed package: `sudo apt-get list [package_name]`;

*More details refer to: [Linux man page](https://linux.die.net/man/8/apt-get)*.

#### [yum](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/5/html/deployment_guide/c1-yum)
Yellowdog Updater Modified, 用作 [Red Hat](https://www.redhat.com/en) Enterprise Linux versions 5 and later 的包管理；

Most Used Commands:
- install: `yum install [package_name]`;
- remove: `yum remove [package_name]`;
- update: `yum update`;

*More details refer to: [Yum Command Cheat Sheet](https://access.redhat.com/articles/yum-cheat-sheet)*.

#### [dnf](https://docs.fedoraproject.org/en-US/fedora/latest/system-administrators-guide/package-management/DNF/)
Dandified YUM, a package manager for .rpm-based Linux distributions, is now the default software package management tool in [Fedora](https://fedoraproject.org/).

Most Used Commands:
- search: `dnf search packagename`;
- install: `dnf install packagename`;
- remove: `dnf remove packagename`;

*More details refer to: [DNF Command Reference](https://dnf.readthedocs.io/en/latest/command_ref.html)*.

#### [pacman](https://pacman.archlinux.page/)
一款简单的 Linux 包管理器。

Most Used Commands:
- search: `sudo pacman -Ss keyword`;
- install: `sudo pacman -S pkgname`;
- remove: `sudo pacman -Rs package_name`;

*More details refer to: [pacman usage](https://wiki.archlinux.org/title/Pacman#Usage)*.

#### [pip](https://pip.pypa.io/en/stable/)
pip is the package installer for [Python](https://www.python.org/). Alternatively, [conda](https://www.anaconda.com/) is another popular package management tool.

Most Used Commands:
- search: `python -m pip search "key_word"`;
- install: `python -m pip install SomePackage`;
- uninstall: `python -m pip uninstall SomePackage`;

*More details refer to: [pip User Guide](https://pip.pypa.io/en/stable/user_guide/)*.

#### [brew](https://brew.sh/)
Homebrew installs the stuff you need that Apple (or your Linux system) didn’t.

Most Used Commands:
- search: `brew search text`;
- install: `brew install package_name`;
- uninstall: `brew uninstall package_name`;

*More details refer to: [brew command documatation](https://docs.brew.sh/Manpage)*.

**Shell learning and troubleshooting: [explainshell](https://explainshell.com/)**.

### Command Line Basics
What is [Bash](https://www.gnu.org/software/bash/)
- man: system manual pager;
- ssh: Secure Shell;
- ls: list all the files in your current working directory;
- pwd: print current working directory;
- cd: change working directory;
- touch: create a file or change file timestamp;
- echo: write arguments to standard output;
- nano: simple and useful editor;
- [vim](https://www.vim.org/): Vi IMproved text editor;
- cat: to see what's inside a file real quickly;
- mkdir: make a new directory;
- cp: copy file to a target directory;
- mv: move or rename a file;
- rm: remove files or directories;
- ln: make links to file;
- clear: clear your terminal screen;
- whoami: print effective userid;
- useradd: create a new user or update default new user information;
- sudo: execute a command as another user;
- su: run a command with substitute user and group ID;
- exit: exit the shell;
- passwd: change user password;
- [curl](https://curl.se/): transfer data with url;
- zip: package and compress (archive) files;
- unzip: extract compressed files in a zip;
- head: display first lines of a file;
- tail: display the last part of a file;
- diff: differential file and directory comparator;
- sort: sort lines of text files;
- find: search for files in a directory hierarchy;
- chmod: change file modes or Access Control Lists;
- chown: change file owner and group;
- ifconfig: configure network interface parameters;
- grep: file pattern searcher;
- [awk](https://www.gnu.org/software/gawk/manual/gawk.html): pattern-directed scanning and processing language;
- ping: send ICMP ECHO_REQUEST packets to network hosts;
- netstat: show network status;
- iptables: administration tool for IPv4/IPv6 packet filtering and NAT
- ufw: Uncomplicated Firewall for Ubuntu;
- uname: display information about the system;
- cal: displays a calendar and the date of Easter;
- df: display free disk space;
- ps: process status;
- top: display sorted information about processes;
- kill: terminate or signal a process;
- pkill: find or signal processes by name;
- history: command history;
- reboot: stopping and restarting the system;
- shutdown: close down the system at a given time;

Practical command line use cases on data science: [Data Science at the Command Line](https://jeroenjanssens.com/dsatcl/list-of-command-line-tools);

Learning the shell step by step: [Writing Shell Scripts](https://linuxcommand.org/lc3_writing_shell_scripts.php);

To be continued...