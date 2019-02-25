---
layout: post
title: "[Kernel] Linux Kernel Reading"
tag: Linux
toc: true
---

This article records my reading of Linux kernel based on Linux v3.2.

<!--more-->

# 1 Linux Kernel Brief Introduction

## 1.1 Linux Cross Reference

* [Linux Cross Reference (LXR)](http://lxr.linux.no/)
* [Cross-Referenced Linux and Device Driver Code](http://www.cs.fsu.edu/~baker/devices/lxr/http/source/)
* [Linux Syscall Reference](http://syscalls.kernelgrok.com/)
* [FreeBSD and Linux Kernel Cross-Reference](http://fxr.watson.org/)

## 1.2 Linux Kernel Git Repository

Git repositories hosted at [kernel.org](https://git.kernel.org/cgit/).

Refer to chapter 6.1.3 of **Pro Git** for the Git workflow of Linux kernel:

> 1) Regular developers work on their topic branch and rebase their work on top of master. The master branch is that of the dictator.
>
> 2) Lieutenants merge the developers' topic branches into their master branch.
>
> 3) The dictator merges the lieutenants' master branches into the dictator's master branch.
>
> 4) The dictator pushes their master to the reference repository so the other developers can rebase on it.

[**NOTE**] For Linux kernel developers, maybe it’s better for them to rebase their work on top of **linux-next** branch, refer to [1.2.2 linux-next tree](#1-2-2-linux-next-tree).

Git workflow:

![git_workflow](/assets/git_workflow.jpg)

Linux kernel code flow:

![linux_kernel_code_flow](/assets/linux_kernel_code_flow.jpg)

### 1.2.1 Git client repository

Git client:

* [http://git-scm.com/](http://git-scm.com/)
* [http://ndpsoftware.com/git-cheatsheet.html](http://ndpsoftware.com/git-cheatsheet.html)

Git client repository:

* [https://git.kernel.org/cgit/git/git.git](https://git.kernel.org/cgit/git/git.git)
* [git://git.kernel.org/pub/scm/git/git.git](git://git.kernel.org/pub/scm/git/git.git)
* [https://git.kernel.org/pub/scm/git/git.git](https://git.kernel.org/pub/scm/git/git.git)
* [https://kernel.googlesource.com/pub/scm/git/git.git](https://kernel.googlesource.com/pub/scm/git/git.git)

After Git is installed, you can also get Git via Git itself for updates:

```
# chenwx@chenwx:~ $ git clone git://git.kernel.org/pub/scm/git/git.git
chenwx@chenwx:~ $ git clone https://github.com/git/git

chenwx@chenwx:~ $ cd git/
chenwx@chenwx:~/git $ git checkout master
chenwx@chenwx:~/git $ git pull

chenwx@chenwx:~/git $ git tag -l --sort="v:refname" | tail
  v2.3.3
  v2.3.4
  v2.3.5
  v2.4.0-rc0
chenwx@chenwx:~/git $ git checkout v2.3.5

chenwx@chenwx:~/git $ sudo make prefix=/usr install install-doc install-html install-info
chenwx@chenwx:~/git $ git --version

chenwx@chenwx:~/git $ make distclean
chenwx@chenwx:~/git $ git checkout master
```

### 1.2.2 linux-next tree

**linux-next** tree:

* [git://git.kernel.org/pub/scm/linux/kernel/git/next/linux-next.git](git://git.kernel.org/pub/scm/linux/kernel/git/next/linux-next.git)
* [https://git.kernel.org/pub/scm/linux/kernel/git/next/linux-next.git](https://git.kernel.org/pub/scm/linux/kernel/git/next/linux-next.git)
* [https://kernel.googlesource.com/pub/scm/linux/kernel/git/next/linux-next.git](https://kernel.googlesource.com/pub/scm/linux/kernel/git/next/linux-next.git)

Materials related to **linux-next** tree:

* [http://lwn.net/Articles/268881/](http://lwn.net/Articles/268881/)
* [http://lwn.net/Articles/269120/](http://lwn.net/Articles/269120/)
* [http://lwn.net/Articles/289013/](http://lwn.net/Articles/289013/)
* [http://lwn.net/Articles/289245/](http://lwn.net/Articles/289013/)

This tree, to be maintained by *Stephen Rothwell*, is intended to be a gathering point for the patches which are planned to be merged in the next development cycle.

[**NOTE1**] As a kernel developer, you should send patches against **linux-next** tree, not the **mainline** tree.

[**NOTE2**] You can see which trees have been included by looking in the *linux/Next/Trees* file in the source. There are also *quilt-import.log* and *merge.log* files in the *linux/Next* directory:

```
chenwx@chenwx ~/linux/Next $ ll
-rw-r--r-- 1 chenwx chenwx 11K Feb 24 12:47 SHA1s
-rw-r--r-- 1 chenwx chenwx 17K Feb 24 12:47 Trees
-rw-r--r-- 1 chenwx chenwx 92K Feb 24 12:47 merge.log
-rw-r--r-- 1 chenwx chenwx  81 Feb 24 12:47 quilt-import.log
```

The **linux-next** tree has following branches:

* **stable** branch, trackes the master branch of linux **mainline** tree.
* **akpm** and **akpm-base** branches, track [http://www.ozlabs.org/~akpm/mmotm/](http://www.ozlabs.org/~akpm/mmotm/).
* **master** branch, the tags such as *next-20150324* are on this branch.

```
chenwx@chenwx ~/linux $ git br -r | grep linux-next
  linux-next/akpm
  linux-next/akpm-base
  linux-next/master
  linux-next/stable
```

#### 1.2.2.1 How to track linux-next tree

Tracking **linux-next** tree is a little bit different from usual trees. In particular, since *Stephen Rothwell* rebases it quite frequently, you shouldn't do a *git pull* on **linux-next**.

Note that **linux-next** isn't an 'evolving' tree like mainline, it's best to see it as being a list of individual kernels released as tags, i.e. you shouldn't be merging one into another.

Use following commands to track **linux-next** tree:

```
# (1) Change directory to ~/linux
chenwx@chenwx ~ $ cd linux

# (2) Fetch linux-next plus tags.
#     Note that all tags be fetched from the remote in addition to
#     whatever else is being fetched by command "git fetch --tags".
chenwx@chenwx ~/linux $ git fetch
chenwx@chenwx ~/linux $ git fetch --tags

# (3) Update linux-next tree
chenwx@chenwx ~/linux $ git checkout master
chenwx@chenwx ~/linux $ git remote update
Fetching origin

# (4) List recent linux-next tags
chenwx@chenwx ~/linux $ git tag -l "next-*" | tail
...
next-20141015
next-20141016
next-20141017

# (5) Choose the linux-next version that you will work from, and
#     create a local branch ec-task10-v1 based on that version
chenwx@chenwx ~/linux $ git checkout next-20141017 -b ec-task10-v1
Switched to a new branch 'ec-task10'
```

#### 1.2.2.2 Subsystem trees

[**NOTE**] Refer to file *linux/Next/Trees* in **linux-next** tree for subsystem trees.

##### 1.2.2.2.1 linux-staging tree

**linux-staging** tree:

* [git://git.kernel.org/pub/scm/linux/kernel/git/gregkh/staging.git](git://git.kernel.org/pub/scm/linux/kernel/git/gregkh/staging.git)
* [https://git.kernel.org/pub/scm/linux/kernel/git/gregkh/staging.git](https://git.kernel.org/pub/scm/linux/kernel/git/gregkh/staging.git)
* [https://kernel.googlesource.com/pub/scm/linux/kernel/git/gregkh/staging.git](https://kernel.googlesource.com/pub/scm/linux/kernel/git/gregkh/staging.git)

Materials related to **linux-staging** tree:

* [http://lwn.net/Articles/285599/](http://lwn.net/Articles/285599/)

The **linux-staging** tree was created to hold drivers and filesystems and other semi-major additions to the Linux kernel that are not ready to be merged at this point in time. It is here for companies and authors to get a wider range of testing, and to allow for other members of the community to help with the development of these features for the eventual inclusion into the main kernel tree.

##### 1.2.2.2.2 linux-security tree

**linux-security** tree:

* [https://git.kernel.org/cgit/linux/kernel/git/jmorris/linux-security.git](https://git.kernel.org/cgit/linux/kernel/git/jmorris/linux-security.git)
* [git://git.kernel.org/pub/scm/linux/kernel/git/jmorris/linux-security.git](git://git.kernel.org/pub/scm/linux/kernel/git/jmorris/linux-security.git)
* [https://git.kernel.org/pub/scm/linux/kernel/git/jmorris/linux-security.git](https://git.kernel.org/pub/scm/linux/kernel/git/jmorris/linux-security.git)
* [https://kernel.googlesource.com/pub/scm/linux/kernel/git/jmorris/linux-security.git](https://kernel.googlesource.com/pub/scm/linux/kernel/git/jmorris/linux-security.git)

### 1.2.3 mainline tree

Linux **mainline** tree:

* [git://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git](git://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git)
* [https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git)
* [https://kernel.googlesource.com/pub/scm/linux/kernel/git/torvalds/linux.git](https://kernel.googlesource.com/pub/scm/linux/kernel/git/torvalds/linux.git)

This is *Linux Torvalds*' git tree. There is only one branch, that's **master** branch, on the mainline tree.

[**NOTE1**] As a kernel developer, you should send patches against **linux-staging** or **linux-next** tree, not the mainline tree.

[**NOTE2**] Linux Torvalds负责维护mainline tree，在每个开发周期的merge window，新功能补丁会被合入mainline tree.

### 1.2.4 linux-stable tree

Refer to [Documentataion/translations/zh_CN/stable_kernel_rules.txt](https://git.kernel.org/pub/scm/linux/kernel/git/stable/linux.git/tree/Documentation/translations/zh_CN/stable_kernel_rules.txt).

Linux kernel stable tree:

* [https://git.kernel.org/cgit/linux/kernel/git/stable/linux-stable.git](https://git.kernel.org/cgit/linux/kernel/git/stable/linux-stable.git)
* [git://git.kernel.org/pub/scm/linux/kernel/git/stable/linux-stable.git](git://git.kernel.org/pub/scm/linux/kernel/git/stable/linux-stable.git)
* [http://git.kernel.org/pub/scm/linux/kernel/git/stable/linux-stable.git](http://git.kernel.org/pub/scm/linux/kernel/git/stable/linux-stable.git)
* [https://kernel.googlesource.com/pub/scm/linux/kernel/git/stable/linux-stable.git](https://kernel.googlesource.com/pub/scm/linux/kernel/git/stable/linux-stable.git)

Linux kernel stable patch queue:

* [https://git.kernel.org/cgit/linux/kernel/git/stable/stable-queue.git](https://git.kernel.org/cgit/linux/kernel/git/stable/stable-queue.git)
* [git://git.kernel.org/pub/scm/linux/kernel/git/stable/stable-queue.git](git://git.kernel.org/pub/scm/linux/kernel/git/stable/stable-queue.git)
* [https://git.kernel.org/pub/scm/linux/kernel/git/stable/stable-queue.git](git://git.kernel.org/pub/scm/linux/kernel/git/stable/stable-queue.git)
* [https://kernel.googlesource.com/pub/scm/linux/kernel/git/stable/stable-queue.git](git://git.kernel.org/pub/scm/linux/kernel/git/stable/stable-queue.git)

Each stable release has a corresponding branch on stable tree, such as *linux-3.2.y*. And its latest commits/maintainers can be found at [here](https://git.kernel.org/cgit/linux/kernel/git/stable/linux-stable.git/refs/heads).

Check the longterm branches on [https://www.kernel.org](https://www.kernel.org) and use following commands to track those branches:

```
chenwx@chenwx ~/linux $ git co linux-2.6.32.y
Checking out files: 100% (32771/32771), done.
Branch linux-2.6.32.y set up to track remote branch linux-2.6.32.y from origin.
Switched to a new branch 'linux-2.6.32.y'

chenwx@chenwx ~/linux $ git co linux-3.2.y
Checking out files: 100% (16874/16874), done.
Branch linux-3.2.y set up to track remote branch linux-3.2.y from origin.
Switched to a new branch 'linux-3.2.y'

chenwx@chenwx ~/linux $ git co linux-3.4.y
Checking out files: 100% (32682/32682), done.
Branch linux-3.4.y set up to track remote branch linux-3.4.y from origin.
Switched to a new branch 'linux-3.4.y'

chenwx@chenwx ~/linux $ git co linux-3.10.y
Checking out files: 100% (22201/22201), done.
Branch linux-3.10.y set up to track remote branch linux-3.10.y from origin.
Switched to a new branch 'linux-3.10.y'

chenwx@chenwx ~/linux $ git co linux-3.12.y
Checking out files: 100% (31307/31307), done.
Branch linux-3.12.y set up to track remote branch linux-3.12.y from origin.
Switched to a new branch 'linux-3.12.y'

chenwx@chenwx ~/linux $ git co linux-3.14.y
Checking out files: 100% (15876/15876), done.
Branch linux-3.14.y set up to track remote branch linux-3.14.y from origin.
Switched to a new branch 'linux-3.14.y'

chenwx@chenwx ~/linux $ git br
  linux-2.6.32.y
  linux-3.10.y
  linux-3.12.y
* linux-3.14.y
  linux-3.2.y
  linux-3.4.y
  master
```

[**NOTE**] **linux-stable** tree是对已发布的正式版本的后续维护，只包括一些bugfix或安全补丁，但不包括功能补丁。

### 1.2.5 Setup Linux Kernel Workarea

Run the following commands to clone all Linux kernel repositories into the same directory:

```
#
# (1) Clone mainline tree linux.git to ~/linux
#     git://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git
#     https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git
#
chenwx@chenwx ~ $ git clone https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git
Cloning into 'linux'...
remote: Counting objects: 3841355, done.
remote: Compressing objects: 100% (75674/75674), done.
remote: Total 3841355 (delta 56478), reused 0 (delta 0)
Receiving objects: 100% (3841355/3841355), 892.40 MiB | 2.47 MiB/s, done.
Resolving deltas: 100% (3147072/3147072), done.
Checking connectivity... done.
Checking out files: 100% (47936/47936), done.

#
# (2) Add next tree linux-next.git to ~/linux
#     git://git.kernel.org/pub/scm/linux/kernel/git/next/linux-next.git
#     https://git.kernel.org/pub/scm/linux/kernel/git/next/linux-next.git
#
chenwx@chenwx ~/linux $ git remote add linux-next https://git.kernel.org/pub/scm/linux/kernel/git/next/linux-next.git

#
# (2.1) Download source code from linux-next tree
#
chenwx@chenwx ~/linux $ git fetch linux-next
chenwx@chenwx ~/linux $ git fetch --tags linux-next

#
# (2.2) Create local branch to track master branch of linux-next tree
#
chenwx@chenwx ~/linux $ git branch --track next-master linux-next/master

#
# (3) Add stable tree linux-stable.git to ~/linux
#     git://git.kernel.org/pub/scm/linux/kernel/git/stable/linux-stable.git
#     https://git.kernel.org/pub/scm/linux/kernel/git/stable/linux-stable.git
#
chenwx@chenwx ~/linux $ git remote add linux-stable https://git.kernel.org/pub/scm/linux/kernel/git/stable/linux-stable.git

#
# (3.1) Download source code from linux-stable tree
#
chenwx@chenwx ~/linux $ git fetch linux-stable
chenwx@chenwx ~/linux $ git fetch --tags linux-stable

#
# (3.2) Create local branches to track longterm stable branches
#       Check the stable branches on website https://www.kernel.org/
#
chenwx@chenwx ~/linux $ git co linux-3.2.y
chenwx@chenwx ~/linux $ git co linux-3.4.y
chenwx@chenwx ~/linux $ git co linux-3.10.y
chenwx@chenwx ~/linux $ git co linux-3.12.y
chenwx@chenwx ~/linux $ git co linux-3.14.y
chenwx@chenwx ~/linux $ git co linux-3.16.y
chenwx@chenwx ~/linux $ git co linux-3.18.y
chenwx@chenwx ~/linux $ git co linux-4.1.y
chenwx@chenwx ~/linux $ git co linux-4.4.y
chenwx@chenwx ~/linux $ git co linux-4.5.y
chenwx@chenwx ~/linux $ git co linux-4.6.y

#
# (4) Show local branches
#
chenwx@chenwx ~/linux $ git br
  linux-3.10.y
  linux-3.12.y
  linux-3.14.y
  linux-3.16.y
  linux-3.18.y
  linux-3.2.y
  linux-3.4.y
  linux-4.1.y
  linux-4.4.y
  linux-4.5.y
  linux-4.6.y
* master
  next-master

#
# (5) Use the following commands to fetch objects from all remotes
#
chenwx@chenwx ~/linux $ git remote -v
linux-next	https://git.kernel.org/pub/scm/linux/kernel/git/next/linux-next.git (fetch)
linux-next	https://git.kernel.org/pub/scm/linux/kernel/git/next/linux-next.git (push)
linux-stable	https://git.kernel.org/pub/scm/linux/kernel/git/stable/linux-stable.git (fetch)
linux-stable	https://git.kernel.org/pub/scm/linux/kernel/git/stable/linux-stable.git (push)
origin	https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git (fetch)
origin	https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git (push)

chenwx@chenwx ~/linux $ git remote update
Fetching origin
Fetching linux-stable
Fetching linux-next

chenwx@chenwx ~/linux $ git fetch -all
Fetching origin
Fetching linux-stable
Fetching linux-next
```

## 1.3 Linux Kernel Mailing lists

订阅和取消订阅邮件列表

* [http://vger.kernel.org/vger-lists.html](http://vger.kernel.org/vger-lists.html)

### 1.3.1 lkml.org

lkml.org

* [https://lkml.org/](https://lkml.org/)

在下列页面中列出了每年的邮件统计数字：

* [https://lkml.org/lkml](https://lkml.org/lkml)

可通过下列方式查看某天的邮件：

https://lkml.org/lkml/\<Year\>/\<Month\>/\<Day\>

例如：[https://lkml.org/lkml/2014/3/31](https://lkml.org/lkml/2014/3/31)

[**NOTE**] 可以通过左侧的"Get diff 1"提取邮件中的Patch.

### 1.3.2 lkml.iu.edu

The Linux-Kernel Archive:

* [http://lkml.iu.edu//hypermail/linux/kernel/index.html](http://lkml.iu.edu//hypermail/linux/kernel/index.html)

### 1.3.3 marc.info

marc.info

* [http://marc.info/?l=linux-kernel](http://marc.info/?l=linux-kernel)

[**NOTE**] 采用Courier New字体，视觉效果好。

## 1.4 Linux Kernel Releases

通过下列命令查看某 Linux kernel release 的信息，以v3.2为例：

```
chenwx@chenwx ~/linux $ git tag -l v3.2
v3.2
chenwx@chenwx ~/linux $ git lc v3.2
commit 805a6af8dba5dfdd35ec35dc52ec0122400b2610 (HEAD, tag: v3.2)
Author:     Linus Torvalds <torvalds@linux-foundation.org>
AuthorDate: Wed Jan 4 15:55:44 2012 -0800
Commit:     Linus Torvalds <torvalds@linux-foundation.org>
CommitDate: Wed Jan 4 15:55:44 2012 -0800

    Linux 3.2

 Makefile | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
```

Refer to <a href="{{ site.base-url }}/2018/05/15/linux-kernel-releases.html">Linux Kernel Releases</a>.

### 1.4.1 Linux Versions

参见 **Understanding the Linux Kernel, 3rd Edition** 第 1. Introduction章第Linux Versions 节：

Up to kernel version 2.5, Linux identified kernels through a simple numbering scheme. Each version was characterized by three numbers, separated by periods. The first two numbers were used to identify the version; the third number identified the release. *The second version number identified the type of kernel: if it was even, it denoted a stable version; otherwise, it denoted a development version.*

During development of Linux kernel version 2.6, however, a significant change in the version numbering scheme has taken place. *Basically, the second number no longer identifies stable or development versions;* thus, nowadays kernel developers introduce large and significant changes in the current kernel version 2.6. A new kernel 2.7 branch will be created only when kernel developers will have to test a really disruptive change; this 2.7 branch will lead to a new current kernel version, or it will be backported to the 2.6 version, or finally it will simply be dropped as a dead end.

On 29 May 2011, *Linus Torvalds* announced that the kernel version would be bumped to 3.0 for the release following 2.6.39, *due to the minor version number getting too large and to commemorate the 20th anniversary of Linux.* It continued the time-based release practice introduced with 2.6.0, but using the second number - e.g. 3.1 would follow 3.0 after a few months. An additional number (now the third number) would be added on when necessary to designate security and bug fixes, as for example with 3.0.18. The major version number might be raised to 4 at some future date. Refer to [https://lkml.org/lkml/2011/5/29/204](https://lkml.org/lkml/2011/5/29/204).

## 1.5 Linux Kernel Bug Reporting

Linux内核开发者用于追踪内核Bug的网站

* [https://bugzilla.kernel.org/](https://bugzilla.kernel.org/)

## 1.6 Linux Kernel Development Process

参见下列文档：

* [Documentation/process/](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/Documentation/process)
* [How to Participate in the Linux Community](/docs/How_to_Participate_in_the_Linux_Community.pdf) from Linux Foundation

Linux kernel development cycle:

![linux_kernel_develop_process](/assets/linux_kernel_develop_process.jpg)

Linux kernel code flow:

![linux_kernel_code_flow](/assets/linux_kernel_code_flow.jpg)

## 1.7 Linux Kernel Related Books

* **Linux Kernel Development, 3rd Edition**. *Robert Love* Done on 2014-01-12
* **Understanding the Linux Kernel, 3rd Edition**. *Daniel P. Bovet & Marco Cesati* Done on 2014-03-13
* **Understanding the Linux Virtual Memory Manager**, July 9 2007, *Mel Gorman* Done on 2014-03-17
* **Linux Device Drivers, 3rd Edition**. *Jonathan Corbet, Alessandro Rubini, Greg Kroah-Hartman*
* **Understanding Linux Network Internals**. *Christian Benvenuti*
* [Linux Memory Management](http://linux-mm.org)

## 1.8 Linux Distributions

GNU/Linux Distribution Timeline:

* [http://futurist.se/gldt/](http://futurist.se/gldt/)

The LWN.net Linux Distribution List:

* [http://lwn.net/Distributions/](http://lwn.net/Distributions/)

### 1.8.1 Git trees for linux distributions

Git trees for linux distributions:

| Linux distributions | Git trees |
| :------------------ | :-------- |
| Ubuntu              | [https://wiki.ubuntu.com/Kernel/Dev/KernelGitGuide](https://wiki.ubuntu.com/Kernel/Dev/KernelGitGuide) |
| LinuxMint           | [https://github.com/linuxmint](https://github.com/linuxmint) |

<p/>

### 1.8.2 How to check version of linux distributions

#### 1.8.2.1 lsb_release -a

```
chenwx@chenwx:~ $ lsb_release -a
No LSB modules are available.
Distributor ID:	LinuxMint
Description:	Linux Mint 19 Tara
Release:	19
Codename:	tara
```

#### 1.8.2.2 /etc/\*-release

```
chenwx@chenwx:~ $ cat /etc/issue
Linux Mint 19 Tara \n \l

chenwx@chenwx:~ $ cat /etc/issue.net
Linux Mint 19 Tara

chenwx@chenwx:~ $ cat /etc/lsb-release
DISTRIB_ID=LinuxMint
DISTRIB_RELEASE=19
DISTRIB_CODENAME=tara
DISTRIB_DESCRIPTION="Linux Mint 19 Tara"

chenwx@chenwx:~ $ cat /etc/os-release
NAME="Linux Mint"
VERSION="19 (Tara)"
ID=linuxmint
ID_LIKE=ubuntu
PRETTY_NAME="Linux Mint 19"
VERSION_ID="19"
HOME_URL="https://www.linuxmint.com/"
SUPPORT_URL="https://forums.ubuntu.com/"
BUG_REPORT_URL="http://linuxmint-troubleshooting-guide.readthedocs.io/en/latest/"
PRIVACY_POLICY_URL="https://www.linuxmint.com/"
VERSION_CODENAME=tara
UBUNTU_CODENAME=bionic

chenwx@chenwx:~ $ cat /etc/debian_version
buster/sid
```

#### 1.8.2.3 uname -a

```
chenwx@chenwx:~ $ uname -a
Linux chenwx 4.15.0-39-generic #42-Ubuntu SMP Tue Oct 23 15:48:01 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux
```

#### 1.8.2.4 /proc/version

```
chenwx@chenwx:~ $ cat /proc/version
Linux version 4.15.0-39-generic (buildd@lgw01-amd64-054) (gcc version 7.3.0 (Ubuntu 7.3.0-16ubuntu3)) #42-Ubuntu SMP Tue Oct 23 15:48:01 UTC 2018
```

#### 1.8.2.5 dmesg

```
chenwx@chenwx:~ $ dmesg | grep "Linux"
[    0.000000] Linux version 4.15.0-39-generic (buildd@lgw01-amd64-054) (gcc version 7.3.0 (Ubuntu 7.3.0-16ubuntu3)) #42-Ubuntu SMP Tue Oct 23 15:48:01 UTC 2018 (Ubuntu 4.15.0-39.42-generic 4.15.18)
[    0.044097] ACPI: Added _OSI(Linux-Dell-Video)
[    0.044098] ACPI: Added _OSI(Linux-Lenovo-NV-HDMI-Audio)
[    0.050972] ACPI: [Firmware Bug]: BIOS _OSI(Linux) query ignored
[    1.099636] Linux agpgart interface v0.103
[    2.044123] usb usb1: Manufacturer: Linux 4.15.0-39-generic ehci_hcd
[    2.064119] usb usb2: Manufacturer: Linux 4.15.0-39-generic ehci_hcd
[    2.064730] usb usb3: Manufacturer: Linux 4.15.0-39-generic uhci_hcd
[    2.065177] usb usb4: Manufacturer: Linux 4.15.0-39-generic uhci_hcd
[    2.065640] usb usb5: Manufacturer: Linux 4.15.0-39-generic uhci_hcd
[    2.066097] usb usb6: Manufacturer: Linux 4.15.0-39-generic uhci_hcd
[    2.066567] usb usb7: Manufacturer: Linux 4.15.0-39-generic uhci_hcd
[    2.455143] pps_core: LinuxPPS API ver. 1 registered
[   18.436173] VBoxPciLinuxInit
```

#### 1.8.2.6 yum / dnf

```
$ yum info nano
Loaded plugins: fastestmirror, ovl
Loading mirror speeds from cached hostfile
* base: centos.zswap.net
* extras: mirror2.evolution-host.com
* updates: centos.zswap.net
Available Packages
Name : nano
Arch : x86_64
Version : 2.3.1
Release : 10.el7
Size : 440 k
Repo : base/7/x86_64
Summary : A small text editor
URL : http://www.nano-editor.org
License : GPLv3+
Description : GNU nano is a small and friendly text editor.

$ yum repolist
Loaded plugins: fastestmirror, ovl
Loading mirror speeds from cached hostfile
* base: centos.zswap.net
* extras: mirror2.evolution-host.com
* updates: centos.zswap.net
repo id repo name status
base/7/x86_64 CentOS-7 - Base 9591
extras/7/x86_64 CentOS-7 - Extras 388
updates/7/x86_64 CentOS-7 - Updates 1929
repolist: 11908

$ dnf info nano
Last metadata expiration check: 0:01:25 ago on Thu Feb 15 01:59:31 2018.
Installed Packages
Name : nano
Version : 2.8.7
Release : 1.fc27
Arch : x86_64
Size : 2.1 M
Source : nano-2.8.7-1.fc27.src.rpm
Repo : <a href="http://www.jobbole.com/members/system">@System</a>
From repo : fedora
Summary : A small text editor
URL : https://www.nano-editor.org
License : GPLv3+
Description : GNU nano is a small and friendly text editor.
```

#### 1.8.2.7 rpm

```
$ rpm -q nano
nano-2.8.7-1.fc27.x86_64
```

#### 1.8.2.8 apt-get

```
chenwx@chenwx:~ $ apt-cache policy nano
nano:
  Installed: (none)
  Candidate: 2.9.3-2
  Version table:
     2.9.3-2 500
        500 http://mirrors.aliyun.com/ubuntu bionic/main amd64 Packages
```

# 2 Linux Kernel源代码结构

本文中的目录和文件均相对于目录```~/linux/```，参见[1.2.5 Setup Linux Kernel Workarea](#1-2-5-setup-linux-kernel-workarea)节。

## 2.1 说明文件

linux/目录下的文件：

| Files | Description |
| :---- | :---------- |
| README | Linux内核说明文档，简要介绍了Linux内核的背景，描述了配置和build内核需要什么。 |
| COPYING | 版权声明 |
| CREDITS | Linux内核贡献人员列表 |
| MAINTAINERS | Linux维护人员信息 |
| REPORTING-BUGS | 报告Bug的流程及模板 |

<p/>

Documentation/目录下的文件：

| Documentation/ | Description |
| :------------- | :---------- |
| 00-INDEX | Documentation/下各目录的内容 |
| email-clients.txt | 使用邮件发送patch时， 需要对邮件客户端进行特殊配置。 |
| Changes | 列出了成功编译和运行内核所需的各种软件包的最小集合。 |
| CodingStyle | 描述了Linux内核编码风格， 和一些隐藏在背后的基本原理。 所有的想加入内核的新代码应当遵循这篇文档的指导。 绝大数的内核代码维护者只愿意接受那些符合这篇文档描述的风格的补丁， 许多内核开发者也只愿意审查那些符合Linux内核编码风格的代码。 |
| development-process | Linux kernel development process. |
| SubmittingPatches<br>SubmittingDrivers<br>SubmitChecklist | 描述了如何成功的创建和向社区递交一个补丁， 包括：邮件内容、邮件格式、发送者和接收者。 遵循文档里提倡的规则并不一定保证你提交补丁成功 (因为所有的补丁遭受详细而严格的内容和风格的审查)， 但是不遵循它们， 提交补丁肯定不成功。 |
| stable_api_nonsense.txt | 这篇文档描述了有意决定在内核里没有固定内核API的基本原因， 这对于理解Linux的开发哲学非常关键， 也对于从其他操作系统转移到Linux上的开发人员非常重要。 |
| SecurityBugs | 如果你确知在Linux Kernel里发现了security problem， 请遵循这篇文档描述的步骤， 帮助通知内核的开发者们并解决这类问题。 |
| ManagementStyle | 这篇文档描述了Linux内核开发者们如何进行管理运作， 以及运作方法背后的分享精神(shared ethos)。 这篇文档对于那些内核开发新手们(或者那些好奇者)值得一读， 因为它解决或解释了很多对于内核维护者独特行为的误解。 |
| stable_kernel_rules.txt | 这篇文档描述了一个稳定的内核版本如何发布的规则， 以及需要做些什么如果你想把一个修改加入到其中的一个版本。 |
| kernel-docs.txt | 关于内核开发的外部文档列表。 |
| applying-patches.txt | 描述了什么是补丁(patch)， 以及如何将它应用到内核的不同开发分支(branch)上。 |
| kbuild/kconfig.txt | Information on using the Linux kernel config tools. |
| DocBook/ | 内核里有大量的由内核源码自动生成的文档。 其中包括了内核内部API的全面描述， 和如何处理好锁的规则。 文档格式包括 PDF, Postscritpt, HTML 和 man pages， 可在内核源码主目录下运行下列命令自动生成， 见下文。 |

<p/>

检查内核代码风格：

```
step 1) 运行脚本scripts/Lindent使源代码符合Linux Kernel的代码风格：
# scripts/Lindent <file>

或者，运行下列命令来格式化源代码：
# indent -kr -i8 -ts8 -sob -l80 -ss -bs -psl <file>

step 2) 运行下列脚本来检查代码格式的合法性：
# scripts/checkpatch.pl --terse --file <file>
```

[**NOTE**] The style checker ```scripts/chechpatch.pl``` should be viewed as a guide not as the final word. If your code looks better with a violation then its probably best left alone.

[**NOTE**] The pre-condition of running ```scripts/Lindent``` and ```indent``` is that the source files use unix format, use below command to transfer source file format:

```
# dos2unix <file>
# unix2dos <file>
```

在内核源码根目录下执行下列命令会在DocBook/目录下生成不同格式的文档：

```
/*
 * (1) 顶层Makefile中有关内核文档的目标
 */
chenwx@chenwx ~/linux $ ll Documentation/DocBook/
total 1.1M
-rw-r--r-- 1 chenwx chenwx  21K Aug 11 09:12 80211.tmpl
-rw-r--r-- 1 chenwx chenwx 7.1K Aug 11 09:12 Makefile
-rw-r--r-- 1 chenwx chenwx 4.0K Aug 11 09:12 alsa-driver-api.tmpl
-rw-r--r-- 1 chenwx chenwx  69K Aug 12 08:25 crypto-API.tmpl
-rw-r--r-- 1 chenwx chenwx  16K Aug 11 09:10 debugobjects.tmpl
-rw-r--r-- 1 chenwx chenwx  15K Aug 11 09:12 device-drivers.tmpl
-rw-r--r-- 1 chenwx chenwx  12K Aug 11 09:10 deviceiobook.tmpl
-rw-r--r-- 1 chenwx chenwx 174K Aug 12 08:25 drm.tmpl
...

chenwx@chenwx ~/linux $ make help
...
Documentation targets:
 Linux kernel internal documentation in different formats:
  htmldocs        - HTML
  pdfdocs         - PDF
  psdocs          - Postscript
  xmldocs         - XML DocBook
  mandocs         - man pages
  installmandocs  - install man pages generated by mandocs
  cleandocs       - clean all generated DocBook files
...

/*
 * (2) 编译HTML格式的内核文档
 */
chenwx@chenwx ~/linux $ make O=../linux-build/ htmldocs
...
  HTML    Documentation/DocBook/z8530book.html
rm -rf Documentation/DocBook/index.html; echo '<h1>Linux Kernel HTML Documentation</h1>' >> Documentation/DocBook/index.html && echo '<h2>Kernel Version: 4.1.6</h2>' >> Documentation/DocBook/index.html && cat Documentation/DocBook/80211.html Documentation/DocBook/alsa-driver-api.html Documentation/DocBook/crypto-API.html Documentation/DocBook/debugobjects.html Documentation/DocBook/device-drivers.html Documentation/DocBook/deviceiobook.html Documentation/DocBook/drm.html Documentation/DocBook/filesystems.html Documentation/DocBook/gadget.html Documentation/DocBook/genericirq.html Documentation/DocBook/kernel-api.html Documentation/DocBook/kernel-hacking.html Documentation/DocBook/kernel-locking.html Documentation/DocBook/kgdb.html Documentation/DocBook/libata.html Documentation/DocBook/librs.html Documentation/DocBook/lsm.html Documentation/DocBook/media_api.html Documentation/DocBook/mtdnand.html Documentation/DocBook/networking.html Documentation/DocBook/rapidio.html Documentation/DocBook/regulator.html Documentation/DocBook/s390-drivers.html Documentation/DocBook/scsi.html Documentation/DocBook/sh.html Documentation/DocBook/tracepoint.html Documentation/DocBook/uio-howto.html Documentation/DocBook/usb.html Documentation/DocBook/w1.html Documentation/DocBook/writing-an-alsa-driver.html Documentation/DocBook/writing_musb_glue_layer.html Documentation/DocBook/writing_usb_driver.html Documentation/DocBook/z8530book.html >> Documentation/DocBook/index.html

/*
 * (3) 查看编译后的HTML格式的内核文档
 */
chenwx@chenwx ~/linux $ firefox ../linux-build/Documentation/DocBook/index.html &
```

## 2.2 配置文件

| Files | Description |
| :---- | :---------- |
| Kconfig, */Kconfig | 内核配置选项文件Kconfig |
| Kbuild, */Kbuild | 内核编译系统Kbuild的Makefile文件 |
| Makefile | 顶层Makefile文件 |

<p/>

## 2.3 代码文件

| Directory | Description |
| :-------- | :---------- |
| arch/ | 包含所有与特定硬件结构相关的内核代码。arch目录下处理器体系架构介绍，参见arch目录下处理器体系架构介绍节。 |
| block/ | block层的实现。最初block层的代码一部分位于drivers/目录，一部分位于fs/目录，从2.6.15开始，block 层的核心代码被提取出来放在了顶层的block/目录。 |
| certs/ | Since Linux kernel version 3.7 onwards, support has been added for signed kernel modules. When enabled, the Linux kernel will only load kernel modules that are digitally signed with the proper key. This allows further hardening of the system by disallowing unsigned kernel modules, or kernel modules signed with the wrong key, to be loaded. Malicious kernel modules are a common method for loading rootkits on a Linux system. Refer to [Signed Kernel Module Support](/docs/signed_kernel_module_support.pdf). |
| crypto/ | 内核本身所用的加密API，实现了常用的加密和散列算法，还有一些压缩和CRC校验算法。 |
| drivers/ | 包含内核中所有的设备驱动程序，每种驱动程序占用一个子目录，如块设备，scsi设备驱动程序等。 |
| firmware/ | 使用某些驱动程序而需要的设备固件。 |
| fs/ | 包含所有文件系统的代码，每种文件系统占用一个子目录，如ext2、ext3、ext4等。 |
| include/ | 包含编译内核代码时所需的大部分头文件。与体系架构无关的头文件包含在include/linux目录下。 |
| init/ | 包含内核的初始化代码，这是内核开始工作的起点。 |
| ipc/ | 包含进程间通信的代码。 |
| kernel/ | 包含主要的核心代码。与体系架构有关的核心代码包含在arch/$(ARCH)/kernel/目录下。 |
| lib/ | 核心的库代码。与arch/$(ARCH)/lib下的代码不同，这里的库代码都是用C编写的，在内核新的移植版本中可以直接使用。 |
| mm/ | 包含所有与体系架构无关的内存管理代码。与体系架构有关的内存管理代码包含在arch/$(ARCH)/mm/目录下。 |
| net/ | 包含内核的网络代码。 |
| samples/ | Linux内核的示范代码。 |
| scripts/ | 包含编译内核所用的脚本等文件。 |
| security/ | 包括了不同的Linux安全模型的代码，例如: NSA Security-Enhanced Linux. |
| sound/ | 声卡驱动以及其他声音相关的代码。 |
| tools/ | Tools helpful for developing Linux. |
| perf/ | 由内核维护人员Ingo Molnar等人开发的Linux内核综合性能概要分析工具。 |
| usr/ | Early user-space code (called initramfs). |
| virt/ | Virtualization infrastructure. |

<p/>

脚本```scripts/get_maintainer.pl```用于检测指定内核子系统的维护者，例如：

```
chenwx@chenwx ~/linux $ scripts/get_maintainer.pl -f fs
Alexander Viro <viro@zeniv.linux.org.uk> (maintainer:FILESYSTEMS (VFS...)
linux-fsdevel@vger.kernel.org (open list:FILESYSTEMS (VFS...)
linux-kernel@vger.kernel.org (open list)
```

# 3 Linux Kernel配置、编译与升级

Linux kernel的编译流程：

![Linux_Kernel_Compiling](/assets/Linux_Kernel_Compiling.jpg)

## 3.1 概述

参见目录[Documentation/kbuild/](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/Documentation/kbuild)中的下列文档：

| 00-INDEX | info on the kernel build process |
| kbuild.txt | developer information on kbuild |
| kconfig.txt | usage help for make *config |
| kconfig-language.txt | specification of Config Language, the language in Kconfig files |
| makefiles.txt | developer information for linux kernel makefiles |
| modules.txt | how to build modules and to install them |

<p/>

[**NOTE**] 编译系统前，需要先检查系统中相关工具的版本是否满足文件Documentation/Changes所列出的最小要求！

## 3.2 Kbuild编译系统

参见下列说明文档：

* [Documentation/kbuild/kbuild.txt](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/Documentation/kbuild/kbuild.txt)
* [Documentation/kbuild/makefiles.txt](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/Documentation/kbuild/makefiles.txt)
* [Documentation/kbuild/modules.txt](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/Documentation/kbuild/modules.txt)

### 3.2.0 Kbuild和Makefile的关系

由[Documentation/kbuild/makefiles.txt](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/Documentation/kbuild/modules.txt)中的描述:

> The preferred name for the kbuild files are 'Makefile' but 'Kbuild' can be used and if both a 'Makefile' and a 'Kbuild' file exists, then the 'Kbuild' file will be used.

可知，Kbuild编译系统的配置文件名为Makefile或Kbuild，若在同一个目录中同时存在Makefile和Kbuild，则优先采用Kbuild，参见scripts/Makefile.build：

```
# The filename Kbuild has precedence over Makefile
kbuild-dir := $(if $(filter /%,$(src)),$(src),$(srctree)/$(src))
kbuild-file := $(if $(wildcard $(kbuild-dir)/Kbuild),$(kbuild-dir)/Kbuild,$(kbuild-dir)/Makefile)
include $(kbuild-file)
```

运行下列命令查找同时包含Kbuild和Makefile的目录：

```
chenwx@chenwx ~/linux $ find . -name Makefile | xargs dirname | sort > dir_Makefile.txt
chenwx@chenwx ~/linux $ find . -name Kbuild | xargs dirname | sort > dir_Kbuild.txt

chenwx@chenwx ~/linux $ comm -12 dir_Makefile.txt dir_Kbuild.txt
.
./arch/arc
./arch/mips
./arch/s390
./arch/sparc
./arch/tile
./arch/x86
./tools/testing/nvdimm
```

由此可知，同时包含Makefile和Kbuild的目录仅有：

```
~/linux/
~/linux/arch/arc/
~/linux/arch/mips/
~/linux/arch/s390/
~/linux/arch/sparc/
~/linux/arch/tile/
~/linux/arch/x86/
```

其中，顶层Makefile是make直接调用的，其他的linux/$(SRCARCH)/Makefile则是通过顶层Makefile引入的：

```
linux/Makefile
+- include scripts/Kbuild.include
|  +- build := -f $(srctree)/scripts/Makefile.build obj
+- include arch/$(SRCARCH)/Makefile
```

此外，还可以通过下列命令统计内核中Makefile和Kbuild文件数目(v4.9-rc1)：

```
chenwx@chenwx ~/linux $ find . -name Makefile | wc -l
2260
chenwx@chenwx ~/linux $ find . -name Kbuild | wc -l
173
```

### 3.2.1 采用Kbuild编译系统的Linux Kernel版本

从Linux Kernel v2.6起，Linux内核的编译采用Kbuild系统。和过去的编译系统有很大的不同，尤其对于Linux内核模块的编译。在新的系统下，Linux编译系统会两次扫描Linux的Makefile:

* 首先，编译系统会读取Linux内核顶层的Makefile (通过在linux的顶层目录执行make命令来读取Makefiles);
* 然后，根据读到的内容第二次读取Kbuild的Makefile来编译Linux内核 (参见[3.2.0 Kbuild和Makefile的关系](#3-2-0-kbuild-makefile-)节)。

### 3.2.2 Kbuild编译系统概述

#### 3.2.2.1 编译进内核/$(obj-y)

Kbuild Makefile规定所有编译进内核的目标文件都保存在$(obj-y)列表中，而该列表依赖于内核的配置。Kbuild编译$(obj-y)列表中的所有文件。然后，调用"$(LD) -r"将它们连接到*/build-in.o，该类文件会被顶层Makefile链接进vmlinux中。

注意：在Documentation/kbuild/makefiles.txt中，包含下列描述：

> The order of files in $(obj-y) is significant. Duplicates in the lists are allowed: the first instance will be linked into built-in.o and succeeding instances will be ignored.

由此可知，$(obj-y)中文件的顺序是重要的！

**如何确定$(obj-y)中文件的顺序?**

可以根据下列几个方面来确定$(obj-y)中文件的顺序:

1) 确定目录及其子目录的编译顺序，参见[3.2.2.4 递归访问下级目录](#3-2-2-4-)节和[Appendix A: make -f scripts/Makefile.build obj=列表](#appendix-a-make-f-scripts-makefile-build-obj-)节;

2) 根据该目录中的Makefile及配置文件.config来确定该目录下文件的编译顺序。例如linux/fs/ext2/Makefile，根据宏CONFIG_EXT2_*的取值就可以确定文件的编译顺序了:

```
obj-$(CONFIG_EXT2_FS)            += ext2.o
ext2-y                           := balloc.o dir.o file.o ialloc.o inode.o \
                                    ioctl.o namei.o super.o symlink.o
ext2-$(CONFIG_EXT2_FS_XATTR)     += xattr.o xattr_user.o xattr_trusted.o
ext2-$(CONFIG_EXT2_FS_POSIX_ACL) += acl.o
ext2-$(CONFIG_EXT2_FS_SECURITY)  += xattr_security.o
```

#### 3.2.2.2 编译成模块/$(obj-m)

模块可以通过insmod命令加载。$(obj-m)列举出了哪些文件要编译成可加载模块。一个模块可以由一个或多个文件编译而成。如果是一个源文件，Kbuild Makefile只需简单的将其加到$(obj-m)中去就可以了。如果内核模块是由多个源文件编译而成，那就要采用下列方法声明所要编译的模块：

```
#drivers/isdn/i4l/Makefile
obj-$(CONFIG_FOO) += isdn.o
isdn-objs := isdn_net_lib.o isdn_v110.o isdn_common.o
```

Kbuild需要知道所编译的模块是基于哪些源文件，所以需要通过变量$(<module_name>-objs)来告诉它。在本例中，isdn是模块名，Kbuild将编译在$(isdn-objs)中列出的所有文件，然后使用"$(LD) -r"生成isdn.o。

注：上述语法同样适用于将源文件编译进内核。

#### 3.2.2.3 编译成库文件/$(lib-y)/$(lib-m)

在$(lib-y)中列出的文件用来编译成该目录下的一个库文件lib.a，例如lib/lib.a和arch/x86/lib/lib.a。通常，$(lib-y)用于lib/和arch/\*/lib目录。

在$(obj-y)与$(lib-y)中同时列出的文件，因为该文件在内核和库文件中都是可以访问的，所以该文件是不会被包含在库文件中的。在，$(lib-m)中的文件就要包含在lib.a库文件中。参见scripts/Makefile.lib:

```
# Figure out what we need to build from the various variables
# ===========================================================================

# When an object is listed to be built compiled-in and modular,
# only build the compiled-in version

obj-m := $(filter-out $(obj-y),$(obj-m))

# Libraries are always collected in one lib file.
# Filter out objects already built-in

lib-y := $(filter-out $(obj-y), $(sort $(lib-y) $(lib-m)))
```

注：Kbuild Makefile可以同时列出要编译进内核的文件和要编译成库的文件。所以，在一个目录里可以同时存在built-in.o和lib.a两个文件，例如由checksum.o和delay.o两个文件创建一个库文件lib.a：

```
#arch/x86/lib/Makefile
lib-y := chechsum.o delay.o
```

为了让Kbuild真正认识到这里要有一个库文件lib.a要创建，其所在的目录要加到$(libs-y)列表中，参见顶层Makefile:

```
libs-y	:= lib/

libs-y1	:= $(patsubst %/, %/lib.a, $(libs-y))
libs-y2	:= $(patsubst %/, %/built-in.o, $(libs-y))
libs-y	:= $(libs-y1) $(libs-y2)

vmlinux-main := $(core-y) $(libs-y) $(drivers-y) $(net-y)
```

此外，可以使用下列命令查看lib.a中包含的目标文件：

```
chenwx@chenwx ～/linux $ objdump -a lib/lib.a
In archive lib/lib.a:

argv_split.o:     file format elf32-i386
rw-r--r-- 0/0   1708 Jan  1 02:00 1970 argv_split.o

bug.o:     file format elf32-i386
rw-r--r-- 0/0   2256 Jan  1 02:00 1970 bug.o

cmdline.o:     file format elf32-i386
rw-r--r-- 0/0   1936 Jan  1 02:00 1970 cmdline.o

...
chenwx@chenwx ～/linux $ readelf -A lib/lib.a

File: lib/lib.a(argv_split.o)

File: lib/lib.a(bug.o)

File: lib/lib.a(cmdline.o)

...
```

#### 3.2.2.4 递归访问下级目录

一个Kbuild Makefile只对编译所在目录的对象负责。在子目录中文件的编译要由其所在子目录中的Makefile来管理。只要让Kbuild知道它应该递归操作，那么该系统就会在其子目录中自动的调用make递归操作，这就是$(obj-y)和$(obj-m)的作用。例如，ext2被放的一个单独的目录下，在fs目录下的Makefile会告诉Kbuild使用下面的赋值进行向下递归操作：

```
# fs/Makefile
obj-$(CONFIG_EXT2_FS) += ext2/
```

如果CONFIG_EXT2_FS被设置为'y'(编译进内核)或是'm'(编译成模块)，相应的obj-变量就会被设置，故Kbuild就会递归向下访问ext2目录。Kbuild只是用这些信息来决定是否需要访问该目录，而具体怎么编译由该目录中的Makefile来决定，参见编译$(obj)下的子目录节。

[**NOTE**] 将CONFIG_变量设置成目录名是一个好的编程习惯，这让Kbuild在完全忽略那些相应的CONFIG_值不是'y'和'm'的目录。

#### 3.2.2.5 编译标志

Kbuild编译系统中用到的编译标志包括：

```
EXTRA_CFLAGS		// 用$(CC)编译C源文件时的选项
EXTRA_AFLAGS		// 针对每个目录的选项，只不过它是用来编译汇编源代码的
EXTRA_LDFLAGS
EXTRA_ARFLAGS
CFLAGS_$@		// 是$(CC)针对每个文件的选项，而不是目录。$@ 表明了具体操作的文件
AFLAGS_$@
```

这些EXTRA_开头的大写字母变量都是编译标志，所有的EXTRA_变量只在所定义的Kbuild Makefile中起作用。EXTRA_变量可以在Kbuild Makefile中所有命令中使用。

参见[Documentation/kbuild/makefiles.txt](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/Documentation/kbuild/makefiles.txt)第3.7 Compilation flags节。

### 3.2.3 Make命令

make命令：

```
# make help   // 帮助信息，参见下表
# make V=1    // 输出详细命令，默认V=0
# make -n     // 仅打印出要执行的命令，并不进行实际编译
# make -j4    // 可加快编译速度
```

对于kernel v3.18而言，make help打印下列帮助信息：

```
chenwx@chenwx ~/linux $ make help
Cleaning targets:
  clean			- Remove most generated files but keep the config and
			  enough build support to build external modules
  mrproper		- Remove all generated files + config + various backup files
  distclean		- mrproper + remove editor backup and patch files

Configuration targets:
  config		- Update current config utilising a line-oriented program
  nconfig		- Update current config utilising a ncurses menu based program
  menuconfig		- Update current config utilising a menu based program
  xconfig		- Update current config utilising a QT based front-end
  gconfig		- Update current config utilising a GTK based front-end
  oldconfig		- Update current config utilising a provided .config as base
  localmodconfig	- Update current config disabling modules not loaded
  localyesconfig	- Update current config converting local mods to core
  silentoldconfig	- Same as oldconfig, but quietly, additionally update deps
  defconfig		- New config with default from ARCH supplied defconfig
  savedefconfig		- Save current config as ./defconfig (minimal config)
  allnoconfig		- New config where all options are answered with no
  allyesconfig		- New config where all options are accepted with yes
  allmodconfig		- New config selecting modules when possible
  alldefconfig		- New config with all symbols set to default
  randconfig		- New config with random answer to all options
  listnewconfig		- List new options
  olddefconfig		- Same as silentoldconfig but sets new symbols to their default value
  kvmconfig		- Enable additional options for guest kernel support
  tinyconfig		- Configure the tiniest possible kernel

Other generic targets:
  all			- Build all targets marked with [*]
* vmlinux		- Build the bare kernel
* modules		- Build all modules
  modules_install	- Install all modules to INSTALL_MOD_PATH (default: /)
  firmware_install	- Install all firmware to INSTALL_FW_PATH
			  (default: $(INSTALL_MOD_PATH)/lib/firmware)
  dir/			- Build all files in dir and below
  dir/file.[oisS]	- Build specified target only
  dir/file.lst		- Build specified mixed source/assembly target only
			  (requires a recent binutils and recent build (System.map))
  dir/file.ko		- Build module including final link
  modules_prepare	- Set up for building external modules
  tags/TAGS		- Generate tags file for editors
  cscope		- Generate cscope index
  gtags			- Generate GNU GLOBAL index
  kernelrelease		- Output the release version string (use with make -s)
  kernelversion		- Output the version stored in Makefile (use with make -s)
  image_name		- Output the image name (use with make -s)
  headers_install	- Install sanitised kernel headers to INSTALL_HDR_PATH
			  (default: ./usr)

Static analysers
  checkstack		- Generate a list of stack hogs
  namespacecheck	- Name space analysis on compiled kernel
  versioncheck		- Sanity check on version.h usage
  includecheck		- Check for duplicate included header files
  export_report		- List the usages of all exported symbols
  headers_check		- Sanity check on exported headers
  headerdep		- Detect inclusion cycles in headers
  coccicheck		- Check with Coccinelle.

Kernel selftest
  kselftest		- Build and run kernel selftest (run as root)
			  Build, install, and boot kernel before
			  running kselftest on it

Kernel packaging:
  rpm-pkg		- Build both source and binary RPM kernel packages
  binrpm-pkg		- Build only the binary kernel package
  deb-pkg		- Build the kernel as a deb package
  tar-pkg		- Build the kernel as an uncompressed tarball
  targz-pkg		- Build the kernel as a gzip compressed tarball
  tarbz2-pkg		- Build the kernel as a bzip2 compressed tarball
  tarxz-pkg		- Build the kernel as a xz compressed tarball
  perf-tar-src-pkg	- Build perf-3.18.0.tar source tarball
  perf-targz-src-pkg	- Build perf-3.18.0.tar.gz source tarball
  perf-tarbz2-src-pkg	- Build perf-3.18.0.tar.bz2 source tarball
  perf-tarxz-src-pkg	- Build perf-3.18.0.tar.xz source tarball

Documentation targets:
 Linux kernel internal documentation in different formats:
  htmldocs		- HTML
  pdfdocs		- PDF
  psdocs		- Postscript
  xmldocs		- XML DocBook
  mandocs		- man pages
  installmandocs	- install man pages generated by mandocs
  cleandocs		- clean all generated DocBook files

Architecture specific targets (x86):
* bzImage		- Compressed kernel image (arch/x86/boot/bzImage)
  install		- Install kernel using
			  (your) ~/bin/installkernel or
			  (distribution) /sbin/installkernel or
			  install to $(INSTALL_PATH) and run lilo
  fdimage		- Create 1.4MB boot floppy image (arch/x86/boot/fdimage)
  fdimage144		- Create 1.4MB boot floppy image (arch/x86/boot/fdimage)
  fdimage288		- Create 2.8MB boot floppy image (arch/x86/boot/fdimage)
  isoimage		- Create a boot CD-ROM image (arch/x86/boot/image.iso)
			  bzdisk/fdimage*/isoimage also accept:
			  FDARGS="..."  arguments for the booted kernel
			  FDINITRD=file initrd for the booted kernel

  i386_defconfig	- Build for i386
  x86_64_defconfig	- Build for x86_64

  make V=0|1 [targets]   0 => quiet build (default), 1 => verbose build
  make V=2   [targets]   2 => give reason for rebuild of target
  make O=dir [targets]   Locate all output files in "dir", including .config
  make C=1   [targets]   Check all c source with $CHECK (sparse by default)
  make C=2   [targets]   Force check of all c source with $CHECK
  make RECORDMCOUNT_WARN=1 [targets]   Warn about ignored mcount sections
  make W=n   [targets]   Enable extra gcc checks, n=1,2,3 where
		1: warnings which may be relevant and do not occur too often
		2: warnings which occur quite often but may still be relevant
		3: more obscure warnings, can most likely be ignored
		Multiple levels can be combined with W=12 or W=123

Execute "make" or "make all" to build all targets marked with [*]
For further info see the ./README file
```

## 3.3 内核配置

内核版本号由顶层Makefile中的下列变量决定的：

```
VERSION = 3
PATCHLEVEL = 2
SUBLEVEL = 0
EXTRAVERSION =

# Read KERNELRELEASE from include/config/kernel.release (if it exists)
KERNELRELEASE = $(shell cat include/config/kernel.release 2> /dev/null)
KERNELVERSION = $(VERSION)$(if $(PATCHLEVEL),.$(PATCHLEVEL)$(if $(SUBLEVEL),.$(SUBLEVEL)))$(EXTRAVERSION)
```

可以更改EXTRAVERSION的取值来定义自己的版本号。例如，EXTRAVERSION = -chenwx，则新内核的版本号为3.2.0-chenwx，可通过下列命令查看：

```
chenwx@chenwx ~/linux $ make kernelrelease
scripts/kconfig/conf --silentoldconfig Kconfig
3.2.1-chenwx
```

### 3.3.1 make config

执行make config的流程：

![make_config_1](/assets/make_config_1.jpg)

执行make config命令，会调用顶层Makefile中的config目标：

```
// 定义$(build)变量
include $(srctree)/scripts/Kbuild.include

// 目标config参见config节
config: scripts_basic outputmakefile FORCE
	// 创建目录
	$(Q)mkdir -p include/linux include/config
	/*
	 * $(build)定义于scripts/Kbuild.include
	 * 扩展为 $(MAKE) -f scripts/Makefile.build obj=scripts/kconfig config
	 */
	$(Q)$(MAKE) $(build)=scripts/kconfig $@			

// 编译script/basic/fixdep，参见scripts_basic节
scripts_basic:
	// 扩展为 $(MAKE) -f scripts/Makefile.build obj=scripts/basic
	$(Q)$(MAKE) $(build)=scripts/basic
	$(Q)rm -f .tmp_quiet_recordmcount

// 参见outputmakefile节
outputmakefile:
ifneq ($(KBUILD_SRC),)
	$(Q)ln -fsn $(srctree) source
	// 执行脚本scripts/mkmakefile，用于在$(objtree)指定的目录中生成Makefile
	$(Q)$(CONFIG_SHELL) $(srctree)/scripts/mkmakefile \
	    $(srctree) $(objtree) $(VERSION) $(PATCHLEVEL)
endif

// 因为本规则没有依赖，目标FORCE总会被认为是最新的，所以规则中定义的命令总会被执行
FORCE:
```

#### 3.3.1.1 scripts_basic

在顶层Makefile中，包含下列规则：

```
// 定义$(build)变量
include $(srctree)/scripts/Kbuild.include

// 编译scripts/basic/fixdep
scripts_basic:
	$(Q)$(MAKE) $(build)=scripts/basic
	$(Q)rm -f .tmp_quiet_recordmcount	// 参见scripts/recordmcount.pl
```

$(build)定义于scripts/Kbuild.include:

```
build := -f $(if $(KBUILD_SRC),$(srctree)/)scripts/Makefile.build obj
```

因此，$(Q)$(MAKE) $(build)=scripts/basic被扩展为：

```
$(Q)$(MAKE) -f scripts/Makefile.build obj=scripts/basic
```

该命令用于编译scripts/basic目录。由于未指定编译目标，故编译scripts/Makefile.build中的默认目标__build：

```
PHONY := __build
__build:

...
__build: $(if $(KBUILD_BUILTIN),$(builtin-target) $(lib-target) $(extra-y))	\
		   $(if $(KBUILD_MODULES),$(obj-m) $(modorder-target))		\
		   $(subdir-ym) $(always)
	@:
```

而其中的$(always)则是由scripts/basic/Makefile引入的。

首先，scripts/Makefile.build中的下列语句将scripts/basic/Makefile包含进来：

```
// 扩展为kbuild-dir := script/basic
kbuild-dir := $(if $(filter /%,$(src)),$(src),$(srctree)/$(src))
// 扩展为kbuild-file := script/basic/Makefile
kbuild-file := $(if $(wildcard $(kbuild-dir)/Kbuild),$(kbuild-dir)/Kbuild,$(kbuild-dir)/Makefile)
// 此处将script/basic/Makefile包含进来
include $(kbuild-file)
```

其次，根据scripts/basic/Makefile中的规则：

```
hostprogs-y	:= fixdep
always		:= $(hostprogs-y)

# fixdep is needed to compile other host programs
$(addprefix $(obj)/,$(filter-out fixdep,$(always))): $(obj)/fixdep
```

可知，$(always)的取值为fixdep。

那么，fixdep是如何被编译出来的呢？

1) scripts/Makefile.build中的下列语句将scripts/Makefile.host包含进来：

```
# Do not include host rules unless needed
// 由scripts/basic/Makefile可知，$(hostprogs-y)=fixdep
ifneq ($(hostprogs-y)$(hostprogs-m),)
include scripts/Makefile.host
endif
```

2) 在scripts/Makefile.host中，包含下列编译fixdep的规则：

```
// 扩展为__hostprogs := fixdep
__hostprogs := $(sort $(hostprogs-y) $(hostprogs-m))

# C code
# Executables compiled from a single .c file
// 扩展为host-csingle := fixdep
host-csingle	:= $(foreach m,$(__hostprogs),$(if $($(m)-objs),,$(m)))

// 扩展为host-csingle := scripts/basic/fixdep
host-csingle	:= $(addprefix $(obj)/,$(host-csingle))

# Create executable from a single .c file
# host-csingle -> Executable
quiet_cmd_host-csingle 	= HOSTCC  $@
      cmd_host-csingle	= $(HOSTCC) $(hostc_flags) -o $@ $< \
	  	$(HOST_LOADLIBES) $(HOSTLOADLIBES_$(@F))

// 此处的%为fixdep，故fixdep由fixdep.c编译而来的
$(host-csingle): $(obj)/%: $(src)/%.c FORCE
	// 调用cmd_host-csingle来实际编译fixdep
	$(call if_changed_dep,host-csingle)
```

注：在linux-2.6.18中，script/basic目录中包含两个程序：fixdep, docproc

#### 3.3.1.2 outputmakefile

在顶层Makefile中，包含下列规则：

```
outputmakefile:
// 由命令行中传入该参数，如make -f ../linux/Makefile KBUILD_SRC=../linux/ config
ifneq ($(KBUILD_SRC),)
	$(Q)ln -fsn $(srctree) source				// source链接到KBUILD_SRC所代表的目录
	$(Q)$(CONFIG_SHELL) $(srctree)/scripts/mkmakefile \
	    $(srctree) $(objtree) $(VERSION) $(PATCHLEVEL)	// $(objtree)为执行make命令时的当前目录
endif
```

该规则执行脚本scripts/mkmakefile，在输出目录$(objtree)中生成Makefile文件，以便于在目录$(objtree)中直接执行make命令即可编译内核。

举例，假设目录结构如下：

```
~/
 +- linux/			// 包含linux 3.2版本的内核源代码
 +- linux-build/		// 输出目录
```

在～/linux/目录执行下列命令：

```
chenwx@chenwx ~/linux $ mkdir ../linux-build
chenwx@chenwx ~/linux $ make O=../linux-build/ outputmakefile
  HOSTCC  scripts/basic/fixdep
  GEN     /home/chenwx/linux-build/Makefile
  HOSTCC  scripts/kconfig/conf.o
  SHIPPED scripts/kconfig/zconf.tab.c
  SHIPPED scripts/kconfig/zconf.lex.c
  SHIPPED scripts/kconfig/zconf.hash.c
  HOSTCC  scripts/kconfig/zconf.tab.o
  HOSTLD  scripts/kconfig/conf
scripts/kconfig/conf --silentoldconfig Kconfig
***
*** Configuration file ".config" not found!
***
*** Please run some configurator (e.g. "make oldconfig" or
*** "make menuconfig" or "make xconfig").
***
/home/chenwx/linux/scripts/kconfig/Makefile:33: recipe for target 'silentoldconfig' failed
make[3]: *** [silentoldconfig] Error 1
/home/chenwx/linux/Makefile:492: recipe for target 'silentoldconfig' failed
make[2]: *** [silentoldconfig] Error 2
  GEN     /home/chenwx/linux-build/Makefile
```

则会在~/linux-build/目录中生成Makefile文件，以后直接在~/linux-build/目录执行make命令就可编译内核了。生成的Makefile如下：

```
# Automatically generated by /home/chenwx/linux/scripts/mkmakefile: don't edit

VERSION = 3
PATCHLEVEL = 2

lastword = $(word $(words $(1)),$(1))
makedir := $(dir $(call lastword,$(MAKEFILE_LIST)))

ifeq ("$(origin V)", "command line")
VERBOSE := $(V)
endif
ifneq ($(VERBOSE),1)
Q := @
endif

MAKEARGS := -C /home/chenwx/linux
MAKEARGS += O=$(if $(patsubst /%,,$(makedir)),$(CURDIR)/)$(patsubst %/,%,$(makedir))

MAKEFLAGS += --no-print-directory

.PHONY: all $(MAKECMDGOALS)

all	:= $(filter-out all Makefile,$(MAKECMDGOALS))

all:
	$(Q)$(MAKE) $(MAKEARGS) $(all)

Makefile:;

$(all): all
	@:

%/: all
	@:
```

那么，在～/linux-build/目录执行make config命令时，其执行过程是怎样的呢？

1) 根据～/linux-build/Makefile中的规则，执行make config命令时，实际执行下列规则：

```
all:
	$(Q)$(MAKE) $(MAKEARGS) $(all)
```

该规则被扩展为：

```
make -C /home/chenwx/linux O=/home/chenwx/linux-build/ config
```

2) 根据顶层Makefile中的下列规则，继而执行其中的sub-make规则：

```
ifneq ($(KBUILD_OUTPUT),)
# Invoke a second make in the output directory, passing relevant variables
# check that the output directory actually exists
saved-output := $(KBUILD_OUTPUT)
KBUILD_OUTPUT := $(shell cd $(KBUILD_OUTPUT) && /bin/pwd)
$(if $(KBUILD_OUTPUT),, \
     $(error output directory "$(saved-output)" does not exist))

PHONY += $(MAKECMDGOALS) sub-make

$(filter-out _all sub-make $(CURDIR)/Makefile, $(MAKECMDGOALS)) _all: sub-make
	$(Q)@:

sub-make: FORCE
	$(if $(KBUILD_VERBOSE:1=),@)$(MAKE) -C $(KBUILD_OUTPUT) \
	KBUILD_SRC=$(CURDIR) \
	KBUILD_EXTMOD="$(KBUILD_EXTMOD)" -f $(CURDIR)/Makefile \
	$(filter-out _all sub-make,$(MAKECMDGOALS))

# Leave processing to above invocation of make
skip-makefile := 1
endif # ifneq ($(KBUILD_OUTPUT),)
```

该规则被扩展为：

```
make -C /home/chenwx/linux-build/ \
	KBUILD_SRC=/home/chenwx/linux \
	KBUILD_EXTMOD="" -f /home/chenwx/linux-build/Makefile \
	config
```

此后，make config的编译过程与[3.3.1 make config](#3-3-1-make-config)节完全相同。

#### 3.3.1.3 config

在顶层Makefile中，包含下列有关config的规则：

```
config: scripts_basic outputmakefile FORCE
	$(Q)mkdir -p include/linux include/config
	$(Q)$(MAKE) $(build)=scripts/kconfig $@
```

首先，创建两个目录：include/linux和include/config。

其次，根据scripts/Kbuild.include中对$(build)的定义：

```
build := -f $(if $(KBUILD_SRC),$(srctree)/)scripts/Makefile.build obj
```

$(Q)$(MAKE) $(build)=scripts/kconfig $@ 被扩展为：

```
$(Q)$(MAKE) -f scripts/Makefile.build obj=scripts/kconfig config
```

而scripts/Makefile.build中的下列语句将scripts/kconfig/Makefile包含进来：

```
// 扩展为kbuild-dir := script/kconfig
kbuild-dir := $(if $(filter /%,$(src)),$(src),$(srctree)/$(src))
// 扩展为kbuild-file := script/kconfig/Makefile
kbuild-file := $(if $(wildcard $(kbuild-dir)/Kbuild),$(kbuild-dir)/Kbuild,$(kbuild-dir)/Makefile)
// 此处将script/kconfig/Makefile包含进来
include $(kbuild-file)

因而，make config的最终目标为scripts/kconfig/Makefile中的config：
ifdef KBUILD_KCONFIG			// 此处，变量KBUILD_KCONFIG未定义
	Kconfig := $(KBUILD_KCONFIG)
else
	Kconfig := Kconfig		// 故进入本分支
endif
...
config: $(obj)/conf
	$< --oldaskconfig $(Kconfig)
...
conf-objs	:= conf.o  zconf.tab.o	// conf-objs用于scripts/Makefile.host中的host-cobjs变量
...
hostprogs-y := conf
...
$(obj)/zconf.tab.o: $(obj)/zconf.lex.c $(obj)/zconf.hash.c
```

而config又依赖于$(obj)/conf，因此需要先编译$(obj)/conf。

那么，$(obj)/conf是如何编译链接的呢？

1) 由scripts/Makefile.host中的下列规则：

```
__hostprogs := $(sort $(hostprogs-y) $(hostprogs-m))			// __hostprogs := conf
...
# Object (.o) files compiled from .c files
host-cobjs	:= $(sort $(foreach m,$(__hostprogs),$($(m)-objs)))	// host-cobjs := conf-objs
...
# Create .o file from a single .c file
# host-cobjs -> .o
quiet_cmd_host-cobjs	= HOSTCC  $@
      cmd_host-cobjs	= $(HOSTCC) $(hostc_flags) -c -o $@ $<
$(host-cobjs): $(obj)/%.o: $(src)/%.c FORCE
	$(call if_changed_dep,host-cobjs)				// 调用cmd_host-cobjs编译
```

可知，conf.o由conf.c编译而来，zconf.tab.o由zconf.lex.c和zconf.hash.c编译而来。

2) 然后再根据scripts/Makefile.host中的下列规则：

```
# C executables linked based on several .o files
host-cmulti	:= $(foreach m,$(__hostprogs),\			// host-cmulti := conf
		   $(if $($(m)-cxxobjs),,$(if $($(m)-objs),$(m))))
...
host-cmulti	:= $(addprefix $(obj)/,$(host-cmulti))		// host-cmulti := scripts/Kconfig/conf
...
# Link an executable based on list of .o files, all plain c
# host-cmulti -> executable
quiet_cmd_host-cmulti	= HOSTLD  $@
      cmd_host-cmulti	= $(HOSTCC) $(HOSTLDFLAGS) -o $@	\
			  $(addprefix $(obj)/,$($(@F)-objs)) 	\
			  $(HOST_LOADLIBES) $(HOSTLOADLIBES_$(@F))
$(host-cmulti): $(obj)/%: $(host-cobjs) $(host-cshlib) FORCE
	$(call if_changed,host-cmulti)				// 调用cmd_host-cmulti链接各.o文件生成conf
```

将conf.o和zconf.tab.o链接生成conf可执行文件。

在scripts/kconfig/Makefile中，目标config下的规则 $< --oldaskconfig $(Kconfig) 被扩展为：
scripts/kconfig/conf --oldaskconfig Kconfig

即调用conf程序解析顶层内核配置文件Kconfig(注：顶层配置文件中又引入与体系结构有关的配置文件，参见下文)，并将用户的配置结果输出到.config文件中(通过scripts\kconfig\confdata.c中的函数conf_write())。

```
config SRCARCH
	string
	option env="SRCARCH"
source "arch/$SRCARCH/Kconfig"
```

### 3.3.2 make \*config

执行make \*config的流程:

![make_config.all](/assets/make_config.all.jpg)

参见linux/README中的下列命令：

```
make menuconfig		// Text based color menus, radiolists & dialogs
make nconfig		// Enhanced text based color menus
make xconfig		// X windows (Qt) based configuration tool
make gconfig		// X windows (Gtk) based configuration tool
make oldconfig		// Default all questions based on the contents of
			// your existing ./.config file and asking about new config symbols
make localmodconfig	// Update current config disabling modules not loaded
make localyesconfig	// Update current config converting local mods to core
make silentoldconfig	// Like “make oldconfig”, but avoids cluttering the screen
			// with questions already answered. Additionally updates dependencies
make oldnoconfig	// Same as silentoldconfig but sets new symbols to their default value
make defconfig		// Create file .config by using the default symbol values from either
			// arch/$ARCH/defconfig or arch/$ARCH/configs/${PLATFORM}_defconfig,
			// depending on the architecture
Make savedefconfig	// Save current config as ./defconfig (minimal config)
make ${PLATFORM}_defconfig
			// Create a ./.config file by using the default symbol values from
			// arch/$ARCH/configs/${PLATFORM}_defconfig. Use "make help" to get
			// a list of all available platforms of your architecture
make allyesconfig	// Create a ./.config file by setting symbol values to 'y' as much as possible
make allnoconfig	// Create a ./.config file by setting symbol values to 'n' as much as possible
make allmodconfig	// Create a ./.config file by setting symbol values to 'm' as much as possible
make alldefconfig	// New config with all symbols set to default
make randconfig		// Create a ./.config file by setting symbol values to random values
make listnewconfig	// List new options
```

注：可通过执行命令make help，查看系统支持的Configuration Targets.

执行make \*config命令，会调用顶层Makefile中的目标：

```
// 定义$(build)变量
include $(srctree)/scripts/Kbuild.include

// 下列两行与config相同，参见config节
%config: scripts_basic outputmakefile FORCE
	$(Q)mkdir -p include/linux include/config
	/*
	 * $(build)定义参见scripts/Kbuild.include
	 * $(MAKE) -f scripts/Makefile.build obj=scripts/kconfig *config
	 */
	$(Q)$(MAKE) $(build)=scripts/kconfig $@

// To build script/basic/fixdep，参见scripts_basic节
scripts_basic:
	// $(Q)$(MAKE) -f scripts/Makefile.build obj=scripts/basic
	$(Q)$(MAKE) $(build)=scripts/basic
	$(Q)rm -f .tmp_quiet_recordmcount

// 参见outputmakefile节
outputmakefile:
ifneq ($(KBUILD_SRC),)
	$(Q)ln -fsn $(srctree) source
	// 执行scripts/mkmakefile，该脚本在$(objtree)指定的目录中生成Makefile
	$(Q)$(CONFIG_SHELL) $(srctree)/scripts/mkmakefile \
	    $(srctree) $(objtree) $(VERSION) $(PATCHLEVEL)
endif

// 因为本规则没有依赖，目标FORCE总会被认为是最新的，所以规则中定义的命令总会被执行
FORCE:
```

%config: scripts_basic outputmakefile FORCE的下列语句：

```
$(Q)$(MAKE) $(build)=scripts/kconfig $@			// $(build)定义于scripts/Kbuild.include
```

被扩展后，变为：

```
$(Q)$(MAKE) -f scripts/Makefile.build obj=scripts/kconfig *config
```

而scripts/Makefile.build中的下列语句将scripts/kconfig/Makefile包含进来：

```
// 扩展为kbuild-dir := script/kconfig
kbuild-dir := $(if $(filter /%,$(src)),$(src),$(srctree)/$(src))
// 扩展为kbuild-file := script/kconfig/Makefile
kbuild-file := $(if $(wildcard $(kbuild-dir)/Kbuild),$(kbuild-dir)/Kbuild,$(kbuild-dir)/Makefile)
// 此处将script/kconfig/Makefile包含进来
include $(kbuild-file)
```

因而，make config的最终目标为scripts/kconfig/Makefile中的*config：

```
xconfig: $(obj)/qconf
	$< $(Kconfig)			// 扩展为scripts/kconfig/qconf Kconfig

gconfig: $(obj)/gconf
	$< $(Kconfig)			// 扩展为scripts/kconfig/gconf Kconfig

menuconfig: $(obj)/mconf
	$< $(Kconfig)			// 扩展为scripts/kconfig/mconf Kconfig

nconfig: $(obj)/nconf
	$< $(Kconfig)			// 扩展为scripts/kconfig/nconf Kconfig

oldconfig: $(obj)/conf
	$< --$@ $(Kconfig)		// 扩展为scripts/kconfig/conf --oldconfig Kconfig

silentoldconfig: $(obj)/conf
	$(Q)mkdir -p include/generated
	$< --$@ $(Kconfig)		// 扩展为scripts/kconfig/conf --silentoldconfig Kconfig

allnoconfig allyesconfig allmodconfig alldefconfig randconfig: $(obj)/conf
	$< --$@ $(Kconfig)		// 扩展为scripts/kconfig/conf --$@ Kconfig

defconfig: $(obj)/conf
ifeq ($(KBUILD_DEFCONFIG),)
	$< --defconfig $(Kconfig)	// 扩展为scripts/kconfig/conf –defconfig Kconfig
else
	@echo "*** Default configuration is based on '$(KBUILD_DEFCONFIG)'"
	// 扩展为scripts/kconfig/conf --defconfig=arch/$(SRCARCH)/config/$(KBUILD_DEFCONFIG) Kconfig
	$(Q)$< --defconfig=arch/$(SRCARCH)/configs/$(KBUILD_DEFCONFIG) $(Kconfig)
endif

%_defconfig: $(obj)/conf
	// 扩展为scripts/kconfig/conf --defconfig=arch/$(SRCARCH)/configs/$@ Kconfig
	$(Q)$< --defconfig=arch/$(SRCARCH)/configs/$@ $(Kconfig)
```

\*config的具体编译链接过程与[3.3.1 make config](#3-3-1-make-config)节类似。

[**NOTE1**] It's entirely possible that that existing .config you used as the basis for your configuration isn't quite up to date; that is, it may have no entries representing extremely new features that have been added to the kernel. If that's the case, the "make oldconfig" will stop at each one of those choices and ask you what to do. And if you're new to building a kernel, you may not know the right answer. One solution is to just keep hitting ENTER and take the default, but that can get tedious. A faster solution is:

```
// two single quotes, no space between
chenwx ~/linux # yes '' | make oldconfig
```

[**NOTE2**] Perhaps the most useful target for beginners is defconfig (short for "default config") which simply sets your .config to an established set of defaults for your system and architecture. And how can you see these defaults? Simple -- from the top of the kernel source tree, just run following command, and you'll see dozens of default config files for all of the kernel's supported architectures.

```
chenwx ~/linux # find arch -name "*defconfig"
```

### 3.3.3 Kconfig/内核配置选项文件

内核配置文件包括：

* Kconfig
* arch/$(SRCARCH)/Kconfig
* ...

其说明参见：

* [Documentation/kbuild/kconfig.txt](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/Documentation/kbuild/kconfig.txt)
* [Documentation/kbuild/kconfig-language.txt](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/Documentation/kbuild/kconfig-language.txt)

Linux Kernel中的所有Kconfig文件形成了一棵树，参见[Appendix C: Kconfig tree](#appendix-c-kconfig-tree)。

### 3.3.4 .config/内核配置结果文件

#### 3.3.4.1 .config的格式

在[3.3.1 make config](#3-3-1-make-config)节和[3.3.2 make \*config](#3-3-2-make-config)节中生成的conf等配置程序读取内核配置选项文件Kconfig中的内核配置信息，并根据用户的选择，生成内核配置结果文件.config，以供后续编译内核时使用(顶层Makefile会读取该文件，参见[3.3.4.2 .config如何被顶层Makefile调用](#3-3-4-2-config-makefile-)节)。内核配置结果文件.config中包含下列内容：

```
# CONFIG_64BIT is not set
CONFIG_X86_32=y
# CONFIG_X86_64 is not set
CONFIG_X86=y
CONFIG_INSTRUCTION_DECODER=y
CONFIG_OUTPUT_FORMAT="elf32-i386"
CONFIG_ARCH_DEFCONFIG="arch/x86/configs/i386_defconfig"
CONFIG_LOCKDEP_SUPPORT=y
CONFIG_STACKTRACE_SUPPORT=y
...
```

#### 3.3.4.2 .config如何被顶层Makefile调用

由[3.4.2 编译bzImage/$(obj-y)](#3-4-2-bzimage-obj-y-)节和[3.4.3 编译modules/$(obj-m)](#3-4-3-modules-obj-m-)节可知，在编译内核和模块时，其目标都要依赖于$(vmlinux-dirs)。而由[3.4.2.1.3 $(vmlinux-dirs)](#3-4-2-1-3-vmlinux-dirs-)节和[3.4.2.1.1 prepare](#3-4-2-1-1-prepare)节可知，存在下列依赖关系：

```
$(vmlinux-dirs) <= prepare <= prepare0 <= archprepare <= prepare1 <= include/config/auto.conf
```

而由[3.4.2.1.1.1 include/config/auto.conf](#3-4-2-1-1-1-include-config-auto-conf)节可知，include/config/auto.conf是由.config生成的。需要理解下列两点：

1) 执行make \*config命令，将会生成配置文件.config，参见[3.3.1 make config](#3-3-1-make-config)节和[3.3.2 make \*config](#3-3-2-make-config)节；

2) 执行make silentoldconfig命令(执行本命令的前提条件是，系统中必须已存在配置文件.config)，将会同时生成下列三个文件:

**include/config/auto.conf**

与.config文件中的配置完全相同 (注意：这两个文件中行的顺序可能不同，可通过执行下列命令来比较这两个文件的差异):

```
chenwx@chenwx ~/linux $ cd ../linux-build
chenwx@chenwx ~/linux-build $ sort .config > config.sort
chenwx@chenwx ~/linux-build $ sort .config > .config.sort
chenwx@chenwx ~/linux-build $ sed '/#/d' .config.sort > .config.sort.sed
chenwx@chenwx ~/linux-build $ sort include/config/auto.conf > auto.conf.sort
chenwx@chenwx ~/linux-build $ sed '/#/d' auto.conf.sort > auto.conf.sort.sed
chenwx@chenwx ~/linux-build $ diff -B .config.sort.sed auto.conf.sort.sed
chenwx@chenwx ~/linux-build $
```

**include/config/auto.conf.cmd**

本文件包含了生成配置文件include/config/auto.conf时所用到的Kconfig文件。

**include/config/tristate.conf**

本文件包含了include/config/auto.conf中的部分配置信息，且全为大写字母。

由于include/config/auto.conf与.config完全相同，因此顶层Makefile通过下列语句将.config包含进编译系统中(注意：当include/config/auto.conf生成后，make会重新读取Makefile文件，因而可以通过该语句读取最新的include/config/auto.conf)：

```
# Read in config
-include include/config/auto.conf;
```

当满足下列条件之一时：

* 不存在include/config/auto.conf时，
* .config比include/config/auto.conf要新时，
* include/config/auto.conf.cmd中包含的任意Kconfig文件比include/config/auto.conf要新时，

将调用顶层Makefile中的下列规则，创建或更新include/config/auto.conf和include/config/auto.conf.cmd文件，此时更新后的include/config/auto.conf与系统中已存在的.config文件完全相同，因而可以自动包含最新的配置：

```
# If .config is newer than include/config/auto.conf, someone tinkered
# with it and forgot to run make oldconfig.
# if auto.conf.cmd is missing then we are probably in a cleaned tree so
# we execute the config step to be sure to catch updated Kconfig files
include/config/%.conf: $(KCONFIG_CONFIG) include/config/auto.conf.cmd
	// 扩展为make scripts/kconfig/conf --silentoldconfig
	$(Q)$(MAKE) -f $(srctree)/Makefile silentoldconfig
```

## 3.4 内核编译

Linux内核的Makefile分为下列5个部分：

```
Makefile			- 顶层Makefile
.config				- 内核配置结果文件，参见.config/内核配置结果文件节
arch/$(ARCH)/Makefile		- 具体架构的Makefile
scripts/Makefile.*		- 通用的规则等，面向所有的Kbuild Makefiles
Kbuild Makefiles		- 内核源代码中大约有500个这样的文件(文件名为Kbuild)
```

**Makefile**

The top Makefile reads the .config file, which comes from the kernel configuration process. The top Makefile is responsible for building two major products: vmlinux (the resident kernel image) and modules (any module files). It builds these goals by recursively descending into the subdirectories of the kernel source tree.

**\*/Kbuild**

每一个子目录都有一个Kbuild Makefile文件，用来执行从其上层目录传递下来的命令。Kbuild Makefile从.config文件中提取信息，生成Kbuild完成内核编译所需的文件列表。

**scripts/Makefile.***

包含了所有的定义、规则等信息。这些文件被用来编译基于Kbuild Makefile的内核。这些文件包括：

```
Makefile.asm-generic
Makefile.build
Makefile.clean
Makefile.fwinst
Makefile.headersinst
Makefile.help
Makefile.host
Makefile.lib
Makefile.modbuiltin
Makefile.modinst
Makefile.modpost
```

各Makefile之间存在调用关系，这形成了一棵树，参见[Appendix B: Makefile Tree](#appendix-b-makefile-tree)节。

### 3.4.1 Makefile的Default Target

【**GNU Make知识点**】

根据《GNU make v3.8.2》第4.10节可知：

> One file can be the target of several rules. All the prerequisites mentioned in all the rules are merged into one list of prerequisites for the target. If the target is older than any prerequisite from any rule, the recipe is executed.
>
> There can only be one recipe to be executed for a file. If more than one rule gives a recipe for the same file, make uses the last one given and prints an error message. (As a special case, if the file’s name begins with a dot, no error message is printed. This odd behavior is only for compatibility with other implementations of make - you should avoid using it). Occasionally it is useful to have the same target invoke multiple recipes which are defined in different parts of your makefile; you can use double-colon rules for this.

顶层Makefile及其包含的Makefile，包含如下规则：

1) 来自顶层Makefile

```
# That's our default target when none is given on the command line
PHONY := _all
_all:

# If building an external module we do not care about the all: rule
# but instead _all depend on modules
PHONY += all
/*
 * 若仅执行make命令，则$(KBUILD_EXTMOD)为空
 * 若执行make M=dir或make ... SUBDIRS=$PWD命令，则$(KBUILD_EXTMOD)不为空
 */
ifeq ($(KBUILD_EXTMOD),)
_all: all			// 若仅执行make命令，则进入本分支
else
_all: modules			// 若执行make M=dir或make ... SUBDIRS=$PWD命令，则进入本分支
endif

// 以x86体系为例，此处为include arch/x86/Makefile，其中包含all: bzImage，如下
include $(srctree)/arch/$(SRCARCH)/Makefile
```

2) 来自arch/x86/Makfile

```
# Default kernel to build
all: bzImage
```

3) 来自顶层Makefile

```
// 该目标被arch/x86/Makefile中的目标all: bzImage覆盖
all: vmlinux

ifdef CONFIG_MODULES

# By default, build modules as well
all: modules

else # CONFIG_MODULES
...
endif # CONFIG_MODULES
```

由上述规则可知：

* 执行make命令时，调用顶层Makefile中的目标_all，目标_all依赖于目标all，而目标all又依次依赖于目标bzImage、vmlinux和modules。即执行make命令时，将依次执行目标bzImage、vmlinux和modules，因而生成下列文件：

```
vmlinux
System.map
arch/x86/boot/bzImage
arch/i386/boot/bzImage		(link to ./arch/x86/boot/bzImage)
oneDir/twoDir/*.ko		(modules)
./arch/x86/lib/lib.a		(library)
./lib/lib.a			(library)
```

* 执行make vmlinux命令，编译顶层Makefile中的目标vmlinux，参见[3.4.2 编译bzImage/$(obj-y)](#3-4-2-bzimage-obj-y-)节。生成下列文件：

```
vmlinux
```

* 执行make modules命令，编译顶层Makefile中的目标modules，参见[3.4.3 编译modules/$(obj-m)](#3-4-3-modules-obj-m-)节。生成下列文件：

```
oneDir/twoDir/*.ko (modules)
```

* 执行下列命令之一来编译外部模块，参见[3.4.4 编译external modules](#3-4-4-external-modules)节：

```
# make -C <kernel_src_dir> M=<ext_module_dir> modules
# make -C <kernel_src_dir> SUBDIRS=$PWD modules
```

注：KBUILD_EXTMOD称为Kbuild扩展模式，若执行命令make M=dir，则变量KBUILD_EXTMOD被置为dir，继而执行下列规则：

```
# That's our default target when none is given on the command line
PHONY := _all
_all:

# If building an external module we do not care about the all: rule
# but instead _all depend on modules
PHONY += all
ifeq ($(KBUILD_EXTMOD),)	// 此时，$(KBUILD_EXTMOD)=dir
_all: all
else
_all: modules			// 进入此分支
endif
```

执行依赖于modules的代码，就相当于执行make modules。这个make方法也是用来提高驱动开发效率，如果只想编译某个具体的驱动，只要指定对应的子目录，就可以只编译这个驱动而不去编译其他的内核代码了。

执行make命令时，各目标的依赖关系:

![Targets_Tree](/assets/Targets_Tree.jpg)

其中的数字表示对应目标的生成顺序。

### 3.4.2 编译bzImage/$(obj-y)

在arch/x86/Makefile中，包含下列规则：

```
boot := arch/x86/boot

# Default kernel to build
all: bzImage

# KBUILD_IMAGE specify target image being built
KBUILD_IMAGE := $(boot)/bzImage

bzImage: vmlinux
ifeq ($(CONFIG_X86_DECODER_SELFTEST),y)
	$(Q)$(MAKE) $(build)=arch/x86/tools posttest
endif
	$(Q)$(MAKE) $(build)=$(boot) $(KBUILD_IMAGE)
	$(Q)mkdir -p $(objtree)/arch/$(UTS_MACHINE)/boot
	$(Q)ln -fsn ../../x86/boot/bzImage $(objtree)/arch/$(UTS_MACHINE)/boot/$@
```

在顶层Makefile中，包含下列规则：

```
vmlinux: $(vmlinux-lds) $(vmlinux-init) $(vmlinux-main) vmlinux.o $(kallsyms.o) FORCE
ifdef CONFIG_HEADERS_CHECK
	$(Q)$(MAKE) -f $(srctree)/Makefile headers_check
endif
ifdef CONFIG_SAMPLES
	$(Q)$(MAKE) $(build)=samples
endif
ifdef CONFIG_BUILD_DOCSRC
	$(Q)$(MAKE) $(build)=Documentation
endif
	$(call vmlinux-modpost)
	$(call if_changed_rule,vmlinux__)
	$(Q)rm -f .old_version

...
vmlinux-lds	:= arch/$(SRCARCH)/kernel/vmlinux.lds
vmlinux-init	:= $(head-y) $(init-y)
vmlinux-main	:= $(core-y) $(libs-y) $(drivers-y) $(net-y)
vmlinux-all	:= $(vmlinux-init) $(vmlinux-main)
...
vmlinux.o	: $(modpost-init) $(vmlinux-main) FORCE
modpost-init	:= $(filter-out init/built-in.o, $(vmlinux-init))
...
kallsyms.o	:= .tmp_kallsyms$(last_kallsyms).o	// last_kallsyms := 2 or 3

...
# The actual objects are generated when descending,
# make sure no implicit rule kicks in
$(sort $(vmlinux-init) $(vmlinux-main)) $(vmlinux-lds): $(vmlinux-dirs) ;

...
PHONY += $(vmlinux-dirs)
$(vmlinux-dirs): prepare scripts
	$(Q)$(MAKE) $(build)=$@
```

#### 3.4.2.1 $(vmlinux-dirs)

在顶层Makefile中，包含下列规则：

```
vmlinux-dirs	:=	$(patsubst %/,%,$(filter %/, $(init-y) $(init-m)	\
			$(core-y) $(core-m) $(drivers-y) $(drivers-m)		\
			$(net-y) $(net-m) $(libs-y) $(libs-m)))

# The actual objects are generated when descending,
# make sure no implicit rule kicks in
$(sort $(vmlinux-init) $(vmlinux-main)) $(vmlinux-lds): $(vmlinux-dirs) ;

PHONY += $(vmlinux-dirs)
$(vmlinux-dirs): prepare scripts
	$(Q)$(MAKE) $(build)=$@
```

由此可知，编译vmlinux时，vmlinux所依赖的目标$(vmlinux-init)、$(vmlinux-main)和$(vmlinux-lds)均依赖于$(vmlinux-dirs)。而$(vmlinux-dirs)又依赖于目标prepare和scripts。

##### 3.4.2.1.1 prepare

在顶层Makefile中，包含下列规则：

```
scripts_basic:
	$(Q)$(MAKE) $(build)=scripts/basic
	$(Q)rm -f .tmp_quiet_recordmcount

# prepare3 is used to check if we are building in a separate output directory,
# and if so do:
# 1) Check that make has not been executed in the kernel src $(srctree)
prepare3: include/config/kernel.release
ifneq ($(KBUILD_SRC),)	// 当不在linux源代码目录编译(参见outputmakefile节)时，执行下列命令
	@$(kecho) '  Using $(srctree) as source for kernel'
	$(Q)if [ -f $(srctree)/.config -o -d $(srctree)/include/config ]; then \
		echo "  $(srctree) is not clean, please run 'make mrproper'"; \
		echo "  in the '$(srctree)' directory."; \
		/bin/false; \
	fi;
endif

# prepare2 creates a makefile if using a separate output directory
prepare2: prepare3 outputmakefile asm-generic

prepare1: prepare2 include/linux/version.h include/generated/utsrelease.h \
                       include/config/auto.conf
	$(cmd_crmodverdir)

archprepare: prepare1 scripts_basic

prepare0: archprepare FORCE
	// 扩展为make -f scripts/Makefile.build obj=.
	$(Q)$(MAKE) $(build)=.

# All the preparing..
prepare: prepare0
```

###### 3.4.2.1.1.1 include/config/auto.conf

在Documentation/kbuild/kconfig.txt中，包含下列描述：

```
KCONFIG_AUTOCONFIG
--------------------------------------------------
This environment variable can be set to specify the path & name of the
"auto.conf" file.  Its default value is "include/config/auto.conf".
```

在顶层Makefile中，包含下列规则：

```
KCONFIG_CONFIG	?= .config

...
no-dot-config-targets := clean mrproper distclean \
                         cscope gtags TAGS tags help %docs check% coccicheck \
                         include/linux/version.h headers_% \
                         kernelversion %src-pkg

config-targets	:= 0
mixed-targets	:= 0
dot-config	:= 1

// 执行make命令，$(MAKECMDGOALS)为空，不进入本分支
ifneq ($(filter $(no-dot-config-targets), $(MAKECMDGOALS)),)
	ifeq ($(filter-out $(no-dot-config-targets), $(MAKECMDGOALS)),)
		dot-config := 0
	endif
endif

ifeq ($(KBUILD_EXTMOD),)
    ifneq ($(filter config %config,$(MAKECMDGOALS)),)
        config-targets := 1
        ifneq ($(filter-out config %config,$(MAKECMDGOALS)),)
            mixed-targets := 1
        endif
    endif
endif

...
// 由上述规则可知，dot-config取值为1
ifeq ($(dot-config),1)
# Read in config
/*
 * 2) 由下文1)处的规则生成include/config/auto.conf后，
 * make会重新读取Makefile文件，在此处读取该文件的内容
 */
-include include/config/auto.conf

// 执行make命令时，$(KBUILD_EXTMOD)为空，进入本分支
ifeq ($(KBUILD_EXTMOD),)

# Read in dependencies to all Kconfig* files, make sure to run
# oldconfig if changes are detected.
-include include/config/auto.conf.cmd

# To avoid any implicit rule to kick in, define an empty command
$(KCONFIG_CONFIG) include/config/auto.conf.cmd: ;

# If .config is newer than include/config/auto.conf, someone tinkered
# with it and forgot to run make oldconfig.
# if auto.conf.cmd is missing then we are probably in a cleaned tree so
# we execute the config step to be sure to catch updated Kconfig files
/*
 * 1) 执行下列规则，由.config生成include/config/*.conf文件，
 * 此后在上文2)处读取该文件的内容
 */
include/config/%.conf: $(KCONFIG_CONFIG) include/config/auto.conf.cmd
	$(Q)$(MAKE) -f $(srctree)/Makefile silentoldconfig

else # KBUILD_EXTMOD

# external modules needs include/generated/autoconf.h and include/config/auto.conf
# but do not care if they are up-to-date. Use auto.conf to trigger the test
PHONY += include/config/auto.conf

include/config/auto.conf:
	$(Q)test -e include/generated/autoconf.h -a -e $@ || (		\
	echo;								\
	echo "  ERROR: Kernel configuration is invalid.";		\
	echo "         include/generated/autoconf.h or $@ are missing.";\
	echo "         Run 'make oldconfig && make prepare' on kernel src to fix it.";	\
	echo;								\
	/bin/false)

endif # KBUILD_EXTMOD

else
# Dummy target needed, because used as prerequisite
include/config/auto.conf: ;
endif # $(dot-config)
```

由上述规则可知，若.config比include/config/auto.conf要新，则执行下列命令更新include/config/auto.conf文件，将最新的配置包含进来，参见[3.3.4.1 .config的格式](#3-3-4-1-config-)节和[3.3.4.2 .config如何被顶层Makefile调用](#3-3-4-2-config-makefile-)节：

```
$(Q)$(MAKE) -f $(srctree)/Makefile silentoldconfig
```

###### 3.4.2.1.1.2 include/config/kernel.release

在顶层Makefile中，包含下列规则：

```
# Store (new) KERNELRELASE string in include/config/kernel.release
include/config/kernel.release: include/config/auto.conf FORCE
	$(Q)rm -f $@
	$(Q)echo "$(KERNELVERSION)$$($(CONFIG_SHELL) $(srctree)/scripts/setlocalversion $(srctree))" > $@
```

生成的include/config/kernel.release包含下列内容：

```
3.2.0-chenwx
```

###### 3.4.2.1.1.3 outputmakefile

参见[3.3.1.2 outputmakefile](#3-3-1-2-outputmakefile)节。

###### 3.4.2.1.1.4 asm-generic

在顶层Makefile中，包含下列规则：

```
# Support for using generic headers in asm-generic
PHONY += asm-generic
asm-generic:
	$(Q)$(MAKE) -f $(srctree)/scripts/Makefile.asm-generic \
	            obj=arch/$(SRCARCH)/include/generated/asm
```

该命令被扩展为(以x86体系为例)：

```
make -f scripts/Makefile.asm-generic obj=arch/x86/include/generated/asm
```

在scripts/Makefile.asm-generic中，包含下列规则：

```
// 扩展为arch/x86/include/asm/Kbuild
kbuild-file := $(srctree)/arch/$(SRCARCH)/include/asm/Kbuild
-include $(kbuild-file)

include scripts/Kbuild.include

// 创建目录arch/x86/include/generated/asm
# Create output directory if not already present
_dummy := $(shell [ -d $(obj) ] || mkdir -p $(obj))

quiet_cmd_wrap = WRAP    $@
cmd_wrap = echo "\#include <asm-generic/$*.h>" >$@

/*
 * 在x86体系中，$(generic-y)为空，故本目标不产生任何文件
 * 在其他体系中，该目标用于生成指定的头文件，例如:
 * arch/blackfin/include/asm/Kbuild
 */
all: $(patsubst %, $(obj)/%, $(generic-y))
	@:

$(obj)/%.h:
	$(call cmd,wrap)	// 调用cmd_wrap命令
```

###### 3.4.2.1.1.5 include/linux/version.h

在顶层Makefile中，包含下列规则：

```
define filechk_version.h
	(echo \#define LINUX_VERSION_CODE $(shell				\
	expr $(VERSION) \* 65536 + 0$(PATCHLEVEL) \* 256 + 0$(SUBLEVEL));    	\
	echo '#define KERNEL_VERSION(a,b,c) (((a) << 16) + ((b) << 8) + (c))';)
endef

...
include/linux/version.h: $(srctree)/Makefile FORCE
	/*
	 * 参见scripts/Kbuild.include，调用filechk_version.h
	 * 生成include/linux/version.h
	 */
	$(call filechk,version.h)
```

生成的include/linux/version.h包含下列内容：

```
#define LINUX_VERSION_CODE 197120
#define KERNEL_VERSION(a,b,c) (((a) << 16) + ((b) << 8) + (c))
```

###### 3.4.2.1.1.6 include/generated/utsrelease.h

在顶层Makefile中，包含下列规则：

```
uts_len := 64
define filechk_utsrelease.h
	if [ `echo -n "$(KERNELRELEASE)" | wc -c ` -gt $(uts_len) ]; then \
	  echo '"$(KERNELRELEASE)" exceeds $(uts_len) characters' >&2;    \
	  exit 1;                                                         \
	fi;                                                               \
	(echo \#define UTS_RELEASE \"$(KERNELRELEASE)\";)
endef

// 依赖于kernel.release，参见include/config/kernel.release节
include/generated/utsrelease.h: include/config/kernel.release FORCE
	/*
	 * 参见scripts/Kbuild.include，调用filechk_utsrelease.h
	 * 生成include/linux/utsrelease.h
	 */
	$(call filechk,utsrelease.h)
```

生成的include/generated/utsrelease.h包含下列内容：

```
#define UTS_RELEASE "3.2.0-chenwx"
```

###### 3.4.2.1.1.7 prepare1

在顶层Makefile中，包含下列规则：

```
# When compiling out-of-tree modules, put MODVERDIR in the module
# tree rather than in the kernel tree. The kernel tree might
# even be read-only.
// 扩展为.tmp_versions
export MODVERDIR := $(if $(KBUILD_EXTMOD),$(firstword $(KBUILD_EXTMOD))/).tmp_versions

ifeq ($(KBUILD_EXTMOD),)

prepare1: prepare2 include/linux/version.h include/generated/utsrelease.h \
                   include/config/auto.conf
	// 执行命令cmd_crmodverdir，生成空目录.tmp_versions/
	$(cmd_crmodverdir)

...
else # KBUILD_EXTMOD
...
endif # KBUILD_EXTMOD

...
# Create temporary dir for module support files
# clean it up only when building all modules
// 扩展mkdir -p .tmp_versions ; rm -f .tmp_versions/*
cmd_crmodverdir = $(Q)mkdir -p $(MODVERDIR) \
                  $(if $(KBUILD_MODULES),; rm -f $(MODVERDIR)/*)
```

###### 3.4.2.1.1.8 scripts_basic

参见[3.3.1.1 scripts_basic](#3-3-1-1-scripts-basic)节。

###### 3.4.2.1.1.9 prepare0

在顶层Makefile中，包含下列规则：

```
prepare0: archprepare FORCE
	$(Q)$(MAKE) $(build)=.
```

其中，命令 $(Q)$(MAKE) $(build)=. 被扩展为：

```
make -f scripts/Makefile.build obj=.
```

该命令将编译scripts/Makefile.build中的默认规则__build：

```
PHONY := __build
__build:

# The filename Kbuild has precedence over Makefile
kbuild-dir := $(if $(filter /%,$(src)),$(src),$(srctree)/$(src))
kbuild-file := $(if $(wildcard $(kbuild-dir)/Kbuild),$(kbuild-dir)/Kbuild,$(kbuild-dir)/Makefile)
include $(kbuild-file)				// 将linux/Kbuild包含进来

__build: $(if $(KBUILD_BUILTIN),$(builtin-target) $(lib-target) $(extra-y))	\
	 $(if $(KBUILD_MODULES),$(obj-m) $(modorder-target))			\
	 $(subdir-ym) $(always)			// 变量$(always)由linux/Kbuild引入
	@:
```

由$(always)可知，该目标会生成下列文件：

```
include/generated/bounds.h
include/generated/asm-offsets.h
```

##### 3.4.2.1.2 scripts

在顶层Makefile中，包含下列规则：

```
ifeq ($(KBUILD_EXTMOD),)
# Additional helpers built in scripts/
# Carefully list dependencies so we do not try to build scripts twice
# in parallel
PHONY += scripts
scripts: scripts_basic include/config/auto.conf include/config/tristate.conf
	$(Q)$(MAKE) $(build)=$(@)

...
endif # KBUILD_EXTMOD
```

###### 3.4.2.1.2.1 scripts_basic

参见[3.3.1.1 scripts_basic](#3-3-1-1-scripts-basic)节。

###### 3.4.2.1.2.2 include/config/auto.conf

参见[3.4.2.1.1.1 include/config/auto.conf](#3-4-2-1-1-1-include-config-auto-conf)节。

###### 3.4.2.1.2.3 include/config/tristate.conf

参见Documentation/kbuild/kconfig.txt:

```
KCONFIG_TRISTATE
--------------------------------------------------
This environment variable can be set to specify the path & name of the
"tristate.conf" file.  Its default value is "include/config/tristate.conf".
```

在顶层Makefile中，包含下列规则：

```
include/config/%.conf: $(KCONFIG_CONFIG) include/config/auto.conf.cmd
	$(Q)$(MAKE) -f $(srctree)/Makefile silentoldconfig
```

由此可知，include/config/tristate.conf是由下列命令生成的：

```
$(Q)$(MAKE) -f $(srctree)/Makefile silentoldconfig
```

该命令与生成include/config/auto.conf所用命令是相同的，这两个文件也是同时生成的，参见[3.3.4.2 .config如何被顶层Makefile调用](#3-3-4-2-config-makefile-)节。

在scripts/Makefile.modbuiltin中，通过下列规则将include/config/tristate.conf引入，参见[3.4.3.3.1 make -f scripts/Makefile.modbuiltin obj=$*](#3-4-3-3-1-make-f-scripts-makefile-modbuiltin-obj-)节：

```
# tristate.conf sets tristate variables to uppercase 'Y' or 'M'
# That way, we get the list of built-in modules in obj-Y
-include include/config/tristate.conf
```

###### 3.4.2.1.2.4 scripts

在顶层Makefile中，包含下列规则：

```
ifeq ($(KBUILD_EXTMOD),)
# Additional helpers built in scripts/
# Carefully list dependencies so we do not try to build scripts twice
# in parallel
PHONY += scripts
scripts: scripts_basic include/config/auto.conf include/config/tristate.conf
	$(Q)$(MAKE) $(build)=$(@)

endif # KBUILD_EXTMOD
```

当[3.3.1.1 scripts_basic](#3-3-1-1-scripts-basic)节至[3.4.2.1.2.3 include/config/tristate.conf](#3-4-2-1-2-3-include-config-tristate-conf)节中的目标完成后，执行下列命令编译scripts目录及其子目录：

```
make -f scripts/Makefile.build obj=scripts
```

该编译过程与scripts_basic节类似，只不过变量$(always)是由scripts/Makefile引入的，如下：

```
hostprogs-$(CONFIG_KALLSYMS)		+= kallsyms
hostprogs-$(CONFIG_LOGO)		+= pnmtologo
hostprogs-$(CONFIG_VT)			+= conmakehash
hostprogs-$(CONFIG_IKCONFIG)		+= bin2c
hostprogs-$(BUILD_C_RECORDMCOUNT)	+= recordmcount

always	:= $(hostprogs-y) $(hostprogs-m)

subdir-y				+= mod
```

该目标生成下列可执行文件：

```
scripts/kallsyms
scripts/pnmtologo
scripts/conmakehash
scripts/bin2c
scripts/recordmcount
scripts/mod/modpost
scripts/mod/mk_elfconfig
```

##### 3.4.2.1.3 $(vmlinux-dirs)

在顶层Makefile中，包含下列规则：

```
init-y		:= init/
drivers-y	:= drivers/ sound/ firmware/
net-y		:= net/
libs-y		:= lib/
core-y		:= usr/

// 此处以x86体系为例，将arch/x86/Makefile引入
include $(srctree)/arch/$(SRCARCH)/Makefile

core-y		+= kernel/ mm/ fs/ ipc/ security/ crypto/ block/

vmlinux-dirs	:= $(patsubst %/,%,$(filter %/, $(init-y) $(init-m)	\
		   $(core-y) $(core-m) $(drivers-y) $(drivers-m)	\
		   $(net-y) $(net-m) $(libs-y) $(libs-m)))

PHONY += $(vmlinux-dirs)
$(vmlinux-dirs): prepare scripts
	$(Q)$(MAKE) $(build)=$@
```

在arch/x86/Makefile中，包含下列规则：

```
libs-y += arch/x86/lib/

# See arch/x86/Kbuild for content of core part of the kernel
core-y += arch/x86/

# drivers-y are linked after core-y
drivers-$(CONFIG_MATH_EMULATION)	+= arch/x86/math-emu/
drivers-$(CONFIG_PCI)			+= arch/x86/pci/

# must be linked after kernel/
drivers-$(CONFIG_OPROFILE)		+= arch/x86/oprofile/

# suspend and hibernation support
drivers-$(CONFIG_PM)			+= arch/x86/power/

drivers-$(CONFIG_FB)			+= arch/x86/video/
```

由此可知，vmlinux-dirs被扩展为：

```
vmlinux-dirs :=	\
	init \							// $(init-y)
	usr arch/x86 kernel mm fs ipc security crypto block \	// $(core-y)
	drivers sound firmware \				// $(drivers-y)
	arch/x86/math-emu \					// 与CONFIG_MATH_EMULATION有关
	arch/x86/pci \						// 与CONFIG_PCI有关
	arch/x86/oprofile \					// 与CONFIG_OPROFILE有关
	arch/x86/power \					// 与CONFIG_PM有关
	arch/x86/video \					// 与CONFIG_FB有关
	net \							// $(net-y)
	lib arch/x86/lib					// $(libs-y)
```

当$(vmlinux-dirs)所依赖的目标prepare和scripts完成后，将会执行下列命令编译$(vmlinux-dirs):

```
$(Q)$(MAKE) $(build)=$@
```

根据scripts/Makefile.build中对build的定义，该命令依次被扩展为：

```
make -f scripts/Makefile.build obj=init
make -f scripts/Makefile.build obj=usr
make -f scripts/Makefile.build obj=arch/x86		// 此处以x86体系为例，由arch/x86/Makefile中的core-y引入
make -f scripts/Makefile.build obj=kernel
make -f scripts/Makefile.build obj=mm
make -f scripts/Makefile.build obj=fs
make -f scripts/Makefile.build obj=ipc
make -f scripts/Makefile.build obj=security
make -f scripts/Makefile.build obj=crypto
make -f scripts/Makefile.build obj=block
make -f scripts/Makefile.build obj=drivers
make -f scripts/Makefile.build obj=sound
make -f scripts/Makefile.build obj=firmware
make -f scripts/Makefile.build obj=arch/x86/math-emu	// 与CONFIG_MATH_EMULATION有关
make -f scripts/Makefile.build obj=arch/x86/pci		// 与CONFIG_PCI有关
make -f scripts/Makefile.build obj=arch/x86/oprofile	// 与CONFIG_OPROFILE有关
make -f scripts/Makefile.build obj=arch/x86/power	// 与CONFIG_PM有关
make -f scripts/Makefile.build obj=arch/x86/video	// 与CONFIG_FB有关
make -f scripts/Makefile.build obj=net
make -f scripts/Makefile.build obj=lib
make -f scripts/Makefile.build obj=arch/x86/lib/	// 以x86体系为例，由arch/x86/Makefile中的libs-y引入
```

当执行这些命令时，如果这些目录下存在子目录，则make会递归调用其子目录下的Kbuild或Makefile(若不存在Kbuild文件)，详细的命令调用列表参见[Appendix A: make -f scripts/Makefile.build obj=列表](#appendix-a-make-f-scripts-makefile-build-obj-)节。

###### 3.4.2.1.3.1 make -f scripts/Makefile.build obj=XXX命令的执行过程

因为命令make -f scripts/Makefile.build obj=XXX中没有指定目标，故编译scripts/Makefile.build中的默认目标__build：

```
src := $(obj)
PHONY := __build
__build:

// kbuild-dir被扩展为obj指定的目录
kbuild-dir := $(if $(filter /%,$(src)),$(src),$(srctree)/$(src))
// kbuild-file被扩展为obj指定目录下的Kbuild或Makefile，其中Kbuild的优先级高于Makefile
kbuild-file := $(if $(wildcard $(kbuild-dir)/Kbuild),$(kbuild-dir)/Kbuild,$(kbuild-dir)/Makefile)
// 此处将obj指定目录下的Kbuild或Makefile包含进来
include $(kbuild-file)

__build: $(if $(KBUILD_BUILTIN),$(builtin-target) $(lib-target) $(extra-y))	\
	 $(if $(KBUILD_MODULES),$(obj-m) $(modorder-target))			\
	 $(subdir-ym) $(always)
	@:
```

根据顶层Makefile中对$(KBUILD_BUILTIN)和$(KBUILD_MODULES)的定义可知，上述规则被扩展为：

```
__build: $(builtin-target) $(lib-target) $(extra-y) $(obj-m) $(modorder-target) $(subdir-ym) $(always)
	@:
```

\_\_build所依赖的各目标参见下列几节：

###### 3.4.2.1.3.1.1 $(builtin-target)

在scripts/Makefile.build中，包含下列规则：

```
ifneq ($(strip $(obj-y) $(obj-m) $(obj-n) $(obj-) $(subdir-m) $(lib-target)),)
builtin-target := $(obj)/built-in.o
endif

#
# Rule to compile a set of .o files into one .o file
#
ifdef builtin-target
quiet_cmd_link_o_target = LD      $@
# If the list of objects to link is empty, just create an empty built-in.o
cmd_link_o_target = $(if $(strip $(obj-y)),				\
		    $(LD) $(ld_flags) -r -o $@ $(filter $(obj-y), $^)	\
		    $(cmd_secanalysis),					\
		    rm -f $@; $(AR) rcs$(KBUILD_ARFLAGS) $@)

$(builtin-target): $(obj-y) FORCE		// $(obj-y)参见$(obj-y)节
	$(call if_changed,link_o_target)	// 调用cmd_link_o_target，参见cmd_link_o_target节

targets += $(builtin-target)
endif # builtin-target
```

###### 3.4.2.1.3.1.1.1 $(obj-y)

$(obj)指定目录下的Makefile为$(obj-y)赋值。以fs/Makefile为例，其中包含如下规则：

```
/*
 * 此处使用":="为obj-y重新赋值，冲掉obj-y之前的取值。
 * 其他Makefile也使用类似方法为obj-y赋值
 */
obj-y :=	open.o read_write.o file_table.o super.o		\
		char_dev.o stat.o exec.o pipe.o namei.o fcntl.o		\
		ioctl.o readdir.o select.o fifo.o dcache.o inode.o	\
		attr.o bad_inode.o file.o filesystems.o namespace.o	\
		seq_file.o xattr.o libfs.o fs-writeback.o		\
		pnode.o drop_caches.o splice.o sync.o utimes.o		\
		stack.o fs_struct.o statfs.o

ifeq ($(CONFIG_BLOCK),y)
obj-y += buffer.o bio.o block_dev.o direct-io.o mpage.o ioprio.o
else
obj-y += no-block.o
endif

obj-$(CONFIG_BLK_DEV_INTEGRITY)	+= bio-integrity.o
obj-y				+= notify/
obj-$(CONFIG_EPOLL)		+= eventpoll.o
obj-$(CONFIG_ANON_INODES)	+= anon_inodes.o
obj-$(CONFIG_SIGNALFD)		+= signalfd.o
...
```

由此可知，$(obj-y)的取值与某些配置项的取值有关。$(obj)包含两类取值：

* $(obj)目录下需要编译的目标文件，如open.o。其编译过程参见[3.4.2.1.3.1.1.1.1 编译$(obj)目录下的目标文件](#3-4-2-1-3-1-1-1-1-obj-)节；
* $(obj)下的子目录，如notify/，其编译为notify/built-in.o，参见[3.4.2.1.3.1.1.1.2 编译$(obj)下的子目录](#3-4-2-1-3-1-1-1-2-obj-)节。

###### 3.4.2.1.3.1.1.1.1 编译$(obj)目录下的目标文件

在scripts/Makefile.build中，包含下列规则：

```
// $(CHECK), $(CHECKFLAGS)定义于顶层Makefile
# Linus' kernel sanity checking tool
ifneq ($(KBUILD_CHECKSRC),0)
  ifeq ($(KBUILD_CHECKSRC),2)
    quiet_cmd_force_checksrc	= CHECK   $<
          cmd_force_checksrc	= $(CHECK) $(CHECKFLAGS) $(c_flags) $< ;
  else
      quiet_cmd_checksrc	= CHECK   $<
            cmd_checksrc	= $(CHECK) $(CHECKFLAGS) $(c_flags) $< ;
  endif
endif

ifndef CONFIG_MODVERSIONS
// $(c_flags)定义于scripts/Makefile.lib。cmd_cc_o_c的扩展结果见下文
cmd_cc_o_c = $(CC) $(c_flags) -c -o $@ $<

else
# When module versioning is enabled the following steps are executed:
# o compile a .tmp_<file>.o from <file>.c
# o if .tmp_<file>.o doesn't contain a __ksymtab version, i.e. does
#   not export symbols, we just rename .tmp_<file>.o to <file>.o and
#   are done.
# o otherwise, we calculate symbol versions using the good old
#   genksyms on the preprocessed source and postprocess them in a way
#   that they are usable as a linker script
# o generate <file>.o from .tmp_<file>.o using the linker to
#   replace the unresolved symbols __crc_exported_symbol with
#   the actual value of the checksum generated by genksyms

cmd_cc_o_c = $(CC) $(c_flags) -c -o $(@D)/.tmp_$(@F) $<
cmd_modversions =									\
	/*
	 * $(OBJDUMP)定义于顶层Makefile，取值为objdump
	 * 该语句用于在目标文件中查找是否存在名为__ksymtab的段，
	 * 参见include/linux/export.h
	 */
	if $(OBJDUMP) -h $(@D)/.tmp_$(@F) | grep -q __ksymtab; then			\
		$(call cmd_gensymtypes,$(KBUILD_SYMTYPES),$(@:.o=.symtypes))		\
		    > $(@D)/.tmp_$(@F:.o=.ver);						\
											\
		$(LD) $(LDFLAGS) -r -o $@ $(@D)/.tmp_$(@F) 				\
			-T $(@D)/.tmp_$(@F:.o=.ver);					\
		rm -f $(@D)/.tmp_$(@F) $(@D)/.tmp_$(@F:.o=.ver);			\
	else										\
		mv -f $(@D)/.tmp_$(@F) $@;						\
	fi;
endif

ifdef CONFIG_FTRACE_MCOUNT_RECORD
ifdef BUILD_C_RECORDMCOUNT
sub_cmd_record_mcount =									\
	if [ $(@) != "scripts/mod/empty.o" ]; then					\
		$(objtree)/scripts/recordmcount $(RECORDMCOUNT_FLAGS) "$(@)";		\
	fi;
recordmcount_source := $(srctree)/scripts/recordmcount.c 				\
				 $(srctree)/scripts/recordmcount.h
else
sub_cmd_record_mcount = set -e ; perl $(srctree)/scripts/recordmcount.pl "$(ARCH)"	\
	"$(if $(CONFIG_CPU_BIG_ENDIAN),big,little)"					\
	"$(if $(CONFIG_64BIT),64,32)"							\
	"$(OBJDUMP)" "$(OBJCOPY)" "$(CC) $(KBUILD_CFLAGS)"				\
	"$(LD)" "$(NM)" "$(RM)" "$(MV)"							\
	"$(if $(part-of-module),1,0)" "$(@)";
recordmcount_source := $(srctree)/scripts/recordmcount.pl
endif
cmd_record_mcount = 									\
	if [ "$(findstring -pg,$(_c_flags))" = "-pg" ]; then				\
		$(sub_cmd_record_mcount)						\
	fi;
endif

// 将源文件*.c编译成目标文件*.o，并生成命令文件.*.o.cmd
define rule_cc_o_c
	/*
	 * echo-cmd定义于scripts/Kbuild.include，
	 * 此处用于显示命令cmd_checksrc，下同
	 */
	$(call echo-cmd,checksrc) $(cmd_checksrc)					\
	// 调用cmd_cc_o_c将源文件*.c编译成目标文件*.o
	$(call echo-cmd,cc_o_c) $(cmd_cc_o_c);						\
	// 若定义了CONFIG_MODVERSIONS，则命令cmd_modversions有效
	$(cmd_modversions)								\
	$(call echo-cmd,record_mcount)							\
	$(cmd_record_mcount)								\
	scripts/basic/fixdep $(depfile) $@ '$(call make-cmd,cc_o_c)' >			\
	                                            $(dot-target).tmp;			\
	// 删除依赖文件，如fs/.open.o.d，该文件是在命令cmd_cc_o_c中生成的
	rm -f $(depfile);								\
	// 生成命令文件，如fs/.open.o.cmd
	mv -f $(dot-target).tmp $(dot-target).cmd
endef

# Built-in and composite module parts
$(obj)/%.o: $(src)/%.c $(recordmcount_source) FORCE
	$(call cmd,force_checksrc)		// 调用cmd_force_checksrc
	$(call if_changed_rule,cc_o_c)		// 调用rule_cc_o_c

/*
 * 后面还有由源代码.S编译成目标文件.o的规则，与.c编译成.o的规则类似
 */
```

如果未定义CONFIG_MODVERSIONS，则命令cmd_cc_o_c扩展后的结果如下(以fs/open.o为例)：

```
gcc -Wp,-MD,fs/.open.o.d  -nostdinc -isystem /usr/lib/gcc/i686-linux-gnu/4.7/include -I/usr/src/linux-3.2/arch/x86/include -Iarch/x86/include/generated -Iinclude  -include /usr/src/linux-3.2/include/linux/kconfig.h -D__KERNEL__ -Wall -Wundef -Wstrict-prototypes -Wno-trigraphs -fno-strict-aliasing -fno-common -Werror-implicit-function-declaration -Wno-format-security -fno-delete-null-pointer-checks -O2 -m32 -msoft-float -mregparm=3 -freg-struct-return -mpreferred-stack-boundary=2 -march=i686 -Wa,-mtune=generic32 -ffreestanding -DCONFIG_AS_CFI=1 -DCONFIG_AS_CFI_SIGNAL_FRAME=1 -DCONFIG_AS_CFI_SECTIONS=1 -pipe -Wno-sign-compare -fno-asynchronous-unwind-tables -mno-sse -mno-mmx -mno-sse2 -mno-3dnow -Wframe-larger-than=1024 -fno-stack-protector -Wno-unused-but-set-variable -fomit-frame-pointer -Wdeclaration-after-statement -Wno-pointer-sign -fno-strict-overflow -fconserve-stack -DCC_HAVE_ASM_GOTO    -D"KBUILD_STR(s)=#s" -D"KBUILD_BASENAME=KBUILD_STR(open)"  -D"KBUILD_MODNAME=KBUILD_STR(open)" -c -o fs/open.o fs/open.c
```

**假设内核中多个目录下存在同名头文件xxx.h，那么某.c文件包含的是哪个目录下的头文件xxx.h呢？**

命令cmd_cc_o_c中的变量c_flags定义于scripts/Makefile.lib:

```
C_flags = -Wp,-MD,$(depfile) $(NOSTDINC_FLAGS) $(LINUXINCLUDE)		\
		$(__c_flags) $(modkern_cflags)				\
		-D"KBUILD_STR(s)=\#s" $(basename_flags) $(modname_flags)
```

其中，变量LINUXINCLUDE定义于顶层Makefile中:

```
LINUXINCLUDE :=	-I$(srctree)/arch/$(hdr-arch)/include			\
			-Iarch/$(hdr-arch)/include/generated –Iinclude	\
			$(if $(KBUILD_SRC), -I$(srctree)/include)	\
			-include $(srctree)/include/linux/kconfig.h
```
由此可知，编译.c文件时查找头文件的目录顺序。以fs/open.o为例，LINUXINCLUDE被扩展为：

```
-I/usr/src/linux-3.2/arch/x86/include -Iarch/x86/include/generated -Iinclude -include /usr/src/linux-3.2/include/linux/kconfig.h
```

即查找头文件的目录顺序依次为：

```
arch/x86/include/			// 与体系架构有关
arch/x86/include/generated/		// 与体系架构有关
include/
include/linux/kconfig.h
```

###### 3.4.2.1.3.1.1.1.2 编译$(obj)下的子目录

在scripts/Makefile.build中，包含下列规则：

```
// 引入$(obj)指定目录下的Makefile，从中得到$(obj-y)的取值
# The filename Kbuild has precedence over Makefile
kbuild-dir  := $(if $(filter /%,$(src)),$(src),$(srctree)/$(src))
kbuild-file := $(if $(wildcard $(kbuild-dir)/Kbuild),$(kbuild-dir)/Kbuild,$(kbuild-dir)/Makefile)
include $(kbuild-file)

// 定义变量$(subdir-obj-y)和$(subdir-ym)，见下文
include scripts/Makefile.lib

# To build objects in subdirs, we need to descend into the directories
$(sort $(subdir-obj-y)): $(subdir-ym) ;

$(subdir-ym):
	/*
	 * 编译$(obj-y)和$(obj-m)中的子目录，
	 * 参见[make -f scripts/Makefile.build obj=XXX命令的执行过程](#)节;
	 * 以fs/notify为例，该命令扩展为： make -f scripts/Makefile.build obj=fs/notify
	 */
	$(Q)$(MAKE) $(build)=$@
```

**$(subdir-ym)**

$(obj-y)定义于scripts/Makefile.lib:

```
// 获取$(obj-y)和$(obj-m)中的子目录列表
__subdir-y	:= $(patsubst %/,%,$(filter %/, $(obj-y)))
subdir-y	+= $(__subdir-y)
__subdir-m	:= $(patsubst %/,%,$(filter %/, $(obj-m)))
subdir-m	+= $(__subdir-m)

/*
 * $(subdir-ym)为$(obj)的子目录名:
 * 以$(obj)=fs为例，则$(subdir-ym)取值为notify等
 */
# Subdirectories we need to descend into
subdir-ym	:= $(sort $(subdir-y) $(subdir-m))

// Add subdir path
subdir-ym	:= $(addprefix $(obj)/,$(subdir-ym))
```

**$(subdir-obj-y)**

$(subdir-obj-y)定义于scripts/Makefile.lib：

```
// 若$(obj-y)中包含子目录，则将子目录下的文件编译为built-in.o，如notify/built-in.o
obj-y		  := $(patsubst %/, %/built-in.o, $(obj-y))

# $(subdir-obj-y) is the list of objects in $(obj-y) which uses dir/ to
# tell kbuild to descend
subdir-obj-y := $(filter %/built-in.o, $(obj-y))

/*
 * $(subdir-obj-y)的取值，如 fs/notify/built-in.o fs/proc/built-in.o ...
 * 而这些目标文件的编译参见[$(builtin-target)节](#)
 */
subdir-obj-y := $(addprefix $(obj)/,$(subdir-obj-y))
```

如果$(obj-y)中包含子目录，则将子目录下的源文件编译成一个目标文件built-in.o。

###### 3.4.2.1.3.1.1.2 cmd_link_o_target

在scripts/Makefile.build中，包含下列规则：

```
#
# Rule to compile a set of .o files into one .o file
#
ifdef builtin-target

quiet_cmd_link_o_target = LD      $@
# If the list of objects to link is empty, just create an empty built-in.o
cmd_link_o_target = $(if $(strip $(obj-y)),				\
		    $(LD) $(ld_flags) -r -o $@ $(filter $(obj-y), $^)	\
		    $(cmd_secanalysis),					\
		    rm -f $@; $(AR) rcs$(KBUILD_ARFLAGS) $@)

endif # builtin-target
```

命令cmd_link_o_target将$(obj)指定目录及其子目录下的目标文件连接成built-in.o。以obj=fs为例，该命令扩展为：

```
ld -m elf_i386   -r -o fs/built-in.o fs/open.o fs/read_write.o fs/file_table.o fs/super.o fs/char_dev.o fs/stat.o fs/exec.o fs/pipe.o fs/namei.o fs/fcntl.o fs/ioctl.o fs/readdir.o fs/select.o fs/fifo.o fs/dcache.o fs/inode.o fs/attr.o fs/bad_inode.o fs/file.o fs/filesystems.o fs/namespace.o fs/seq_file.o fs/xattr.o fs/libfs.o fs/fs-writeback.o fs/pnode.o fs/drop_caches.o fs/splice.o fs/sync.o fs/utimes.o fs/stack.o fs/fs_struct.o fs/statfs.o fs/buffer.o fs/bio.o fs/block_dev.o fs/direct-io.o fs/mpage.o fs/ioprio.o fs/notify/built-in.o fs/eventpoll.o fs/anon_inodes.o fs/signalfd.o fs/timerfd.o fs/eventfd.o fs/aio.o fs/locks.o fs/binfmt_script.o fs/quota/built-in.o fs/proc/built-in.o fs/partitions/built-in.o fs/sysfs/built-in.o fs/devpts/built-in.o fs/ramfs/built-in.o fs/nls/built-in.o fs/exofs/built-in.o
```

###### 3.4.2.1.3.1.2 $(lib-target)

在scripts/Makefile.build中，包含下列规则：

```
// 将$(obj)指定目录下的库文件编译成$(obj)/lib.a
ifneq ($(strip $(lib-y) $(lib-m) $(lib-n) $(lib-)),)
lib-target := $(obj)/lib.a
endif

ifneq ($(strip $(obj-y) $(obj-m) $(obj-n) $(obj-) $(subdir-m) $(lib-target)),)
builtin-target := $(obj)/built-in.o
endif

#
# Rule to compile a set of .o files into one .a file
#
ifdef lib-target
quiet_cmd_link_l_target = AR      $@
cmd_link_l_target = rm -f $@; $(AR) rcs$(KBUILD_ARFLAGS) $@ $(lib-y)

/*
 * $(lib-y)定义于$(obj)指定目录下的Kbuild或Makefile，
 * 并经过scripts/Makefile.lib的进一步处理，参见[$(lib-y)](#)节
 */
$(lib-target): $(lib-y) FORCE
	// 调用cmd_link_l_target编译$(lib-target)
	$(call if_changed,link_l_target)

targets += $(lib-target)
endif
```

###### 3.4.2.1.3.1.2.1 $(lib-y)

$(lib-y)或$(lib-m)定义于$(obj)指定目录下的Kbuild或Makefile，以lib/Makefile为例，其中包含下列规则：

```
lib-y := ctype.o string.o vsprintf.o cmdline.o			\
	 rbtree.o radix-tree.o dump_stack.o timerqueue.o	\
	 idr.o int_sqrt.o extable.o prio_tree.o			\
	 sha1.o md5.o irq_regs.o reciprocal_div.o argv_split.o	\
	 proportions.o prio_heap.o ratelimit.o show_mem.o	\
	 is_single_threaded.o plist.o decompress.o

lib-$(CONFIG_MMU) += ioremap.o
lib-$(CONFIG_SMP) += cpumask.o

lib-y	+= kobject.o kref.o klist.o
...
```

在scripts/Makefile.lib中，对$(lib-y)或$(lib-m)进一步处理，如下所示：

```
# Libraries are always collected in one lib file.
# Filter out objects already built-in
lib-y := $(filter-out $(obj-y), $(sort $(lib-y) $(lib-m)))

/*
 * 此时，$(lib-y)中包含了要编译进库文件中的目标文件列表，如lib/ctype.o lib/string.o ...
 * 编译$(lib-y)时，符合规则"$(obj)/%.o: $(src)/%.c $(recordmcount_source) FORCE"
 */ 参见[编译$(obj)目录下的目标文件](#)节
lib-y := $(addprefix $(obj)/,$(lib-y))
```

###### 3.4.2.1.3.1.3 $(extra-y)

$(extra-y)的取值参见$(obj)指定目录下的Kbuild或Makefile。在scripts/Makefile.lib中，对其做进一步处理：

```
/*
 * 此时，$(extra-y)为目标文件列表
 * 编译$(lib-y)时，符合规则"$(obj)/%.o: $(src)/%.c $(recordmcount_source) FORCE"
 * 参见编译$(obj)目录下的目标文件节
 */
extra-y := $(addprefix $(obj)/,$(extra-y))
```

###### 3.4.2.1.3.1.4 $(obj-m)

$(obj-m)的取值参见$(obj)指定目录下的Kbuild或Makefile。在scripts/Makefile.lib中，对其做进一步处理：

```
# When an object is listed to be built compiled-in and modular,
# only build the compiled-in version
obj-m			:= $(filter-out $(obj-y),$(obj-m))

__subdir-m		:= $(patsubst %/,%,$(filter %/, $(obj-m)))
/*
 * $(subdir-m)中包含$(obj-m)中的子目录名，
 * 其将在$(builtin-target)中编译，参见[编译$(obj)下的子目录](#)节
 */
subdir-m		+= $(__subdir-m)
obj-m			:= $(filter-out %/, $(obj-m))

/*
 * 此时，$(obj-m)中仅包含$(obj)指定目录下的目标文件列表，不包含子目录
 * 编译$(obj-m)时，符合规则"$(obj)/%.o: $(src)/%.c $(recordmcount_source) FORCE"，
 * 参见[编译$(obj)目录下的目标文件](#)节
 */
obj-m			:= $(addprefix $(obj)/,$(obj-m))
```

###### 3.4.2.1.3.1.5 $(modorder-target)

在scripts/Makefile.build中，包含下列规则：

```
modorder-target := $(obj)/modules.order

#
# Rule to create modules.order file
#
# Create commands to either record .ko file or cat modules.order from
# a subdirectory
modorder-cmds =						\
	// $(modorder)定义于scripts/Makefile.lib，见下文
	$(foreach m, $(modorder),			\
		$(if $(filter %/modules.order, $m),	\
			cat $m;, echo kernel/$m;))

// $(subdir-ym)的编译参见[编译$(obj)下的子目录](#)节
$(modorder-target): $(subdir-ym) FORCE
	/*
	 * 调用modorder-cmds生成$(obj)/modules.order文件，
	 * 以fs/notify为例，该语句展开为：
 * (cat /dev/null;   cat fs/notify/dnotify/modules.order;
 *  cat fs/notify/inotify/modules.order;
 *  cat fs/notify/fanotify/modules.order;) > fs/notify/modules.order
 */
	$(Q)(cat /dev/null; $(modorder-cmds)) > $@
```

$(modorder)定义于scripts/Makefile.lib：

```
# Determine modorder.
# Unfortunately, we don't have information about ordering between -y
# and -m subdirs.  Just put -y's first.
modorder	:= $(patsubst %/,%/modules.order, $(filter %/, $(obj-y)) $(obj-m:.o=.ko))

...
modorder	:= $(addprefix $(obj)/,$(modorder))
```

###### 3.4.2.1.3.1.6 $(always)

$(always)的取值参见$(obj)指定目录下的Kbuild或Makefile。在scripts/Makefile.lib中，对其做进一步处理：

```
/*
 * 此时，$(always)中仅包含$(obj)指定目录下的目标文件列表
 * 编译$(always)时，符合规则"$(obj)/%.o: $(src)/%.c $(recordmcount_source) FORCE"，
 * 参见[编译$(obj)目录下的目标文件](#)节
 */
always		:= $(addprefix $(obj)/,$(always))
```

#### 3.4.2.2 $(vmlinux-lds)

##### 3.4.2.2.1 vmlinux.lds的作用

编译内核的过程分两步：一是"编译"，二是"链接"。vmlinux.lds就是告诉编译器如何链接编译好的各个.o文件，即如何组织内核的每个函数存放在内核镜像文件的位置，参见[Appendix F: vmlinux.lds.S](#appendix-f-vmlinux-lds-s)。

![Vmlinux_Image](/assets/Vmlinux_Image.png)

##### 3.4.2.2.2 vmlinux.lds如何生成

在顶层Makefile中，包含下列规则：

```
vmlinux-lds  := arch/$(SRCARCH)/kernel/vmlinux.lds

...
# The actual objects are generated when descending,
# make sure no implicit rule kicks in
$(sort $(vmlinux-init) $(vmlinux-main)) $(vmlinux-lds): $(vmlinux-dirs) ;
```

由$(vmlinux-dirs)节可知，当编译arch/x86目录时，将执行下列命令：

```
// 此处以x86体系为例，由arch/x86/Makefile中的core-y引入
make -f scripts/Makefile.build obj=arch/x86
```

其中，arch/x86/Kbuild包含下列规则：

```
obj-y += kernel/
```

即编译子目录arch/x86/kernel，并调用arch/x86/kernel/Makefile，其包含下列规则：

```
extra-y := head_$(BITS).o head$(BITS).o head.o init_task.o vmlinux.lds
```

根据$(extra-y)节的规则编译$(extra-y)，即根据scripts/Makefile.build中的下列规则来生成vmlinux.lds：

```
# Linker scripts preprocessor (.lds.S -> .lds)
# ---------------------------------------------------------------------------
quiet_cmd_cpp_lds_S = LDS     $@
       cmd_cpp_lds_S = $(CPP) $(cpp_flags) -P -C -U$(ARCH) \
	               -D__ASSEMBLY__ -DLINKER_SCRIPT -o $@ $<

$(obj)/%.lds: $(src)/%.lds.S FORCE		// vmlinux.lds是由vmlinux.lds.S预编译得来的
	$(call if_changed_dep,cpp_lds_S)	// 调用cmd_cpp_lds_S生成vmlinux.lds
```

由此可知，vmlinux.lds是由arch/x86/kernel/vmlinux.lds.S预处理而来，所用命令为cmd_cpp_lds_S。该命令被扩展为(参见编译后产生的命令文件arch/x86/kernel/.vmlinux.lds.cmd)：

```
gcc -E -Wp,-MD,arch/x86/kernel/.vmlinux.lds.d  -nostdinc -isystem /usr/lib/gcc/i686-linux-gnu/4.7/include -I/usr/src/linux-3.2/arch/x86/include -Iarch/x86/include/generated -Iinclude  -include /usr/src/linux-3.2/include/linux/kconfig.h -D__KERNEL__   -Ui386 -P -C -Ui386 -D__ASSEMBLY__ -DLINKER_SCRIPT -o arch/x86/kernel/vmlinux.lds arch/x86/kernel/vmlinux.lds.S
```

预处理后的vmlinux.lds是链接器GNU ld的link script文件，它是由Linker Command Language写成的，用于链接.o文件，参见[3.4.2.6.1.2 cmd_vmlinux__](#3-4-2-6-1-2-cmd-vmlinux-)节。关于link script语法，参见[Using ld](/docs/Using_ld_v2.17.pdf)。

#### 3.4.2.3 $(vmlinux-init)

在顶层Makefile中，包含下列规则：

```
vmlinux-init := $(head-y) $(init-y)
```

##### 3.4.2.3.1 $(head-y)

$(head-y)与体系结构有关，其定义于arch/$(ARCH)/Makefile。以x86为例，参见arch/x86/Makefile:

```
head-y := arch/x86/kernel/head_$(BITS).o	// $(BITS) = 32 or 64, 由head_32.S或head_64.S编译而来
head-y += arch/x86/kernel/head$(BITS).o		// $(BITS) = 32 or 64, 由head32.S或head64.S编译而来
head-y += arch/x86/kernel/head.o		// 由head.c编译而来
head-y += arch/x86/kernel/init_task.o		// 由init_task.c编译而来
```

根据$(vmlinux-dirs)节，在执行命令：

```
make -f scripts/Makefile.build obj=arch/x86/kernel
```

编译arch/x86/kernel/目录时，调用arch/x86/kernel/Makefile，其中包含：

```
extra-y := head_$(BITS).o head$(BITS).o head.o init_task.o vmlinux.lds
```

因此，$(head-y)中定义的目标文件是在编译$(extra-y)时编译的，参见[3.4.2.1.3.1.3 $(extra-y)](#3-4-2-1-3-1-3-extra-y-)节。

##### 3.4.2.3.2 $(init-y)

在顶层Makefile中，包含下列规则：

```
init-y	:= init/
init-y	:= $(patsubst %/, %/built-in.o, $(init-y))
```

根据$(vmlinux-dirs)节，在执行命令：

```
make -f scripts/Makefile.build obj=init
```

编译init/目录时，由于没有指定目标，故编译scripts/Makefile.build中的默认目标__build：

```
// 扩展为kbuild-dir := init
kbuild-dir := $(if $(filter /%,$(src)),$(src),$(srctree)/$(src))
// 扩展为kbuild-file := init/Makefile
kbuild-file := $(if $(wildcard $(kbuild-dir)/Kbuild),$(kbuild-dir)/Kbuild,$(kbuild-dir)/Makefile)
// 此处将init/Makefile包含进来，其中包括$(obj-y)变量
include $(kbuild-file)

...
__build: $(if $(KBUILD_BUILTIN),$(builtin-target) $(lib-target) $(extra-y))	\
	 $(if $(KBUILD_MODULES),$(obj-m) $(modorder-target))			\
	 $(subdir-ym) $(always)
	@:
```

\_\_build依赖于$(builtin-target)，在scripts/Makefile.build中，包含如下规则：

```
// 由init/Makefile可知，$(obj-y)不为空，故此条件成立
ifneq ($(strip $(obj-y) $(obj-m) $(obj-n) $(obj-) $(subdir-m) $(lib-target)),)
builtin-target := $(obj)/built-in.o		// init/built-in.o
endif

...
ifdef builtin-target
...
/*
 * 扩展为init/built-in.o: $(obj-y) FORCE，
 * 其中$(obj-y)定义于init/Makefile
 */
$(builtin-target): $(obj-y) FORCE
	/*
	 * if_changed定义于scripts/Kbuild.include，
	 * 若为真，则调用cmd_link_o_target
	 */
	$(call if_changed,link_o_target)
...
endif # builtin-target
```

由上述规则可知，先编译init/Makefile中$(obj-y)定义的目标文件，然后调用cmd_link_o_target函数链接$(obj-y)中的.o文件生成init/built-in.o。

```
$(obj-y)定义于init/Makefile中，且与.config中的配置有关：
obj-y						:= main.o version.o mounts.o
ifneq ($(CONFIG_BLK_DEV_INITRD),y)
obj-y						+= noinitramfs.o
else
obj-$(CONFIG_BLK_DEV_INITRD)			+= initramfs.o
endif
obj-$(CONFIG_GENERIC_CALIBRATE_DELAY)		+= calibrate.o
```

**$(obj-y)中main.o和version.o的编译**

main.o和version.o分别由main.c和version.c编译而来，其编译过程满足scripts/ Makefile.build中的规则"$(obj)/%.o: $(src)/%.c $(recordmcount_source) FORCE"，参见[3.4.2.1.3.1.1.1.1 编译$(obj)目录下的目标文件](#3-4-2-1-3-1-1-1-1-obj-)节。

**$(obj-y)中mounts.o的编译、链接**

mounts.o的编译有些复杂。首先，mounts.o不是由mounts.c编译而来，它由多个.c文件编译而来。由init/Makefile中的规则：

```
mounts-y				:= do_mounts.o
mounts-$(CONFIG_BLK_DEV_RAM)		+= do_mounts_rd.o
mounts-$(CONFIG_BLK_DEV_INITRD)		+= do_mounts_initrd.o
mounts-$(CONFIG_BLK_DEV_MD)		+= do_mounts_md.o
```

如果配置项取值如下：

```
CONFIG_BLK_DEV_RAM = y
CONFIG_BLK_DEV_INITRD = y
CONFIG_BLK_DEV_MD = y
```

则，mounts-y = do_mounts.o do_mounts_rd.o do_mounts_initrd.o do_mounts_md.o

由scripts/Makefile.lib中的如下规则：

```
// 扩展为multi-used-y := mounts.o
multi-used-y := $(sort $(foreach m,$(obj-y), $(if $(strip $($(m:.o=-objs)) $($(m:.o=-y))), $(m))))

...
/*
 * 扩展为multi-objs-y := $(mounts-y)，即：
 * multi-objs-y := do_mounts.o do_mounts_rd.o do_mounts_initrd.o do_mounts_md.o
 */
multi-objs-y := $(foreach m, $(multi-used-y), $($(m:.o=-objs)) $($(m:.o=-y)))
```

再根据scritps/Makefile.build中的如下规则：

```
$(multi-used-y) : %.o: $(multi-objs-y) FORCE
	$(call if_changed,link_multi-y)
...
# Built-in and composite module parts
$(obj)/%.o: $(src)/%.c $(recordmcount_source) FORCE
	$(call cmd,force_checksrc)
	$(call if_changed_rule,cc_o_c)
```

可知，首先执行rule_cc_o_c命令，分别将do_mounts.c、do_mounts_rd.c、do_mounts_initrd.c、do_mounts_md.c编译成do_mounts.o、do_mounts_rd.o、do_mounts_initrd.o、do_mounts_md.o；然后执行cmd_multi-y命令，将这些.o文件链接成mounts.o文件。

#### 3.4.2.4 $(vmlinux-main)

在顶层Makefile中，包含如下规则：

```
vmlinux-main := $(core-y) $(libs-y) $(drivers-y) $(net-y)
...
# The actual objects are generated when descending,
# make sure no implicit rule kicks in
$(sort $(vmlinux-init) $(vmlinux-main)) $(vmlinux-lds): $(vmlinux-dirs) ;
```

$(vmlinux-main)依赖于$(vmlinux-dirs)，其编译过程参见$(vmlinux-dirs)节，即通过下列命令编译：

```
// 编译$(core-y)
make -f scripts/Makefile.build obj=usr
make -f scripts/Makefile.build obj=arch/x86		// 此处以x86体系为例，由arch/x86/Makefile中的core-y引入
make -f scripts/Makefile.build obj=kernel
make -f scripts/Makefile.build obj=mm
make -f scripts/Makefile.build obj=fs
make -f scripts/Makefile.build obj=ipc
make -f scripts/Makefile.build obj=security
make -f scripts/Makefile.build obj=crypto
make -f scripts/Makefile.build obj=block
// 编译$(drivers-y)
make -f scripts/Makefile.build obj=drivers
make -f scripts/Makefile.build obj=sound
make -f scripts/Makefile.build obj=firmware
make -f scripts/Makefile.build obj=arch/x86/math-emu	// 与CONFIG_MATH_EMULATION有关
make -f scripts/Makefile.build obj=arch/x86/pci		// 与CONFIG_PCI有关
make -f scripts/Makefile.build obj=arch/x86/oprofile	// 与CONFIG_OPROFILE有关
make -f scripts/Makefile.build obj=arch/x86/power	// 与CONFIG_PM有关
make -f scripts/Makefile.build obj=arch/x86/video	// 与CONFIG_FB有关
// 编译$(net-y)
make -f scripts/Makefile.build obj=net
// 编译$(libs-y)
make -f scripts/Makefile.build obj=lib
make -f scripts/Makefile.build obj=arch/x86/lib/	// 以x86为例，由arch/x86/Makefile中的lib-y引入
```

#### 3.4.2.5 vmlinux.o

在顶层Makefile中，包含如下规则：

```
/*
 * 由$(vmlinux-init)节可知，modpost-init被扩展
 * 为$(head-y)，而$(head-y)的编译参见$(head-y)节
 */
modpost-init := $(filter-out init/built-in.o, $(vmlinux-init))

// $(vmlinux-main)的编译参见$(vmlinux-main)节
vmlinux.o: $(modpost-init) $(vmlinux-main) FORCE
	// 调用rule_vmlinux-modpost生成vmlinux.o
	$(call if_changed_rule,vmlinux-modpost)

...
// 参见cmd_vmlinux-modpost节
quiet_cmd_vmlinux-modpost = LD      $@
     cmd_vmlinux-modpost   = $(LD) $(LDFLAGS) -r -o $@			\
	 $(vmlinux-init) --start-group $(vmlinux-main) --end-group	\
	 $(filter-out $(vmlinux-init) $(vmlinux-main) FORCE ,$^)

// 参见rule_vmlinux-modpost节
define rule_vmlinux-modpost
	:
	// 调用cmd_vmlinux-modpost编译vmlinux.o
	+$(call cmd,vmlinux-modpost)
	// 根据vmlinux.o生成Module.symvers
	$(Q)$(MAKE) -f $(srctree)/scripts/Makefile.modpost $@
	// 生成命令文件.vmlinux.o.cmd
	$(Q)echo 'cmd_$@ := $(cmd_vmlinux-modpost)' > $(dot-target).cmd
endef
```

##### 3.4.2.5.1 cmd_vmlinux-modpost

调用下列命令链接vmlinux.o：

```
cmd_vmlinux-modpost = $(LD) $(LDFLAGS) -r -o $@					\
		      $(vmlinux-init) --start-group $(vmlinux-main) --end-group	\
		      $(filter-out $(vmlinux-init) $(vmlinux-main) FORCE ,$^)
```

其中，$^ is a list of all the prerequisites of the rule，此处取值为$(modpost-init) $(vmlinux-main)。

该命令被扩展为：

```
ld -m elf_i386 -r -o vmlinux.o arch/x86/kernel/head_32.o arch/x86/kernel/head32.o arch/x86/kernel/head.o arch/x86/kernel/init_task.o  init/built-in.o --start-group  usr/built-in.o  arch/x86/built-in.o  kernel/built-in.o  mm/built-in.o  fs/built-in.o  ipc/built-in.o  security/built-in.o  crypto/built-in.o  block/built-in.o  lib/lib.a  arch/x86/lib/lib.a  lib/built-in.o  arch/x86/lib/built-in.o  drivers/built-in.o  sound/built-in.o  firmware/built-in.o  arch/x86/math-emu/built-in.o  arch/x86/power/built-in.o  net/built-in.o –end-group
```

##### 3.4.2.5.2 rule_vmlinux-modpost

调用cmd_vmlinux-modpost链接vmlinux.o完成后，将执行下列命令：

```
$(Q)$(MAKE) -f $(srctree)/scripts/Makefile.modpost $@
```

该命令被扩展为：

```
make -f scripts/Makefile.modpost vmlinux.o
```

即执行scripts/Makefile.modpost中的vmlinux.o目标：

```
modpost	= scripts/mod/modpost						\
	  $(if $(CONFIG_MODVERSIONS),-m)				\
	  $(if $(CONFIG_MODULE_SRCVERSION_ALL),-a,)			\
	  // kernelsymfile := $(objtree)/Module.symvers
	  $(if $(KBUILD_EXTMOD),-i,-o) $(kernelsymfile)			\
	  $(if $(KBUILD_EXTMOD),-I $(modulesymfile))			\
	  $(if $(KBUILD_EXTRA_SYMBOLS), $(patsubst %, -e %,$(KBUILD_EXTRA_SYMBOLS)))	\
	  $(if $(KBUILD_EXTMOD),-o $(modulesymfile))			\
	  $(if $(CONFIG_DEBUG_SECTION_MISMATCH),,-S)			\
	  $(if $(KBUILD_EXTMOD)$(KBUILD_MODPOST_WARN),-w)		\
	  $(if $(cross_build),-c)

...
quiet_cmd_kernel-mod = MODPOST $@
	cmd_kernel-mod = $(modpost) $@

vmlinux.o: FORCE
	// 调用cmd_kernel-mod
	$(call cmd,kernel-mod)
```

调用cmd_kernel-mod执行下列命令：

```
scripts/mod/modpost -m -o /usr/src/linux-3.2/Module.symvers  vmlinux.o
```

输出Module.symvers文件，其中包含基本内核导出的、供模块使用的符号以及CRC校验和。

上述命令执行完成后，继续执行下列命令：

```
$(Q)echo 'cmd_$@ := $(cmd_vmlinux-modpost)' > $(dot-target).cmd
```

根据scripts/Kbuild.include中对dot-target的定义：

```
# Name of target with a '.' as filename prefix. foo/bar.o => foo/.bar.o
dot-target = $(dir $@).$(notdir $@)
```

可知，该命令被扩展为：

```
echo 'cmd_vmlinux.o := ld -m elf_i386 -r -o vmlinux.o arch/x86/kernel/head_32.o arch/x86/kernel/head32.o arch/x86/kernel/head.o arch/x86/kernel/init_task.o  init/built-in.o --start-group  usr/built-in.o  arch/x86/built-in.o  kernel/built-in.o  mm/built-in.o  fs/built-in.o  ipc/built-in.o  security/built-in.o  crypto/built-in.o  block/built-in.o  lib/lib.a  arch/x86/lib/lib.a  lib/built-in.o  arch/x86/lib/built-in.o  drivers/built-in.o  sound/built-in.o  firmware/built-in.o  arch/x86/math-emu/built-in.o  arch/x86/power/built-in.o  net/built-in.o --end-group ' > ./.vmlinux.o.cmd
```

其输出为./.vmlinux.o.cmd，该文件包含编译vmlinux.o时所用的命令。

#### 3.4.2.6 $(kallsyms.o)

在顶层Makefile中，包含如下规则：

```
// kallsyms.o的取值与.config中的配置项CONFIG_KALLSYMS有关
ifdef CONFIG_KALLSYMS
    last_kallsyms := 2
    ifdef KALLSYMS_EXTRA_PASS
        ifneq ($(KALLSYMS_EXTRA_PASS),0)
            last_kallsyms := 3
        endif
    endif

// kallsymb.o := .tmp_kallsyms2.o或.tmp_kallsyms3.o
kallsyms.o := .tmp_kallsyms$(last_kallsyms).o

...
endif

.tmp_kallsyms1.o .tmp_kallsyms2.o .tmp_kallsyms3.o: %.o: %.S scripts FORCE
	// 调用cmd_as_o_S生成.tmp_kallsym%.o
	$(call if_changed_dep,as_o_S)

// KALLSYMS = scripts/kallsyms
.tmp_kallsyms%.S: .tmp_vmlinux% $(KALLSYMS)
	// 调用cmd_kallsyms生成.tmp_kallsym%.S
	$(call cmd,kallsyms)

# .tmp_vmlinux1 must be complete except kallsyms, so update vmlinux version
.tmp_vmlinux1: $(vmlinux-lds) $(vmlinux-all) FORCE
	// 调用rule_ksym_ld链接生成.tmp_vmlinux1
	$(call if_changed_rule,ksym_ld)

.tmp_vmlinux2: $(vmlinux-lds) $(vmlinux-all) .tmp_kallsyms1.o FORCE
	// 调用cmd_vmlinux__链接生成.tmp_vmlinux2
	$(call if_changed,vmlinux__)

.tmp_vmlinux3: $(vmlinux-lds) $(vmlinux-all) .tmp_kallsyms2.o FORCE
	// 调用cmd_vmlinux__链接生成.tmp_vmlinux3
	$(call if_changed,vmlinux__)
```

编译链接$(kallsyms.o)的流程：

![Compiling_kallsyms.o](/assets/Compiling_kallsyms.o.jpg)

##### 3.4.2.6.1 rule_ksym_ld

在顶层Makefile中，包含如下规则：

```
# Rule to link vmlinux - also used during CONFIG_KALLSYMS
# May be overridden by arch/$(ARCH)/Makefile
quiet_cmd_vmlinux__ ?= LD      $@
      cmd_vmlinux__ ?= $(LD) $(LDFLAGS) $(LDFLAGS_vmlinux) -o $@		\
      -T $(vmlinux-lds) $(vmlinux-init)						\
      --start-group $(vmlinux-main) --end-group					\
      $(filter-out $(vmlinux-lds) $(vmlinux-init) $(vmlinux-main) vmlinux.o FORCE ,$^)

# Generate new vmlinux version
quiet_cmd_vmlinux_version = GEN     .version
      cmd_vmlinux_version = set -e;						\
	  if [ ! -r .version ]; then						\
	    rm -f .version;							\
	    echo 1 >.version;							\
	  else									\
	    mv .version .old_version;						\
	    expr 0$$(cat .old_version) + 1 >.version;				\
	  fi;									\
	  $(MAKE) $(build)=init

...
define rule_ksym_ld
	:
	// 调用cmd_vmlinux_version
	+$(call cmd,vmlinux_version)
	// 调用cmd_vmlinux__
	$(call cmd,vmlinux__)
	// 生成命令文件..tmp_vmlinux1.cmd
	$(Q)echo 'cmd_$@ := $(cmd_vmlinux__)' > $(@D)/.$(@F).cmd
endef
```

###### 3.4.2.6.1.1 cmd_vmlinux_version

该命令的输出.version文件。

###### 3.4.2.6.1.2 cmd_vmlinux__

链接.tmp_vmlinux1时，该命令被扩展为：

```
cmd_.tmp_vmlinux1 := ld -m elf_i386 --build-id -o .tmp_vmlinux1 -T arch/x86/kernel/vmlinux.lds arch/x86/kernel/head_32.o arch/x86/kernel/head32.o arch/x86/kernel/head.o arch/x86/kernel/init_task.o  init/built-in.o --start-group  usr/built-in.o  arch/x86/built-in.o  kernel/built-in.o  mm/built-in.o  fs/built-in.o  ipc/built-in.o  security/built-in.o  crypto/built-in.o  block/built-in.o  lib/lib.a  arch/x86/lib/lib.a  lib/built-in.o  arch/x86/lib/built-in.o  drivers/built-in.o  sound/built-in.o  firmware/built-in.o  arch/x86/math-emu/built-in.o  arch/x86/power/built-in.o  net/built-in.o --end-group
```

链接.tmp_vmlinux2时，该命令被扩展为：

```
cmd_.tmp_vmlinux2 := ld -m elf_i386 --build-id -o .tmp_vmlinux2 -T arch/x86/kernel/vmlinux.lds arch/x86/kernel/head_32.o arch/x86/kernel/head32.o arch/x86/kernel/head.o arch/x86/kernel/init_task.o  init/built-in.o --start-group  usr/built-in.o  arch/x86/built-in.o  kernel/built-in.o  mm/built-in.o  fs/built-in.o  ipc/built-in.o  security/built-in.o  crypto/built-in.o  block/built-in.o  lib/lib.a  arch/x86/lib/lib.a  lib/built-in.o  arch/x86/lib/built-in.o  drivers/built-in.o  sound/built-in.o  firmware/built-in.o  arch/x86/math-emu/built-in.o  arch/x86/power/built-in.o  net/built-in.o --end-group .tmp_kallsyms1.o
```

链接.tmp_vmlinux3时，该命令被扩展为：

```
cmd_.tmp_vmlinux3 := ld -m elf_i386 --build-id -o .tmp_vmlinux3 -T arch/x86/kernel/vmlinux.lds arch/x86/kernel/head_32.o arch/x86/kernel/head32.o arch/x86/kernel/head.o arch/x86/kernel/init_task.o  init/built-in.o --start-group  usr/built-in.o  arch/x86/built-in.o  kernel/built-in.o  mm/built-in.o  fs/built-in.o  ipc/built-in.o  security/built-in.o  crypto/built-in.o  block/built-in.o  lib/lib.a  arch/x86/lib/lib.a  lib/built-in.o  arch/x86/lib/built-in.o  drivers/built-in.o  sound/built-in.o  firmware/built-in.o  arch/x86/math-emu/built-in.o  arch/x86/power/built-in.o  net/built-in.o --end-group .tmp_kallsyms2.o
```

该命令的输出为.tmp_vmlinux1, .tmp_vmlinux2, 或.tmp_vmlinux3。

链接vmlinux时(参见rule_vmlinux__节)，该命令被扩展为：

```
cmd_vmlinux := ld -m elf_i386 --build-id -o vmlinux -T arch/x86/kernel/vmlinux.lds arch/x86/kernel/head_32.o arch/x86/kernel/head32.o arch/x86/kernel/head.o arch/x86/kernel/init_task.o  init/built-in.o --start-group  usr/built-in.o  arch/x86/built-in.o  kernel/built-in.o  mm/built-in.o  fs/built-in.o  ipc/built-in.o  security/built-in.o  crypto/built-in.o  block/built-in.o  lib/lib.a  arch/x86/lib/lib.a  lib/built-in.o  arch/x86/lib/built-in.o  drivers/built-in.o  sound/built-in.o  firmware/built-in.o  arch/x86/math-emu/built-in.o  arch/x86/power/built-in.o  net/built-in.o --end-group .tmp_kallsyms2.o
```

注：本命令中最后一个参数为.tmp_kallsyms2.o，也可能为.tmp_kallsyms3.o，具体取值与配置有关，参见[3.4.2.6 $(kallsyms.o)](#3-4-2-6-kallsyms-o-)节。

##### 3.4.2.6.2 cmd_kallsyms

在顶层Makefile中，包含如下规则：

```
# Generate .S file with all kernel symbols
quiet_cmd_kallsyms = KSYM    $@
      cmd_kallsyms = $(NM) -n $< | $(KALLSYMS) \
                     $(if $(CONFIG_KALLSYMS_ALL),--all-symbols) > $@
```

在编译.tmp_kallsyms1.S时，该命令被扩展为：

```
nm -n .tmp_vmlinux1 | scripts/kallsyms --all-symbols > .tmp_kallsyms1.S
```

在编译.tmp_kallsyms2.S时，该命令被扩展为：

```
nm -n .tmp_vmlinux2 | scripts/kallsyms --all-symbols > .tmp_kallsyms2.S
```

在编译.tmp_kallsyms3.S时，该命令被扩展为：

```
nm -n .tmp_vmlinux3 | scripts/kallsyms --all-symbols > .tmp_kallsyms3.S
```

##### 3.4.2.6.3 cmd_as_o_S

在顶层Makefile中，包含如下规则：

```
quiet_cmd_as_o_S = AS      $@
cmd_as_o_S       = $(CC) $(a_flags) -c -o $@ $<
```

在编译.tmp_kallsyms1.o时，该命令被扩展为：

```
cmd_.tmp_kallsyms1.o := gcc -Wp,-MD,./..tmp_kallsyms1.o.d -D__ASSEMBLY__ -m32 -DCONFIG_AS_CFI=1 -DCONFIG_AS_CFI_SIGNAL_FRAME=1 -DCONFIG_AS_CFI_SECTIONS=1   -gdwarf-2    -nostdinc -isystem /usr/lib/gcc/i686-linux-gnu/4.7/include -I/usr/src/linux-3.2/arch/x86/include -Iarch/x86/include/generated -Iinclude  -include /usr/src/linux-3.2/include/linux/kconfig.h -D__KERNEL__    -c -o .tmp_kallsyms1.o .tmp_kallsyms1.S
```

在编译.tmp_kallsyms2.o时，该命令被扩展为：

```
cmd_.tmp_kallsyms2.o := gcc -Wp,-MD,./..tmp_kallsyms1.o.d -D__ASSEMBLY__ -m32 -DCONFIG_AS_CFI=1 -DCONFIG_AS_CFI_SIGNAL_FRAME=1 -DCONFIG_AS_CFI_SECTIONS=1   -gdwarf-2    -nostdinc -isystem /usr/lib/gcc/i686-linux-gnu/4.7/include -I/usr/src/linux-3.2/arch/x86/include -Iarch/x86/include/generated -Iinclude  -include /usr/src/linux-3.2/include/linux/kconfig.h -D__KERNEL__    -c -o .tmp_kallsyms2.o .tmp_kallsyms2.S
```

在编译.tmp_kallsyms3.o时，该命令被扩展为：

```
cmd_.tmp_kallsyms3.o := gcc -Wp,-MD,./..tmp_kallsyms1.o.d -D__ASSEMBLY__ -m32 -DCONFIG_AS_CFI=1 -DCONFIG_AS_CFI_SIGNAL_FRAME=1 -DCONFIG_AS_CFI_SECTIONS=1   -gdwarf-2    -nostdinc -isystem /usr/lib/gcc/i686-linux-gnu/4.7/include -I/usr/src/linux-3.2/arch/x86/include -Iarch/x86/include/generated -Iinclude  -include /usr/src/linux-3.2/include/linux/kconfig.h -D__KERNEL__    -c -o .tmp_kallsyms3.o .tmp_kallsyms3.S
```

#### 3.4.2.7 vmlinux

在顶层Makefile中，包含如下规则：

```
vmlinux: $(vmlinux-lds) $(vmlinux-init) $(vmlinux-main) vmlinux.o $(kallsyms.o) FORCE

/* 参见make -f Makefile headers_check节 */
ifdef CONFIG_HEADERS_CHECK
	$(Q)$(MAKE) -f $(srctree)/Makefile headers_check
endif

/*
 * 参见make -f scripts/Makefile.build obj=samples节，
 * 编译samples目录，内核编程示例
 */
ifdef CONFIG_SAMPLES
	$(Q)$(MAKE) $(build)=samples
endif

/*
 * 参见make -f scripts/Makefile.build obj=Documentation节，
 * 编译Documentation目录
 */
ifdef CONFIG_BUILD_DOCSRC
	$(Q)$(MAKE) $(build)=Documentation
endif
	// 未定义函数vmlinux-modpost，忽略
	$(call vmlinux-modpost)
	/*
	 * 调用rule_vmlinux__链接vmlinux，并
	 * 生成System.map，参见rule_vmlinux__节
	 */
	$(call if_changed_rule,vmlinux__)
	$(Q)rm -f .old_version
```

##### 3.4.2.7.1 make -f Makefile headers_check

在顶层Makefile中，包含如下规则：

```
# Where to locate arch specific headers
hdr-arch	:= $(SRCARCH)		// 此处以x86体系为例

hdr-inst	:= -rR -f $(srctree)/scripts/Makefile.headersinst obj

# If we do an all arch process set dst to asm-$(hdr-arch)
hdr-dst	= $(if $(KBUILD_HEADERS), dst=include/asm-$(hdr-arch), dst=include/asm)

PHONY += __headers
// 参见include/linux/version.h，scripts_basic和asm-generic节
__headers: include/linux/version.h scripts_basic asm-generic FORCE
	// 编译scripts/unifdef
	$(Q)$(MAKE) $(build)=scripts build_unifdef

PHONY += headers_install
headers_install: __headers
	$(if $(wildcard $(srctree)/arch/$(hdr-arch)/include/asm/Kbuild),, \
	$(error Headers not exportable for the $(SRCARCH) architecture))
	// 扩展为make -rR -f $(srctree)/scripts/Makefile.headersinst obj=include
	$(Q)$(MAKE) $(hdr-inst)=include
	// 扩展为make -rR -f $(srctree)/scripts/Makefile.headersinst obj=arch/x86/include/asm $(hdr-dst)
	$(Q)$(MAKE) $(hdr-inst)=arch/$(hdr-arch)/include/asm $(hdr-dst)

PHONY += headers_check
headers_check: headers_install
	// 扩展为make -rR -f $(srctree)/scripts/Makefile.headersinst obj=include  HDRCHECK=1
	$(Q)$(MAKE) $(hdr-inst)=include HDRCHECK=1
	// 扩展为make -rR -f $(srctree)/scripts/Makefile.headersinst obj=arch/x86/include/asm $(hdr-dst) HDRCHECK=1
	$(Q)$(MAKE) $(hdr-inst)=arch/$(hdr-arch)/include/asm $(hdr-dst) HDRCHECK=1
```

##### 3.4.2.7.2 make -f scripts/Makefile.build obj=samples

参见[3.4.2.1.3.1 make -f scripts/Makefile.build obj=XXX命令的执行过程](#3-4-2-1-3-1-make-f-scripts-makefile-build-obj-xxx-)节。samples目录包含内核编程的示例。

##### 3.4.2.7.3 make -f scripts/Makefile.build obj=Documentation

参见[3.4.2.1.3.1 make -f scripts/Makefile.build obj=XXX命令的执行过程](#3-4-2-1-3-1-make-f-scripts-makefile-build-obj-xxx-)节。

##### 3.4.2.7.4 rule_vmlinux__

在顶层Makefile中，包含如下规则：

```
# Generate System.map
quiet_cmd_sysmap = SYSMAP
      cmd_sysmap = $(CONFIG_SHELL) $(srctree)/scripts/mksysmap

# Link of vmlinux
# If CONFIG_KALLSYMS is set .version is already updated
# Generate System.map and verify that the content is consistent
# Use + in front of the vmlinux_version rule to silent warning with make -j2
# First command is ':' to allow us to use + in front of the rule
define rule_vmlinux__
	:
	// 调用cmd_vmlinux_version，参见cmd_vmlinux_version节
	$(if $(CONFIG_KALLSYMS),,+$(call cmd,vmlinux_version))
	// 调用cmd_vmlinux__链接vmlinux，参见cmd_vmlinux__节
	$(call cmd,vmlinux__)
	// 生成命令文件./.vmlinux.cmd
	$(Q)echo 'cmd_$@ := $(cmd_vmlinux__)' > $(@D)/.$(@F).cmd
	$(Q)$(if $($(quiet)cmd_sysmap),						\
	  echo '  $($(quiet)cmd_sysmap)  System.map' &&)			\
	// 用于提取vmlinux中的符号，并保存于System.map
	$(cmd_sysmap) $@ System.map;						\
	if [ $$? -ne 0 ]; then							\
		rm -f $@;							\
		/bin/false;							\
	fi;
	$(verify_kallsyms)
endef

...
define verify_kallsyms
	$(Q)$(if $($(quiet)cmd_sysmap),						\
	  echo '  $($(quiet)cmd_sysmap)  .tmp_System.map' &&)			\
	  /*
	   * 提取.tmp_vmlinux2或.tmp_vmlinux3中的符号
	   * (参见$(kallsyms.o)节和cmd_vmlinux__节)，
	   * 并保存于.tmp_System.map
	   */
	  $(cmd_sysmap) .tmp_vmlinux$(last_kallsyms) .tmp_System.map
	/*
	 * 比较System.map和.tmp_System.map，
	 * 应该相同；若不同，则打印错误信息
	 */
	$(Q)cmp -s System.map .tmp_System.map ||				\
		(echo Inconsistent kallsyms data;				\
		 echo This is a bug - please report about it;			\
		 echo Try "make KALLSYMS_EXTRA_PASS=1" as a workaround;		\
		 rm .tmp_kallsyms* ; /bin/false )
endef
```

本命令的输出为vmlinux和System.map，其中：

* vmlinux用来生成bzImage，参见[3.4.2.8.5.1 $(obj)/piggy.o](#3-4-2-8-5-1-obj-piggy-o)节；
* System.map被安装到/boot/System.map-3.2.0(通过执行命令make install)，参见[编译内核](#)节。

查看vmlinux的文件属性：

```
chenwx@chenwx /usr/src/linux $ file vmlinux
vmlinux: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), statically linked, BuildID[sha1]=0xb14c81a12cca7144a29770565166fe7f8b1748d7, not stripped
```

#### 3.4.2.8 bzImage

在arch/x86/Makefile中，包含如下规则：

```
boot := arch/x86/boot

# Default kernel to build
all: bzImage

# KBUILD_IMAGE specify target image being built
KBUILD_IMAGE := $(boot)/bzImage

bzImage: vmlinux
ifeq ($(CONFIG_X86_DECODER_SELFTEST),y)
	// 扩展为make -f scripts/Makefile.build obj=arch/x86/tools posttest
	$(Q)$(MAKE) $(build)=arch/x86/tools posttest
endif
	// 扩展为make -f scripts/Makefile.build obj=arch/x86/boot arch/x86/boot/bzImage
	$(Q)$(MAKE) $(build)=$(boot) $(KBUILD_IMAGE)
	// 扩展为mkdir -p /usr/src/linux-3.2/arch/i386/boot
	$(Q)mkdir -p $(objtree)/arch/$(UTS_MACHINE)/boot
	/*
	 * 扩展为ln -fsn ../../x86/boot/bzImage /usr/src/linux-3.2/arch/i386/boot/bzImage
	 * 即arch/i386/boot/目录中的bzImage为符号链接，指向arch/x86/boot/bzImage
	 */
	$(Q)ln -fsn ../../x86/boot/bzImage $(objtree)/arch/$(UTS_MACHINE)/boot/$@
```

编译bzImage的命令为：

```
$(Q)$(MAKE) $(build)=$(boot) $(KBUILD_IMAGE)
```

该命令被扩展为：

```
make -f scripts/Makefile.build obj=arch/x86/boot arch/x86/boot/bzImage
```

该命令调用arch/x86/boot/Makefile，并编译目标arch/x86/boot/bzImage。在arch/x86/boot/Makefile中，包含如下规则：

```
/*
 * (1) 参见3.4.2.8.1 $(src)/setup.ld节，
 *     3.4.2.8.2 $(SETUP_OBJS)节，3.4.2.8.3 $(obj)/setup.elf节
 */
LDFLAGS_setup.elf	:= -T
$(obj)/setup.elf: $(src)/setup.ld $(SETUP_OBJS) FORCE
	$(call if_changed,ld)

// (2) 参见3.4.2.8.4 $(obj)/setup.bin节
OBJCOPYFLAGS_setup.bin	:= -O binary
$(obj)/setup.bin: $(obj)/setup.elf FORCE
	$(call if_changed,objcopy)

// (3) 参见3.4.2.8.5 $(obj)/compressed/vmlinux节
$(obj)/compressed/vmlinux: FORCE
	$(Q)$(MAKE) $(build)=$(obj)/compressed $@

// (4) 参见3.4.2.8.6 $(obj)/vmlinux.bin节
OBJCOPYFLAGS_vmlinux.bin := -O binary -R .note -R .comment -S
$(obj)/vmlinux.bin: $(obj)/compressed/vmlinux FORCE
	$(call if_changed,objcopy)

// (5) 参见3.4.2.8.8 arch/x86/boot/bzImage节
$(obj)/bzImage: $(obj)/setup.bin $(obj)/vmlinux.bin $(obj)/tools/build FORCE
	$(call if_changed,image)
	@echo 'Kernel: $@ is ready' ' (#'`cat .version`')'
```

各目标之间的依赖关系，参见[Appendix I: Targets Tree](#appendix-i-targets-tree)。

bzImage的生成过程：

![bzImage](/assets/bzImage.jpg)

![bzImage_1](/assets/bzImage_1.png)

##### 3.4.2.8.1 $(src)/setup.ld

arch/x86/boot/setup.ld是GNU ld的Linker script文件，与$(vmlinux.lds)类似，参见[3.4.2.2 $(vmlinux-lds)](#3-4-2-2-vmlinux-lds-)节。

##### 3.4.2.8.2 $(SETUP_OBJS)

该变量定义于arch/x86/boot/Makefile:

```
setup-y		+= a20.o bioscall.o cmdline.o copy.o cpu.o cpucheck.o
setup-y		+= early_serial_console.o edd.o header.o main.o mca.o memory.o
setup-y		+= pm.o pmjump.o printf.o regs.o string.o tty.o video.o
setup-y		+= video-mode.o version.o
setup-$(CONFIG_X86_APM_BOOT) += apm.o

# The link order of the video-*.o modules can matter.  In particular,
# video-vga.o *must* be listed first, followed by video-vesa.o.
# Hardware-specific drivers should follow in the order they should be
# probed, and video-bios.o should typically be last.
setup-y		+= video-vga.o
setup-y		+= video-vesa.o
setup-y		+= video-bios.o

...
SETUP_OBJS = $(addprefix $(obj)/,$(setup-y))
```

##### 3.4.2.8.3 $(obj)/setup.elf

在arch/x86/boot/Makefile中，包含如下规则：

```
LDFLAGS_setup.elf	:= -T
$(obj)/setup.elf: $(src)/setup.ld $(SETUP_OBJS) FORCE
	$(call if_changed,ld)
```

调用命令cmd_ld将$(SETUP_OBJS)中的文件连接生成arch/x86/boot/setup.elf。命令cmd_ld定义于scripts/Makefile.lib，被扩展为：

```
ld -m elf_i386   -T arch/x86/boot/setup.ld arch/x86/boot/a20.o arch/x86/boot/bioscall.o arch/x86/boot/cmdline.o arch/x86/boot/copy.o arch/x86/boot/cpu.o arch/x86/boot/cpucheck.o arch/x86/boot/early_serial_console.o arch/x86/boot/edd.o arch/x86/boot/header.o arch/x86/boot/main.o arch/x86/boot/mca.o arch/x86/boot/memory.o arch/x86/boot/pm.o arch/x86/boot/pmjump.o arch/x86/boot/printf.o arch/x86/boot/regs.o arch/x86/boot/string.o arch/x86/boot/tty.o arch/x86/boot/video.o arch/x86/boot/video-mode.o arch/x86/boot/version.o arch/x86/boot/apm.o arch/x86/boot/video-vga.o arch/x86/boot/video-vesa.o arch/x86/boot/video-bios.o -o arch/x86/boot/setup.elf
```

##### 3.4.2.8.4 $(obj)/setup.bin

在arch/x86/boot/Makefile中，包含如下规则：

```
OBJCOPYFLAGS_setup.bin	:= -O binary
$(obj)/setup.bin: $(obj)/setup.elf FORCE
	$(call if_changed,objcopy)
```

调用命令cmd_objcopy由arch/x86/boot/setup.elf生成arch/x86/boot/setup.bin。命令cmd_objcopy定义于scripts/Makefile.lib，被扩展为：

```
objcopy  -O binary arch/x86/boot/setup.elf arch/x86/boot/setup.bin
```

That's write the output file **setup.bin** from input file **setup.elf** using the object format binary.

##### 3.4.2.8.5 $(obj)/compressed/vmlinux

在arch/x86/boot/Makefile中，包含如下规则：

```
$(obj)/compressed/vmlinux: FORCE
	$(Q)$(MAKE) $(build)=$(obj)/compressed $@
```

执行下列命令编译arch/x86/boot/compressed目录：

```
$(Q)$(MAKE) $(build)=$(obj)/compressed $@
```

该命令被扩展为：

```
make -f scripts/Makefile.build obj=arch/x86/boot/compressed arch/x86/boot/compressed/vmlinux
```

该命令调用arch/x86/boot/compressed/Makefile，其中包含如下规则：

```
$(obj)/vmlinux: $(obj)/vmlinux.lds $(obj)/head_$(BITS).o $(obj)/misc.o $(obj)/string.o $(obj)/cmdline.o $(obj)/early_serial_console.o $(obj)/piggy.o FORCE
	$(call if_changed,ld)
	@:
```

调用命令cmd_ld生成arch/x86/boot/compressed/vmlinux。命令cmd_ld定义于scripts/Makefile.lib，被扩展为：

```
ld -m elf_i386   -T arch/x86/boot/compressed/vmlinux.lds arch/x86/boot/compressed/head_32.o arch/x86/boot/compressed/misc.o arch/x86/boot/compressed/string.o arch/x86/boot/compressed/cmdline.o arch/x86/boot/compressed/early_serial_console.o arch/x86/boot/compressed/piggy.o -o arch/x86/boot/compressed/vmlinux
```

输出为arch/x86/boot/compressed/vmlinux，该压缩内核文件大小约为1.6M，要比/usr/src/linux-3.2/vmlinux (参见rule_vmlinux__节，其大小约46M) 小很多。

查看arch/x86/boot/compressed/vmlinux的文件属性：

```
chenwx@chenwx /usr/src/linux $ file arch/x86/boot/compressed/vmlinux
arch/x86/boot/compressed/vmlinux: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), statically linked, not stripped
```

arch/x86/boot/compressed/vmlinux所依赖的各目标文件分别由如下文件生成：

```
arch/x86/boot/compressed/vmlinux.lds			<= arch/x86/boot/compressed/vmlinux.lds.S
arch/x86/boot/compressed/head_32.o			<= arch/x86/boot/compressed/head_32.S
arch/x86/boot/compressed/head_64.o			<= arch/x86/boot/compressed/head_64.S
arch/x86/boot/compressed/misc.o				<= arch/x86/boot/compressed/misc.c, misc.h
arch/x86/boot/compressed/string.o			<= arch/x86/boot/compressed/string.c
arch/x86/boot/compressed/cmdline.o			<= arch/x86/boot/compressed/cmdline.c
arch/x86/boot/compressed/early_serial_console.o		<= arch/x86/boot/compressed/early_serial_console.c
arch/x86/boot/compressed/piggy.o			<= arch/x86/boot/compressed/piggy.S，参见$(obj)/piggy.o节
```

###### 3.4.2.8.5.1 $(obj)/piggy.o

在arch/x86/boot/compressed/Makefile中，包含如下规则：

```
// (1) 参见3.4.2.8.5.1.1 $(obj)/vmlinux.bin节
OBJCOPYFLAGS_vmlinux.bin :=  -R .comment -S
$(obj)/vmlinux.bin: vmlinux FORCE
	$(call if_changed,objcopy)

// (2) 参见3.4.2.8.5.1.2 $(obj)/vmlinux.bin.gz节
vmlinux.bin.all-y := $(obj)/vmlinux.bin
$(obj)/vmlinux.bin.gz: $(vmlinux.bin.all-y) FORCE
	$(call if_changed,gzip)

/*
 * (3) 参见3.4.2.8.5.1.3 $(obj)/mkpiggy节，
 *     3.4.2.8.5.1.4 $(obj)/piggy.S节，
 *     3.4.2.8.5.1.5 $(obj)/piggy.o节
 */
suffix-$(CONFIG_KERNEL_GZIP)	:= gz

quiet_cmd_mkpiggy = MKPIGGY $@
      cmd_mkpiggy = $(obj)/mkpiggy $< > $@ || ( rm -f $@ ; false )

$(obj)/piggy.S: $(obj)/vmlinux.bin.$(suffix-y) $(obj)/mkpiggy FORCE
	$(call if_changed,mkpiggy)
```

arch/x86/boot/compressed/piggy.o是由arch/x86/boot/compressed/piggy.S编译而来的，各目标之间的依赖关系参见[Appendix I: Targets Tree](#appendix-i-targets-tree)。

###### 3.4.2.8.5.1.1 $(obj)/vmlinux.bin

在arch/x86/boot/compressed/Makefile中，包含如下规则：

```
OBJCOPYFLAGS_vmlinux.bin :=  -R .comment -S
$(obj)/vmlinux.bin: vmlinux FORCE
	$(call if_changed,objcopy)
```

调用命令cmd_objcopy生成arch/x86/boot/compressed/vmlinux.bin。命令cmd_objcopy定义于scripts/Makefile.lib，被扩展为：

```
objcopy  -R .comment -S vmlinux arch/x86/boot/compressed/vmlinux.bin
```

其中，参数的含义如下：

```
-R sectionname: Remove any section named sectionname from the output file.
-S/--strip-all: Do not copy relocation and symbol information from the source file.
```

vmlinux为linux-3.2/vmlinux，参见[3.4.2.7 vmlinux](#3-4-2-7-vmlinux)节。

###### 3.4.2.8.5.1.2 $(obj)/vmlinux.bin.gz

在arch/x86/boot/compressed/Makefile中，包含如下规则：

```
// 扩展为arch/x86/boot/compressed/vmlinux.bin
vmlinux.bin.all-y := $(obj)/vmlinux.bin

$(obj)/vmlinux.bin.gz: $(vmlinux.bin.all-y) FORCE
	$(call if_changed,gzip)
```

调用命令cmd_gzip将arch/x86/boot/compressed/vmlinux.bin压缩成arch/x86/boot/compressed/vmlinux.bin.gz。命令cmd_gzip定义于scripts/Makefile.lib，被扩展为：

```
(cat arch/x86/boot/compressed/vmlinux.bin | gzip -n -f -9 > arch/x86/boot/compressed/vmlinux.bin.gz) || (rm -f arch/x86/boot/compressed/vmlinux.bin.gz ; false)
```

###### 3.4.2.8.5.1.3 $(obj)/mkpiggy

```
arch/x86/boot/compressed/mkpiggy		<= arch/x86/boot/compressed/mkpiggy.c
```

where, the executable mkpiggy is used when creating arch/x86/boot/compressed/piggy.S, see [3.4.2.8.5.1.4 $(obj)/piggy.S](#3-4-2-8-5-1-4-obj-piggy-s).

###### 3.4.2.8.5.1.4 $(obj)/piggy.S

在arch/x86/boot/compressed/Makefile中，包含如下规则：

```
suffix-$(CONFIG_KERNEL_GZIP)	:= gz

...
quiet_cmd_mkpiggy = MKPIGGY $@
      cmd_mkpiggy = $(obj)/mkpiggy $< > $@ || ( rm -f $@ ; false )

$(obj)/piggy.S: $(obj)/vmlinux.bin.$(suffix-y) $(obj)/mkpiggy FORCE
	$(call if_changed,mkpiggy)
```

调用命令cmd_mkpiggy生成arch/x86/boot/compressed/piggy.S，该命令被扩展为：

```
arch/x86/boot/compressed/mkpiggy arch/x86/boot/compressed/vmlinux.bin.gz > arch/x86/boot/compressed/piggy.S || ( rm -f arch/x86/boot/compressed/piggy.S ; false )
```

由arch/x86/boot/compressed/mkpiggy.c可知，上述命令将arch/x86/boot/compressed/vmlinux.bin.gz添加到 arch/x86/boot/compressed/piggy.S中：

```
/*
 * 此代码被放到.rodata..compressed段中，对该段的连接参见:
 * 3.4.2.8.5.2 arch/x86/boot/compressed/vmlinux.lds
 * 下列变量用于解压二进制文件vmlinux.bin.gz:
 * z_input_len, z_extract_offset, input_data
 * 参见4.3.4.1.3 arch/x86/boot/compressed/head_32.S节
 */
.section .rodata..compressed,"a",@progbits
.globl z_input_len
z_input_len = <ilen>
.globl z_output_len
z_output_len = <olen>
.globl z_extract_offset
z_extract_offset = <offs>
/* z_extract_offset_negative allows simplification of head_32.S */
.globl z_extract_offset_negative
z_extract_offset_negative = <offs>
.globl z_run_size
z_run_size = <run_size>

.globl input_data, input_data_end
input_data:
// 将二进制文件vmlinux.bin.gz包含到这里
.incbin arch/x86/boot/compressed/vmlinux.bin.gz
input_data_end:

NOTE: How to embed a binary in your executable?
Method #1: Covert the binary to the "hex" text, and #include
(binary_file.hex)
0xeb, 0xfe, 0x90, 0x90, ...

(C file)
unsigned char binary[] = {
    #include "binary_file.hex"
};

Method #2: Use ".incbin" mnemonic in the assembler
.section .rodata
.global input_data, input_data_end
input_data:
    .incbin "binary_file.bin"
input_data_end:
```

Obviously, mkpiggy uses the Method #2 to generate arch/x86/boot/compressed/piggy.S

###### 3.4.2.8.5.1.5 $(obj)/piggy.o

在scripts/Makefile.build中，包含如下规则：

```
quiet_cmd_as_o_S = AS $(quiet_modtag)  $@
cmd_as_o_S       = $(CC) $(a_flags) -c -o $@ $<

$(obj)/%.o: $(src)/%.S FORCE
	$(call if_changed_dep,as_o_S)
```

调用命令cmd_as_o_S将arch/x86/boot/compressed/piggy.S编译成arch/x86/boot/compressed/piggy.o。命令cmd_as_o_S被扩展为：

```
gcc -Wp,-MD,arch/x86/boot/compressed/.piggy.o.d  -nostdinc -isystem /usr/lib/gcc/i686-linux-gnu/4.7/include -I/usr/src/linux-3.2/arch/x86/include -Iarch/x86/include/generated -Iinclude  -include /usr/src/linux-3.2/include/linux/kconfig.h -D__KERNEL__ -m32 -D__KERNEL__  -O2 -fno-strict-aliasing -fPIC -DDISABLE_BRANCH_PROFILING -march=i386 -ffreestanding -fno-stack-protector -D__ASSEMBLY__         -c -o arch/x86/boot/compressed/piggy.o arch/x86/boot/compressed/piggy.S
```

That's, make an object (arch/x86/boot/compressed/piggy.o) that contains the compressed image (arch/x86/boot/compressed/vmlinux.bin.gz ), see 3.4.2.8.5.1.4 $(obj)/piggy.S.

###### 3.4.2.8.5.2 arch/x86/boot/compressed/vmlinux.lds

该文件包含如下内容:

```
#include <asm-generic/vmlinux.lds.h>

OUTPUT_FORMAT(CONFIG_OUTPUT_FORMAT, CONFIG_OUTPUT_FORMAT, CONFIG_OUTPUT_FORMAT)

#undef i386

#include <asm/cache.h>
#include <asm/page_types.h>

#ifdef CONFIG_X86_64
OUTPUT_ARCH(i386:x86-64)
ENTRY(startup_64)
#else
OUTPUT_ARCH(i386)
ENTRY(startup_32)
#endif

SECTIONS
{
	/* Be careful parts of head_64.S assume startup_32 is at
	 * address 0.
	 */
	. = 0;
	.head.text : {
		_head = . ;
		// 包含arch/x86/boot/compressed/head_32.S中的代码
		HEAD_TEXT
		_ehead = . ;
	}
	.rodata..compressed : {
		/*
		 * 包含arch/x86/boot/compressed/piggy.S中的代码，
		 * 参见3.4.2.8.5.1.4 $(obj)/piggy.S节
		 */
		*(.rodata..compressed)
	}
	.text :	{
		_text = .; 	/* Text */
		*(.text)
		*(.text.*)
		_etext = . ;
	}
	.rodata : {
		_rodata = . ;
		*(.rodata)	 /* read-only data */
		*(.rodata.*)
		_erodata = . ;
	}
	.got : {
		_got = .;
		KEEP(*(.got.plt))
		KEEP(*(.got))
		_egot = .;
	}
	.data :	{
		_data = . ;
		*(.data)
		*(.data.*)
		_edata = . ;
	}
	. = ALIGN(L1_CACHE_BYTES);
	.bss : {
		_bss = . ;
		*(.bss)
		*(.bss.*)
		*(COMMON)
		. = ALIGN(8);	/* For convenience during zeroing */
		_ebss = .;
	}
#ifdef CONFIG_X86_64
       . = ALIGN(PAGE_SIZE);
       .pgtable : {
		_pgtable = . ;
		*(.pgtable)
		_epgtable = . ;
	}
#endif
	_end = .;
}
```

##### 3.4.2.8.6 $(obj)/vmlinux.bin

在arch/x86/boot/Makefile中，包含如下规则：

```
OBJCOPYFLAGS_vmlinux.bin := -O binary -R .note -R .comment -S
$(obj)/vmlinux.bin: $(obj)/compressed/vmlinux FORCE
	$(call if_changed,objcopy)
```

调用命令cmd_objcopy由arch/x86/boot/compressed/vmlinux 生成arch/x86/boot/vmlinux.bin。命令cmd_objcopy定义于scripts/Makefile.lib，被扩展为：

```
objcopy  -O binary -R .note -R .comment -S arch/x86/boot/compressed/vmlinux arch/x86/boot/vmlinux.bin
```

其中，参数的含义如下：

```
-R sectionname: Remove any section named sectionname from the output file.
-S/--strip-all: Do not copy relocation and symbol information from the source file.
```

##### 3.4.2.8.7 $(obj)/tools/build

```
arch/x86/boot/tools/build  <=  arch/x86/boot/tools/build.c
```

arch/x86/boot/tools/build的用法参见[3.4.2.8.8 arch/x86/boot/bzImage](#3-4-2-8-8-arch-x86-boot-bzimage)节。

##### 3.4.2.8.8 arch/x86/boot/bzImage

在arch/x86/boot/Makefile中，包含如下规则：

```
$(obj)/bzImage: $(obj)/setup.bin $(obj)/vmlinux.bin $(obj)/tools/build FORCE
	/*
	 * 调用cmd_image，由arch/x86/boot/setup.bin和
	 * arch/x86/boot/vmlinux.bin生成arch/x86/boot/bzImage
	 */
	$(call if_changed,image)
	// 打印 Kernel: arch/x86/boot/bzImage is ready  (#1)
	@echo 'Kernel: $@ is ready' ' (#'`cat .version`')'

...
quiet_cmd_image = BUILD   $@
cmd_image = $(obj)/tools/build $(obj)/setup.bin $(obj)/vmlinux.bin > $@
```

调用命令cmd_image由arch/x86/boot/setup.bin和arch/x86/boot/vmlinux.bin生成arch/x86/boot/bzImage，该命令被扩展为：

```
arch/x86/boot/tools/build arch/x86/boot/setup.bin arch/x86/boot/vmlinux.bin > arch/x86/boot/bzImage
```

查看arch/x86/boot/bzImage的文件属性：

```
chenwx@chenwx /usr/src/linux $ file arch/x86/boot/bzImage
arch/x86/boot/bzImage: Linux kernel x86 boot executable bzImage, version 3.2.0 (chenwx@chenwx) #1 SMP Tue Feb 19 23:35:53 EET 2013, RO-rootFS, swap_dev 0x2, Normal VGA
```

### 3.4.3 编译modules/$(obj-m)

运行make modules命令(或者make命令)，执行顶层Makefile中的modules目标：

```
KBUILD_AFLAGS_MODULE  := -DMODULE
KBUILD_CFLAGS_MODULE  := -DMODULE
KBUILD_LDFLAGS_MODULE := -T $(srctree)/scripts/module-common.lds

...
export KBUILD_AFLAGS_MODULE KBUILD_CFLAGS_MODULE KBUILD_LDFLAGS_MODULE

...
/*
 * 由此可知，必须满足CONFIG_MODULES=y才能编译modules；
 * 而CONFIG_MODULES根据init/Kconfig中的如下配置项生成:
 * 		menuconfig MODULES
 *  			bool "Enable loadable module support"
 */
ifdef CONFIG_MODULES

all: modules

PHONY += modules
modules: $(vmlinux-dirs) $(if $(KBUILD_BUILTIN),vmlinux) modules.builtin
	$(Q)$(AWK) '!x[$$0]++' $(vmlinux-dirs:%=$(objtree)/%/modules.order) > $(objtree)/modules.order
	@$(kecho) '  Building modules, stage 2.';
	$(Q)$(MAKE) -f $(srctree)/scripts/Makefile.modpost
	$(Q)$(MAKE) -f $(srctree)/scripts/Makefile.fwinst obj=firmware __fw_modbuild

modules.builtin: $(vmlinux-dirs:%=%/modules.builtin)
	$(Q)$(AWK) '!x[$$0]++' $^ > $(objtree)/modules.builtin

%/modules.builtin: include/config/auto.conf
	$(Q)$(MAKE) $(modbuiltin)=$*

...
else # CONFIG_MODULES

# Modules not configured
# ---------------------------------------------------------------------------

modules modules_install: FORCE
	@echo
	@echo "The present kernel configuration has modules disabled."
	@echo "Type 'make config' and enable loadable module support."
	@echo "Then build a kernel with module support enabled."
	@echo
	@exit 1

endif # CONFIG_MODULES
```

#### 3.4.3.1 $(vmlinux-dirs)

参见[3.4.2.1 $(vmlinux-dirs)](#3-4-2-1-vmlinux-dirs-)节。

#### 3.4.3.2 vmlinux

在顶层Makefile中，包含如下规则：

```
KBUILD_BUILTIN := 1

#	If we have only "make modules", don't compile built-in objects.
#	When we're building modules with modversions, we need to consider
#	the built-in objects during the descend as well, in order to
#	make sure the checksums are up to date before we record them.

// 执行make modules时，满足条件，进入本分支；仅执行make时，不进入本分支
ifeq ($(MAKECMDGOALS),modules)
  // 参见linux-3.2/Documentation/kbuild/modules.txt第6节
  KBUILD_BUILTIN := $(if $(CONFIG_MODVERSIONS),1)
endif
```

若配置了CONFIG_MODVERSIONS，则modules依赖于vmlinux，其执行过程参见[3.4.2.7 vmlinux](#3-4-2-7-vmlinux)节；否则，modules不依赖于vmlinux。

#### 3.4.3.3 modules.builtin

modules.builtin的含义：

> This file lists all modules that are built into the kernel. This is used by modprobe to not fail when trying to load something builtin.

在顶层Makefile中，包含如下规则：

```
ifdef CONFIG_MODULES

...
modules.builtin: $(vmlinux-dirs:%=%/modules.builtin)
	$(Q)$(AWK) '!x[$$0]++' $^ > $(objtree)/modules.builtin

%/modules.builtin: include/config/auto.conf
	$(Q)$(MAKE) $(modbuiltin)=$*

...
else # CONFIG_MODULES
...
endif # CONFIG_MODULES
```

由$(vmlinux-dirs)节可知，$(vmlinux-dirs:%=%/modules.builtin)被扩展为：

```
init/modules.builtin usr/modules.builtin arch/x86/modules.builtin kernel/modules.builtin mm/modules.builtin fs/modules.builtin ipc/modules.builtin security/modules.builtin crypto/modules.builtin block/modules.builtin drivers/modules.builtin sound/modules.builtin firmware/modules.builtin net/modules.builtin lib/modules.builtin arch/x86/lib/modules.builtin
```

该列表匹配规则：

```
// include/config/auto.conf参见.config如何被顶层Makefile调用节和include/config/auto.conf节
%/modules.builtin: include/config/auto.conf
	$(Q)$(MAKE) $(modbuiltin)=$*
```

因而执行下列命令：

```
$(Q)$(MAKE) $(modbuiltin)=$*
```

其中，$(modbuiltin)定义于scripts/Kbuild.include：

```
modbuiltin := -f $(if $(KBUILD_SRC),$(srctree)/)scripts/Makefile.modbuiltin obj
```

故该命令分别被扩展为：

```
make -f scripts/Makefile.modbuiltin obj=$*
```

其执行过程参见[3.4.3.3.1 make -f scripts/Makefile.modbuiltin obj=$*](#3-4-3-3-1-make-f-scripts-makefile-modbuiltin-obj-)节。

##### 3.4.3.3.1 make -f scripts/Makefile.modbuiltin obj=$*

由modules.builtin节可知，make -f scripts/Makefile.modbuiltin obj=$* 被扩展为：

```
make -f scripts/Makefile.modbuiltin obj=init
make -f scripts/Makefile.modbuiltin obj=usr
make -f scripts/Makefile.modbuiltin obj=arch/x86
make -f scripts/Makefile.modbuiltin obj=kernel
make -f scripts/Makefile.modbuiltin obj=mm
make -f scripts/Makefile.modbuiltin obj=fs
make -f scripts/Makefile.modbuiltin obj=ipc
make -f scripts/Makefile.modbuiltin obj=security
make -f scripts/Makefile.modbuiltin obj=crypto
make -f scripts/Makefile.modbuiltin obj=block
make -f scripts/Makefile.modbuiltin obj=drivers
make -f scripts/Makefile.modbuiltin obj=sound
make -f scripts/Makefile.modbuiltin obj=firmware
make -f scripts/Makefile.modbuiltin obj=net
make -f scripts/Makefile.modbuiltin obj=lib
make -f scripts/Makefile.modbuiltin obj=arch/x86/lib/
```

当执行这些命令时，如果这些目录下存在子目录，则make会递归调用其子目录下的Kbuild或Makefile(若不存在Kbuild文件)，详细的命令调用列表参见[Appendix D: make -f scripts/Makefile.modbuiltin obj=列表](#appendix-d-make-f-scripts-makefile-modbuiltin-obj-)节。

由于这些命令未指明编译目标，故编译scripts/Makefile.modbuiltin的默认目标__modbuiltin:

```
src := $(obj)

PHONY := __modbuiltin
__modbuiltin:

modbuiltin-target  := $(obj)/modules.builtin

__modbuiltin: $(modbuiltin-target) $(subdir-ym)
	@:

$(modbuiltin-target): $(subdir-ym) FORCE
	$(Q)(for m in $(modbuiltin-mods); do echo kernel/$$m; done;	\
	cat /dev/null $(modbuiltin-subdirs)) > $@

...
$(subdir-ym):
	$(Q)$(MAKE) $(modbuiltin)=$@
```

###### 3.4.3.3.1.1 $(subdir-ym)

在scripts/Makefile.modbuiltin中，包含如下规则：

```
// auto.conf中的所有配置项格式为CONFIG_xxx=y或m，均为小写，故可得到obj-y和obj-m列表
-include include/config/auto.conf
# tristate.conf sets tristate variables to uppercase 'Y' or 'M'
# That way, we get the list of built-in modules in obj-Y
// tristate.conf中的所有配置项格式为CONFIG_xxx=Y或M，均为大写，故可得到obj-Y或obj-M列表
-include include/config/tristate.conf

...
include scripts/Makefile.lib
__subdir-Y	:= $(patsubst %/,%,$(filter %/, $(obj-Y)))
subdir-Y	+= $(__subdir-Y)
subdir-ym	:= $(sort $(subdir-y) $(subdir-Y) $(subdir-m))
subdir-ym	:= $(addprefix $(obj)/,$(subdir-ym))

...
$(subdir-ym):
	$(Q)$(MAKE) $(modbuiltin)=$@
```

$(subdir-ym)的取值与如下三部分有关：

* $(subdir-y)，参见scripts/Makefile.lib
* $(subdir-m)，参见scripts/Makefile.lib
* $(subdir-Y)，与配置文件include/config/tristate.conf有关

调用命令 $(Q)$(MAKE) $(modbuiltin)=$@ 递归编译$(obj)指定目录的子目录，该命令被扩展为:

```
make -f scripts/Makefile.modbuiltin obj=$@
```

详细命令列表参见[Appendix D: make -f scripts/Makefile.modbuiltin obj=列表](#appendix-d-make-f-scripts-makefile-modbuiltin-obj-)节。

###### 3.4.3.3.1.2 $(modbuiltin-target)

在scripts/Makefile.modbuiltin中，包含如下规则：

```
modbuiltin-subdirs	:= $(patsubst %,%/modules.builtin, $(subdir-ym))
modbuiltin-mods		:= $(filter %.ko, $(obj-Y:.o=.ko))
modbuiltin-target	:= $(obj)/modules.builtin

...
$(modbuiltin-target): $(subdir-ym) FORCE
	$(Q)(for m in $(modbuiltin-mods); do echo kernel/$$m; done;	\
	cat /dev/null $(modbuiltin-subdirs)) > $@
```

该目标执行下列命令：

```
	$(Q)(for m in $(modbuiltin-mods); do echo kernel/$$m; done;	\
	cat /dev/null $(modbuiltin-subdirs)) > $@
```

以drivers/input/目录为例，该命令被扩展为：

```
(for m in drivers/input/input-core.ko drivers/input/mousedev.ko; do echo kernel/$m; done;	\
	cat /dev/null drivers/input/joystick/modules.builtin drivers/input/keyboard/modules.builtin drivers/input/misc/modules.builtin) > drivers/input/modules.builtin
```

该命令输出文件modules.builtin，用于保存对应目录及其子目录下的*.ko文件列表。

##### 3.4.3.3.2 modules.builtin

在顶层Makefile中，包含如下规则：

```
ifdef CONFIG_MODULES

...
modules.builtin: $(vmlinux-dirs:%=%/modules.builtin)
	$(Q)$(AWK) '!x[$$0]++' $^ > $(objtree)/modules.builtin

...
else # CONFIG_MODULES
...
endif # CONFIG_MODULES
```

命令：

```
$(Q)$(AWK) '!x[$$0]++' $^ > $(objtree)/modules.builtin
```

被扩展为：

```
awk '!x[$0]++' init/modules.builtin usr/modules.builtin arch/x86/modules.builtin kernel/modules.builtin mm/modules.builtin fs/modules.builtin ipc/modules.builtin security/modules.builtin crypto/modules.builtin block/modules.builtin drivers/modules.builtin sound/modules.builtin firmware/modules.builtin arch/x86/pci/modules.builtin arch/x86/power/modules.builtin arch/x86/video/modules.builtin net/modules.builtin lib/modules.builtin arch/x86/lib/modules.builtin > /usr/src/linux-3.2/modules.builtin
```

该命令将所有子目录下的modules.builtin文件(参见[3.4.3.3.1.2 $(modbuiltin-target)](#3-4-3-3-1-2-modbuiltin-target-)节)内容输出到文件linux-3.2/modules.builtin中。该文件包含make modules命令生成的所有*.ko文件列表。执行make modules_install时，将linux-3.2/modules.builtin拷贝到/lib/modules/3.2.0/modules.builtin，参见[安装内核](#3-5-5-)节。

#### 3.4.3.4 modules

在顶层Makefile中，包含如下规则：

```
ifdef CONFIG_MODULES

...
modules: $(vmlinux-dirs) $(if $(KBUILD_BUILTIN),vmlinux) modules.builtin
	// 生成linux-3.2/modules.order，参见modules.order节
	$(Q)$(AWK) '!x[$$0]++' $(vmlinux-dirs:%=$(objtree)/%/modules.order) > $(objtree)/modules.order
	@$(kecho) '  Building modules, stage 2.';
	// 参见make -f scripts/Makefile.modpost节
	$(Q)$(MAKE) -f $(srctree)/scripts/Makefile.modpost
	// 参见make -f scripts/Makefile.fwinst obj=firmware __fw_modbuild节
	$(Q)$(MAKE) -f $(srctree)/scripts/Makefile.fwinst obj=firmware __fw_modbuild

...
else # CONFIG_MODULES
...
endif # CONFIG_MODULES
```

##### 3.4.3.4.1 modules.order

命令 $(Q)$(AWK) '!x[$$0]++' $(vmlinux-dirs:%=$(objtree)/%/modules.order) > $(objtree)/modules.order 被扩展为：

```
awk '!x[$0]++' /usr/src/linux-3.2/init/modules.order /usr/src/linux-3.2/usr/modules.order /usr/src/linux-3.2/arch/x86/modules.order /usr/src/linux-3.2/kernel/modules.order /usr/src/linux-3.2/mm/modules.order /usr/src/linux-3.2/fs/modules.order /usr/src/linux-3.2/ipc/modules.order /usr/src/linux-3.2/security/modules.order /usr/src/linux-3.2/crypto/modules.order /usr/src/linux-3.2/block/modules.order /usr/src/linux-3.2/drivers/modules.order /usr/src/linux-3.2/sound/modules.order /usr/src/linux-3.2/firmware/modules.order /usr/src/linux-3.2/arch/x86/video/modules.order /usr/src/linux-3.2/net/modules.order /usr/src/linux-3.2/lib/modules.order /usr/src/linux-3.2/arch/x86/lib/modules.order > /usr/src/linux-3.2/modules.order
```

该命令将所有子目录下的modules.order文件(参见$(modorder-target)节)内容输出到文件linux-3.2/modules.order中，该文件列出了构建系统内部模块的次序。执行make modules_install时，将linux-3.2/modules.order拷贝到/lib/modules/3.2.0/modules.order，参见[安装内核](#3-5-5-)节。

##### 3.4.3.4.2 make -f scripts/Makefile.modpost

命令 $(Q)$(MAKE) -f $(srctree)/scripts/Makefile.modpost 被扩展为：

```
make -f /usr/src/linux-3.2/scripts/Makefile.modpost
```

因为该命令未指定编译目标，故编译scripts/Makefile.modpost中的默认目标_modpost：

```
PHONY := _modpost
_modpost: __modpost

/*
 * 在顶层Makefile中，MODVERDIR := $(if $(KBUILD_EXTMOD),$(firstword $(KBUILD_EXTMOD))/).tmp_versions
 * .tmp_versions目录是在目标prepare1中创建的，参见prepare1节
 * __modules用于保存.tmp_versions/*.mod文件中以dir/subdir/*.ko结尾的所有行，并按字母顺序进行排序
 */
__modules	:= $(sort $(shell grep -h '\.ko' /dev/null $(wildcard $(MODVERDIR)/*.mod)))
// modules的取值与__modules相同，均为dir/subdir/*.ko列表
modules	:= $(patsubst %.o,%.ko, $(wildcard $(__modules:.ko=.o)))

...
_modpost: $(if $(KBUILD_MODPOST_NOFINAL), $(modules:.ko:.o),$(modules))

__modpost: $(modules:.ko=.o) FORCE
	$(call cmd,modpost) $(wildcard vmlinux) $(filter-out FORCE,$^)

$(modules): %.ko :%.o %.mod.o FORCE
	$(call if_changed,ld_ko_o)

$(modules:.ko=.mod.o): %.mod.o: %.mod.c FORCE
	$(call if_changed_dep,cc_o_c)

$(modules:.ko=.mod.c): __modpost ;
```

各目标之间的依赖关系如下：

![target_modpost](/assets/target_modpost.png)

###### 3.4.3.4.2.1 \_\_modpost

在scripts/Makefile.modpost中，包含如下规则：

```
modpost = scripts/mod/modpost                    \
 $(if $(CONFIG_MODVERSIONS),-m)                  \
 $(if $(CONFIG_MODULE_SRCVERSION_ALL),-a,)       \
 $(if $(KBUILD_EXTMOD),-i,-o) $(kernelsymfile)   \
 $(if $(KBUILD_EXTMOD),-I $(modulesymfile))      \
 $(if $(KBUILD_EXTRA_SYMBOLS), $(patsubst %, -e %,$(KBUILD_EXTRA_SYMBOLS))) \
 $(if $(KBUILD_EXTMOD),-o $(modulesymfile))      \
 $(if $(CONFIG_DEBUG_SECTION_MISMATCH),,-S)      \
 $(if $(KBUILD_EXTMOD)$(KBUILD_MODPOST_WARN),-w) \
 $(if $(cross_build),-c)

quiet_cmd_modpost = MODPOST $(words $(filter-out vmlinux FORCE, $^)) modules
      cmd_modpost = $(modpost) -s

__modpost: $(modules:.ko=.o) FORCE
	$(call cmd,modpost) $(wildcard vmlinux) $(filter-out FORCE,$^)
```

$(modules:.ko=.o)是在目标$(vmlinux-dirs)中编译完成的，参见[3.4.3.1 $(vmlinux-dirs)](#3-4-3-1-vmlinux-dirs-)节。

之后，目标__modpost调用命令：

```
$(call cmd,modpost) $(wildcard vmlinux) $(filter-out FORCE,$^)
```

该命令被扩展为：

```
$(modpost) –s $(wildcard vmlinux) $(filter-out FORCE,$^)
```

继而被扩展为：

```
scripts/mod/modpost -a -o /usr/src/linux-3.2/Module.symvers -s $(modules:.ko=.o)
```

该命令会生成如下文件：

**1) /usr/src/linux-3.2/Module.symvers**

See Documentation/kbuild/modules.txt:

> Module.symvers contains a list of all exported symbols from a kernel build.

**2) *.mod.c**

以hello.c为例，生成的hello.mod.c文件如下，另参见[mod->init/mod->exit与init_module()/cleanup_module()的关联](#)节:

```
#include <linux/module.h>
#include <linux/vermagic.h>
#include <linux/compiler.h>

MODULE_INFO(vermagic, VERMAGIC_STRING);

struct module __this_module
__attribute__((section(".gnu.linkonce.this_module"))) = {
	.name = KBUILD_MODNAME,
	.init = init_module,
#ifdef CONFIG_MODULE_UNLOAD
	.exit = cleanup_module,
#endif
	.arch = MODULE_ARCH_INIT,
};

static const struct modversion_info ____versions[]
__used
__attribute__((section("__versions"))) = {
	{ 0x35ec255d, "module_layout" },
	{ 0x50eedeb8, "printk" },
	{ 0xb4390f9a, "mcount" },
};

static const char __module_depends[]
__used
__attribute__((section(".modinfo"))) =
"depends=";


MODULE_INFO(srcversion, "C8EB943C79F42BA9921FE81");
```

以drivers/net/ethernet/intel/e1000e/e1000e.c为例，编译过程中生成的e1000e.mod.c文件如下:

```
#include <linux/module.h>
#include <linux/vermagic.h>
#include <linux/compiler.h>

MODULE_INFO(vermagic, VERMAGIC_STRING);

__visible struct module __this_module
__attribute__((section(".gnu.linkonce.this_module"))) = {
	.name = KBUILD_MODNAME,
	.init = init_module,
#ifdef CONFIG_MODULE_UNLOAD
	.exit = cleanup_module,
#endif
	.arch = MODULE_ARCH_INIT,
};

MODULE_INFO(intree, "Y");

static const struct modversion_info ____versions[]
__used
__attribute__((section("__versions"))) = {
	{ 0x420178e0, __VMLINUX_SYMBOL_STR(module_layout) },
	{ 0x6860880e, __VMLINUX_SYMBOL_STR(alloc_pages_current) },
	{ 0x3ce4ca6f, __VMLINUX_SYMBOL_STR(disable_irq) },
	{ 0x2d3385d3, __VMLINUX_SYMBOL_STR(system_wq) },
	{ 0xf744ad48, __VMLINUX_SYMBOL_STR(netdev_info) },
	{ 0x8728198a, __VMLINUX_SYMBOL_STR(kmalloc_caches) },
	{ 0xd2b09ce5, __VMLINUX_SYMBOL_STR(__kmalloc) },
	{ 0x20fcaa16, __VMLINUX_SYMBOL_STR(ethtool_op_get_ts_info) },
	{ 0xe4689576, __VMLINUX_SYMBOL_STR(ktime_get_with_offset) },
	{ 0xf9a482f9, __VMLINUX_SYMBOL_STR(msleep) },
	{ 0x99840d00, __VMLINUX_SYMBOL_STR(timecounter_init) },
	{  0xeec2d, __VMLINUX_SYMBOL_STR(__pm_runtime_idle) },
	{ 0xd6ee688f, __VMLINUX_SYMBOL_STR(vmalloc) },
	{ 0x65b5fe49, __VMLINUX_SYMBOL_STR(param_ops_int) },
	{ 0x91eb9b4, __VMLINUX_SYMBOL_STR(round_jiffies) },
	{ 0xaf34e5b8, __VMLINUX_SYMBOL_STR(napi_disable) },
	{ 0x754d539c, __VMLINUX_SYMBOL_STR(strlen) },
	{ 0xdd2baf66, __VMLINUX_SYMBOL_STR(skb_pad) },
	{ 0xee2f9e23, __VMLINUX_SYMBOL_STR(dma_set_mask) },
	{ 0x30ad4c2e, __VMLINUX_SYMBOL_STR(pci_disable_device) },
	{ 0xf33ff0e, __VMLINUX_SYMBOL_STR(pci_disable_msix) },
	{ 0x4ea25709, __VMLINUX_SYMBOL_STR(dql_reset) },
	{ 0xb0d99f0c, __VMLINUX_SYMBOL_STR(netif_carrier_on) },
	{ 0xea41f64, __VMLINUX_SYMBOL_STR(pm_qos_add_request) },
	{ 0x7f13d491, __VMLINUX_SYMBOL_STR(pm_qos_remove_request) },
	{ 0xc0a3d105, __VMLINUX_SYMBOL_STR(find_next_bit) },
	{ 0x6b06fdce, __VMLINUX_SYMBOL_STR(delayed_work_timer_fn) },
	{ 0xab51580, __VMLINUX_SYMBOL_STR(netif_carrier_off) },
	{ 0x88bfa7e, __VMLINUX_SYMBOL_STR(cancel_work_sync) },
	{ 0xbd8afbb8, __VMLINUX_SYMBOL_STR(__dev_kfree_skb_any) },
	{ 0xeae3dfd6, __VMLINUX_SYMBOL_STR(__const_udelay) },
	{ 0x9580deb, __VMLINUX_SYMBOL_STR(init_timer_key) },
	{ 0xd3000832, __VMLINUX_SYMBOL_STR(pcie_capability_clear_and_set_word) },
	{ 0xa57863e, __VMLINUX_SYMBOL_STR(cancel_delayed_work_sync) },
	{ 0xe6048175, __VMLINUX_SYMBOL_STR(mutex_unlock) },
	{ 0xed21bd02, __VMLINUX_SYMBOL_STR(__pm_runtime_resume) },
	{ 0x999e8297, __VMLINUX_SYMBOL_STR(vfree) },
	{ 0x83472897, __VMLINUX_SYMBOL_STR(dma_free_attrs) },
	{ 0xbfe2cb70, __VMLINUX_SYMBOL_STR(pci_bus_write_config_word) },
	{ 0x893a01a6, __VMLINUX_SYMBOL_STR(pci_disable_link_state_locked) },
	{ 0xf4c91ed, __VMLINUX_SYMBOL_STR(ns_to_timespec) },
	{ 0xc499ae1e, __VMLINUX_SYMBOL_STR(kstrdup) },
	{ 0x7d11c268, __VMLINUX_SYMBOL_STR(jiffies) },
	{ 0x91ba0d02, __VMLINUX_SYMBOL_STR(__dynamic_netdev_dbg) },
	{ 0x3ce3bc30, __VMLINUX_SYMBOL_STR(skb_trim) },
	{ 0x1b3b6da3, __VMLINUX_SYMBOL_STR(__netdev_alloc_skb) },
	{ 0x27c33efe, __VMLINUX_SYMBOL_STR(csum_ipv6_magic) },
	{ 0xcaaacf2e, __VMLINUX_SYMBOL_STR(__pskb_pull_tail) },
	{ 0xb0e16c7, __VMLINUX_SYMBOL_STR(ptp_clock_unregister) },
	{ 0x4f8b5ddb, __VMLINUX_SYMBOL_STR(_copy_to_user) },
	{ 0x76f5966a, __VMLINUX_SYMBOL_STR(pci_set_master) },
	{ 0xee7cdf54, __VMLINUX_SYMBOL_STR(netif_schedule_queue) },
	{ 0x706d051c, __VMLINUX_SYMBOL_STR(del_timer_sync) },
	{ 0xfb578fc5, __VMLINUX_SYMBOL_STR(memset) },
	{ 0xf0df2a5f, __VMLINUX_SYMBOL_STR(pci_enable_pcie_error_reporting) },
	{ 0xe0e4f728, __VMLINUX_SYMBOL_STR(netif_tx_wake_queue) },
	{ 0x36c1f2ce, __VMLINUX_SYMBOL_STR(pci_restore_state) },
	{ 0x9c9c66a4, __VMLINUX_SYMBOL_STR(dev_err) },
	{ 0x1916e38c, __VMLINUX_SYMBOL_STR(_raw_spin_unlock_irqrestore) },
	{ 0x85467e31, __VMLINUX_SYMBOL_STR(current_task) },
	{ 0xeb784c5f, __VMLINUX_SYMBOL_STR(ethtool_op_get_link) },
	{ 0x27e1a049, __VMLINUX_SYMBOL_STR(printk) },
	{ 0xa00aca2a, __VMLINUX_SYMBOL_STR(dql_completed) },
	{ 0x4c9d28b0, __VMLINUX_SYMBOL_STR(phys_base) },
	{ 0xc39a30e2, __VMLINUX_SYMBOL_STR(free_netdev) },
	{ 0xa1c76e0a, __VMLINUX_SYMBOL_STR(_cond_resched) },
	{ 0xc7c5ae39, __VMLINUX_SYMBOL_STR(register_netdev) },
	{ 0x5792f848, __VMLINUX_SYMBOL_STR(strlcpy) },
	{ 0xe6a1061c, __VMLINUX_SYMBOL_STR(dma_alloc_attrs) },
	{ 0x16305289, __VMLINUX_SYMBOL_STR(warn_slowpath_null) },
	{ 0xfbd63449, __VMLINUX_SYMBOL_STR(__pci_enable_wake) },
	{ 0xa5bba893, __VMLINUX_SYMBOL_STR(mutex_lock) },
	{ 0x393d4de9, __VMLINUX_SYMBOL_STR(crc32_le) },
	{ 0x6d8b0e69, __VMLINUX_SYMBOL_STR(dev_close) },
	{ 0x20e7f58, __VMLINUX_SYMBOL_STR(__dev_kfree_skb_irq) },
	{ 0x16e5c2a, __VMLINUX_SYMBOL_STR(mod_timer) },
	{ 0x660735f6, __VMLINUX_SYMBOL_STR(netif_napi_add) },
	{ 0x71b0e23f, __VMLINUX_SYMBOL_STR(ptp_clock_register) },
	{ 0x2072ee9b, __VMLINUX_SYMBOL_STR(request_threaded_irq) },
	{ 0x3b803f6d, __VMLINUX_SYMBOL_STR(device_wakeup_enable) },
	{ 0xf6fd855f, __VMLINUX_SYMBOL_STR(pci_clear_master) },
	{ 0x1be08d7c, __VMLINUX_SYMBOL_STR(dev_open) },
	{ 0xe523ad75, __VMLINUX_SYMBOL_STR(synchronize_irq) },
	{ 0xc542933a, __VMLINUX_SYMBOL_STR(timecounter_read) },
	{ 0x69653fc1, __VMLINUX_SYMBOL_STR(dev_notice) },
	{ 0x167c5967, __VMLINUX_SYMBOL_STR(print_hex_dump) },
	{ 0xfef0a6f1, __VMLINUX_SYMBOL_STR(pci_select_bars) },
	{ 0xa8b76a68, __VMLINUX_SYMBOL_STR(timecounter_cyc2time) },
	{ 0xa916ca8d, __VMLINUX_SYMBOL_STR(netif_device_attach) },
	{ 0xe3c8a6d7, __VMLINUX_SYMBOL_STR(napi_gro_receive) },
	{ 0x50e8877f, __VMLINUX_SYMBOL_STR(_dev_info) },
	{ 0x40a9b349, __VMLINUX_SYMBOL_STR(vzalloc) },
	{ 0xeeb1eb27, __VMLINUX_SYMBOL_STR(pci_disable_link_state) },
	{ 0xee2754a8, __VMLINUX_SYMBOL_STR(netif_device_detach) },
	{ 0x6839ed62, __VMLINUX_SYMBOL_STR(__alloc_skb) },
	{ 0x42c8de35, __VMLINUX_SYMBOL_STR(ioremap_nocache) },
	{ 0x12a38747, __VMLINUX_SYMBOL_STR(usleep_range) },
	{ 0x7e03c231, __VMLINUX_SYMBOL_STR(pci_enable_msix_range) },
	{ 0x927a02a0, __VMLINUX_SYMBOL_STR(pci_bus_read_config_word) },
	{ 0x6c4d5fb, __VMLINUX_SYMBOL_STR(__napi_schedule) },
	{ 0x70cd1f, __VMLINUX_SYMBOL_STR(queue_delayed_work_on) },
	{ 0xb81c3712, __VMLINUX_SYMBOL_STR(pci_cleanup_aer_uncorrect_error_status) },
	{ 0x46258b48, __VMLINUX_SYMBOL_STR(pm_schedule_suspend) },
	{ 0xa89987e1, __VMLINUX_SYMBOL_STR(napi_complete_done) },
	{ 0x7478f512, __VMLINUX_SYMBOL_STR(eth_type_trans) },
	{ 0x14496b8c, __VMLINUX_SYMBOL_STR(pskb_expand_head) },
	{ 0xbdfb6dbb, __VMLINUX_SYMBOL_STR(__fentry__) },
	{ 0x5bbb85a1, __VMLINUX_SYMBOL_STR(netdev_err) },
	{ 0x467df16d, __VMLINUX_SYMBOL_STR(netdev_rss_key_fill) },
	{ 0x855db502, __VMLINUX_SYMBOL_STR(pci_enable_msi_range) },
	{ 0x7f243c4d, __VMLINUX_SYMBOL_STR(pci_unregister_driver) },
	{ 0xcc5005fe, __VMLINUX_SYMBOL_STR(msleep_interruptible) },
	{ 0x9b5c5d69, __VMLINUX_SYMBOL_STR(kmem_cache_alloc_trace) },
	{ 0xe259ae9e, __VMLINUX_SYMBOL_STR(_raw_spin_lock) },
	{ 0x680ec266, __VMLINUX_SYMBOL_STR(_raw_spin_lock_irqsave) },
	{ 0xf6ebc03b, __VMLINUX_SYMBOL_STR(net_ratelimit) },
	{ 0x7bf58702, __VMLINUX_SYMBOL_STR(netdev_warn) },
	{ 0xf7de5d93, __VMLINUX_SYMBOL_STR(eth_validate_addr) },
	{ 0xabab7c3a, __VMLINUX_SYMBOL_STR(pci_disable_pcie_error_reporting) },
	{ 0xfcec0987, __VMLINUX_SYMBOL_STR(enable_irq) },
	{ 0x37a0cba, __VMLINUX_SYMBOL_STR(kfree) },
	{ 0x69acdf38, __VMLINUX_SYMBOL_STR(memcpy) },
	{ 0x93f11a07, __VMLINUX_SYMBOL_STR(___pskb_trim) },
	{ 0x75e1fdc7, __VMLINUX_SYMBOL_STR(param_array_ops) },
	{ 0x55f9b4c, __VMLINUX_SYMBOL_STR(ptp_clock_index) },
	{ 0x53c47218, __VMLINUX_SYMBOL_STR(pci_disable_msi) },
	{ 0xf2f2267b, __VMLINUX_SYMBOL_STR(dma_supported) },
	{ 0xedc03953, __VMLINUX_SYMBOL_STR(iounmap) },
	{ 0x38db821d, __VMLINUX_SYMBOL_STR(pci_prepare_to_sleep) },
	{ 0x78dd04c6, __VMLINUX_SYMBOL_STR(pci_dev_run_wake) },
	{ 0x880670bd, __VMLINUX_SYMBOL_STR(__pci_register_driver) },
	{ 0xc357923c, __VMLINUX_SYMBOL_STR(pm_qos_update_request) },
	{ 0x58a2b881, __VMLINUX_SYMBOL_STR(put_page) },
	{ 0xb352177e, __VMLINUX_SYMBOL_STR(find_first_bit) },
	{ 0xf2c69a59, __VMLINUX_SYMBOL_STR(dev_warn) },
	{ 0x3590fefe, __VMLINUX_SYMBOL_STR(unregister_netdev) },
	{ 0x2e0d2f7f, __VMLINUX_SYMBOL_STR(queue_work_on) },
	{ 0x28318305, __VMLINUX_SYMBOL_STR(snprintf) },
	{ 0xbdcf9640, __VMLINUX_SYMBOL_STR(consume_skb) },
	{ 0x32b060f5, __VMLINUX_SYMBOL_STR(pci_enable_device_mem) },
	{ 0xb99003f0, __VMLINUX_SYMBOL_STR(__napi_alloc_skb) },
	{ 0xb651ecb0, __VMLINUX_SYMBOL_STR(skb_tstamp_tx) },
	{ 0xfd33aebd, __VMLINUX_SYMBOL_STR(skb_put) },
	{ 0xcce7db0, __VMLINUX_SYMBOL_STR(pci_release_selected_regions) },
	{ 0x4f6b400b, __VMLINUX_SYMBOL_STR(_copy_from_user) },
	{ 0x11a87f43, __VMLINUX_SYMBOL_STR(param_ops_uint) },
	{ 0xdf59da8e, __VMLINUX_SYMBOL_STR(pcie_capability_write_word) },
	{ 0x9e7d6bd0, __VMLINUX_SYMBOL_STR(__udelay) },
	{ 0x68ce7ed9, __VMLINUX_SYMBOL_STR(dma_ops) },
	{ 0xd619a163, __VMLINUX_SYMBOL_STR(pci_request_selected_regions_exclusive) },
	{ 0xd9f6c399, __VMLINUX_SYMBOL_STR(device_set_wakeup_enable) },
	{ 0x7a29ada4, __VMLINUX_SYMBOL_STR(pcie_capability_read_word) },
	{ 0xf20dabd8, __VMLINUX_SYMBOL_STR(free_irq) },
	{ 0xd8d8f7a7, __VMLINUX_SYMBOL_STR(pci_save_state) },
	{ 0xd831efc6, __VMLINUX_SYMBOL_STR(alloc_etherdev_mqs) },
};

static const char __module_depends[]
__used
__attribute__((section(".modinfo"))) =
"depends=ptp";

/*
 * 宏MODULE_ALIAS参见13.1.2.1 MODULE_INFO()/__MODULE_INFO()节;
 * 通过下列命令查看编译后的e1000e.ko中的alias:
 *   # objdump -s --section=.modinfo ./drivers/net/ethernet/intel/e1000e/e1000e.ko
 */
MODULE_ALIAS("pci:v00008086d0000105Esv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d0000105Fsv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d000010A4sv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d000010BCsv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d000010A5sv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d00001060sv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d000010D9sv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d000010DAsv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d000010D5sv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d000010B9sv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d0000107Dsv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d0000107Esv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d0000107Fsv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d0000108Bsv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d0000108Csv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d0000109Asv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d000010D3sv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d000010F6sv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d0000150Csv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d00001096sv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d000010BAsv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d00001098sv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d000010BBsv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d0000104Csv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d000010C5sv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d000010C4sv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d0000104Asv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d0000104Bsv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d0000104Dsv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d00001049sv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d00001501sv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d000010C0sv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d000010C2sv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d000010C3sv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d000010BDsv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d0000294Csv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d000010E5sv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d000010BFsv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d000010F5sv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d000010CBsv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d000010CCsv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d000010CDsv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d000010CEsv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d000010DEsv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d000010DFsv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d00001525sv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d000010EAsv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d000010EBsv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d000010EFsv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d000010F0sv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d00001502sv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d00001503sv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d0000153Asv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d0000153Bsv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d0000155Asv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d00001559sv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d000015A0sv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d000015A1sv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d000015A2sv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d000015A3sv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d0000156Fsv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d00001570sv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d000015B7sv*sd*bc*sc*i*");
MODULE_ALIAS("pci:v00008086d000015B8sv*sd*bc*sc*i*");

MODULE_INFO(srcversion, "224852E6236A925EFB3CC8C");
```

###### 3.4.3.4.2.2 %.mod.c=>%.mod.o

\*.mod.c是在目标$(builtin-target)执行命令cmd_link_o_target时生成的，参见[3.4.2.1.3.1.1.2 cmd_link_o_target](#3-4-2-1-3-1-1-2-cmd-link-o-target)节。

在scripts/Makefile.modpost中，包含如下规则：

```
quiet_cmd_cc_o_c = CC      $@
      cmd_cc_o_c = $(CC) $(c_flags) $(KBUILD_CFLAGS_MODULE) $(CFLAGS_MODULE) \
		   -c -o $@ $<

$(modules:.ko=.mod.o): %.mod.o: %.mod.c FORCE
	$(call if_changed_dep,cc_o_c)
```

调用命令cmd_cc_o_c将*.mod.c编译成*.mod.o。以arch/x86/crypto/aes-i586.mod.c为例，该命令被扩展为：

```
gcc -Wp,-MD,arch/x86/crypto/.aes-i586.mod.o.d  -nostdinc -isystem /usr/lib/gcc/i686-linux-gnu/4.7/include -I/usr/src/linux-3.2/arch/x86/include -Iarch/x86/include/generated -Iinclude  -include /usr/src/linux-3.2/include/linux/kconfig.h -D__KERNEL__ -Wall -Wundef -Wstrict-prototypes -Wno-trigraphs -fno-strict-aliasing -fno-common -Werror-implicit-function-declaration -Wno-format-security -fno-delete-null-pointer-checks -Os -m32 -msoft-float -mregparm=3 -freg-struct-return -mpreferred-stack-boundary=2 -march=i686 -maccumulate-outgoing-args -Wa,-mtune=generic32 -ffreestanding -fstack-protector -DCONFIG_AS_CFI=1 -DCONFIG_AS_CFI_SIGNAL_FRAME=1 -DCONFIG_AS_CFI_SECTIONS=1 -pipe -Wno-sign-compare -fno-asynchronous-unwind-tables -mno-sse -mno-mmx -mno-sse2 -mno-3dnow -Wframe-larger-than=1024 -Wno-unused-but-set-variable -fno-omit-frame-pointer -fno-optimize-sibling-calls -fno-inline-functions-called-once -Wdeclaration-after-statement -Wno-pointer-sign -fno-strict-overflow -fconserve-stack -DCC_HAVE_ASM_GOTO   -D"KBUILD_STR(s)=#s" -D"KBUILD_BASENAME=KBUILD_STR(aes_i586.mod)"  -D"KBUILD_MODNAME=KBUILD_STR(aes_i586)" -DMODULE  -c -o arch/x86/crypto/aes-i586.mod.o arch/x86/crypto/aes-i586.mod.c
```

###### 3.4.3.4.2.3 $(modules)

在scripts/Makefile.modpost中，包含如下规则：

```
quiet_cmd_ld_ko_o = LD [M]  $@
      cmd_ld_ko_o = $(LD) -r $(LDFLAGS)							\
                             $(KBUILD_LDFLAGS_MODULE) $(LDFLAGS_MODULE)	\
                             -o $@ $(filter-out FORCE,$^)

$(modules): %.ko : %.o %.mod.o FORCE
	$(call if_changed,ld_ko_o)
```

调用命令cmd_ld_ko_o将*.mod.o和*.o连接成*.ko。以arch/x86/crypto/aes-i586.mod.c为例，该命令被扩展为：

```
ld -r -m elf_i386 -T /usr/src/linux-3.2/scripts/module-common.lds --build-id  -o arch/x86/crypto/aes-i586.ko arch/x86/crypto/aes-i586.o arch/x86/crypto/aes-i586.mod.o
```

[**NOTE1**] 执行make modules_install命令时，这些*.ko文件会被安装到/lib/modules/3.2.0/kernel/目录，参见[安装内核](#3-5-5-)节；

[**NOTE2**] script/module-common.lds是生成*.ko文件的链接脚本文件，参见[Appendix H: scripts/module-common.lds](#appendix-h-scripts-module-common-lds)节。

##### 3.4.3.4.3 make -f scripts/Makefile.fwinst obj=firmware \_\_fw_modbuild

在scripts/Makefile.fwinst中，包含如下规则：

```
/*
 * 引入firmware/Makefile，其中包含$(fw-shipped-m)和$(fw-shipped-y)
 * $(fw-shipped-m)和$(fw-shipped-y)中包含*.fw, *.bin, *.dsp列表
 */
include $(srctree)/$(obj)/Makefile

...
mod-fw := $(fw-shipped-m)
# If CONFIG_FIRMWARE_IN_KERNEL isn't set, then install the
# firmware for in-kernel drivers too.
ifndef CONFIG_FIRMWARE_IN_KERNEL
mod-fw += $(fw-shipped-y)
endif

...
__fw_modbuild: $(addprefix $(obj)/,$(mod-fw))
	@:
```

###### 3.4.3.4.3.1 $(mod-fw)

在firmware/Makefile中，包含如下规则：

```
...
// $(fw-shipped-m)和$(fw-shipped-y)中包含*.fw, *.bin, *.dsp列表
fw-shipped-$(CONFIG_3C359) += 3com/3C359.bin
fw-shipped-$(CONFIG_ACENIC) += $(acenic-objs)
fw-shipped-$(CONFIG_ADAPTEC_STARFIRE) += adaptec/starfire_rx.bin \
				 adaptec/starfire_tx.bin
fw-shipped-$(CONFIG_ATARI_DSP56K) += dsp56k/bootstrap.bin
fw-shipped-$(CONFIG_ATM_AMBASSADOR) += atmsar11.fw
fw-shipped-$(CONFIG_BNX2X) += bnx2x/bnx2x-e1-6.2.9.0.fw \
			     bnx2x/bnx2x-e1h-6.2.9.0.fw \
			     bnx2x/bnx2x-e2-6.2.9.0.fw
fw-shipped-$(CONFIG_BNX2) += bnx2/bnx2-mips-09-6.2.1a.fw \
			     bnx2/bnx2-rv2p-09-6.0.17.fw \
			     bnx2/bnx2-rv2p-09ax-6.0.17.fw \
			     bnx2/bnx2-mips-06-6.2.1.fw \
			     bnx2/bnx2-rv2p-06-6.0.15.fw
fw-shipped-$(CONFIG_CASSINI) += sun/cassini.bin
fw-shipped-$(CONFIG_COMPUTONE) += intelliport2.bin
...

quiet_cmd_ihex	= IHEX    $@
      cmd_ihex		= $(OBJCOPY) -Iihex -Obinary $< $@

...
// firmware/目录中已存在*.ihex文件
$(obj)/%: $(obj)/%.ihex | $(objtree)/$(obj)/$$(dir %)
	$(call cmd,ihex)
```

调用cmd_ihex命令，以firmware/bnx2/bnx2-mips-09-6.2.1a.fw为例，该命令被扩展为：

```
objcopy -Iihex -Obinary firmware/bnx2/bnx2-mips-09-6.2.1a.fw.ihex firmware/bnx2/bnx2-mips-09-6.2.1a.fw
```

该命令输出如下文件：

```
firmware/oneSubdir/twoSubdir/.../*.fw
firmware/oneSubdir/twoSubdir/.../*.bin
firmware/oneSubdir/twoSubdir/.../*.dsp
```

### 3.4.4 编译external modules

执行下列命令之一来编译外部模块:

```
# make -C <kernel_src_dir> M=<ext_module_dir> modules
# make -C <kernel_src_dir> SUBDIRS=$PWD modules
```

在顶层Makefile中，包含如下规则：

```
# Use make M=dir to specify directory of external module to build
# Old syntax make ... SUBDIRS=$PWD is still supported
# Setting the environment variable KBUILD_EXTMOD take precedence
ifdef SUBDIRS
  KBUILD_EXTMOD ?= $(SUBDIRS)
endif

ifeq ("$(origin M)", "command line")
  KBUILD_EXTMOD := $(M)
endif

...

# That's our default target when none is given on the command line
PHONY := _all
_all:

# If building an external module we do not care about the all: rule
# but instead _all depend on modules
PHONY += all
ifeq ($(KBUILD_EXTMOD),)
_all: all
else
_all: modules	// 此时定义了KBUILD_EXTMOD，故进入本分支
endif

# When compiling out-of-tree modules, put MODVERDIR in the module
# tree rather than in the kernel tree. The kernel tree might
# even be read-only.
// 在命令cmd_crmodverdir中使用
export MODVERDIR := $(if $(KBUILD_EXTMOD),$(firstword $(KBUILD_EXTMOD))/).tmp_versions

ifeq ($(KBUILD_EXTMOD),)
...
else # KBUILD_EXTMOD

###
# External module support.
# When building external modules the kernel used as basis is considered
# read-only, and no consistency checks are made and the make
# system is not used on the basis kernel. If updates are required
# in the basis kernel ordinary make commands (without M=...) must
# be used.
#
# The following are the only valid targets when building external
# modules.
# make M=dir clean	Delete all automatically generated files
# make M=dir modules	Make all modules in specified dir
# make M=dir		Same as 'make M=dir modules'
# make M=dir modules_install
#				Install the modules built in the module directory
#				Assumes install directory is already created

# We are always building modules
KBUILD_MODULES := 1
PHONY += crmodverdir

/*
 * (1) 该目标执行命令cmd_crmodverdir，用于在外部模块源代码
 *     目录中创建临时目录.tmp_versions/，用于保存*.mod文件
 */
crmodverdir:
	$(cmd_crmodverdir)

/*
 * (2) 检查$(objtree)/Module.symvers是否存在
 */
PHONY += $(objtree)/Module.symvers
$(objtree)/Module.symvers:
	@test -e $(objtree)/Module.symvers || ( \
	echo; \
	echo "  WARNING: Symbol version dump $(objtree)/Module.symvers"; \
	echo "           is missing; modules will have no dependencies and modversions."; \
	echo )

/*
 * (3) 编译外部模块源代码目录
 *     示例：若外部模块源代码所在目录为/ext/module/src，
 *     则module-dirs被扩展为_module_/ext/module/src
 */
module-dirs := $(addprefix _module_,$(KBUILD_EXTMOD))
PHONY += $(module-dirs) modules
$(module-dirs): crmodverdir $(objtree)/Module.symvers
	/*
	 * 示例：扩展为make –f scripts/Makefile.build obj=/ext/module/src
	 * 编译外部模块源代码目录，参见make -f scripts/Makefile.build obj=XXX命令的执行过程节，
	 * 其中，$(obj-m)由外部模块源代码目录中的Makefile配置
	 */
	$(Q)$(MAKE) $(build)=$(patsubst _module_%,%,$@)

/*
 * (4) Stage 2 of building external modules
 */
modules: $(module-dirs)
	@$(kecho) '  Building modules, stage 2.';
	/*
	 * 扩展为make –f scripts/Makefile.modpost，
	 * 其执行过程参见make -f scripts/Makefile.modpost节
	 */
	$(Q)$(MAKE) -f $(srctree)/scripts/Makefile.modpost

endif # KBUILD_EXTMOD

...
# Create temporary dir for module support files
# clean it up only when building all modules
cmd_crmodverdir = $(Q)mkdir -p $(MODVERDIR) \
                      $(if $(KBUILD_MODULES),; rm -f $(MODVERDIR)/*)
```

各目标之间的依赖关系如下：

![target_modules](/assets/target_modules.png)

### 3.4.4A 只编译内核中的某个驱动程序

以驱动程序drivers/net/ethernet/intel/e1000e为例，按照如下步骤编译该驱动程序:

```
# 切换到和当前内核版本匹配的内核版本
chenwx@chenwx ~/linux $ uname -r
4.2.2-alex
chenwx@chenwx ~/linux $ git co v4.2.2
Previous HEAD position was 64291f7db5bd... Linux 4.2
HEAD is now at 7659db320e01... Linux 4.2.2

# 为编译内核做准备
chenwx@chenwx ~/linux $ make O=../linux-build/ modules_prepare
make[1]: Entering directory `/home/chenwx/linux-build'
  SYSTBL  arch/x86/entry/syscalls/../../include/generated/asm/syscalls_32.h
  SYSHDR  arch/x86/entry/syscalls/../../include/generated/asm/unistd_32_ia32.h
  SYSHDR  arch/x86/entry/syscalls/../../include/generated/asm/unistd_64_x32.h
  SYSTBL  arch/x86/entry/syscalls/../../include/generated/asm/syscalls_64.h
  SYSHDR  arch/x86/entry/syscalls/../../include/generated/uapi/asm/unistd_32.h
  SYSHDR  arch/x86/entry/syscalls/../../include/generated/uapi/asm/unistd_64.h
  SYSHDR  arch/x86/entry/syscalls/../../include/generated/uapi/asm/unistd_x32.h
  HOSTCC  scripts/basic/bin2c
  HOSTCC  arch/x86/tools/relocs_32.o
  HOSTCC  arch/x86/tools/relocs_64.o
  HOSTCC  arch/x86/tools/relocs_common.o
  HOSTLD  arch/x86/tools/relocs
  CHK     include/config/kernel.release
  UPD     include/config/kernel.release
  Using /home/chenwx/linux as source for kernel
  GEN     ./Makefile
  WRAP    arch/x86/include/generated/asm/clkdev.h
  WRAP    arch/x86/include/generated/asm/cputime.h
  WRAP    arch/x86/include/generated/asm/dma-contiguous.h
  WRAP    arch/x86/include/generated/asm/early_ioremap.h
  WRAP    arch/x86/include/generated/asm/mcs_spinlock.h
  WRAP    arch/x86/include/generated/asm/mm-arch-hooks.h
  CHK     include/generated/uapi/linux/version.h
  UPD     include/generated/uapi/linux/version.h
  CHK     include/generated/utsrelease.h
  UPD     include/generated/utsrelease.h
  CC      kernel/bounds.s
  CHK     include/generated/bounds.h
  UPD     include/generated/bounds.h
  CHK     include/generated/timeconst.h
  UPD     include/generated/timeconst.h
  CC      arch/x86/kernel/asm-offsets.s
  CHK     include/generated/asm-offsets.h
  UPD     include/generated/asm-offsets.h
  CALL    /home/chenwx/linux/scripts/checksyscalls.sh
  HOSTCC  scripts/genksyms/genksyms.o
  SHIPPED scripts/genksyms/parse.tab.c
  HOSTCC  scripts/genksyms/parse.tab.o
  SHIPPED scripts/genksyms/lex.lex.c
  SHIPPED scripts/genksyms/keywords.hash.c
  SHIPPED scripts/genksyms/parse.tab.h
  HOSTCC  scripts/genksyms/lex.lex.o
  HOSTLD  scripts/genksyms/genksyms
  CC      scripts/mod/empty.o
  HOSTCC  scripts/mod/mk_elfconfig
  MKELF   scripts/mod/elfconfig.h
  HOSTCC  scripts/mod/modpost.o
  CC      scripts/mod/devicetable-offsets.s
  GEN     scripts/mod/devicetable-offsets.h
  HOSTCC  scripts/mod/file2alias.o
  HOSTCC  scripts/mod/sumversion.o
  HOSTLD  scripts/mod/modpost
  HOSTCC  scripts/selinux/genheaders/genheaders
  HOSTCC  scripts/selinux/mdp/mdp
  HOSTCC  scripts/kallsyms
  HOSTCC  scripts/conmakehash
  HOSTCC  scripts/recordmcount
  HOSTCC  scripts/sortextable
make[1]: Leaving directory `/home/chenwx/linux-build'

# 编译驱动程序drivers/net/ethernet/intel/e1000e
chenwx@chenwx ~/linux $ make O=../linux-build/ M=drivers/net/ethernet/intel/e1000e
make[1]: Entering directory `/home/chenwx/linux-build'

  WARNING: Symbol version dump ./Module.symvers
           is missing; modules will have no dependencies and modversions.

  CC [M]  drivers/net/ethernet/intel/e1000e/82571.o
  CC [M]  drivers/net/ethernet/intel/e1000e/ich8lan.o
  CC [M]  drivers/net/ethernet/intel/e1000e/80003es2lan.o
  CC [M]  drivers/net/ethernet/intel/e1000e/mac.o
  CC [M]  drivers/net/ethernet/intel/e1000e/manage.o
  CC [M]  drivers/net/ethernet/intel/e1000e/nvm.o
  CC [M]  drivers/net/ethernet/intel/e1000e/phy.o
  CC [M]  drivers/net/ethernet/intel/e1000e/param.o
  CC [M]  drivers/net/ethernet/intel/e1000e/ethtool.o
  CC [M]  drivers/net/ethernet/intel/e1000e/netdev.o
  CC [M]  drivers/net/ethernet/intel/e1000e/ptp.o
  LD [M]  drivers/net/ethernet/intel/e1000e/e1000e.o
  Building modules, stage 2.
  MODPOST 1 modules
  CC      drivers/net/ethernet/intel/e1000e/e1000e.mod.o
  LD [M]  drivers/net/ethernet/intel/e1000e/e1000e.ko
make[1]: Leaving directory `/home/chenwx/linux-build'

# 查看编译后的驱动程序drivers/net/ethernet/intel/e1000e
chenwx@chenwx ~/linux $ ll ../linux-build/drivers/net/ethernet/intel/e1000e
total 13M
-rw-r--r-- 1 chenwx chenwx 314K Oct  7 21:39 80003es2lan.o
-rw-r--r-- 1 chenwx chenwx 334K Oct  7 21:39 82571.o
-rw-r--r-- 1 chenwx chenwx    0 Oct  7 21:39 Module.symvers
-rw-r--r-- 1 chenwx chenwx    8 Oct  7 21:39 built-in.o
-rw-r--r-- 1 chenwx chenwx 4.2M Oct  7 21:39 e1000e.ko
-rw-r--r-- 1 chenwx chenwx  13K Oct  7 21:39 e1000e.mod.c
-rw-r--r-- 1 chenwx chenwx  70K Oct  7 21:39 e1000e.mod.o
-rw-r--r-- 1 chenwx chenwx 4.1M Oct  7 21:39 e1000e.o
-rw-r--r-- 1 chenwx chenwx 410K Oct  7 21:39 ethtool.o
-rw-r--r-- 1 chenwx chenwx 476K Oct  7 21:39 ich8lan.o
-rw-r--r-- 1 chenwx chenwx 337K Oct  7 21:39 mac.o
-rw-r--r-- 1 chenwx chenwx 263K Oct  7 21:39 manage.o
-rw-r--r-- 1 chenwx chenwx   51 Oct  7 21:39 modules.order
-rw-r--r-- 1 chenwx chenwx 855K Oct  7 21:39 netdev.o
-rw-r--r-- 1 chenwx chenwx 284K Oct  7 21:39 nvm.o
-rw-r--r-- 1 chenwx chenwx 287K Oct  7 21:39 param.o
-rw-r--r-- 1 chenwx chenwx 386K Oct  7 21:39 phy.o
-rw-r--r-- 1 chenwx chenwx 266K Oct  7 21:39 ptp.o

chenwx@chenwx ~/linux $ modinfo ../linux-build/drivers/net/ethernet/intel/e1000e/e1000e.ko
filename:       /home/chenwx/linux/../linux-build/drivers/net/ethernet/intel/e1000e/e1000e.ko
version:        3.2.5-k
license:        GPL
description:    Intel(R) PRO/1000 Network Driver
author:         Intel Corporation, <linux.nics@intel.com>
srcversion:     224852E6236A925EFB3CC8C
alias:          pci:v00008086d000015B8sv*sd*bc*sc*i*
alias:          pci:v00008086d000015B7sv*sd*bc*sc*i*
alias:          pci:v00008086d00001570sv*sd*bc*sc*i*
alias:          pci:v00008086d0000156Fsv*sd*bc*sc*i*
alias:          pci:v00008086d000015A3sv*sd*bc*sc*i*
alias:          pci:v00008086d000015A2sv*sd*bc*sc*i*
alias:          pci:v00008086d000015A1sv*sd*bc*sc*i*
alias:          pci:v00008086d000015A0sv*sd*bc*sc*i*
alias:          pci:v00008086d00001559sv*sd*bc*sc*i*
alias:          pci:v00008086d0000155Asv*sd*bc*sc*i*
alias:          pci:v00008086d0000153Bsv*sd*bc*sc*i*
alias:          pci:v00008086d0000153Asv*sd*bc*sc*i*
alias:          pci:v00008086d00001503sv*sd*bc*sc*i*
alias:          pci:v00008086d00001502sv*sd*bc*sc*i*
alias:          pci:v00008086d000010F0sv*sd*bc*sc*i*
alias:          pci:v00008086d000010EFsv*sd*bc*sc*i*
alias:          pci:v00008086d000010EBsv*sd*bc*sc*i*
alias:          pci:v00008086d000010EAsv*sd*bc*sc*i*
alias:          pci:v00008086d00001525sv*sd*bc*sc*i*
alias:          pci:v00008086d000010DFsv*sd*bc*sc*i*
alias:          pci:v00008086d000010DEsv*sd*bc*sc*i*
alias:          pci:v00008086d000010CEsv*sd*bc*sc*i*
alias:          pci:v00008086d000010CDsv*sd*bc*sc*i*
alias:          pci:v00008086d000010CCsv*sd*bc*sc*i*
alias:          pci:v00008086d000010CBsv*sd*bc*sc*i*
alias:          pci:v00008086d000010F5sv*sd*bc*sc*i*
alias:          pci:v00008086d000010BFsv*sd*bc*sc*i*
alias:          pci:v00008086d000010E5sv*sd*bc*sc*i*
alias:          pci:v00008086d0000294Csv*sd*bc*sc*i*
alias:          pci:v00008086d000010BDsv*sd*bc*sc*i*
alias:          pci:v00008086d000010C3sv*sd*bc*sc*i*
alias:          pci:v00008086d000010C2sv*sd*bc*sc*i*
alias:          pci:v00008086d000010C0sv*sd*bc*sc*i*
alias:          pci:v00008086d00001501sv*sd*bc*sc*i*
alias:          pci:v00008086d00001049sv*sd*bc*sc*i*
alias:          pci:v00008086d0000104Dsv*sd*bc*sc*i*
alias:          pci:v00008086d0000104Bsv*sd*bc*sc*i*
alias:          pci:v00008086d0000104Asv*sd*bc*sc*i*
alias:          pci:v00008086d000010C4sv*sd*bc*sc*i*
alias:          pci:v00008086d000010C5sv*sd*bc*sc*i*
alias:          pci:v00008086d0000104Csv*sd*bc*sc*i*
alias:          pci:v00008086d000010BBsv*sd*bc*sc*i*
alias:          pci:v00008086d00001098sv*sd*bc*sc*i*
alias:          pci:v00008086d000010BAsv*sd*bc*sc*i*
alias:          pci:v00008086d00001096sv*sd*bc*sc*i*
alias:          pci:v00008086d0000150Csv*sd*bc*sc*i*
alias:          pci:v00008086d000010F6sv*sd*bc*sc*i*
alias:          pci:v00008086d000010D3sv*sd*bc*sc*i*
alias:          pci:v00008086d0000109Asv*sd*bc*sc*i*
alias:          pci:v00008086d0000108Csv*sd*bc*sc*i*
alias:          pci:v00008086d0000108Bsv*sd*bc*sc*i*
alias:          pci:v00008086d0000107Fsv*sd*bc*sc*i*
alias:          pci:v00008086d0000107Esv*sd*bc*sc*i*
alias:          pci:v00008086d0000107Dsv*sd*bc*sc*i*
alias:          pci:v00008086d000010B9sv*sd*bc*sc*i*
alias:          pci:v00008086d000010D5sv*sd*bc*sc*i*
alias:          pci:v00008086d000010DAsv*sd*bc*sc*i*
alias:          pci:v00008086d000010D9sv*sd*bc*sc*i*
alias:          pci:v00008086d00001060sv*sd*bc*sc*i*
alias:          pci:v00008086d000010A5sv*sd*bc*sc*i*
alias:          pci:v00008086d000010BCsv*sd*bc*sc*i*
alias:          pci:v00008086d000010A4sv*sd*bc*sc*i*
alias:          pci:v00008086d0000105Fsv*sd*bc*sc*i*
alias:          pci:v00008086d0000105Esv*sd*bc*sc*i*
depends:        
vermagic:       4.2.2 SMP mod_unload modversions
parm:           debug:Debug level (0=none,...,16=all) (int)
parm:           copybreak:Maximum size of packet that is copied to a new buffer on receive (uint)
parm:           TxIntDelay:Transmit Interrupt Delay (array of int)
parm:           TxAbsIntDelay:Transmit Absolute Interrupt Delay (array of int)
parm:           RxIntDelay:Receive Interrupt Delay (array of int)
parm:           RxAbsIntDelay:Receive Absolute Interrupt Delay (array of int)
parm:           InterruptThrottleRate:Interrupt Throttling Rate (array of int)
parm:           IntMode:Interrupt Mode (array of int)
parm:           SmartPowerDownEnable:Enable PHY smart power down (array of int)
parm:           KumeranLockLoss:Enable Kumeran lock loss workaround (array of int)
parm:           WriteProtectNVM:Write-protect NVM [WARNING: disabling this can lead to corrupted NVM] (array of int)
parm:           CrcStripping:Enable CRC Stripping, disable if your BMC needs the CRC (array of int)
```

### 3.4.5 交叉编译ARM

交叉编译内核需要安装交叉编译器，参见[Cross compiling Linux kernel on x86_64](/docs/Cross_Compile_Linux.pdf)。

根据顶层Makefile中的如下定义可知，Makefile的Default Target节至编译external modules节的编译与当前环境的体系架构有关：

```
# SUBARCH tells the usermode build what the underlying arch is.  That is set
# first, and if a usermode build is happening, the "ARCH=um" on the command
# line overrides the setting of ARCH below.  If a native build is happening,
# then ARCH is assigned, getting whatever value it gets normally, and
# SUBARCH is subsequently ignored.
SUBARCH := $(shell uname -m | sed -e s/i.86/i386/ -e s/sun4u/sparc64/				\
						  -e s/arm.*/arm/ -e s/sa110/arm/		\
						  -e s/s390x/s390/ -e s/parisc64/parisc/		\
						  -e s/ppc.*/powerpc/ -e s/mips.*/mips/		\
						  -e s/sh[234].*/sh/ )

# Cross compiling and selecting different set of gcc/bin-utils
# ---------------------------------------------------------------------------
#
# When performing cross compilation for other architectures ARCH shall be set
# to the target architecture. (See arch/* for the possibilities).
# ARCH can be set during invocation of make:
# make ARCH=ia64
# Another way is to have ARCH set in the environment.
# The default ARCH is the host where make is executed.

# CROSS_COMPILE specify the prefix used for all executables used
# during compilation. Only gcc and related bin-utils executables
# are prefixed with $(CROSS_COMPILE).
# CROSS_COMPILE can be set on the command line
# make CROSS_COMPILE=ia64-linux-
# Alternatively CROSS_COMPILE can be set in the environment.
# A third alternative is to store a setting in .config so that plain
# "make" in the configured kernel build directory always uses that.
# Default value for CROSS_COMPILE is not to prefix executables
# Note: Some architectures assign CROSS_COMPILE in their arch/*/Makefile
export KBUILD_BUILDHOST := $(SUBARCH)
ARCH			?= $(SUBARCH)
CROSS_COMPILE	?= $(CONFIG_CROSS_COMPILE:"%"=%)

# Architecture as present in compile.h
UTS_MACHINE 	:= $(ARCH)
SRCARCH 		:= $(ARCH)

...
```

如果为其他体系架构编译内核，则需要进行交叉编译。下面以ARM为例，讲解Linux Kernel的交叉编译。

#### 3.4.5.1 安装交叉编译器

以ARM为例，运行下列命令安装交叉编译器:

```
chenwx@chenwx ~/linux $ sudo apt-get install gcc-arm-linux-gnueabi
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following extra packages will be installed:
  binutils-arm-linux-gnueabi cpp-4.7-arm-linux-gnueabi cpp-arm-linux-gnueabi gcc-4.7-arm-linux-gnueabi gcc-4.7-arm-linux-gnueabi-base libc6-armel-cross
  libc6-dev-armel-cross libgcc1-armel-cross libgomp1-armel-cross linux-libc-dev-armel-cross
Suggested packages:
  binutils-doc gcc-4.7-locales cpp-doc gcc-4.7-multilib-arm-linux-gnueabi libmudflap0-4.7-dev-armel-cross gcc-4.7-doc libgcc1-dbg-armel-cross
  libgomp1-dbg-armel-cross libitm1-dbg-armel-cross libquadmath-dbg-armel-cross libmudflap0-dbg-armel-cross binutils-gold automake1.9 flex bison
  gdb-arm-linux-gnueabi gcc-doc
The following NEW packages will be installed:
  binutils-arm-linux-gnueabi cpp-4.7-arm-linux-gnueabi cpp-arm-linux-gnueabi gcc-4.7-arm-linux-gnueabi gcc-4.7-arm-linux-gnueabi-base gcc-arm-linux-gnueabi
  libc6-armel-cross libc6-dev-armel-cross libgcc1-armel-cross libgomp1-armel-cross linux-libc-dev-armel-cross
0 upgraded, 11 newly installed, 0 to remove and 417 not upgraded.
Need to get 20.6 MB of archives.
After this operation, 41.0 MB of additional disk space will be used.
...

chenwx@chenwx ~/linux $ ll /usr/bin | grep arm
-rwxr-xr-x  1 root   root        9648 Jul  9  2012 arm2hpdl
-rwxr-xr-x  1 root   root       26524 Sep 21  2012 arm-linux-gnueabi-addr2line
-rwxr-xr-x  2 root   root       55228 Sep 21  2012 arm-linux-gnueabi-ar
-rwxr-xr-x  2 root   root      569784 Sep 21  2012 arm-linux-gnueabi-as
-rwxr-xr-x  1 root   root       22164 Sep 21  2012 arm-linux-gnueabi-c++filt
lrwxrwxrwx  1 root   root          25 Oct  6  2012 arm-linux-gnueabi-cpp -> arm-linux-gnueabi-cpp-4.7
-rwxr-xr-x  1 root   root      515328 Sep 21  2012 arm-linux-gnueabi-cpp-4.7
-rwxr-xr-x  1 root   root       26384 Sep 21  2012 arm-linux-gnueabi-elfedit
lrwxrwxrwx  1 root   root          25 Oct  6  2012 arm-linux-gnueabi-gcc -> arm-linux-gnueabi-gcc-4.7
-rwxr-xr-x  1 root   root      515328 Sep 21  2012 arm-linux-gnueabi-gcc-4.7
-rwxr-xr-x  1 root   root       22088 Sep 21  2012 arm-linux-gnueabi-gcc-ar-4.7
-rwxr-xr-x  1 root   root       22088 Sep 21  2012 arm-linux-gnueabi-gcc-nm-4.7
-rwxr-xr-x  1 root   root       22092 Sep 21  2012 arm-linux-gnueabi-gcc-ranlib-4.7
lrwxrwxrwx  1 root   root          26 Oct  6  2012 arm-linux-gnueabi-gcov -> arm-linux-gnueabi-gcov-4.7
-rwxr-xr-x  1 root   root      210704 Sep 21  2012 arm-linux-gnueabi-gcov-4.7
-rwxr-xr-x  1 root   root       92728 Sep 21  2012 arm-linux-gnueabi-gprof
-rwxr-xr-x  4 root   root      494592 Sep 21  2012 arm-linux-gnueabi-ld
-rwxr-xr-x  4 root   root      494592 Sep 21  2012 arm-linux-gnueabi-ld.bfd
-rwxr-xr-x  2 root   root     2886436 Sep 21  2012 arm-linux-gnueabi-ld.gold
-rwxr-xr-x  2 root   root       35092 Sep 21  2012 arm-linux-gnueabi-nm
-rwxr-xr-x  2 root   root      204668 Sep 21  2012 arm-linux-gnueabi-objcopy
-rwxr-xr-x  2 root   root      307456 Sep 21  2012 arm-linux-gnueabi-objdump
-rwxr-xr-x  2 root   root       55240 Sep 21  2012 arm-linux-gnueabi-ranlib
-rwxr-xr-x  1 root   root      369540 Sep 21  2012 arm-linux-gnueabi-readelf
-rwxr-xr-x  1 root   root       26488 Sep 21  2012 arm-linux-gnueabi-size
-rwxr-xr-x  1 root   root       26476 Sep 21  2012 arm-linux-gnueabi-strings
-rwxr-xr-x  2 root   root      204668 Sep 21  2012 arm-linux-gnueabi-strip
lrwxrwxrwx  1 root   root           9 Jan 22  2013 charmap -> gucharmap
lrwxrwxrwx  1 root   root           9 Jan 22  2013 gnome-character-map -> gucharmap
-rwxr-xr-x  1 root   root       68276 Sep 28  2012 gucharmap
lrwxrwxrwx  1 root   root          26 Jan 22  2013 testparm -> /etc/alternatives/testparm
-rwxr-xr-x  1 root   root     1427132 Oct  5  2012 testparm.samba3

chenwx@chenwx ~/linux $ arm-linux-gnueabi-gcc --version
arm-linux-gnueabi-gcc (Ubuntu/Linaro 4.7.2-1ubuntu1) 4.7.2
Copyright (C) 2012 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

#### 3.4.5.2 配置内核ARM

可采用如下两种方式之一配置内核：

**1) 运行下列命令配置内核，配置结果保存在~/linux-build/.config中**

```
# make O=../linux-build/ ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- menuconfig
```

**2) 运行下列命令使用arch/arm/configs/目录中的默认配置文件，以acs5k_defconfig为例(参见[3.3.2 make *config](#3-3-2-make-config)节)**

```
# make O=../linux-build/ ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- acs5k_defconfig
```

```
chenwx@chenwx ~/linux $ ll arch/arm/configs/
-rw-r--r-- 1 chenwx chenwx  1998 Jul  8 20:53 acs5k_defconfig
-rw-r--r-- 1 chenwx chenwx  2011 Jul  8 20:53 acs5k_tiny_defconfig
-rw-r--r-- 1 chenwx chenwx  2617 Jul  8 20:53 am200epdkit_defconfig
-rw-r--r-- 1 chenwx chenwx  2289 Jul  8 20:53 ape6evm_defconfig
-rw-r--r-- 1 chenwx chenwx  4068 Jul  8 20:53 armadillo800eva_defconfig
-rw-r--r-- 1 chenwx chenwx  1315 Jul  8 20:53 assabet_defconfig
-rw-r--r-- 1 chenwx chenwx  5249 Jul  8 20:53 at91_dt_defconfig
...

chenwx@chenwx ~/linux $ make O=../linux-build/ ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- help
...
Architecture specific targets (arm):
* zImage        - Compressed kernel image (arch/arm/boot/zImage)
  Image         - Uncompressed kernel image (arch/arm/boot/Image)
* xipImage      - XIP kernel image, if configured (arch/arm/boot/xipImage)
  uImage        - U-Boot wrapped zImage
  bootpImage    - Combined zImage and initial RAM disk
                  (supply initrd image via make variable INITRD=<path>)
* dtbs          - Build device tree blobs for enabled boards
  dtbs_install  - Install dtbs to /boot/dtbs/3.15.0
  install       - Install uncompressed kernel
  zinstall      - Install compressed kernel
  uinstall      - Install U-Boot wrapped compressed kernel
                  Install using (your) ~/bin/installkernel or
                  (distribution) /sbin/installkernel or
                  install to $(INSTALL_PATH) and run lilo

  acs5k_defconfig            - Build for acs5k
  acs5k_tiny_defconfig       - Build for acs5k_tiny
  am200epdkit_defconfig      - Build for am200epdkit
  ape6evm_defconfig          - Build for ape6evm
  armadillo800eva_defconfig  - Build for armadillo800eva
  assabet_defconfig          - Build for assabet
  at91_dt_defconfig          - Build for at91_dt
  at91rm9200_defconfig       - Build for at91rm9200
  at91sam9260_9g20_defconfig - Build for at91sam9260_9g20
  at91sam9261_9g10_defconfig - Build for at91sam9261_9g10
  at91sam9263_defconfig      - Build for at91sam9263
  at91sam9g45_defconfig      - Build for at91sam9g45
  at91sam9rl_defconfig       - Build for at91sam9rl
...

chenwx@chenwx ~/linux $ make O=../linux-build/ ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- acs5k_defconfig
#
# configuration written to .config
#
```

#### 3.4.5.3 内核的交叉编译

运行下列命令为ARM架构交叉编译内核：

```
chenwx@chenwx ～/linux $ make O=../linux-build/ ARCH=arm CROSS_COMPILE=arm-linux-gnueabi-
...
  LD      vmlinux
  SORTEX  vmlinux
  SYSMAP  System.map
  OBJCOPY arch/arm/boot/Image
  Kernel: arch/arm/boot/Image is ready
  AS      arch/arm/boot/compressed/head.o
  GZIP    arch/arm/boot/compressed/piggy.gzip
  AS      arch/arm/boot/compressed/piggy.gzip.o
  CC      arch/arm/boot/compressed/misc.o
  CC      arch/arm/boot/compressed/decompress.o
  CC      arch/arm/boot/compressed/string.o
  SHIPPED arch/arm/boot/compressed/hyp-stub.S
  AS      arch/arm/boot/compressed/hyp-stub.o
  SHIPPED arch/arm/boot/compressed/lib1funcs.S
  AS      arch/arm/boot/compressed/lib1funcs.o
  SHIPPED arch/arm/boot/compressed/ashldi3.S
  AS      arch/arm/boot/compressed/ashldi3.o
  LD      arch/arm/boot/compressed/vmlinux
  OBJCOPY arch/arm/boot/zImage
  Kernel: arch/arm/boot/zImage is ready
  MODPOST 42 modules
  CC      arch/arm/crypto/aes-arm.mod.o
  LD [M]  arch/arm/crypto/aes-arm.ko
  CC      arch/arm/crypto/sha1-arm.mod.o
  LD [M]  arch/arm/crypto/sha1-arm.ko
  CC      crypto/ansi_cprng.mod.o
  LD [M]  crypto/ansi_cprng.ko
...

chenwx@chenwx ～/linux-build $ ll vmlinux System.map arch/arm/boot/compressed/vmlinux arch/arm/boot/zImage
-rwxr-xr-x 1 chenwx chenwx  985956 Dec  9 11:56 arch/arm/boot/compressed/vmlinux
-rwxr-xr-x 1 chenwx chenwx  947208 Dec  9 11:56 arch/arm/boot/zImage
-rw-r--r-- 1 chenwx chenwx  316549 Dec  9 11:56 System.map
-rwxr-xr-x 1 chenwx chenwx 2435755 Dec  9 11:56 vmlinux

chenwx@chenwx ～/linux-build $ file vmlinux
vmlinux: ELF 32-bit LSB executable, ARM, version 1, statically linked, BuildID[sha1]=0xf2e0153fb842be3137df94af05c48f27dfd510b9, not stripped

chenwx@chenwx ～/linux-build $ file arch/arm/boot/compressed/vmlinux
arch/arm/boot/compressed/vmlinux: ELF 32-bit LSB executable, ARM, version 1, statically linked, not stripped

chenwx@chenwx ～/linux-build $ file arch/arm/boot/zImage
arch/arm/boot/zImage: Linux kernel ARM boot executable zImage (little-endian)
```

#### 3.4.5.4 安装编译的内核模块

运行下列命令安装编译的内核模块：

```
# make O=../linux-build/ ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- INSTALL_MOD_PATH=/path/install/modules/ modules_install
```

```
chenwx@chenwx ～/linux $ make O=../linux-build/ ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- INSTALL_MOD_PATH=~/arm_mod modules_install
  INSTALL arch/arm/crypto/aes-arm.ko
  INSTALL arch/arm/crypto/sha1-arm.ko
  INSTALL crypto/ansi_cprng.ko
  INSTALL crypto/anubis.ko
  INSTALL crypto/arc4.ko
  INSTALL crypto/blowfish_common.ko
  INSTALL crypto/blowfish_generic.ko
  INSTALL crypto/camellia_generic.ko
  INSTALL crypto/cast5_generic.ko
  INSTALL crypto/cast6_generic.ko
  INSTALL crypto/cast_common.ko
  INSTALL crypto/ccm.ko
  INSTALL crypto/cmac.ko
  INSTALL crypto/crc32.ko
  INSTALL crypto/crc32c.ko
  INSTALL crypto/crct10dif_common.ko
  INSTALL crypto/crct10dif_generic.ko
  INSTALL crypto/ctr.ko
  INSTALL crypto/cts.ko
  INSTALL crypto/des_generic.ko
  INSTALL crypto/fcrypt.ko
  INSTALL crypto/gcm.ko
  INSTALL crypto/ghash-generic.ko
  INSTALL crypto/lrw.ko
  INSTALL crypto/md4.ko
  INSTALL crypto/michael_mic.ko
  INSTALL crypto/pcbc.ko
  INSTALL crypto/rmd128.ko
  INSTALL crypto/rmd160.ko
  INSTALL crypto/rmd256.ko
  INSTALL crypto/rmd320.ko
  INSTALL crypto/seqiv.ko
  INSTALL crypto/sha512_generic.ko
  INSTALL crypto/tcrypt.ko
  INSTALL crypto/tgr192.ko
  INSTALL crypto/vmac.ko
  INSTALL crypto/wp512.ko
  INSTALL crypto/xcbc.ko
  INSTALL crypto/xts.ko
  INSTALL fs/ext3/ext3.ko
  INSTALL fs/jbd/jbd.ko
  INSTALL fs/mbcache.ko
  DEPMOD  3.13.0-rc1-00001-g83836a9

chenwx@chenwx ～/linux $ ll ~/arm_mod/
total 12
drwxr-xr-x  3 chenwx chenwx 4096 Dec 10 03:52 .
drwxr-xr-x 36 chenwx chenwx 4096 Dec  9 12:30 ..
drwxr-xr-x  3 chenwx chenwx 4096 Dec 10 03:52 lib

chenwx@chenwx ～/linux $ ll ~/arm_mod/lib/
total 12
drwxr-xr-x 3 chenwx chenwx 4096 Dec 10 03:52 .
drwxr-xr-x 3 chenwx chenwx 4096 Dec 10 03:52 ..
drwxr-xr-x 3 chenwx chenwx 4096 Dec 10 03:52 modules

chenwx@chenwx ～/linux $ ll ~/arm_mod/lib/modules/
total 12
drwxr-xr-x 3 chenwx chenwx 4096 Dec 10 03:52 .
drwxr-xr-x 3 chenwx chenwx 4096 Dec 10 03:52 ..
drwxr-xr-x 3 chenwx chenwx 4096 Dec 10 03:53 3.13.0-rc1-00001-g83836a9

chenwx@chenwx ～/linux $ ll ~/arm_mod/lib/modules/3.13.0-rc1-00001-g83836a9/
total 88
drwxr-xr-x 3 chenwx chenwx 4096 Dec 10 03:53 .
drwxr-xr-x 3 chenwx chenwx 4096 Dec 10 03:52 ..
lrwxrwxrwx 1 chenwx chenwx   33 Dec 10 03:52 build -> /usr/src/linuxkernel/linux-stable
drwxr-xr-x 5 chenwx chenwx 4096 Dec 10 03:53 kernel
-rw-r--r-- 1 chenwx chenwx  615 Dec 10 03:53 modules.alias
-rw-r--r-- 1 chenwx chenwx  946 Dec 10 03:53 modules.alias.bin
-rw-r--r-- 1 chenwx chenwx 1929 Dec 10 03:52 modules.builtin
-rw-r--r-- 1 chenwx chenwx 2526 Dec 10 03:53 modules.builtin.bin
-rw-r--r-- 1 chenwx chenwx   69 Dec 10 03:53 modules.ccwmap
-rw-r--r-- 1 chenwx chenwx 1295 Dec 10 03:53 modules.dep
-rw-r--r-- 1 chenwx chenwx 2892 Dec 10 03:53 modules.dep.bin
-rw-r--r-- 1 chenwx chenwx   52 Dec 10 03:53 modules.devname
-rw-r--r-- 1 chenwx chenwx   73 Dec 10 03:53 modules.ieee1394map
-rw-r--r-- 1 chenwx chenwx  141 Dec 10 03:53 modules.inputmap
-rw-r--r-- 1 chenwx chenwx   81 Dec 10 03:53 modules.isapnpmap
-rw-r--r-- 1 chenwx chenwx   74 Dec 10 03:53 modules.ofmap
-rw-r--r-- 1 chenwx chenwx 1086 Dec 10 03:52 modules.order
-rw-r--r-- 1 chenwx chenwx   99 Dec 10 03:53 modules.pcimap
-rw-r--r-- 1 chenwx chenwx   43 Dec 10 03:53 modules.seriomap
-rw-r--r-- 1 chenwx chenwx  131 Dec 10 03:53 modules.softdep
-rw-r--r-- 1 chenwx chenwx 2755 Dec 10 03:53 modules.symbols
-rw-r--r-- 1 chenwx chenwx 3571 Dec 10 03:53 modules.symbols.bin
-rw-r--r-- 1 chenwx chenwx  189 Dec 10 03:53 modules.usbmap
lrwxrwxrwx 1 chenwx chenwx   33 Dec 10 03:52 source -> /usr/src/linuxkernel/linux-stable

chenwx@chenwx ～/linux $ ll ~/arm_mod/lib/modules/3.13.0-rc1-00001-g83836a9/kernel/
total 20
drwxr-xr-x 5 chenwx chenwx 4096 Dec 10 03:53 .
drwxr-xr-x 3 chenwx chenwx 4096 Dec 10 03:53 ..
drwxr-xr-x 3 chenwx chenwx 4096 Dec 10 03:52 arch
drwxr-xr-x 2 chenwx chenwx 4096 Dec 10 03:53 crypto
drwxr-xr-x 4 chenwx chenwx 4096 Dec 10 03:53 fs
```

### 3.4.6 Export Header Files

Refer to following documentations:

* http://crashcourse.ca/introduction-linux-kernel-programming/intermission-lets-talk-about-header-files-free-lesson
* [Kernel Headers](/docs/Kernel_Headers.pdf)
* [Header Files](/docs/Header_files.pdf)

If you want to do kernel programming, there is a package corresponding to each running kernel that you can install that provides the kernel space header files against which you can compile your loadable modules so that you don't even need a full kernel source tree.

There are times when you're programming for user space but you need header files that define kernel space structures since you're going to be defining a structure that you want to pass into kernel space, almost certainly via a system call, and you need to get a declaration for that structure somewhere, which leads us to introduce a third type of header file -- the kind that are relevant for both kernel and user space.

Such header files are carefully selected from the header files in the kernel source tree, they're "cleaned" (using a process that will be explained shortly), and they're bundled into yet another package that you'll see in a minute. So ... where do these header files come from? At the top of your kernel source tree, simply run:

```
chenwx@chenwx ~/linux $ make distclean       [optional]
chenwx@chenwx ~/linux $ make headers_install
```

at which point a carefully selected subset of the kernel header files scattered around the tree are collected, sanitized and placed carefully under the kernel source tree directory usr/include/, where you can examine them with:

```
chenwx@chenwx ~/linux $ find usr/include/ | more
usr/include/
usr/include/asm
usr/include/asm/ptrace-abi.h
usr/include/asm/types.h
usr/include/asm/auxvec.h
usr/include/asm/siginfo.h
usr/include/asm/bootparam.h
usr/include/asm/unistd_64.h
usr/include/asm/mman.h
usr/include/asm/hyperv.h
usr/include/asm/perf_regs.h
usr/include/asm/svm.h
usr/include/asm/shmbuf.h
usr/include/asm/fcntl.h
usr/include/asm/unistd.h
usr/include/asm/swab.h
usr/include/asm/stat.h
...
```

What you're looking at in the output above is the collection of kernel header files that are also deemed to be appropriate for user space programmers who want to, perhaps, define structures that they will be passing to kernel code. More to the point, these header files have already been packaged for you and are almost certainly already on your system. In the case of Ubuntu 10.04, this would be the linux-libc-dev package:

```
chenwx@chenwx ~/linux $ dpkg -L linux-libc-dev | more
/.
/usr
/usr/include
/usr/include/asm
/usr/include/asm/ptrace-abi.h
/usr/include/asm/types.h
/usr/include/asm/auxvec.h
/usr/include/asm/siginfo.h
/usr/include/asm/bootparam.h
/usr/include/asm/unistd_64.h
/usr/include/asm/mman.h
/usr/include/asm/hyperv.h
/usr/include/asm/perf_regs.h
/usr/include/asm/svm.h
/usr/include/asm/shmbuf.h
/usr/include/asm/fcntl.h
/usr/include/asm/unistd.h
/usr/include/asm/swab.h
...
```

#### 3.4.6.1 Who decides which kernel header files are exported?

When you run "make headers_install" from the top of your kernel source tree, who or what decides precisely which kernel header files will get bundled up and stashed under the kernel source directory usr/include/ for later "exporting" to user space? That's easy.

The header files to be exported are defined by the Kbuild files scattered throughout the kernel source tree. The one at the very top level is the engine, while elsewhere throughout the tree, you'll find Kbuild files like, say, this one:

```
chenwx@chenwx ~/linux $ cat include/uapi/Kbuild
# UAPI Header export list
# Top-level Makefile calls into asm-$(ARCH)
# List only non-arch directories below

header-y += asm-generic/
header-y += linux/
header-y += sound/
header-y += mtd/
header-y += rdma/
header-y += video/
header-y += drm/
header-y += xen/
header-y += scsi/
header-y += misc/
```

That file simply defines that the export process should recursively continue into those subdirectories and keep checking for more Kbuild files. If we check further, we'll start to see Kbuild files like:

```
chenwx@chenwx ~/linux $ cat include/uapi/linux/Kbuild
# UAPI Header export list
header-y += android/
header-y += byteorder/
header-y += can/
header-y += caif/
header-y += dvb/
header-y += hdlc/
header-y += hsi/
header-y += iio/
header-y += isdn/
...
header-y += acct.h
header-y += adb.h
header-y += adfs_fs.h
header-y += affs_hardblocks.h
header-y += agpgart.h
header-y += aio_abi.h
header-y += am437x-vpfe.h
header-y += apm_bios.h
header-y += arcfb.h
header-y += atalk.h
header-y += atmapi.h
...
```

which clearly represents a combination of more recursive directories, plus immediate header files. Quite simply, all kernel Kbuild files have that general structure and, collectively (throughout the entire kernel source tree), they define all of the kernel header files to be exported to user space.

#### 3.4.6.2 What does it mean to "sanitize" one of those header files?

In many cases, the header files to be exported contain some content that is meaningful only in kernel space, and it's only a subset of the header file that needs to be exported. Kernel-only code is normally surrounded by a preprocessor conditional that checks the value of the ```__KERNEL__``` macro, and part of the the job of the export process (when you run make headers_install) is to examine each file that is being exported, identify the code that is relevant only in kernel space, and remove it. Quite simple, really.

That's why (for example) the kernel version of the header file include/video/edid.h looks like this:

```
#ifndef __linux_video_edid_h__
#define __linux_video_edid_h__

#if !defined(__KERNEL__) || defined(CONFIG_X86)

struct edid_info {
        unsigned char dummy[128];
};

#ifdef __KERNEL__
extern struct edid_info edid_info;
#endif /* __KERNEL__ */

#endif

#endif /* __linux_video_edid_h__ */
```

but by the time it ends up in user space and is placed at /usr/include/video/edid.h, it looks like this:

```
#ifndef __linux_video_edid_h__
#define __linux_video_edid_h__

struct edid_info {
	unsigned char dummy[128];
};

#endif /* __linux_video_edid_h__ */
```

Technically, there's no actual harm in leaving in that kernel-only content since, when you're compiling in user space, you're guaranteed that the preprocessor macro ```__KERNEL__``` will never be set, but it's cleaner to just strip out that irrelevant content during the export process.

NOTE: If you look carefully, you'll notice that many of the Kbuild files contain both the variables header-y and unifdef-y to identify the header files to be sanitized and exported. The latter is now deprecated and Kbuild files should now contain only the first form, but the older form is still supported.

#### 3.4.6.3 Installation of Linux API Headers in LFS

参见[Linux-4.18.5 API Headers (online)](http://www.linuxfromscratch.org/lfs/view/stable-systemd/chapter06/linux-headers.html) and [Linux-4.18.5 API Headers (local pdf)](/docs/Linux-4.18.5_API_Headers.pdf)。

The following steps show the installation of Linux API headers in Linux From Scratch (LFS):

```
chenwx@chenwx ~/linux $ mkdir ../linux-header
chenwx@chenwx ~/linux $ make INSTALL_HDR_PATH=../linux-header/ headers_install
  CHK     include/generated/uapi/linux/version.h
  INSTALL include/asm-generic (35 files)
  INSTALL include/drm (21 files)
  INSTALL include/linux/android (1 file)
  INSTALL include/linux/byteorder (2 files)
  INSTALL include/linux/caif (2 files)
  INSTALL include/linux/can (5 files)
  INSTALL include/linux/dvb (8 files)
  INSTALL include/linux/hdlc (1 file)
  INSTALL include/linux/hsi (2 files)
  INSTALL include/linux/iio (2 files)
  INSTALL include/linux/isdn (1 file)
  INSTALL include/linux/mmc (1 file)
  INSTALL include/linux/netfilter/ipset (4 files)
  INSTALL include/linux/netfilter (86 files)
  INSTALL include/linux/netfilter_arp (2 files)
  INSTALL include/linux/netfilter_bridge (17 files)
  INSTALL include/linux/netfilter_ipv4 (9 files)
  INSTALL include/linux/netfilter_ipv6 (12 files)
  INSTALL include/linux/nfsd (5 files)
  INSTALL include/linux/raid (2 files)
  INSTALL include/linux/spi (1 file)
  INSTALL include/linux/sunrpc (1 file)
  INSTALL include/linux/tc_act (12 files)
  INSTALL include/linux/tc_ematch (4 files)
  INSTALL include/linux/usb (11 files)
  INSTALL include/linux/wimax (1 file)
  INSTALL include/linux (436 files)
  INSTALL include/misc (1 file)
  INSTALL include/mtd (5 files)
  INSTALL include/rdma/hfi (1 file)
  INSTALL include/rdma (14 files)
  INSTALL include/scsi/fc (4 files)
  INSTALL include/scsi (4 files)
  INSTALL include/sound (15 files)
  INSTALL include/video (3 files)
  INSTALL include/xen (4 files)
  INSTALL include/uapi (0 file)
  INSTALL include/asm (65 files)

// Remove .install and ..install.cmd files
chenwx@chenwx ~/linux $ find ../linux-header/include \( -name .install -o -name ..install.cmd \) -delete

// Install the linux headers to /usr/include directory
chenwx@chenwx ~/linux $ cp -rv ../linux-header/include/* /usr/include

// Check the generated linux headers
chenwx@chenwx ~/linux $ ll ../linux-header/
drwxrwxr-x 14 chenwx chenwx 4.0K Oct 23 17:23 include
chenwx@chenwx ~/linux $ ll ../linux-header/include/
drwxrwxr-x  2 chenwx chenwx 4.0K Oct 23 17:24 asm
drwxrwxr-x  2 chenwx chenwx 4.0K Oct 23 17:24 asm-generic
drwxrwxr-x  2 chenwx chenwx 4.0K Oct 23 17:24 drm
drwxrwxr-x 25 chenwx chenwx  20K Oct 23 17:24 linux
drwxrwxr-x  2 chenwx chenwx 4.0K Oct 23 17:24 misc
drwxrwxr-x  2 chenwx chenwx 4.0K Oct 23 17:24 mtd
drwxrwxr-x  3 chenwx chenwx 4.0K Oct 23 17:24 rdma
drwxrwxr-x  3 chenwx chenwx 4.0K Oct 23 17:24 scsi
drwxrwxr-x  2 chenwx chenwx 4.0K Oct 23 17:24 sound
drwxrwxr-x  2 chenwx chenwx 4.0K Oct 23 17:24 uapi
drwxrwxr-x  2 chenwx chenwx 4.0K Oct 23 17:24 video
drwxrwxr-x  2 chenwx chenwx 4.0K Oct 23 17:24 xen
chenwx@chenwx ~/linux $ find ../linux-header/include/ -type f | wc -l
797
```

## 3.5 内核升级

### 3.5.1 内核升级准备

#### 3.5.1.1 查看当前系统内核版本

It is easy to tell if you are running a distribution kernel. Unless you downloaded, compiled and installed your own version of kernel from [kernel.org](https://www.kernel.org/), you are running a distribution kernel. To find out the version of your kernel, run ```uname -r```:

```
chenwx ～ $ uname -r
3.5.0-17-generic
```

[**NOTE**] If you see anything at all after the dash, you are running a distribution kernel. Please use the support channels offered by your distribution vendor to obtain kernel support.

也可通过下列命令查看内核版本:

```
chenwx ～ $ cat /proc/version
Linux version 3.15.0-eudyptula-00054-g783e9e8-dirty (chenwx@chenwx) (gcc version 4.8.1 (Ubuntu/Linaro 4.8.1-10ubuntu8) ) #3 SMP Fri May 9 07:56:01 CST 2014
```

### 3.5.2 获取新版本内核源代码

#### 3.5.2.1 通过Git Repository下载源代码

1) Download source code from linux.git to directory ~/linux

```
$ cd ~
$ git clone git://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git
```

2) Checkout source code of specific git tree

```
$ git branch -a
$ git checkout master
$ git tag -l next-*
$ git checkout next-20150224
```

参见[1.2.5 Setup Linux Kernel Workarea](#1-2-5-setup-linux-kernel-workarea)节。

#### 3.5.2.2 通过源代码压缩包和补丁下载源代码

1) Download source code from [http://www.kernel.org/pub/linux/kernel](http://www.kernel.org/pub/linux/kernel):

```
linux-3.2.tar.bz2        05-Jan-2012 00:40   75M  
linux-3.2.tar.gz         05-Jan-2012 00:40   94M  
linux-3.2.tar.sign       05-Jan-2012 00:40  490
linux-3.2.tar.xz         05-Jan-2012 00:40   62M
```

2) Unzip source code to directory ~/linux:

```
$ cd ~
$ rm -rf linux-3.2				// 删除源代码目录
$ rm -rf linux					// 删除链接目录

$ gzip -cd linux-3.2.tar.gz | tar xvf -		// 生成linux-3.2目录
or,
$ bzip2 -dc linux-3.2.tar.bz2 | tar xvf –	// 生成linux-3.2目录

$ ln -s linux-3.2 linux				// 重新生成linux链接目录
```

Refer to linux/README:

> Do NOT use the /usr/src/linux area! This area has a (usually incomplete) set of kernel headers that are used by the library header files. They should match the library, and not get messed up by whatever the kernel-du-jour happens to be.

3) Apply patches

参见[内核补丁/Patch](#)节。

### 3.5.3 配置内核

执行如下命令配置内核：

```
$ cd ~/linux

// 该命令可确保源代码目录下没有不正确的.o文件
$ make mrproper
$ make clean
$ make distclean

// 参见make config节和make *config节，产生配置输出文件~/linux-build/.config
$ make config O=../linux-build
$ make *config O=../linux-build
or,
$ cp /boot/config-3.15.9-generic ../linux-build/.config
$ make olddefconfig O=../linux-build
$ make menuconfig O=../linux-build

$ make kernelrelease O=../linux-build
3.2.0-chenwx
```

[**NOTE**] It's more convenient to preserve the kernel source untouched and have all the configuration output and compilation results generated in a remote directory.

1) it leaves the source unpolluted by all of those output files, which makes it easier if you want to search the tree using something like grep.

2) it allows you to work with a directory of kernel source for which you have no write access. Perhaps it's a system directory, or in some other user's home directory. As long as you can cd to the top of the source tree and have read access to all of the source, you can generate all of the output elsewhere.

3) it lets you work with multiple configurations and builds simultaneously, since you can simply switch from one output directory to another on the fly, using the same kernel source directory as the basis for all of those builds.

There is only one caution, though. Once you initially select an output directory, you must specify that output directory on every subsequent make invocation, but that should simply be obvious. In fact, the last time I checked, you can't use the remote directory feature if your kernel source tree already shows signs of internal configuration. In short, this feature is meant to be used with a pristine kernel source tree.

在本文中，内核源代码位于```~/linux```目录下，而编译的输出目录位于```～/linux-build```目录下。

### 3.5.4 编译内核

执行如下命令编译内核：

```
# cd ~/linux
# make menuconfig O=../linux-build

// 建立编译时所需的从属文件。
// 注：在linux-3.2中，已经不需要执行该命令了，执行make dep的输出为：
// *** Warning: make dep is unnecessary now.
# make dep

# make O=../linux-build
# make O=../linux-build –j4
```

编译内核，具体过程参见[3.4.1 Makefile的Default Target](#3-4-1-makefile-default-target)节：

* linux-2.6之前版本使用make bzImage命令；
* linux-2.6之后版本可仅使用make命令，相当于之前的make bzImage和make modules命令。

本命令的产生如下输出文件：

```
~/linux-build/vmlinux
~/linux-build/System.map
~/linux-build/arch/x86/boot/bzImage
~/linux-build/arch/i386/boot/bzImage (linked to ~/linux-build/arch/x86/boot/bzImage)
~/linux-build/oneSubDir/twoSubDir/*.ko (modules)
```

### 3.5.5 安装内核

执行如下命令安装内核模块：

```
# sudo make modules_install O=../linux-build
```

可加载模块被安装到/lib/modules/3.2.0/目录下：

```
/lib/modules/3.2.0/source		==链接==>	~/linux-build
/lib/modules/3.2.0/build		==链接==>	~/linux-build
~/linux-build/modules.order		==安装==>	/lib/modules/3.2.0/modules.order
~/linux-build/modules.builtin		==安装==>	/lib/modules/3.2.0/modules.builtin
$(modules)节的输出文件(*.ko)		==安装==>	/lib/modules/3.2.0/kernel/目录
$(mod-fw)节的输出文件(*.fw/*.bin/*.dsp)	==安装==>	/lib/firmware/目录
```

执行如下命令安装新内核：

```
# sudo make install O=../linux-build
```

规则install参见arch/x86/Makefile，其实际执行下列命令：

```
make -f scripts/Makefile.build obj=arch/x86/boot install
sh /home/linux-3.2/arch/x86/boot/install.sh 3.2.0 arch/x86/boot/bzImage System.map "/boot"
```

其中，/home/linux-3.2/arch/x86/boot/install.sh调用下列命令安装内核：

```
/sbin/installkernel 3.2.0 arch/x86/boot/bzImage System.map "/boot"
```

本命令进行如下操作：

```
~/linux-build/arch/x86/boot/bzImage (参见bzImage节)	==安装==>	/boot/vmlinuz-3.2.0-chenwx
~/linux-build/System.map (参见rule_vmlinux__节)		==安装==>	/boot/System.map-3.2.0-chenwx
~/linux-build/.config (参见.config/内核配置结果文件节)	==安装==>	/boot/config-3.2.0-chenwx
```

生成文件：

```
/boot/initrd.img-3.11.0-12-generic
```

#### 3.5.5.1 modules_install

运行```make modules_install```命令，执行顶层Makefile中的modules_install目标：

```
#
# INSTALL_PATH specifies where to place the updated kernel and system map
# images. Default is /boot, but you can set it to other values
export	INSTALL_PATH ?= /boot

#
# INSTALL_DTBS_PATH specifies a prefix for relocations required by build roots.
# Like INSTALL_MOD_PATH, it isn't defined in the Makefile, but can be passed as
# an argument if needed. Otherwise it defaults to the kernel install path
#
export INSTALL_DTBS_PATH ?= $(INSTALL_PATH)/dtbs/$(KERNELRELEASE)

#
# INSTALL_MOD_PATH specifies a prefix to MODLIB for module directory
# relocations required by build roots.  This is not defined in the
# makefile but the argument can be passed to make if needed.
#
MODLIB	= $(INSTALL_MOD_PATH)/lib/modules/$(KERNELRELEASE)
export MODLIB

ifdef CONFIG_MODULES

# Target to install modules
PHONY += modules_install
modules_install: _modinst_ _modinst_post

PHONY += _modinst_
_modinst_:
	@rm -rf $(MODLIB)/kernel
	@rm -f $(MODLIB)/source
	@mkdir -p $(MODLIB)/kernel
	// 创建链接文件/lib/modules/3.2.0/source	至~/linux-build
	@ln -s `cd $(srctree) && /bin/pwd` $(MODLIB)/source
	// 创建链接文件/lib/modules/3.2.0/build至~/linux-build
	@if [ ! $(objtree) -ef  $(MODLIB)/build ]; then \
		rm -f $(MODLIB)/build ; \
		ln -s $(CURDIR) $(MODLIB)/build ; \
	fi
	// 安装文件~/linux-build/modules.order至/lib/modules/3.2.0/modules.order
	@cp -f $(objtree)/modules.order $(MODLIB)/
	// 安装文件~/linux-build/modules.builtin至/lib/modules/3.2.0/modules.builtin
	@cp -f $(objtree)/modules.builtin $(MODLIB)/
	// 执行scripts/Makefile.modinst中的目标__modinst以安装编译好的modules
	$(Q)$(MAKE) -f $(srctree)/scripts/Makefile.modinst

# This depmod is only for convenience to give the initial
# boot a modules.dep even before / is mounted read-write.  However the
# boot script depmod is the master version.
PHONY += _modinst_post
_modinst_post: _modinst_
	// 执行scripts/Makefile.fwinst中的目标__fw_modinst
	$(Q)$(MAKE) -f $(srctree)/scripts/Makefile.fwinst obj=firmware __fw_modinst
	// 参见3.5.5.1.1 cmd_depmod节
	$(call cmd,depmod)

else # CONFIG_MODULES

# Modules not configured
# ---------------------------------------------------------------------------

modules modules_install: FORCE
	@echo >&2
	@echo >&2 "The present kernel configuration has modules disabled."
	@echo >&2 "Type 'make config' and enable loadable module support."
	@echo >&2 "Then build a kernel with module support enabled."
	@echo >&2
	@exit 1

endif # CONFIG_MODULES
```

##### 3.5.5.1.1 cmd_depmod

在顶层Makefile中，包含如下规则：

```
# SHELL used by kbuild
CONFIG_SHELL		:= $(shell if [ -x "$$BASH" ]; then echo $$BASH; \
			     else if [ -x /bin/bash ]; then echo /bin/bash; \
			     else echo sh; fi ; fi)

// 该命令来自module-init-tools或kmod，参见13.3.1 加载/卸载模块的命令节
DEPMOD			= /sbin/depmod

// 获取当前编译的内核版本号，例如: 4.2.0-alex
KERNELRELEASE	= $(shell cat include/config/kernel.release 2> /dev/null)

# Run depmod only if we have System.map and depmod is executable
quiet_cmd_depmod = DEPMOD  $(KERNELRELEASE)
      cmd_depmod = $(CONFIG_SHELL) $(srctree)/scripts/depmod.sh $(DEPMOD) \
                   $(KERNELRELEASE) "$(patsubst y,_,$(CONFIG_HAVE_UNDERSCORE_SYMBOL_PREFIX))"
```

命令cmd_depmod被扩展为:

```
/bin/bash /home/chenwx/linux/scripts/depmod.sh /sbin/depmod 4.2.0-alex
```

其中，命令/sbin/depmod需要首先存在下列目录和文件:

```
/lib/modules/4.2.0-alex/
/lib/modules/4.2.0-alex/modules.builtin
/lib/modules/4.2.0-alex/modules.order
```

然后，命令/sbin/depmod生成下列文件:

```
/lib/modules/4.2.0-alex/modules.alias
/lib/modules/4.2.0-alex/modules.alias.bin
/lib/modules/4.2.0-alex/modules.builtin.bin
/lib/modules/4.2.0-alex/modules.dep
/lib/modules/4.2.0-alex/modules.dep.bin
/lib/modules/4.2.0-alex/modules.devname
/lib/modules/4.2.0-alex/modules.softdep
/lib/modules/4.2.0-alex/modules.symbols
/lib/modules/4.2.0-alex/modules.symbols.bin
```

### 3.5.6 配置引导加载程序GRUB(或LILO)

#### 3.5.6.0 LILO与GRUB的比较

所有引导加载程序都以类似的方式工作，满足共同的目的。不过，LILO和GRUB之间有很多不同之处：

* LILO没有交互式命令界面，而GRUB拥有；
* LILO不支持网络引导，而GRUB支持；
* LILO将关于可以引导的操作系统位置的信息物理上存储在MBR中。如果修改了LILO配置文件，必须将LILO第一阶段引导加载程序重写到MBR。相对于GRUB，这是一个更为危险的选择，因为错误配置的MBR可能会让系统无法引导。使用GRUB，如果配置文件配置错误，则只是默认转到GRUB命令行界面。

#### 3.5.6.1 LILO

编辑LILO的引导配置文件/etc/lilo.conf，添加新内核的启动选项，例如：

```
#
# 主要小节
#
boot=/dev/hda                 # 告诉LILO在哪里安装引导加载程序
map=/boot/map                 # 指向引导期间LILO内部使用的映射文件
install=/boot/boot.b          # 是LILO在引导过程中内部使用的文件之一
message=/boot/message
# 默认启动新内核
default="Linux-3.2"
# 显示启动菜单...
prompt                        # 告诉LILO使用用户界面
# ... 并等候5秒
timeout=50                    # 是引导提示在自动引导默认OS之前的等待时间(以十分之一秒为单位)
#
# 新内核：默认映像
#
image=/boot/vmlinuz-3.2
      label="Linux-3.2"
      root=/dev/hda1
      read-only
      append="devfs=mount resume=/dev/hda5"
#
# 旧内核
# 最好保留旧内核的配置选项，这样不会因升级失败而导致机器无法启动，至少还可用旧内核引导计算机启动
#
image=/boot/vmlinuz
      label="linux"
      root=/dev/hda1
      read-only
      append="devfs=mount resume=/dev/hda5"
#
# 软盘启动
#
other=/dev/floppy
      label="floppy"
      unsafe
```

保存后退出，然后运行命令：

```
# lilo
```

来更新系统引导映象，这样对lilo.conf的修改才会生效。重启操作系统后，在LILO的提示符下按Tab键，可以看到加入的新内核选项。

#### 3.5.6.2 GRUB Legacy

GRUB引导加载程序：

* [http://www.gnu.org/software/grub/grub-legacy.html](http://www.gnu.org/software/grub/grub-legacy.html)
* [ftp://alpha.gnu.org/gnu/grub/](ftp://alpha.gnu.org/gnu/grub/)

GRUB的配置文件保存在/boot/grub中，其目录结构如下：

```
/boot/grub
|-- device.map
|-- menu.lst -> ./grub.conf
|-- grub.conf
|-- grub.conf.201108031656
|-- grub.conf.201210281219
|-- grub.conf.201210281220
|-- grub.conf.ORG_SC
|-- splash.xpm.gz
|-- stage1
|-- e2fs_stage1_5
|-- fat_stage1_5
|-- ffs_stage1_5
|-- iso9660_stage1_5
|-- jfs_stage1_5
|-- minix_stage1_5
|-- reiserfs_stage1_5
|-- ufs2_stage1_5
|-- vstafs_stage1_5
|-- xfs_stage1_5
`-- stage2
```

GRUB有几个重要的文件：stage1、stage1.5、stage2。引导顺序为stage1 -> stage1.5 -> stage2，其中：

* stage1：大小只有512字节，通常放在MBR中，它的作用很简单，就是在系统启动时用于装载stage2并将控制权交给它。
* stage2：GRUB的核心，所有的功能都是由它实现的。
* stage1.5：介于stage1和stage2之间，起桥梁作用，因为stage2较大，通常是放在一个文件系统中的，但是stage1并不能识别文件系统格式，所以需要stage1.5来引导位于某个文件系统中的stage2。根据文件系统格式的不同，stage1.5也需要相应的文件，它们存放于1-63的柱面之间，如：

	* e2fs_stage1_5用于识别ext文件系统格式
	* fat_stage1_5用于识别fat文件系统格式
	* ...

GRUB的引导配置文件为/boot/grub/menu.lst，其链接到./grub.conf，如下所示：

```
# grub.conf generated by anaconda
#
# Note that you do not have to rerun grub after making changes to this file
# NOTICE:  You do not have a /boot partition.  This means that
#          all kernel and initrd paths are relative to /, eg.
#          root (hd0,0)
#          kernel /boot/vmlinuz-version ro root=/dev/cciss/c0d0p1
#          initrd /boot/initrd-version.img
#boot=/dev/cciss/c0d0
default=0
timeout=5
#console.${METH_SUFFIX}#splashimage=(hd0,0)/boot/grub/splash.xpm.gz
hiddenmenu
password  --md5 $1$uQ/fD0$DZpAM1mla6Vauzr2l4rfa0
title Red Hat Enterprise Linux Server (2.6.18-274.18.1.el5)
	root (hd0,0)
	kernel /boot/vmlinuz-2.6.18-274.18.1.el5 ro root=LABEL=/ rhgb quiet apm=off nomce
	initrd /boot/initrd-2.6.18-274.18.1.el5.img
title Red Hat Enterprise Linux Server (2.6.18-194.26.1.el5)
	password  --md5 $1$uQ/fD0$DZpAM1mla6Vauzr2l4rfa0
	root (hd0,0)
	kernel /boot/vmlinuz-2.6.18-194.26.1.el5 ro root=LABEL=/ rhgb quiet apm=off nomce
	initrd /boot/initrd-2.6.18-194.26.1.el5.img
title Infineon Gold Image (2.6.18-164.el5)
	password  --md5 $1$uQ/fD0$DZpAM1mla6Vauzr2l4rfa0
	root (hd0,0)
	kernel /boot/vmlinuz-2.6.18-164.el5 ro root=LABEL=/ rhgb quiet apm=off nomce
	initrd /boot/initrd-2.6.18-164.el5.img
# s+c Service Images start
title Red Hat Enterprise Linux Server (2.6.18-274.18.1.el5) Single User
	password  --md5 $1$uQ/fD0$DZpAM1mla6Vauzr2l4rfa0
	root (hd0,0)
	kernel /boot/vmlinuz-2.6.18-274.18.1.el5 single ro root=LABEL=/ rhgb quiet apm=off nomce
	initrd /boot/initrd-2.6.18-274.18.1.el5.img
title Red Hat E5.0 Rescue System (Network Boot)
	password  --md5 $1$uQ/fD0$DZpAM1mla6Vauzr2l4rfa0
	root (hd0,0)
	kernel /boot/vmlinuz.rhEL5 initrd=/boot/initrd.img rescue root=/dev/nfs ks=nfs:10.216.60.23:/kickstart/rules/netboot.redhatel5 ksdevice=eth0 devfs=nomount ramdisk_size=8192 vga=788 nomce
	initrd /boot/initrd.rhEL5
title Red Hat E5.0 Installer
	password  --md5 $1$uQ/fD0$DZpAM1mla6Vauzr2l4rfa0
	root (hd0,0)
	kernel /boot/vmlinuz.rhEL5 initrd=/boot/initrd.img ks root=/dev/nfs ks=nfs:10.216.60.23:/kickstart/rules/kickstart.redhatel5-x86_64 ksdevice=eth0 devfs=nomount ramdisk_size=8192 vga=788 nomce
	initrd /boot/initrd.rhEL5
# s+c Service Images end
```

其中，

* title表示引导一个操作系统的配置项，可以使多个title项。
* root (hd0,0)用来设置kernel与initrd两项内容的根地址。root指示所需文件存在于哪个磁盘哪个分区上，(hd0,0)表示第一个硬盘，第一个分区，参考/boot/grub/device.map。
* kernel表示内核文件的名字，并且包含一些加载内核时的参数，or代表以只读方式加载。
* initrd是用在内核启动时能够访问的硬盘文件系统，包含一些附加的驱动程序。

编辑/boot/grub/menu.lst并重新启动计算机，就可以看到新添加的内核条目了。

#### 3.5.6.3 GNU GRUB 2

GNU GRUB引导加载程序：

* [http://www.gnu.org/software/grub/grub.html](http://www.gnu.org/software/grub/grub.html)
* [ftp://ftp.gnu.org/gnu/grub/](ftp://ftp.gnu.org/gnu/grub/)
* [ftp://alpha.gnu.org/gnu/grub/](ftp://alpha.gnu.org/gnu/grub/)

**GNU GRUB** is a Multiboot boot loader. It was derived from GRUB, the GRand Unified Bootloader, which was originally designed and implemented by Erich Stefan Boleyn.

**GRUB 2** has replaced what was formerly known as GRUB (i.e. version 0.9x), which has, in turn, become GRUB Legacy. Enhancements to GRUB are still being made, but the current released versions are quite usable for normal operation.

**GRUB Legacy** is no longer being developed.

系统中存在如下有关GRUB 2的命令：

```
chenwx ~ # ll /usr/bin/grub-* /usr/sbin/grub-*
-rwxr-xr-x 1 root root  63456 10月 11  2013 /usr/bin/grub-editenv
-rwxr-xr-x 1 root root 695084 10月 11  2013 /usr/bin/grub-fstest
-rwxr-xr-x 1 root root   1737 10月 11  2013 /usr/bin/grub-kbdcomp
-rwxr-xr-x 1 root root  43216 10月 11  2013 /usr/bin/grub-menulst2cfg
-rwxr-xr-x 1 root root  83552 10月 11  2013 /usr/bin/grub-mkfont
-rwxr-xr-x 1 root root 131380 10月 11  2013 /usr/bin/grub-mkimage
-rwxr-xr-x 1 root root  63488 10月 11  2013 /usr/bin/grub-mklayout
-rwxr-xr-x 1 root root  71844 10月 11  2013 /usr/bin/grub-mkpasswd-pbkdf2
-rwxr-xr-x 1 root root 207120 10月 11  2013 /usr/bin/grub-mkrelpath
-rwxr-xr-x 1 root root  13510 10月 11  2013 /usr/bin/grub-mkrescue
-rwxr-xr-x 1 root root   6387 10月 11  2013 /usr/bin/grub-mkstandalone
-rwxr-xr-x 1 root root 515820 10月 11  2013 /usr/bin/grub-mount
lrwxrwxrwx 1 root root     34  4月 28 20:14 /usr/bin/grub-ntldr-img -> ../lib/grub/i386-pc/grub-ntldr-img
-rwxr-xr-x 1 root root  83744 10月 11  2013 /usr/bin/grub-script-check
lrwxrwxrwx 1 root root     35  4月 28 20:20 /usr/sbin/grub-bios-setup -> ../lib/grub/i386-pc/grub-bios-setup
-rwxr-xr-x 1 root root   1248  5月 14  2013 /usr/sbin/grub-install
-rwxr-xr-x 1 root root  35079 10月 11  2013 /usr/sbin/grub-install.real
-rwxr-xr-x 1 root root   7689 10月 11  2013 /usr/sbin/grub-mkconfig
-rwxr-xr-x 1 root root  38612 10月 11  2013 /usr/sbin/grub-mkdevicemap
-rwxr-xr-x 1 root root   7530 10月 11  2013 /usr/sbin/grub-mknetdir
-rwxr-xr-x 1 root root 780684 10月 11  2013 /usr/sbin/grub-probe
-rwxr-xr-x 1 root root   3933 10月 11  2013 /usr/sbin/grub-reboot
-rwxr-xr-x 1 root root   3442 10月 11  2013 /usr/sbin/grub-set-default

chenwx ~ # ll /usr/bin/update-grub* /usr/sbin/update-grub*
-rwxr-xr-x 1 root root  64 10月  5  2012 /usr/sbin/update-grub
lrwxrwxrwx 1 root root  11  4月 28 20:20 /usr/sbin/update-grub2 -> update-grub
-rwxr-xr-x 1 root root 241  8月 16  2011 /usr/sbin/update-grub-gfxpayload
```

修改配置文件/etc/default/grub:

* 设置内核启动选项earlyprink=vga，把系统早期启动的信息打印到显示屏上
* 注释掉GRUB_HIDDEN_TIMEOUT和GRUB_HIDDEN_TIMEOUT_QUIET
* 设置启动菜单超时时间为10：GRUB_TIMEOUT=10，或者，设置为等待用户选择：GRUB_TIMEOUT=-1

```
#GRUB_HIDDEN_TIMEOUT=0
#GRUB_HIDDEN_TIMEOUT_QUIET=true
GRUB_TIMEOUT=10
GRUB_CMDLINE_LINUX="earlyprink=vga"
```

执行下列命令更新配置文件/boot/grub/grub.cfg:

```
# method (1) to upldate /boot/grub/grub.cfg
chenwx@chenwx /boot $ sudo update-grub
[sudo] password for chenwx:
Generating grub.cfg ...
Found linux image: /boot/vmlinuz-3.11.0-12-generic
Found initrd image: /boot/initrd.img-3.11.0-12-generic
Found memtest86+ image: /boot/memtest86+.bin
  No volume groups found
done

# method (2) to upldate /boot/grub/grub.cfg
chenwx@chenwx /boot $ su
Password:
chenwx boot # grub-mkconfig > /boot/grub/grub.cfg
Generating grub.cfg ...
Found linux image: /boot/vmlinuz-3.15.9-031509-generic
Found initrd image: /boot/initrd.img-3.15.9-031509-generic
Found linux image: /boot/vmlinuz-3.11.0-12-generic
Found initrd image: /boot/initrd.img-3.11.0-12-generic
Found linux image: /boot/vmlinuz-3.2.0
Found initrd image: /boot/initrd.img-3.2.0
Found memtest86+ image: /boot/memtest86+.bin
  No volume groups found
done
```

### 3.5.7 重新启动系统前的准备工作

运行脚本[dmesg_msg_diff.sh](https://github.com/chenweixiang/scripts/blob/master/dmesg_msg_diff.sh)来保存编译内核产生的日志，用于比较和查找错误：

```
$ dmesg -t -l emerg > `uname -r`.dmesg_current_emerg
$ dmesg -t -l alert > `uname -r`.dmesg_current_alert
$ dmesg -t -l crit > `uname -r`.dmesg_current_alert
$ dmesg -t -l err > `uname -r`.dmesg_current_err
$ dmesg -t -l warn > `uname -r`.dmesg_current_warn
$ dmesg -t -k > `uname -r`.dmesg_kernel
$ dmesg -t > `uname -r`.dmesg_current
```

运行脚本[dmesg_msg_save.sh](https://github.com/chenweixiang/scripts/blob/master/dmesg_msg_save.sh)输出日志文件：

```
#!/bin/bash
#
# Copyright(c) Chen Weixiang <weixiang.chen@gmail.com>
#
# License: GPLv2

release=`uname -r`

echo "dmesg -t -l emerg > $release.dmesg_emerg"
dmesg -t -l emerg > $release.dmesg_emerg

echo "dmesg -t -l crit > $release.dmesg_crit"
dmesg -t -l crit > $release.dmesg_crit

echo "dmesg -t -l alert > $release.dmesg_alert"
dmesg -t -l alert > $release.dmesg_alert

echo "dmesg -t -l err > $release.dmesg_err"
dmesg -t -l err > $release.dmesg_err

echo "dmesg -t -l warn > $release.dmesg_warn"
dmesg -t -l warn > $release.dmesg_warn

echo "dmesg -t -k > $release.dmesg_kern"
dmesg -t -k > $release.dmesg_kern

echo "dmesg -t > $release.dmesg"
dmesg -t > $release.dmesg
```

### 3.5.8 重启系统，并验证新内核

重新启动内核后，首先查看新内核的版本信息：

```
/*
 * (1) 查看内核版本信息
 */
chenwx@chenwx ~/linux $ uname -a
Linux chenwx 4.1.5-alex #2 SMP Wed Aug 12 22:53:34 CST 2015 x86_64 x86_64 x86_64 GNU/Linux

chenwx@chenwx ~/linux $ cat /proc/version
Linux version 4.1.5-alex (chenwx@chenwx) (gcc version 4.8.4 (Ubuntu 4.8.4-2ubuntu1~14.04) ) #2 SMP Wed Aug 12 22:53:34 CST 2015

/*
 * (2) 查看头文件信息
 */
chenwx@chenwx ~/linux $ dpkg -L linux-headers-4.1.5-alex
/.
/usr
/usr/src
/usr/src/linux-headers-4.1.5-alex
/usr/src/linux-headers-4.1.5-alex/.config
/usr/src/linux-headers-4.1.5-alex/Module.symvers
/usr/src/linux-headers-4.1.5-alex/init
/usr/src/linux-headers-4.1.5-alex/init/Makefile
/usr/src/linux-headers-4.1.5-alex/init/Kconfig
/usr/src/linux-headers-4.1.5-alex/samples
/usr/src/linux-headers-4.1.5-alex/samples/kdb
/usr/src/linux-headers-4.1.5-alex/samples/kdb/Makefile
/usr/src/linux-headers-4.1.5-alex/samples/hw_breakpoint
...
/usr/src/linux-headers-4.1.5-alex/Kconfig
/usr/share
/usr/share/doc
/usr/share/doc/linux-headers-4.1.5-alex
/usr/share/doc/linux-headers-4.1.5-alex/changelog.Debian.gz
/usr/share/doc/linux-headers-4.1.5-alex/copyright
/lib
/lib/modules
/lib/modules/4.1.5-alex
/lib/modules/4.1.5-alex/build
```

然后，比较新老内核的dmesg信息，看看新的内核有没有编译错误。

使用dmesg查看隐藏的问题，对于定位新代码带来的bug是一个好方法。一般来说，dmesg不会输出新的crit, alert, emerg级别的错误信息，也不应该出现新的err级别的信息。要注意的是那些warn级别的日志信息。注意：warn级别的信息并不是坏消息，新代码带来新的警告信息，不会给内核带去严重的影响。

运行脚本[dmesg_msg_save.sh](https://github.com/chenweixiang/scripts/blob/master/dmesg_msg_save.sh)输出日志文件：

```
#!/bin/bash
#
# Copyright(c) Chen Weixiang <weixiang.chen@gmail.com>
#
# License: GPLv2

if [ "$1" == "" ]; then
        echo "$0 <old uname -r>"
        exit -1
fi

release=`uname -r`

echo "Start dmesg regression check for $release" > dmesg_checks_results

echo "--------------------------" >> dmesg_checks_results

dmesg -t -l emerg > $release.dmesg_emerg
echo "dmesg emergency regressions"
echo "--------------------------" >> dmesg_checks_results
diff $1.dmesg_emerg $release.dmesg_emerg >> dmesg_checks_results
echo "--------------------------" >> dmesg_checks_results

dmesg -t -l crit > $release.dmesg_crit
echo "dmesg critical regressions"
echo "--------------------------" >> dmesg_checks_results
diff $1.dmesg_crit $release.dmesg_crit >> dmesg_checks_results
echo "--------------------------" >> dmesg_checks_results

dmesg -t -l alert > $release.dmesg_alert
echo "dmesg alert regressions" >> dmesg_checks_results
echo "--------------------------" >> dmesg_checks_results
diff $1.dmesg_alert $release.dmesg_alert >> dmesg_checks_results
echo "--------------------------" >> dmesg_checks_results

dmesg -t -l err > $release.dmesg_err
echo "dmesg err regressions" >> dmesg_checks_results
echo "--------------------------" >> dmesg_checks_results
diff $1.dmesg_err $release.dmesg_err >> dmesg_checks_results
echo "--------------------------" >> dmesg_checks_results

dmesg -t -l warn > $release.dmesg_warn
echo "dmesg warn regressions" >> dmesg_checks_results
echo "--------------------------" >> dmesg_checks_results
diff $1.dmesg_warn $release.dmesg_warn >> dmesg_checks_results
echo "--------------------------" >> dmesg_checks_results

dmesg -t -k > $release.dmesg_kern
echo "dmesg_kern regressions" >> dmesg_checks_results
echo "--------------------------" >> dmesg_checks_results
diff $1.dmesg_kern $release.dmesg_kern >> dmesg_checks_results
echo "--------------------------" >> dmesg_checks_results

dmesg -t > $release.dmesg
echo "dmesg regressions" >> dmesg_checks_results
echo "dmesg regressions" >> dmesg_checks_results
echo "--------------------------" >> dmesg_checks_results
diff $1.dmesg $release.dmesg >> dmesg_checks_results
echo "--------------------------" >> dmesg_checks_results

echo "--------------------------" >> dmesg_checks_results

echo "End dmesg regression check for $release" >> dmesg_checks_results
```

运行脚本[dmesg_msg_diff.sh](https://github.com/chenweixiang/scripts/blob/master/dmesg_msg_diff.sh)来对比两者的差别：

```
dmesg -t -l emerg > `uname -r`.dmesg_current_emerg
dmesg -t -l alert > `uname -r`.dmesg_current_alert
dmesg -t -l crit > `uname -r`.dmesg_current_alert
dmesg -t -l err > `uname -r`.dmesg_current_err
dmesg -t -l warn > `uname -r`.dmesg_current_warn
dmesg -t -k > `uname -r`.dmesg_kernel
dmesg -t > `uname -r`.dmesg_current
```

## 3.5A 采用make deb-pkg为Debian系统升级内核

[**NOTE**] Thinkpad R61i采用LinuxMint系统，故使用此方法升级内核更方便，简单！

除了采用[3.5 内核升级](#)节所述的方法升级内核外，也可以采用```make deb-pkg```命令编译内核并为Debian系统升级内核：

```
# 安装必要工具包(仅需执行一次)
chenwx@chenwx ~/linux $ sudo apt-get install kernel-package

# 选择内核版本
chenwx@chenwx ~/linux $ git checkout v4.0.1

# 清空内核目录
chenwx@chenwx ~/linux $ make mrproper
chenwx@chenwx ~/linux $ make mrproper O=../linux-build
chenwx@chenwx ~/linux $ make-kpkg clean
chenwx@chenwx ~/linux $ make-kpkg clean O=../linux-build

# 配置内核
chenwx@chenwx ~/linux $ cp /boot/config-3.13.0-24-generic ../linux-build/.config
chenwx@chenwx ~/linux $ make olddefconfig O=../linux-build

# 清空内核目录，并将内核编译成deb包，其中:
# LOCALVERSION:    等价于顶层Makefile中的EXTRAVERSION
# KDEB_PKGVERSION: 用于命名软件包；若未设置该变量，则采用../linux-build/.version中的取值(该值会递增)
chenwx@chenwx ~/linux $ make deb-pkg O=../linux-build LOCALVERSION=-alex KDEB_PKGVERSION=1
  CHK     include/config/kernel.release
  UPD     include/config/kernel.release
make KBUILD_SRC=
  HOSTCC  scripts/basic/fixdep
  HOSTCC  arch/x86/tools/relocs_32.o
  HOSTCC  arch/x86/tools/relocs_64.o
  HOSTCC  arch/x86/tools/relocs_common.o
  HOSTLD  arch/x86/tools/relocs
  ...

# 编译后的deb包位与上级目录，其命名格式为:
# linux-<PACKAGETYPE>-<VERSION.PATCHLEVEL.SUBLEVEL><LOCALVERSION>_<KDEB_PKGVERSION>_<CPUTYPE>.deb
chenwx@chenwx ~/linux $ ll ../linux-*
-rw-r--r--  1 chenwx chenwx 945K Jun 11 21:03 linux-firmware-image-4.0.1-alex_1_amd64.deb
-rw-r--r--  1 chenwx chenwx 6.7M Jun 11 21:04 linux-headers-4.0.1-alex_1_amd64.deb
-rw-r--r--  1 chenwx chenwx 355M Jun 11 21:43 linux-image-4.0.1-alex-dbg_1_amd64.deb
-rw-r--r--  1 chenwx chenwx  38M Jun 11 21:06 linux-image-4.0.1-alex_1_amd64.deb
-rw-r--r--  1 chenwx chenwx 781K Jun 11 21:04 linux-libc-dev_1_amd64.deb

# 安装新内核
chenwx@chenwx ~/linux $ sudo dpkg -i ../*.deb

# 重启系统，并验证新内核
chenwx@chenwx ~/linux $ uname -a
Linux chenwx 4.0.1-alex #5 SMP Tue May 5 07:01:44 CST 2015 x86_64 x86_64 x86_64 GNU/Linux
```

## 3.5B 采用make-kpkg编译并升级内核

```
NAME
       make-kpkg - build Debian kernel packages from Linux kernel sources

SYNOPSIS
       make-kpkg [options] [target [target ...]]

DESCRIPTION
       This manual page explains the Debian make-kpkg utility, which is used to create the kernel
       related Debian packages. This utility needs to be run from a top level Linux kernel source
       directory, which has been previously configured (unless you are using the configure target).
       Normally, if kernel-package does not find a .config file in the current directory, it tries
       very hard to get an appropriate one (usually a config file already tailored for Debian
       kernels for that architecture), and then calls make oldconfig to let the user answer any new
       questions. However, this might still result in an inappropriate configuration, you are
       encouraged to configure the kernel by the usual means before invoking make-kpkg.

       Typically, make-kpkg should be run under fakeroot,

            make-kpkg --rootcmd fakeroot kernel_image

       but instead you run this command as root (this is not recommended), or under fakeroot, or
       tell make-kpkg how to become root (not recommended either, fakeroot is perhaps the safest
       option), like so:

            make-kpkg --rootcmd sudo kernel_image

       The Debian package file is created in the parent directory of the kernel source directory
       where this command is run.

Refer to the following commands:
chenwx@chenwx ~/linux $ sudo apt-get install kernel-package
chenwx@chenwx ~/linux $ which make-kpkg
/usr/bin/make-kpkg

chenwx@chenwx ~/linux $ make-kpkg --help
This program should be run in a linux kernel source top level directory.
/usr/share/doc/kernel-package/Problems.gz contains a list of known problems.

usage: make-kpkg [options] target [target ...]
  where options are:
 --help                This message.
 --revision number     The debian revision number. ([a-zA-Z.~+0-9]) (Must
                         have digit)
 --append-to-version foo
 --append_to_version foo an additional kernel sub-version. ([-a-z.+0-9])
                         Does not require editing the kernel Makefile
                         over rides env var APPEND_TO_VERSION.
                         requires a make-kpkg clean
 --added-modules foo
 --added_modules foo   Comma/space separated list of add on modules
                       affected by the modules_<blah> targets
 --arch     foo        architecture
 --cross-compile
 --cross_compile       target string
 --subarch  bar        Set the subarch for the image being compiled
                        (have to be on a compatible machine).
 --arch-in-name
 --arch_in_name        Embed the subarch in the image package name
 --stem     foo        Call the packages foo-* instead of kernel-*
 --initrd              Create a image package suitable for initrd.
 -j         jobs       Sec CONCURRENCY_LEVEL to -I<jobs> for this action.
 --jobs     jobs       Set CONCURRENCY_LEVEL to -I<jobs> for this action.
 --pgpsign  name       An ID used to sign the changes file using pgp.
 --config target       Change the type of configure done from the  default
                       oldconfig.
 --targets             Lists the known targets.
 --noexec              Pass a -n option to the make process
 --overlay dir         An overlay directory to (re))place file in ./debian
 --verbose             Pass a V=1 option to the make process
 --zimage              Create a kernel using zImage rather than bzImage
 --bzimage             Create a kernel using bzImage (in case the site
                       wide default is zimage, as set in
                       /etc/kernel-pkg.conf)
 --rootcmd method      A command that provides a means of gaining
                       superuser access (for example, `sudo' or
                       `fakeroot') as needed by dpkg-buildpackages'
                       -r option. Does not work for targets binary,
                        binary-indep,  and  binary-arch.
 --us                  This option is passed to dpkg-buildpackage, and
                       directs that package not to sign the
                       source. This is only relevant for the
                       buildpackage target.
 --uc                  This option is passed to dpkg-buildpackage, and
                       directs that package not to sign the
                       changelog. This is only relevant for the
                       buildpackage target.

Use one of --zimage or --bzimage, or none, but not both.

Option Format: The options may be shortened to the smallest unique
string, and may be entered with either a - or a -- prefix, and you may
use a space between an option string and a value. Please refer to man
Getopt::Long for details on how the options may be entered.

Version: 12.036+nmu3
Manoj Srivastava <srivasta@debian.org>

chenwx@chenwx ~/linux $ make-kpkg --targets
 Known Targets are:
===============================================================================
|     Targets                      |   Automatically builds                   |
===============================================================================
|  clean                           |                                          |
|  buildpackage                    | Builds the whole package                 |
|* binary                          | Builds kernel_{source,headers,image,doc} |
|*      binary-indep               |                                          |
|            kernel_source         |                                          |
|            kernel_doc            |                                          |
|            kernel_manual         |                                          |
|*      binary-arch                |                                          |
|            kernel_headers        |                                          |
|            kernel_debug          |                                          |
|            kernel_image          | Builds build                             |
|                           build  |                                          |
| modules                          |                                          |
| modules_image                    |                                          |
| modules_config                   |                                          |
| modules_clean                    |                                          |
| configure                        | If you wish to edit files                |
|                           debian | generated by make config                 |
| debian                           | Creates ./debian dir                     |
===============================================================================
 *: make-kpkg needs to be run as root (or fakeroot), --rootcmd will not work
See /usr/share/kernel-package/rules for details.

chenwx@chenwx ~/linux $ fakeroot make-kpkg --initrd --append-to-version -alex kernel_image kernel_headers
```

## 3.6 内核补丁/Patch

参见Documentation/applying-patches.txt关于内核补丁的描述：

> A patch is a small text document containing a delta of changes between two different versions of a source tree. Patches are created with the 'diff' program.
>
> To correctly apply a patch you need to know what base it was generated from and what new version the patch will change the source tree into. These should both be present in the patch file metadata or be possible to deduce from the filename.

### 3.6.1 内核补丁的下载地址

The patches are available at:
* [http://kernel.org/](http://kernel.org/)
* [ftp://www.kernel.org/pub/linux/kernel/v3.x/](ftp://www.kernel.org/pub/linux/kernel/v3.x/)

Most recent patches are linked from the front page, but they also have specific homes:

**The v2.6 and v3.x patches live at**

* [ftp://ftp.kernel.org/pub/linux/kernel/v2.6/](ftp://ftp.kernel.org/pub/linux/kernel/v2.6/)
* [ftp://ftp.kernel.org/pub/linux/kernel/v3.x/](ftp://ftp.kernel.org/pub/linux/kernel/v3.x/)

**The v2.6 and v3.x -rc patches live at**

* [ftp://ftp.kernel.org/pub/linux/kernel/v2.6/testing/](ftp://ftp.kernel.org/pub/linux/kernel/v2.6/testing/)
* [ftp://ftp.kernel.org/pub/linux/kernel/v3.x/testing/](ftp://ftp.kernel.org/pub/linux/kernel/v3.x/testing/)

**The v2.6 and v3.x -git patches live at**

* [ftp://ftp.kernel.org/pub/linux/kernel/v2.6/snapshots/](ftp://ftp.kernel.org/pub/linux/kernel/v2.6/snapshots/)
* [ftp://ftp.kernel.org/pub/linux/kernel/v3.x/incr/](ftp://ftp.kernel.org/pub/linux/kernel/v3.x/incr/)

**The v2.6 and v3.x -mm kernels live at**

* [ftp://ftp.kernel.org/pub/linux/kernel/people/akpm/patches/2.6/](ftp://ftp.kernel.org/pub/linux/kernel/people/akpm/patches/2.6/)

### 3.6.2 如何应用内核补丁

注意：安装内核补丁前，需要先进入老版本的内核源代码目录，以linux-3.2为例：

```
# cd ~/linux-3.2
```

Uncompress and apply the patch:

```
# zcat path/to/patch-x.y.z.gz | patch -p1
# bzcat path/to/patch-x.y.z.bz2 | patch -p1
# xzcat path/to/patch-x.y.z.xz | patch -p1
```

Uncompress the patch:

```
# gunzip patch-x.y.z.gz		// 解压后的patch为patch-x.y.z
# bunzip2 patch-x.y.z.bz2		// 解压后的patch为patch-x.y.z
# unxz patch-x.y.z.xz		// 解压后的patch为patch-x.y.z
```

Apply a uncompressed patch:

```
# patch -p1 < path/to/patch-x.y.z
```

Revert (undo) a patch:

```
# patch -p1 -R < path/to/patch-x.y.z
```

Patch can get the name of the file to use via the -i argument:

```
# patch -p1 -i path/to/patch-x.y.z
```

Just print a listing of what would happen, but doesn't actually make any changes:

```
# patch -p1 --dry-run path/to/patch-x.y.z
```

Make patch to be silent except for errors:

```
# patch -p1 -s path/to/patch-x.y.z
```

Print more information about the work being done:

```
# patch -p1 --verbose path/to/patch-x.y.z
```

#### 3.6.2.1 The 2.6.x.y kernels

Kernels with 4-digit versions are -stable kernels.

These patches are not incremental, meaning that, for example the 2.6.12.3 patch does not apply on top of the 2.6.12.2 kernel source, but rather on top of the base 2.6.12 kernel source. So, in order to apply the 2.6.12.3 patch to your existing 2.6.12.2 kernel source, you have to first back out the 2.6.12.2 patch (so you are left with a base 2.6.12 kernel source) and then apply the new 2.6.12.3 patch. For example:


```
$ cd ~/linux-2.6.12.2					# change into the kernel source dir
$ patch -p1 -R < ../patch-2.6.12.2		# revert the 2.6.12.2 patch
$ patch -p1 < ../patch-2.6.12.3			# apply the new 2.6.12.3 patch
$ cd ..
$ mv linux-2.6.12.2 linux-2.6.12.3		# rename the kernel source dir
```

#### 3.6.2.2 The -rc kernels

These are release-candidate kernels (not stable). The -rc patches are not incremental, they apply to a base 2.6.x kernel, just like the 2.6.x.y patches described above (See section The 2.6.x.y kernels).

The kernel version before the –rcN suffix denotes the version of the kernel that this -rc kernel will eventually turn into. So, 2.6.13-rc5 means that this is the fifth release candidate for the 2.6.13 kernel and the patch should be applied on top of the 2.6.12 kernel source.

Here are 3 examples of how to apply these patches:

```
# First an example of moving from 2.6.12 to 2.6.13-rc3
$ cd ~/linux-2.6.12					# change into the 2.6.12 source dir
$ patch -p1 < ../patch-2.6.13-rc3		# apply the 2.6.13-rc3 patch
$ cd ..
$ mv linux-2.6.12 linux-2.6.13-rc3		# rename the source dir

# Now let's move from 2.6.13-rc3 to 2.6.13-rc5
$ cd ~/linux-2.6.13-rc3				# change into the 2.6.13-rc3 dir
$ patch -p1 -R < ../patch-2.6.13-rc3		# revert the 2.6.13-rc3 patch
$ patch -p1 < ../patch-2.6.13-rc5		# apply the new 2.6.13-rc5 patch
$ cd ..
$ mv linux-2.6.13-rc3 linux-2.6.13-rc5	# rename the source dir

# Finally let's try and move from 2.6.12.3 to 2.6.13-rc5
$ cd ~/linux-2.6.12.3					# change to the kernel source dir
$ patch -p1 -R < ../patch-2.6.12.3		# revert the 2.6.12.3 patch
$ patch -p1 < ../patch-2.6.13-rc5		# apply new 2.6.13-rc5 patch
$ cd ..
$ mv linux-2.6.12.3 linux-2.6.13-rc5		# rename the kernel source dir
```

#### 3.6.2.3 The -git kernels

These are daily snapshots of Linus' kernel tree. These patches are usually released daily and represent the current state of Linus's tree. They are more experimental than -rc kernels since they are generated automatically without even a cursory glance to see if they are sane.

-git patches are not incremental and apply either to a base 2.6.x kernel or a base 2.6.x-rc kernel -- you can see which from their name. A patch named 2.6.12-git1 applies to the 2.6.12 kernel source and a patch named 2.6.13-rc3-git2 applies to the source of the 2.6.13-rc3 kernel.

Here are some examples of how to apply these patches:

```
# moving from 2.6.12 to 2.6.12-git1
$ cd ~/linux-2.6.12						# change to the kernel source dir
$ patch -p1 < ../patch-2.6.12-git1			# apply the 2.6.12-git1 patch
$ cd ..
$ mv linux-2.6.12 linux-2.6.12-git1			# rename the kernel source dir

// moving from 2.6.12-git1 to 2.6.13-rc2-git3
$ cd ~/linux-2.6.12-git1					# change to the kernel source dir
// revert the 2.6.12-git1 patch. we now have a 2.6.12 kernel
$ patch -p1 -R < ../patch-2.6.12-git1
// apply the 2.6.13-rc2 patch. the kernel is now 2.6.13-rc2
$ patch -p1 < ../patch-2.6.13-rc2

// apply the 2.6.13-rc2-git3 patch. the kernel is now 2.6.13-rc2-git3
$ patch -p1 < ../patch-2.6.13-rc2-git3
$ cd ..
$ mv linux-2.6.12-git1 linux-2.6.13-rc2-git3	# rename source dir
```

#### 3.6.2.4 The -mm kernels

These are experimental kernels released by Andrew Morton.

Here are some examples of applying the -mm patches:

```
# moving from 2.6.12 to 2.6.12-mm1
$ cd ~/linux-2.6.12				# change to the 2.6.12 source dir
$ patch -p1 < ../2.6.12-mm1			# apply the 2.6.12-mm1 patch
$ cd ..
$ mv linux-2.6.12 linux-2.6.12-mm1	# rename the source appropriately

# moving from 2.6.12-mm1 to 2.6.13-rc3-mm3
$ cd ~/linux-2.6.12-mm1
$ patch -p1 -R < ../2.6.12-mm1		# revert the 2.6.12-mm1 patch. we now have a 2.6.12 source
$ patch -p1 < ../patch-2.6.13-rc3	# apply the 2.6.13-rc3 patch. we now have a 2.6.13-rc3 source
$ patch -p1 < ../2.6.13-rc3-mm3		# apply the 2.6.13-rc3-mm3 patch
$ cd ..
$ mv linux-2.6.12-mm1 linux-2.6.13-rc3-mm3	# rename the source dir
```

### 3.6.3 如何生成内核补丁

#### 3.6.3.1 通过diff生成内核补丁

The simplest way to generate a patch is to have two source trees, one that is the vanilla stock kernel (such as linux-3.2-vanilla) and another that is the stock tree with your modifications (such as linux-3.2). To generate a patch of the two trees, issue the following command from one directory above the standard kernel source tree:

```
# cd /home/
# tar xvf linux-3.2.tar.bz2				// unzip source code to /home/linux-3.2
# mv linux-3.2 linux-3.2-vanilla			// source tree without change
# tar xvf linux-3.2.tar.bz2				// unzip source code to /home/linux-3.2
# vi linux-3.2/some/files				// make your changes
# diff -uprN -X linux-3.2-vanilla/Documentation/dontdiff linux-3.2-vanilla/ linux-3.2/ > my-patch
```

Alternatively, if you need to diff only a single file, you can do

```
# cp linux-3.2/mm/memory.c linux-3.2/mm/memory.c.orig
# vi linux-3.2/mm/memory.c				// make your change
# diff -up linux-3.2/mm/memory.c{.orig,} > my-patch
```

A useful utility is diffstat, which generates a histogram of a patch’s changes (line additions and removals). To generate the output on one of your patches, do

```
# diffstat -p 1 -w 70 my-patch
```

[**NOTE1**] Patches generated with diff should always be unified diff, include the C function that the change affects and be generated from one directory above the kernel source root. A unified diff include more information that just the differences between two lines. It begins with a two line header with the names and creation date of the two files that diff is comparing.

[**NOTE2**] "dontdiff" is a list of files which are generated by the kernel during the build process, and should be ignored in any diff(1)-generated patch. The "dontdiff" file is included in the kernel tree in 2.6.12 and later. For earlier kernel versions, you can get it from <http://www.xenotime.net/linux/doc/dontdiff>.

#### 3.6.3.2 通过git生成内核补丁

When you have a commit (or two) in your tree, you can generate a patch for each commit, which you can treat as you do the patches described in the previous section:

```
# git format-patch -s origin
```

This generates patches for all commits in your repository and not in the original tree. Git creates the patches in the root of your kernel source tree. To generate patches for only the last N commits, you can execute the following:

```
# git format-patch -s -N
```

Use below command to generate the diffstat:

```
# git diff -M --stat –summary <commit-1> <commit-2>
```

where, the ```-M``` enables rename detection, and the ```--summary``` enables a summary of new/deleted or renamed files.

# 4 Linux系统的启动过程

阅读下列文档：

* linux-3.2/Documentation/x86/boot.txt
* [计算机是如何启动的](/docs/How_to_Start_Computer.pdf)
* [Linux的启动流程](/docs/Linux_Startup_Process.pdf)

## 4.1 内核映像的内存布局

在linux-3.2/Documentation/x86/boot.txt中，包含有关内核映像(参见[bzImage](#)节和[安装内核](#3-5-5-)节)的内存布局的描述。根据内核版本的不同，内核映像的内存布局也存在差异，分别参见[4.1.1 Image/zImage (old kernels)](#4-1-1-image-zimage-old-kernels-)节和[4.1.2 bzImage (modern kernel)](#4-1-2-bzimage-modern-kernel-)节，以及下图：

![bzImage_2](/assets/bzImage_2.png)

![bzImage_3](/assets/bzImage_3.png)

![bzImage_4](/assets/bzImage_4.png)

内核映像的内存布局包含如下几个重要部分：

| 内核组成部分 | 模式  | 源代码出处 |
| :--------- | :--- | :------- |
| Kernel boot sector | 实模式 | v2.6.23及其之前版本的内核:<br>arch/i386/boot/bootsect.S<br>arch/x86_64/boot/bootsect.S<br><br>v2.6.24及其之后版本的内核:<br>arch/i386/boot/header.S<br>arch/i386/boot/main.c |
| Kernel setup | 实模式 | v2.6.23及其之前版本的内核:<br>arch/i386/boot/setup.S<br>arch/x86_64/boot/setup.S<br><br>v2.6.24及其之后版本的内核:<br>arch/i386/boot/header.S<br>arch/i386/boot/main.c |
| Protected-mode kernel | 保护模式 | |

<p/>

**实模式**

实模式是指寻址采用和8086相同的16位段和偏移量，最大寻址空间1MB，最大分段64KB。

![real-mode-protocol](/assets/real-mode-protocol.png)

**保护模式**

保护模式是指寻址采用32位段和偏移量，最大寻址空间4GB，最大分段4GB(Pentium Pre及以后为64GB)。在保护模式下，CPU可以进入虚拟8086方式，这是在保护模式下的实模式程序运行环境。

由[4.1.1 Image/zImage (old kernels)](#4-1-1-image-zimage-old-kernels-)节和[4.1.2 bzImage (modern kernel)](#4-1-2-bzimage-modern-kernel-)节可知：

* real-mode code: boot sector and setup code
* real-mode code can total up to 32KB, although the boot loader may choose to load only the first two sectors (1K)

### 4.1.1 Image/zImage (old kernels)

The traditional memory map for the kernel loader, used for Image or zImage kernels, typically looks like:

![Memery_Layout_03](/assets/Memery_Layout_03.jpg)

### 4.1.2 bzImage (modern kernel)

For a modern bzImage kernel with boot protocol version >= 2.02, a memory layout is suggested like:

![Memery_Layout_02](/assets/Memery_Layout_02.jpg)

where, the address X is as low as the design of the boot loader permits.

## 4.2 主引导记录MBR(Master Boot Record)

### 4.2.1 硬盘结构

硬盘有很多盘片组成，每个盘片的每个面都有一个读写磁头。如果有N个盘片，就有2N个面，对应着2N个磁头(Heads)，从0、1、2...开始编号。每个盘片的半径为固定值R的同心圆在逻辑上形成了一个以电机主轴为轴的柱面(Cylinders)，由外至里编号为0、1、2...。每个盘片上的每个磁道又被划分为几十个扇区(Sector)，通常每个扇区的容量是512字节，并按照一定规则编号1、2、3...，形成Cylinders × Heads × Sector个扇区。

![HardDisk](/assets/HardDisk.png)

对于硬盘而言，一个扇区可能的字节数为128 x 2^n (n=0,1,2,3)。大多情况下，取n=2，即一个扇区(sector)大小为512字节。

### 4.2.2 主引导扇区

主引导扇区位于整个硬盘的0柱面0磁头1扇区```{(柱面，磁头，扇区)|(0，0，1)}```，BIOS在执行完自己固有的程序以后就会jump到MBR中的第一条指令，将系统的控制权交由MBR来执行。主引导扇区主要由三部分组成：

* 主引导记录 MBR (Master Boot Record)
* 硬盘分区表 DPT (Disk Partition Table)
* 结束标志字

![MBR_Components](/assets/MBR_Components.png)

![MBR_Sections](/assets/MBR_Sections.png)

#### 4.2.2.1 主引导记录MBR

主引导记录中包含了硬盘的一系列参数和一段引导程序。其中，硬盘引导程序的主要作用是检查硬盘分区表是否正确并且在系统硬件完成自检后将控制权交给硬盘上的引导程序(如GNU GRUB)。MBR是由分区程序(如Fdisk)所产生的，它是低级格式化的产物，和操作系统没有任何关系，即它不依赖任何操作系统；而且硬盘引导程序也是可以改变的，从而能够实现多系统引导。

#### 4.2.2.2 硬盘分区表DPT

硬盘分区表占据主引导扇区的64个字节(01BE-01FD)，可以对四个分区信息进行描述，其中每个分区信息占16个字节。具体每个字节的定义可以参见硬盘分区结构信息。

| 偏移(十六进制) | 长度(字节) | 含义  |
| :----------: | :-------: | :--- |
| 00 | 1 | 分区状态：<br>00->非活动分区<br>80->活动分区，表示系统可引导<br>其他数值无意义 |
| 01 | 1 | 分区起始磁头号(Head)，使用全部8位 |
| 02 | 2 | 分区起始扇区号(Sector)，占据02字节的Bit #0-#5；<br>分区起始柱面号(Cylinder)，占据02字节的Bit #6-#7和03字节的全部8位 |
| 04 | 1 | 文件系统标志位：<br>0B表示分区的文件系统是FAT32<br>04表示分区的文件系统是FAT16<br>07表示分区的文件系统是NTFS |
| 05 | 1 | 分区结束磁头号(Head)，使用全部8位 |
| 06 | 2 | 分区结束扇区号(Sector)，占据06字节的Bit #0-#5；<br>分区结束柱面号(Cylinder)，占据06字节的Bit #6-#7和07字节的全部8位 |
| 08 | 4 | 分区起始相对扇区 |
| 0C | 4 | 分区总的扇区数 |

<p/>

#### 4.2.2.3 结束标志字

结束标志字55AA(偏移1FE-1FF)是主引导扇区的最后两个字节，用于检验主引导记录是否有效的标志。

## 4.3 Linux引导过程

![Linux_Boot](/assets/Linux_Boot.png)

当系统首次引导或系统被重置时，处理器会执行一个位于已知位置处的代码。在PC中，这个位置在基本输入/输出系统(BIOS)中，它保存在主板上的闪存中。嵌入式系统中的中央处理单元(CPU)会调用这个重置向量来启动一个位于闪存/ROM中的已知地址处的程序。在这两种情况下，结果都是相同的。因为PC提供了很多灵活性，BIOS必须确定要使用哪个设备来引导系统。

当找到一个引导设备之后，第一阶段的引导加载程序就被装入RAM并执行。这个引导加载程序的大小小于512字节(一个扇区)，其作用是加载第二阶段引导加载程序。

当第二阶段引导加载程序被装入RAM并执行时，通常会显示一个动画界面，并将Linux和一个可选的初始RAM磁盘(临时根文件系统)加载到内存中。在加载内核映像时，第二阶段引导加载程序会将控制权交给内核映像，然后内核就可以进行解压和初始化了。在这个阶段中，第二阶段引导加载程序会检测系统硬件、枚举系统连接的硬件设备、安装根设备，然后加载必要的内核模块。完成这些操作之后启动第一个用户空间程序(init，参见4.3.5 init节)，并执行高级系统初始化工作。

这就是 Linux 引导的整个过程。

### 4.3.1 系统启动 (System startup)

系统启动阶段依赖于引导Linux系统上的硬件。

在嵌入式平台中，当系统加电或重置时，会使用一个启动环境。这方面的例子包括U-Boot、RedBoot和Lucent的MicroMonitor。嵌入式平台通常都是与引导监视器搭配销售的。这些程序位于目标硬件上的闪存中的某一段特殊区域，它们提供了将Linux内核映像下载到闪存并继续执行的方法。除了可以存储并引导Linux映像之外，这些引导监视器还执行一定级别的系统测试和硬件初始化过程。在嵌入式平台中，这些引导监视器通常会涉及第一阶段和第二阶段的引导加载程序。

在PC中，引导Linux系统是从BIOS中的地址0xFFFF0处(即接近于1M内存处)开始的。BIOS的第一个步骤是加电自检(POST, Power On Self Test)。POST的工作是对硬件进行检测。BIOS的第二个步骤是进行本地设备的枚举和初始化。给定BIOS功能的不同用法之后，BIOS由两部分组成：POST代码和运行时服务。当POST完成之后，它被从内存中清理了出来，但是BIOS运行时服务依然保留在内存中，目标操作系统可以使用这些服务。要引导一个操作系统，BIOS运行时会按照CMOS的设置中定义的顺序来搜索处于活动状态且可以引导的设备。引导设备可以是软盘、CD-ROM、硬盘上的某个分区、网络上的某个设备，甚至是USB闪存。通常Linux是从硬盘引导的，其中主引导记录(MBR)中包含主引导加载程序(即第一阶段引导加载程序)。MBR是一个512字节大小的扇区，位于磁盘的第一个扇区中(0柱面0磁头1扇区，参见[主引导扇区](#)节)。当MBR被加载到RAM中后，BIOS就会将控制权交给MBR。

**提取MBR的信息**

要查看MBR的内容，使用下面的命令：

```
# dd if=/dev/hda of=mbr.bin bs=512 count=1
# od -xa mbr.bin
```

这个dd命令要以root用户的身份运行，它从/dev/hda(第一个IDE盘)上读取前512个字节的内容，并将其写入mbr.bin文件中。od命令会以十六进制和ASCII码格式打印这个二进制文件的内容。

### 4.3.2 第一阶段引导加载程序 (Stage 1 Bootloader)

MBR中的主引导加载程序(第一阶段引导加载程序)是一个512字节大小的映像，其中包含程序代码和硬盘分区表(参见[主引导扇区](#)节)。前446字节是主引导加载程序，其中包含可执行代码和错误消息文本。接下来的64字节是硬盘分区表，其中包含4个分区的记录(每个记录的大小是16字节)。MBR以两个特殊字节(0x55AA)结束，该数字会用来检查MBR的有效性。

主引导加载程序用于查找并加载次引导加载程序(第二阶段引导加载程序)。它是通过在硬盘分区表中查找一个活动分区来实现这种功能的。当找到一个活动分区时，它会扫描分区表中的其他分区，以确保它们都不是活动的。当这个过程验证完成之后，就将该活动分区的引导记录从这个设备中读入RAM中并执行它。

### 4.3.3 第二阶段引导加载程序 (Stage 2 Bootloader)

次引导加载程序(第二阶段引导加载程序)被形象地称为内核加载程序。这个阶段的任务是加载Linux内核和可选的初始RAM磁盘。

在x86 PC环境中，第一阶段引导加载程序和第二阶段引导加载程序一起被称为GRand Unified Bootloader (GRUB，参见[GRUB Legacy](#)节和[GNU GRUB](#)节)或者Linux Loader (LILO，参见[LILO](#)节)。由于LILO有一些缺点，而GRUB克服了这些缺点(参见[LILO与GRUB的比较](#)节)，因此下面重点关注GRUB。

GRUB包含了有关Linux文件系统的知识。GRUB不像LILO一样使用裸扇区，而是可以从ext2或ext3文件系统中加载Linux内核。它是通过将两阶段的引导加载程序转换成三阶段的引导加载程序来实现这项功能的。阶段1 (MBR)引导阶段1.5的引导加载程序，它可以理解包含Linux内核映像的特殊文件系统。这方面的例子包括reiserfs_stage1_5 (要从Reiser日志文件系统上进行加载)或者e2fs_stage1_5 (要从ext2或ext3文件系统上进行加载)。当阶段1.5的引导加载程序被加载并运行时，阶段2的引导加载程序就可以被加载了。

当阶段2加载之后，GRUB就可以在请求时显示可用内核列表(在/etc/grub.conf中定义，同时还有几个软符号链接/etc/grub/menu.lst和/etc/grub.conf)。我们可以选择内核甚至修改附加内核参数。另外，还可以使用一个命令行的shell对引导过程进行高级手工控制。

将第二阶段引导加载程序加载到内存中之后，就可以对文件系统进行查询了，并将默认的内核映像和initrd映像加载到内存中。当这些映像文件准备好之后，阶段2的引导加载程序就可以调用内核映像了。

**GRUB 阶段引导加载程序**

/boot/grub目录中包含了stage1、stage1.5和stage2引导加载程序，以及很多其他加载程序(例如，CR-ROM使用的是iso9660_stage_1_5)。

### 4.3.4 Kernel v3.2.0

当内核映像被加载到内存中，且第二阶段引导加载程序释放控制权之后，内核阶段就开始了。内核映像并不是一个可执行的内核，而是一个压缩过的内核映像。通常它是一个zImage (压缩映像，小于512KB)或一个bzImage (较大的压缩映像，大于512KB)，它是用gzip压缩的(参见[$(obj)/piggy.o](#)节)。在这个内核映像之前是一个例程，它实现少量硬件设置，并对内核映像中包含的内核进行解压，然后将其放入高端内存中，如果有初始RAM磁盘映像，就会将它移动到内存中，并标明以后使用。然后该例程会调用内核，并开始启动内核引导的过程。

#### 4.3.4.1 内核启动过程中的函数调用关系

由arch/x86/boot/setup.ld中的如下代码：

```
ENTRY(_start)
```

可知，setup.bin (参见[bzImage](#)节)的入口点是_start。GRUB会执行jmp_far(0x20, 0)跳过/boot/vmlinuz-3.2.0-chenwx的前0x200个字节，即跳到vmlinuz实模式代码_start处执行，即arch/x86/boot/header.S中的_start，参见[arch/x86/boot/header.S](#)节。

##### 4.3.4.1.1 arch/x86/boot/header.S

注：几乎header.S中所有的代码都是在准备实模式下的C语言环境。

```
/*
 * header.S从开始到此处(# offset 512, entry point)代码实现的功能和
 * 以前内核中arch/i386/boot/bootsect.S的功能是一样的。
 * header.S中(# offset 512, entry point)之后代码所实现的功能和以前
 * 内核中arch/i386/boot/setup.S的一部分类似，包括：
 *  - 设置setup header参数；
 *  - 设置堆栈；
 *  - 检查setup中的标签；
 *  - 清除BSS段；
 *  - 调用入口函数main()
 */
	# offset 512, entry point

	.globl	_start
_start:
	# Explicitly enter this as bytes, or the assembler
	# tries to generate a 3-byte jump here, which causes
	# everything else to push off to the wrong offset.
	.byte	0xeb		# short (2-byte) jump		// #0200处的第一个字节代表短跳转指令，其中0xeb是该指令代码
	.byte	start_of_setup-1f				// #0201处的一个字节表示跳转距离，从标号1到start_of_setup

/*
 * CAN_USE_HEAP表示本体系架构是否支持堆，其定义于arch/x86/include/asm/bootparam.h:
 * #define CAN_USE_HEAP (1<<7)
 */
CAN_USE_HEAP	= 0x80		# If set, the loader also has set

/*
 * STACK_SIZE定义于arch/x86/boot/boot.h中，取值为512，故堆的大小为512字节
 * _end来自arch/x86/boot/setup.ld，表示整个setup.bin的结尾，
 * 参见Image/zImage (old kernels)节(0x00098000处)和bzImage (modern kernel)节(X+08000)
 */
heap_end_ptr:	.word	_end+STACK_SIZE-512

...
	.section ".entrytext", "ax"
start_of_setup:
#ifdef SAFE_RESET_DISK_CONTROLLER
# Reset the disk controller.
	movw	$0x0000, %ax		# Reset disk controller
	movb	$0x80, %dl			# All disks
	int	$0x13				// 用13号中断重设系统盘的磁盘控制器ax=0x0，dl=0x80
#endif

# Force %es = %ds			// 先强制让附加数据段es的内容等于数据段ds的内容
	movw	%ds, %ax
	movw	%ax, %es
	cld

# Apparently some ancient versions of LILO invoked the kernel with %ss != %ds,
# which happened to work by accident for the old code.  Recalculate the stack
# pointer if %ss is invalid.  Otherwise leave it alone, LOADLIN sets up the
# stack behind its own code, so we can't blindly put it directly past the heap.

	movw	%ss, %dx
	cmpw	%ax, %dx	# %ds == %ss?
	movw	%sp, %dx
	je	2f		# -> assume %sp is reasonably set

	# Invalid %ss, make up a new stack	// 设置实模式下的堆栈，参见bzImage (modern kernel)节
	movw	$_end, %dx					// _end来自arch/x86/boot/setup.ld，表示整个setup.bin的结尾
	testb	$CAN_USE_HEAP, loadflags
	jz	1f
	movw	heap_end_ptr, %dx
1:	addw	$STACK_SIZE, %dx
	jnc	2f
	xorw	%dx, %dx	# Prevent wraparound

// 标号2处的代码将dx中的栈顶地址按双字对齐，即将最低两位清零
2:	# Now %dx should point to the end of our stack space
	andw	$~3, %dx	# dword align (might as well...)
	jnz	3f
	movw	$0xfffc, %dx	# Make sure we're not zero
3:	movw	%ax, %ss
	movzwl	%dx, %esp	# Clear upper half of %esp
	sti			# Now we should have a working stack

# We will have entered with %cs = %ds+0x20, normalize %cs so
# it is on par with the other segments.
	pushw	%ds
	pushw	$6f
	lretw
6:

# Check signature at end of setup
	cmpl	$0x5a5aaa55, setup_sig
	jne	setup_bad

/*
 * 清空setup的bss段(非初始化数据段)，注意bss段和数据段(data段)的区别：
 * bss段存放未初始化的全局变量和静态变量，data段存放初始化后的全局变量和静态变量
 */
# Zero the bss
	movw	$__bss_start, %di
	movw	$_end+3, %cx
	xorl	%eax, %eax
	subw	%di, %cx
	shrw	$2, %cx
	rep; stosl

# Jump to C code (should not return)
	calll	main		// 跳转到arch/x86/boot/main.c中的main()，参见arch/x86/boot/main.c节
```

##### 4.3.4.1.2 arch/x86/boot/main.c

arch/x86/boot/header.S最终调用arch/x86/boot/main.c中的main()函数。在执行main()函数时，系统还处于实模式下，其主要功能是为进入保护模式做准备，涉及初始化计算机中的硬件设备，并为内核程序的执行建立环境。虽然之前BIOS已经初始化了大部分硬件设备，但是Linux并不依赖于BIOS，而是以自己的方式重新初始化硬件设备以增强可移植性和健壮性。

```
void main(void)
{
	/* First, copy the boot header into the "zeropage" */
	copy_boot_params();					// 参见copy_boot_params()节

	/* Initialize the early-boot console */
	console_init();						// 解析内核参数earlyprintk
	if (cmdline_find_option_bool("debug"))
		puts("early console in setup code\n");

	/* End of heap check */
	init_heap();						// 参见init_heap()节

	/* Make sure we have all the proper CPU support */
	if (validate_cpu()) {					// 参见validate_cpu()节
		puts("Unable to boot - please use a kernel appropriate for your CPU.\n");
		die();
	}

	/* Tell the BIOS what CPU mode we intend to run in. */
	set_bios_mode();						// 参见set_bios_mode()节

	/* Detect memory layout */
	// 参见detect_memory()节和检测内存段及其大小/boot_params.e820_map节
	detect_memory();

	/* Set keyboard repeat rate (why?) */
	keyboard_set_repeat();				// 参见keyboard_set_repeat()节

	/* Query MCA information */
	query_mca();						// 参见query_mca()节

	/* Query Intel SpeedStep (IST) information */
	query_ist();						// 参见query_ist()节

	/* Query APM information */
#if defined(CONFIG_APM) || defined(CONFIG_APM_MODULE)
	// 填充boot_params.apm_bios_info，其执行过程与query_ist()类似，参见query_ist()节
	query_apm_bios();
#endif

	/* Query EDD information */
#if defined(CONFIG_EDD) || defined(CONFIG_EDD_MODULE)
	/*
	 * 填充boot_params.eddbuf_entries, boot_params.edd_mbr_sig_buf_entries,
	 * boot_params.eddbuf，其执行过程与query_ist()类似，参见query_edd()节
	 */
	query_edd();
#endif

	/* Set the video mode */
	set_video();						// 参见set_video()节

	/* Do the last things and invoke protected mode */
	go_to_protected_mode();				// 参见go_to_protected_mode()节
}
```

###### 4.3.4.1.2.1 copy_boot_params()

该函数定义于arch/x86/boot/main.c:

```
/*
 * struct boot_params定义于arch/x86/include/asm/bootparam.h
 * 变量boot_params是main.c的全局变量，且未被初始化，故位于bss段。
 * GRUB把vmlinuz加载到内存后，boot_params就位于_bss_start的开始位置。
 * 此后，当启动保护模式的分页功能后，第一个页面就是从它开始的(注：不是从0x0开始)。
 * 故内核注释它为zeropage，即所谓的0号页面，足见变量boot_params的重要性。
 */
struct boot_params boot_params __attribute__((aligned(16)));

static void copy_boot_params(void)
{
	struct old_cmdline {
		u16 cl_magic;
		u16 cl_offset;
	};

	// arch/x86/include/asm/setup.h中OLD_CL_ADDRESS取值为0x020
	const struct old_cmdline * const oldcmd = (const struct old_cmdline *)OLD_CL_ADDRESS;

	// 变量boot_params的大小刚好是一个页面的大小，即4KB
	BUILD_BUG_ON(sizeof boot_params != 4096);
	/*
	 * 将hdr的内容拷贝到全局变量boot_params.hdr中。其中，
	 * hdr是arch/x86/boot/header.S中定义的数据段中的内容
	 * 而boot_params.hdr的类型为struct setup_header
	 * (参见arch/x86/include/asm/bootparam.h)，其定义与
	 * arch/x86/boot/header.S中hdr段的定义相同，故可以使用memcpy()拷贝
	 */
	memcpy(&boot_params.hdr, &hdr, sizeof hdr);

	// arch/x86/include/asm/setup.h中OLD_CL_MAGIC取值为0xA33F
	if (!boot_params.hdr.cmd_line_ptr && oldcmd->cl_magic == OLD_CL_MAGIC) {
		/* Old-style command line protocol. */
		u16 cmdline_seg;

		/* Figure out if the command line falls in the region
		   of memory that an old kernel would have copied up
		   to 0x90000... */
		if (oldcmd->cl_offset < boot_params.hdr.setup_move_size)
			cmdline_seg = ds();
		else
			cmdline_seg = 0x9000;

		// 针对旧内核，调整boot_params.hdr.cmd_line_ptr字段的取值
		boot_params.hdr.cmd_line_ptr = (cmdline_seg << 4) + oldcmd->cl_offset;
	}
}
```

###### 4.3.4.1.2.2 init_heap()

该函数用来检查内核初始化阶段使用的堆，其定义于arch/x86/boot/main.c:

```
static void init_heap(void)
{
	char *stack_end;

	if (boot_params.hdr.loadflags & CAN_USE_HEAP) {
		asm("leal %P1(%%esp),%0" : "=r" (stack_end) : "i" (-STACK_SIZE));

		// heap_end_ptr参见arch/x86/boot/header.S节，堆栈大小为512字节
		heap_end = (char *) ((size_t)boot_params.hdr.heap_end_ptr + 0x200);
		// 检查堆的大小，不能溢出，否则就调整到stack_end
		if (heap_end > stack_end)
			heap_end = stack_end;
	} else {
		/* Boot protocol 2.00 only, no heap available */
		puts("WARNING: Ancient bootloader, some functionality may be limited!\n");
	}
}

4.3.4.1.2.3 validate_cpu()

该函数定义于arch/x86/boot/cpu.c:
int validate_cpu(void)
{
	u32 *err_flags;
	int cpu_level, req_level;
	const unsigned char *msg_strs;

	/*
	 * 通过调用arch/x86/boot/cpucheck.c中的check_cpu()函数读取CPU信息，
	 * 并将其存放在如下变量中：
	 *   cpu_level - 表示系统实际的CPU版本
	 *   req_level - 表示运行本内核所需要的CPU最低版本
	 * 若cpu_level < req_level，则报错
	 */
	check_cpu(&cpu_level, &req_level, &err_flags);

	if (cpu_level < req_level) {
		printf("This kernel requires an %s CPU, ", cpu_name(req_level));
		printf("but only detected an %s CPU.\n", cpu_name(cpu_level));
		return -1;
	}

	if (err_flags) {
		int i, j;
		puts("This kernel requires the following features not present on the CPU:\n");

		msg_strs = (const unsigned char *)x86_cap_strs;

		for (i = 0; i < NCAPINTS; i++) {
			u32 e = err_flags[i];

			for (j = 0; j < 32; j++) {
				if (msg_strs[0] < i || (msg_strs[0] == i && msg_strs[1] < j)) {
					/* Skip to the next string */
					msg_strs += 2;
					while (*msg_strs++)
						;
				}
				if (e & 1) {
					if (msg_strs[0] == i && msg_strs[1] == j && msg_strs[2])
						printf("%s ", msg_strs+2);
					else
						printf("%d:%d ", i, j);
				}
				e >>= 1;
			}
		}
		putchar('\n');
		return -1;
	} else {
		return 0;
	}
}
```

###### 4.3.4.1.2.4 set_bios_mode()

该函数定义于arch/x86/boot/main.c:

```
/*
 * Tell the BIOS what CPU mode we intend to run in.
 */
static void set_bios_mode(void)
{
#ifdef CONFIG_X86_64
	struct biosregs ireg;

	initregs(&ireg);
	ireg.ax = 0xec00;
	ireg.bx = 2;
	/*
	 * intcall是初始化阶段的中断处理函数，此处调用0x15中断，并将BIOS的寄存器设置成ireg变量的值。
	 * 注：初始化阶段至此，仍处于实模式阶段，Linux内核的中断系统还没有被初始化，该函数是内核编译后临时
	 * 生成的一个BIOS服务程序，跟arch/x86/boot/header.S中的那些int指令的效果是一样的
	 */
	intcall(0x15, &ireg, NULL);
#endif
}
```

###### 4.3.4.1.2.5 detect_memory()

该函数根据物理内存的类型探测内存布局，其定义于arch/x86/boot/memory.c:

```
int detect_memory(void)
{
	int err = -1;

	// 填充boot_params.e820_entries和boot_params.e820_map，参见detect_memory_e820()节
	if (detect_memory_e820() > 0)
		err = 0;

	// 填充boot_params.alt_mem_k
	if (!detect_memory_e801())
		err = 0;

	// 填充boot_params.screen_info.ext_mem_k
	if (!detect_memory_88())
		err = 0;

	return err;
}
```

###### 4.3.4.1.2.5.1 detect_memory_e820()

该函数定义于arch/x86/boot/memory.c，另参见检测内存段及其大小/boot_params.e820_map节：

```
static int detect_memory_e820(void)
{
	int count = 0;
	struct biosregs ireg, oreg;
	// 此时，boot_params.e820_map是一个空数组，本函数用于初始化该数组
	struct e820entry *desc = boot_params.e820_map;
	static struct e820entry buf; /* static so it is zeroed */

	initregs(&ireg);
	ireg.ax  = 0xe820;
	ireg.cx  = sizeof buf;
	ireg.edx = SMAP;
	ireg.di  = (size_t)&buf;

	do {
		/*
		 * int 0x15中断查询物理内存时，每次返回一个内存段的信息，
		 * 因此要想返回系统中所有的物理内存信息，必须以循环的方式查询
		 */
		intcall(0x15, &ireg, &oreg);
		ireg.ebx = oreg.ebx; /* for next iteration... */

		/* BIOSes which terminate the chain with CF = 1 as opposed
		   to %ebx = 0 don't always report the SMAP signature on
		   the final, failing, probe. */
		if (oreg.eflags & X86_EFLAGS_CF)
			break;

		/* Some BIOSes stop returning SMAP in the middle of
		   the search loop.  We don't know exactly how the BIOS
		   screwed up the map at that point, we might have a
		   partial map, the full map, or complete garbage, so
		   just return failure. */
		if (oreg.eax != SMAP) {
			count = 0;
			break;
		}

		*desc++ = buf;
		count++;
	/*
	 * boot_params.e820_map数组的最大下标为E820MAX，取值为128，
	 * 参见arch/x86/include/asm/e820.h
	 */
	} while (ireg.ebx && count < ARRAY_SIZE(boot_params.e820_map));

	// 返回检测到的物理内存段的个数
	return boot_params.e820_entries = count;
}
```

###### 4.3.4.1.2.6 keyboard_set_repeat()

该函数定义于arch/x86/boot/main.c:

```
/*
 * Set the keyboard repeat rate to maximum.  Unclear why this
 * is done here; this might be possible to kill off as stale code.
 */
static void keyboard_set_repeat(void)
{
	struct biosregs ireg;
	initregs(&ireg);
	ireg.ax = 0x0305;
	// 调用int 0x16中断，设置键盘的重复延时和速率
	intcall(0x16, &ireg, NULL);
}
```

###### 4.3.4.1.2.7 query_mca()

该函数定义于arch/x86/boot/mca.c:

```
int query_mca(void)
{
	struct biosregs ireg, oreg;
	u16 len;

	initregs(&ireg);
	ireg.ah = 0xc0;
	// 调用BIOS的int 0x15中断，读取系统环境配置表信息
	intcall(0x15, &ireg, &oreg);

	if (oreg.eflags & X86_EFLAGS_CF)
		return -1;	/* No MCA present */

	set_fs(oreg.es);
	len = rdfs16(oreg.bx);

	if (len > sizeof(boot_params.sys_desc_table))
		len = sizeof(boot_params.sys_desc_table);

	// 将系统环境配置表信息保存到boot_params.sys_desc_table中
	copy_from_fs(&boot_params.sys_desc_table, oreg.bx, len);
	return 0;
}
```

###### 4.3.4.1.2.8 query_ist()

该函数定义于arch/x86/boot/main.c:

```
/*
 * Get Intel SpeedStep (IST) information.
 */
static void query_ist(void)
{
	struct biosregs ireg, oreg;

	/* Some older BIOSes apparently crash on this call, so filter
	   it from machines too old to have SpeedStep at all. */
	if (cpu.level < 6)
		return;

	initregs(&ireg);
	ireg.ax  = 0xe980;		/* IST Support */
	ireg.edx = 0x47534943;	/* Request value */
	// 调用BIOS的int 0x15中断，查询IST信息，并将其保存到boot_params.ist_info中
	intcall(0x15, &ireg, &oreg);

	boot_params.ist_info.signature  = oreg.eax;
	boot_params.ist_info.command    = oreg.ebx;
	boot_params.ist_info.event      = oreg.ecx;
	boot_params.ist_info.perf_level = oreg.edx;
}
```

###### 4.3.4.1.2.9 query_edd()

该函数定义于arch/x86/boot/edd.c:

```
void query_edd(void)
{
	char eddarg[8];
	int do_mbr = 1;
#ifdef CONFIG_EDD_OFF
	int do_edd = 0;
#else
	int do_edd = 1;
#endif
	int be_quiet;
	int devno;
	struct edd_info ei, *edp;
	u32 *mbrptr;

	if (cmdline_find_option("edd", eddarg, sizeof eddarg) > 0) {
		if (!strcmp(eddarg, "skipmbr") || !strcmp(eddarg, "skip")) {
			do_edd = 1;
			do_mbr = 0;
		}
		else if (!strcmp(eddarg, "off"))
			do_edd = 0;
		else if (!strcmp(eddarg, "on"))
			do_edd = 1;
	}

	be_quiet = cmdline_find_option_bool("quiet");

	edp     = boot_params.eddbuf;
	mbrptr = boot_params.edd_mbr_sig_buffer;

	if (!do_edd)
		return;

	/* Bugs in OnBoard or AddOnCards Bios may hang the EDD probe,
	 * so give a hint if this happens.
	 */

	if (!be_quiet)
		printf("Probing EDD (edd=off to disable)... ");

	for (devno = 0x80; devno < 0x80+EDD_MBR_SIG_MAX; devno++) {
		/*
		 * Scan the BIOS-supported hard disks and query EDD
		 * information...
		 */
		if (!get_edd_info(devno, &ei) && boot_params.eddbuf_entries < EDDMAXNR) {
			memcpy(edp, &ei, sizeof ei);
			edp++;
			boot_params.eddbuf_entries++;
		}

		if (do_mbr && !read_mbr_sig(devno, &ei, mbrptr++))
			boot_params.edd_mbr_sig_buf_entries = devno-0x80+1;
	}

	if (!be_quiet)
		printf("ok\n");
}
```

###### 4.3.4.1.2.10 set_video()

该函数定义于arch/x86/boot/video.c:

```
void set_video(void)
{
	// vid_mode定义于arch/x86/boot/header.S，其取值为SVGA_MODE
	u16 mode = boot_params.hdr.vid_mode;

	// 重新设置堆的位置，把它设定到_end处，参见arch/x86/boot/header.S节
	RESET_HEAP();

	store_mode_params();	// 利用BIOS的显示服务程序对视频显示进行设置，保存到boot_params.screen_info
	save_screen();		// 将当前屏幕的内容存储到指定的内存空间中
	probe_cards(0);		// 扫描显卡

	for (;;) {
		if (mode == ASK_VGA)
			mode = mode_menu();

		if (!set_mode(mode))
			break;

		printf("Undefined video mode number: %x\n", mode);
		mode = ASK_VGA;
	}
	boot_params.hdr.vid_mode = mode;
	vesa_store_edid();
	/*
	 * 设置EDID。EDID是一种VESA标准数据格式，其中包含有关监视器及其性能的参数，
	 * 包括供应商信息、最大图像大小、颜色设置、厂商预设置、频率范围的限制以及显示器
	 * 名和序列号的字符串
	 */
	store_mode_params();

	/*
	 * 根据是否进入mode_menu()设置了do_restore来恢复刚刚被保存的screen_info信息，
	 * 它与save_screen()正好相反
	 */
	if (do_restore)
		restore_screen();
}
```

###### 4.3.4.1.2.11 go_to_protected_mode()

当main()函数执行到go_to_protected_mode()时，系统就即将告别实模式环境，进入保护模式了。保护模式(Protected Mode，或简写为pmode)是一种80286系列及之后的x86兼容CPU操作模式。保护模式有一些新的特色，设计用来增强多功能和系统稳定度，像是内存保护，分页系统，以及硬件支持的虚拟内存。现今大部分的x86操作系统都在保护模式下运行，包含Linux、FreeBSD、微软Windows 2.0及之后版本。

注意：在执行go_to_protected_mode()时，系统还处于实模式下，只不过是进入与保护模式相关的代码。

该函数定义于arch/x86/boot/pm.c:

```
/*
 * Actual invocation sequence
 */
void go_to_protected_mode(void)
{
	/* Hook before leaving real mode, also disables interrupts.
	 * 若boot_params.hdr.realmode_swtch被设置，则执行该函数，否则，关中断(NMI)
	 * 由arch/x86/boot/header.S可知，该字段未被设置：
	 * realmode_swtch:	.word	0, 0
	 */
	realmode_switch_hook();

	/* Enable the A20 gate */
	if (enable_a20()) {			// 参见enable_a20()节
		puts("A20 gate not responding, unable to boot...\n");
		die();
	}

	/* Reset coprocessor (IGNNE#) */
	reset_coprocessor();

	/* Mask all interrupts in the PIC */
	mask_all_interrupts();

	/* Actual transition to protected mode... */
	setup_idt();
	setup_gdt();
	/*
	 * 函数protected_mode_jump()参见protected_mode_jump()节，该函数需要两个入参：
	 * 1) 第一个入参：boot_params.hdr.code32_start，定义于arch/x86/boot/header.S，
	 *    取值为0x100000，由bzImage (modern kernel)节的图可知，该参数表示进入保护模式后
	 *    执行的第一条内核代码(Protected-mode kernel)；
	 * 2) 第二个入参：&boot_params+(ds()<<4)是传递给内核的参数，为0号页面的地址，
	 *    即变量boot_params，参见copy_boot_params()节
	 */
	protected_mode_jump(boot_params.hdr.code32_start, (u32)&boot_params + (ds() << 4));
}
```

###### 4.3.4.1.2.11.1 enable_a20()

PC及其兼容机的第21根地址线(A20)较特殊，PC中安排了一个“门”来控制该地址线是否有效。到了80286，系统的地址总线有原来的20根发展到24根，这样能够访问的内存可以达到224=16MB。Intel在设计80286时提出的目标是向下兼容，所以在实模式下，系统所表现的行为应该和8086/8088所表现的行为完全一样，也就是说，在实模式下，80286及其后续系列，应该和8086/8088完全兼容。但80286芯片却存在一个BUG：因为80286有了A20线，如果程序员访问100000H-10FFEFH之间的内存，系统将实际访问这块内存，而不是像8086/8088那样从0开始。如下图所示：

为了解决上述兼容性问题，IBM使用键盘控制器上剩余的一些输出线来管理第21根地址线(从0开始数是第20根)的有效性，被称为A20 Gate：

1) 如果A20 Gate被打开，则当程序员给出100000H-10FFEFH之间的地址时，系统将真正访问这块内存区域；

2) 如果A20 Gate被关闭，则当程序员给出100000H-10FFEFH之间的地址时，系统仍然使用8086/8088的方式，即取模方式(8086仿真)。绝大多数IBM PC兼容机默认A20 Gate是被关闭的。现在许多新型PC上存在直接通过BIOS功能调用来控制A20 Gate的功能。

上述的内存访问模式都是实模式，在80286以及更高系列的PC中，即使A20 Gate被打开，在实模式下所能够访问的内存最大也只能为10FFEFH，尽管它们的地址总线所能够访问的能力都大大超过这个限制。为了能够访问10FFEFH以上的内存，则必须进入保护模式。

enable_a20()就是用来打开A20 Gate的，参见arch/x86/boot/a20.c。

###### 4.3.4.1.2.11.2 protected_mode_jump()

该函数定义于arch/x86/boot/pmjump.S:

```
	.text
	.code16

/*
 * void protected_mode_jump(u32 entrypoint, u32 bootparams);
 */
GLOBAL(protected_mode_jump)
	movl	%edx, %esi		# Pointer to boot_params table

	xorl	%ebx, %ebx
	movw	%cs, %bx
	shll	$4, %ebx
	addl	%ebx, 2f
	jmp	1f				# Short jump to serialize on 386/486
1:

	movw	$__BOOT_DS, %cx
	movw	$__BOOT_TSS, %di

	movl	%cr0, %edx
	// 执行完本行代码后，内核从此告别实模式，开始了内核的保护模式
	orb	$X86_CR0_PE, %dl		# Protected mode
	movl	%edx, %cr0

	# Transition to 32-bit mode
	.byte	0x66, 0xea		# ljmpl opcode
2:	.long	in_pm32			# offset		// 开始执行in_pm32函数
	.word	__BOOT_CS			# segment
ENDPROC(protected_mode_jump)


	.code32
	.section ".text32","ax"
GLOBAL(in_pm32)
	# Set up data segments for flat 32-bit mode
	movl	%ecx, %ds
	movl	%ecx, %es
	movl	%ecx, %fs
	movl	%ecx, %gs
	movl	%ecx, %ss
	# The 32-bit code sets up its own stack, but this way we do have
	# a valid stack if some debugging hack wants to use it.
	addl	%ebx, %esp

	# Set up TR to make Intel VT happy
	ltr	%di

	# Clear registers to allow for future extensions to the
	# 32-bit boot protocol
	xorl	%ecx, %ecx
	xorl	%edx, %edx
	xorl	%ebx, %ebx
	xorl	%ebp, %ebp
	xorl	%edi, %edi

	# Set up LDTR to make Intel VT happy
	lldt	%cx

	/*
	 * 开始执行由入参传来的boot_params.hdr.code32_start，
	 * 即0x100000处的代码(Protected-mode Kernel)，
	 * 参见bzImage (modern kernel)节；
	 * 在解压vmlinuz之前，这段代码为arch/x86/boot/compressed/head_32.S
	 * 中的函数startup_32，参见arch/x86/boot/compressed/head_32.S节
	 */
	jmpl	*%eax			# Jump to the 32-bit entrypoint
ENDPROC(in_pm32)
```

注意：从系统启动到函数protected_mode_jump()，并不是第一次进入保护模式，在bootloader阶段，GRUB已经执行过一次保护模式的命令了，即把vmlinuz第三部分的代码拷贝到内存0x100000之后。参见[第二阶段引导加载程序 (Stage 2 Bootloader](#)节。

##### 4.3.4.1.3 arch/x86/boot/compressed/head_32.S

由protected_mode_jump()节可知，进入保护模式后执行的第一个函数为arch/x86/boot/compressed/head_32.S中的函数startup_32：

```
	__HEAD
ENTRY(startup_32)
	cld
	/*
	 * Test KEEP_SEGMENTS flag to see if the bootloader is asking
	 * us to not reload segments
	 */
	/*
	 * 参见arch/x86/kernel/asm-offsets.c，BP_loadflags是字段hdr.loadflags相对于
	 * 结构体boot_params首地址的偏移量，因而，BP_loadflags+%esi就是hdr.loadflags的地址
	 */
	testb	$(1<<6), BP_loadflags(%esi)
	jnz	1f

	cli
	// 参见arch/x86/include/asm/segment.h，__BOOT_DS的取值为24
	movl	$__BOOT_DS, %eax
	movl	%eax, %ds
	movl	%eax, %es
	movl	%eax, %fs
	movl	%eax, %gs
	movl	%eax, %ss
1:

/*
 * Calculate the delta between where we were compiled to run
 * at and where we were actually loaded at.  This can only be done
 * with a short local call on x86.  Nothing  else will tell us what
 * address we are running at.  The reserved chunk of the real-mode
 * data at 0x1e4 (defined as a scratch field) are used as the stack
 * for this calculation. Only 4 bytes are needed.
 */
	/*
	 * BP_scratch是字段scratch相对于结构体boot_params首地址的偏移量，
	 * 即让栈顶指向(BP_scratch+4)(%esi)地址lea指令得到boot_params.scratch
	 * 的32位物理地址，并将其存放到esp寄存器中
	 */
	leal	(BP_scratch+4)(%esi), %esp
	call	1f
1:	popl	%ebp
	subl	$1b, %ebp

/*
 * %ebp contains the address we are loaded at by the boot loader and %ebx
 * contains the address where we should move the kernel image temporarily
 * for safe in-place decompression.
 */

#ifdef CONFIG_RELOCATABLE
	movl	%ebp, %ebx
	/*
	 * BP_kernel_alignment是字段hdr.kernel_alignment相对于结构体boot_params首地址的偏移量
	 * 在arch/x86/boot/header.S中，kernel_alignment:  .long CONFIG_PHYSICAL_ALIGN
	 * 又在.config中，CONFIG_PHYSICAL_ALIGN=0x1000000
	 */
	movl	BP_kernel_alignment(%esi), %eax
	decl	%eax
	addl    %eax, %ebx
	notl	%eax
	andl    %eax, %ebx
#else
	// 根据arch/x86/include/asm/boot.h和.config中配置可知，LOAD_PHYSICAL_ADDR取值为0x1000000
	movl	$LOAD_PHYSICAL_ADDR, %ebx
#endif

	/* Target address to relocate to for decompression */
	addl	$z_extract_offset, %ebx

	/* Set up the stack */
	leal	boot_stack_end(%ebx), %esp

	/* Zero EFLAGS */
	pushl	$0
	popfl

/*
 * Copy the compressed kernel to the end of our buffer
 * where decompression in place becomes safe.
 */
	pushl	%esi
	leal	(_bss-4)(%ebp), %esi
	leal	(_bss-4)(%ebx), %edi
	// _bss是解压缩程序的BSS段
	/*
	 * _bss – startup_32涵盖了vmlinuz从startup_32以后的代码长度，
	 * 包括整个待解压内核，然后再右移2位，即除以4
	 */
	movl	$(_bss - startup_32), %ecx
	shrl	$2, %ecx
	// 设置方向标志置位，下一行中的rep是由高地址向低地址进行，
	// 即_bss到当前正在执行的startup_32
	std
	// 把内核映像拷贝到0x1000000以后的内存单元中
	rep	movsl
	cld
	popl	%esi

/*
 * Jump to the relocated address.
 */
	leal	relocated(%ebx), %eax
	/*
	 * 本行之前的代码将原GRUB加载的内核映像(位于0x100000处)拷贝到0x1000000
	 * 之后新的内存单元中。本行代码跳转到拷贝后的内核映像(位于0x1000000之后)中
	 * 的relocated处执行，即离开原GRUB加载的内核映像(位于0x100000处)
	 */
	jmp	*%eax
ENDPROC(startup_32)


.text
relocated:

/*
 * Clear BSS (stack is currently empty)
 */
	xorl	%eax, %eax
	leal	_bss(%ebx), %edi
	leal	_ebss(%ebx), %ecx
	subl	%edi, %ecx
	shrl	$2, %ecx
	rep	stosl

/*
 * Adjust our own GOT
 */
	leal	_got(%ebx), %edx
	leal	_egot(%ebx), %ecx
1:
	cmpl	%ecx, %edx
	jae	2f
	addl	%ebx, (%edx)
	addl	$4, %edx
	jmp	1b
2:

/*
 * Do the decompression, and jump to the new kernel..
 */
	/*
	 * 此段中的下列变量均来自于arch/x86/boot/compressed/mkpiggy.c:
	 * z_extract_offset_negative, z_input_len, input_data
	 * 参见3.4.2.8.5.1.4 $(obj)/piggy.S节
	 */
	leal	z_extract_offset_negative(%ebx), %ebp
						/* push arguments for decompress_kernel: */
	pushl	%ebp				/* output address */		// 解压缩的缓存首地址
	pushl	$z_input_len		/* input_len */			// 待解压缩内核的大小
	leal	input_data(%ebx), %eax
	pushl	%eax				/* input_data */			// 待解压缩内核的开始地址
	leal	boot_heap(%ebx), %eax
	pushl	%eax				/* heap area */			// 解压缩内核所用的堆
	pushl	%esi				/* real mode pointer */	// 表示拷贝内核映像之前的内核映像地址
	/*
	 * 调用arch/x86/boot/compressed/misc.c中的decompress_kernel()解压内核，
	 * 参见decompress_kernel()节
	 */
	call	decompress_kernel
	addl	$20, %esp

#if CONFIG_RELOCATABLE
/*
 * Find the address of the relocations.
 */
	leal	z_output_len(%ebp), %edi

/*
 * Calculate the delta between where vmlinux was compiled to run
 * and where it was actually loaded.
 */
	movl	%ebp, %ebx
	subl	$LOAD_PHYSICAL_ADDR, %ebx
	jz	2f	/* Nothing to be done if loaded at compiled addr. */
/*
 * Process relocations.
 */

1:	subl	$4, %edi
	movl	(%edi), %ecx
	testl	%ecx, %ecx
	jz	2f
	addl	%ebx, -__PAGE_OFFSET(%ebx, %ecx)
	jmp	1b
2:
#endif

/*
 * Jump to the decompressed kernel.
 */
	xorl	%ebx, %ebx
	/*
	 * 开始执行解压后的内核，即第二个startup_32()函数，
	 * 该函数定义于arch/x86/kernel/head_32.S，
	 * 参见arch/x86/kernel/head_32.S节
	 */
	jmp	*%ebp
```

###### 4.3.4.1.3.1 decompress_kernel()

该函数定义于arch/x86/boot/compressed/misc.c:

```
asmlinkage void decompress_kernel(void *rmode, memptr heap, unsigned char *input_data,
						 unsigned long input_len, unsigned char *output)
{
	real_mode = rmode;

	if (cmdline_find_option_bool("quiet"))
		quiet = 1;
	if (cmdline_find_option_bool("debug"))
		debug = 1;

	if (real_mode->screen_info.orig_video_mode == 7) {
		vidmem = (char *) 0xb0000;
		vidport = 0x3b4;
	} else {
		vidmem = (char *) 0xb8000;
		vidport = 0x3d4;
	}

	lines = real_mode->screen_info.orig_video_lines;
	cols = real_mode->screen_info.orig_video_cols;

	console_init();
	if (debug)
		putstr("early console in decompress_kernel\n");

	free_mem_ptr     = heap;	/* Heap */
	free_mem_end_ptr = heap + BOOT_HEAP_SIZE;

	if ((unsigned long)output & (MIN_KERNEL_ALIGN - 1))
		error("Destination address inappropriately aligned");
#ifdef CONFIG_X86_64
	if (heap > 0x3fffffffffffUL)
		error("Destination address too large");
#else
	if (heap > ((-__PAGE_OFFSET-(128<<20)-1) & 0x7fffffff))
		error("Destination address too large");
#endif
#ifndef CONFIG_RELOCATABLE
	if ((unsigned long)output != LOAD_PHYSICAL_ADDR)
		error("Wrong destination address");
#endif

	if (!quiet)
		putstr("\nDecompressing Linux... ");
	// 调用lib/decompress_xxx.c中的同名函数
	decompress(input_data, input_len, NULL, NULL, output, NULL, error);
	parse_elf(output);
	if (!quiet)
		putstr("done.\nBooting the kernel.\n");
	return;
}
```

##### 4.3.4.1.4 arch/x86/kernel/head_32.S

由arch/x86/boot/compressed/head_32.S节可知，解压内核后，执行的第一个函数是arch/x86/kernel/head_32.S中的startup_32()函数。该函数主要是为第一个Linux进程(进程0)建立执行环境，主要执行以下操作：

* 把段寄存器初始化为最终值
* 把内核的bss段填充为0
* 初始化包含在swapper_pg_dir的临时内核页表，并初始化pg0，以使线性地址一致地映射同一物理地址
* 把页全局目录的地址存放在cr3寄存器中，并通过设置cr0寄存器的PG位启用分页
* 把从BIOS中获得的系统参数和传递给操作系统的参数boot_params放入第一个页框中
* 为进程0建立内核态堆栈
* 该函数再一次清零eflags寄存器的所有位
* 调用setup_idt用空的中断处理程序填充中断描述符表IDT
* 识别处理器的型号
* 用编译好的GDT和IDT表的地址来填充gdtr和idtr寄存器
* 初始化虚拟机监视器xen
* 调用start_kernel()函数

函数startup_32()定义于arch/x86/kernel/head_32.S:

```
...
__HEAD
ENTRY(startup_32)
	movl pa(stack_start),%ecx

...
	movl $(__KERNEL_STACK_CANARY),%eax
	movl %eax,%gs

	xorl %eax,%eax			# Clear LDT
	lldt %ax

	cld					# gcc2 wants the direction flag cleared at all times
	pushl $0				# fake return address for unwinder
	movb $1, ready
	jmp *(initial_code)

...
	__REFDATA
.align 4
ENTRY(initial_code)
	.long i386_start_kernel			// 参见386_start_kernel()节

...
.data
.balign 4
ENTRY(stack_start)
	.long init_thread_union+THREAD_SIZE	// 为进程0建立内核态堆栈，参见init_thread_union节
```

###### 4.3.4.1.4.1 init_thread_union

该变量定义于include/linux/sched.h:

```
union thread_union {
	struct thread_info thread_info;				// 0号进程的thread_info
	unsigned long stack[THREAD_SIZE/sizeof(long)];	// THREAD_SIZE的取值为8kB
};

extern union thread_union init_thread_union;

init_thread_union实际定义于arch/x86/kernel/init_task.c中：
union thread_union init_thread_union __init_task_data =
	{ INIT_THREAD_INFO(init_task) };				// 参见arch/x86/include/asm/thread_info.h

struct task_struct init_task = INIT_TASK(init_task);	// 0号进程的task_struct
```

其中的__init_task_data定义于include/linux/init_task.h中：

```
/* Attach to the init_task data structure for proper alignment */
#define __init_task_data __attribute__((__section__(".data..init_task")))
```

###### 4.3.4.1.4.2 i386_start_kernel()

该函数定义于arch/x86/kernel/head32.c:

```
void __init i386_start_kernel(void)
{
	memblock_init();

	memblock_x86_reserve_range(__pa_symbol(&_text), __pa_symbol(&__bss_stop), "TEXT DATA BSS");

#ifdef CONFIG_BLK_DEV_INITRD
	/* Reserve INITRD */
	if (boot_params.hdr.type_of_loader && boot_params.hdr.ramdisk_image) {
		/* Assume only end is not page aligned */
		u64 ramdisk_image = boot_params.hdr.ramdisk_image;
		u64 ramdisk_size  = boot_params.hdr.ramdisk_size;
		u64 ramdisk_end   = PAGE_ALIGN(ramdisk_image + ramdisk_size);
		memblock_x86_reserve_range(ramdisk_image, ramdisk_end, "RAMDISK");
	}
#endif

	/* Call the subarch specific early setup function */
	switch (boot_params.hdr.hardware_subarch) {
	case X86_SUBARCH_MRST:
		x86_mrst_early_setup();
		break;
	case X86_SUBARCH_CE4100:
		x86_ce4100_early_setup();
		break;
	default:
		i386_default_early_setup();
		break;
	}

	/*
	 * At this point everything still needed from the boot loader
	 * or BIOS or kernel text should be early reserved or marked not
	 * RAM in e820. All other memory is free game.
	 */
	start_kernel();		// 参见start_kernel()节
}
```

###### 4.3.4.1.4.3 start_kernel()

As specified in chapter 16 of **Linux Device Drivers, 2nd Edition**:

> The architecture-independent starting point is start_kernel() in init/main.c. This function is invoked from architecture-specific code, to which it never returns.

由[386_start_kernel()](#)节可知，i386_start_kernel()将调用start_kernel()，其定义于init/main.c:

```
asmlinkage void __init start_kernel(void)
{
	char * command_line;
	/*
	 * 在arch/x86/kernel/vmlinux.lds中包含该变量的定义；
	 * 与__initcall_start[], __initcall_end[], __early_initcall_end[]类似，
	 * 参见__initcall_start[], __early_initcall_end[]节
	 */
	extern const struct kernel_param __start___param[], __stop___param[];

	smp_setup_processor_id();

	/*
	 * Need to run as early as possible, to initialize the
	 * lockdep hash:
	 */
	lockdep_init();					// 参见lockdep_init()节
	debug_objects_early_init();			// 参见debug_objects_early_init()节

	/*
	 * Set up the the initial canary ASAP:
	 */
	boot_init_stack_canary();

	cgroup_init_early();

	local_irq_disable();				// 关闭可屏蔽中断，与下文中的local_irq_enable()对应
	early_boot_irqs_disabled = true; 	// 在下文调用local_irq_enable()前将该变量置为false

/*
 * Interrupts are still disabled. Do necessary setups, then
 * enable them
 */

	// 参见Architecture-dependent routine / tick_handle_periodic()节
	tick_init();
	boot_cpu_init();
	page_address_init();
	printk(KERN_NOTICE "%s", linux_banner);
	/*
	 * 该函数与体系架构有关。内核启动命令行参见内核启动命令行节，
	 * 内存初始化参见boot_params.e820_map[]=>e820 / e820_saved节
	 */
	setup_arch(&command_line);
	mm_init_owner(&init_mm, &init_task);	// 与配置项CONFIG_MM_OWNER的取值有关
	mm_init_cpumask(&init_mm);			// 与配置项CONFIG_CPUMASK_OFFSTACK的取值有关
	setup_command_line(command_line);	// 保存命令行参数，以备后续使用，参见内核启动命令行节
	setup_nr_cpu_ids();
	setup_per_cpu_areas();			// 参见Per-CPU Variables的初始化节
	smp_prepare_boot_cpu();	/* arch-specific boot-cpu hooks */

	build_all_zonelists(NULL);
	page_alloc_init();

	// 解析boot_command_line中的内核选项，参见parse_early_param() / parse_args()节
	printk(KERN_NOTICE "Kernel command line: %s\n", boot_command_line);
	parse_early_param();
	parse_args("Booting kernel", static_command_line, __start___param,
				 __stop___param - __start___param, &unknown_bootoption);

	jump_label_init();

	/*
	 * These use large bootmem allocations and must precede
	 * kmem_cache_init(). 参见19.2.1.1.1 默认分配的log_buf节
	 */
	setup_log_buf(0);
	// 初始化PID哈希链表头，参见PID散列表和链表节
	pidhash_init();
	/*
	 * 初始化VFS的两个重要数据结构dcache和inode的缓存，
	 * 参见vfs_caches_init_early()节
	 */
	vfs_caches_init_early();
	/*
	 * sort_main_extable()用于把编译期间kbuild设置的异常表
	 * __start___ex_table和__stop___ex_table中的所有元素排序
	 * 参见include/asm-generic/vmlinux.lds.h和
	 * arch/x86/kernel/vmlinux.lds.S中的宏EXCEPTION_TABLE，
	 * 以及arch/x86/kernel/entry_32.S中的__ex_table
	 */
	sort_main_extable();
	// 初始化中断描述符表idt_table[NR_VECTORS]，参见trap_init()节
	trap_init();
	// 初始化内存管理，参见mm_init()节
	mm_init();

	/*
	 * Set up the scheduler prior starting any interrupts (such as the
	 * timer interrupt). Full topology setup happens at smp_init()
	 * time - but meanwhile we still have a functioning scheduler.
	 */
	sched_init();			// 初始化调度程序，参见sched_init()节
	/*
	 * Disable preemption - early bootup scheduling is extremely
	 * fragile until we cpu_idle() for the first time.
	 */
	preempt_disable();		// 与配置项CONFIG_PREEMPT_COUNT的取值有关，参见preempt_disable()节
	if (!irqs_disabled()) {
		printk(KERN_WARNING "start_kernel(): bug: interrupts were "
				"enabled *very* early, fixing it\n");
		local_irq_disable();
	}
	idr_init_cache();		// 参见idr_init_cache()节
	perf_event_init();		// 与配置项CONFIG_PERF_EVENTS的取值有关
	rcu_init();			// 参见RCU的初始化节
	radix_tree_init();
	/* init some links before init_ISA_irqs() */
	early_irq_init();		// 参见early_irq_init()节
	init_IRQ();			// 参见init_IRQ()节
	prio_tree_init();
	init_timers();			// 参见定时器模块的编译及初始化节
	hrtimers_init();			// 参见hrtimer的编译及初始化节
	softirq_init();			// 初始化软中断的TASKLET_SOFTIRQ和HI_SOFTIRQ，参见softirq_init()节
	timekeeping_init();
	// 初始化系统日期和时间，参见Architecture-dependent routine / tick_handle_periodic()节
	time_init();
	profile_init();			// 与配置项CONFIG_PROFILING的取值有关
	call_function_init();
	if (!irqs_disabled())
		printk(KERN_CRIT "start_kernel(): bug: interrupts were "
				 "enabled early\n");
	early_boot_irqs_disabled = false;
	local_irq_enable();		// 打开可屏蔽中断，与上文中的local_irq_disable()对应

	/* Interrupts are enabled now so all GFP allocations are safe. */
	gfp_allowed_mask = __GFP_BITS_MASK;

	kmem_cache_init_late();

	/*
	 * HACK ALERT! This is early. We're enabling the console before
	 * we've done PCI setups etc, and console_init() must be aware of
	 * this. But we do want output early, in case something goes wrong.
	 */
	/*
	 * 初始化系统控制台结构，该函数执行后可调用printk()函数
	 * 将log_buf中符合打印级别要求的系统信息打印到控制台上
	 */
	console_init();
	if (panic_later)
		/*
		 * 当系统出现无法继续运行下去的故障时才调用函数panic()，
		 * 该函数会导致系统中止，然后显示系统错误号
		 */
		panic(panic_later, panic_param);

	lockdep_info();

	/*
	 * Need to run this when irqs are enabled, because it wants
	 * to self-test [hard/soft]-irqs on/off lock inversion bugs
	 * too:
	 */
	locking_selftest();

#ifdef CONFIG_BLK_DEV_INITRD
	if (initrd_start && !initrd_below_start_ok &&
	    page_to_pfn(virt_to_page((void *)initrd_start)) < min_low_pfn) {
		 printk(KERN_CRIT "initrd overwritten (0x%08lx < 0x%08lx) - disabling it.\n",
		    page_to_pfn(virt_to_page((void *)initrd_start)), min_low_pfn);
		initrd_start = 0;
	}
#endif
	page_cgroup_init();
	enable_debug_pagealloc();
	debug_objects_mem_init();		// 与配置项CONFIG_DEBUG_OBJECTS的取值有关
	kmemleak_init();
	setup_per_cpu_pageset();
	numa_policy_init();
	if (late_time_init)			// 参见Architecture-dependent routine / tick_handle_periodic()节
		late_time_init();
	sched_clock_init();
	calibrate_delay();			// 确定CPU时钟的速度
	pidmap_init();				// 初始化pid_namespace结构的全局变量init_pid_ns
	anon_vma_init();
#ifdef CONFIG_X86
	if (efi_enabled)
		efi_enter_virtual_mode();
#endif
	thread_info_cache_init();
	cred_init();
	fork_init(totalram_pages);
	proc_caches_init();
	buffer_init();				// 初始化页高速缓存
	key_init();
	security_init();				// 初始化LSM，参见LSM的初始化节
	dbg_late_init();
	// 用于初始化VFS数据结构的slab缓存，参见vfs_caches_init()节
	vfs_caches_init(totalram_pages);
	signals_init();				// 建立数据结构sigqueue的slab缓存，参见信号的初始化节
	/* rootfs populating might need page-writeback */
	page_writeback_init();
#ifdef CONFIG_PROC_FS
	proc_root_init();			// 初始化proc文件系统，参见proc_root_init()节
#endif
	cgroup_init();
	cpuset_init();
	taskstats_init_early();
	delayacct_init();

	check_bugs();

	// 初始化高级配置和电源管理接口ACPI(Advanced Configuration and Power Interface)
	acpi_early_init(); /* before LAPIC and SMP init */
	sfi_init_late();

	ftrace_init();				// 与配置项CONFIG_FTRACE_MCOUNT_RECORD的取值有关

	/* Do the rest non-__init'ed, we're now alive */
	rest_init();				// 参见rest_init()节
}
```

###### 4.3.4.1.4.3.1 lockdep_init()

该函数定义于kernel/lockdep.c：

```
void lockdep_init(void)
{
	int i;

	/*
	 * Some architectures have their own start_kernel()
	 * code which calls lockdep_init(), while we also
	 * call lockdep_init() from the start_kernel() itself,
	 * and we want to initialize the hashes only once:
	 */
	if (lockdep_initialized)
		return;

	for (i = 0; i < CLASSHASH_SIZE; i++)		// CLASSHASH_SIZE取值为4096
		INIT_LIST_HEAD(classhash_table + i);	// 初始化散列表classhash_table[CLASSHASH_SIZE]

	for (i = 0; i < CHAINHASH_SIZE; i++)		// CHAINHASH_SIZE取值为16384
		INIT_LIST_HEAD(chainhash_table + i);	// 初始化散列表chainhash_table[CHAINHASH_SIZE]

	lockdep_initialized = 1;
}
```

###### 4.3.4.1.4.3.2 debug_objects_early_init()

由lib/Makefile中的如下规则：

```
obj-$(CONFIG_DEBUG_OBJECTS) += debugobjects.o
```

可知，debugobjects.c的编译与.config中的配置项CONFIG_DEBUG_OBJECTS有关。

如果CONFIG_DEBUG_OBJECTS=y，则在lib/debugobjects.c中，包含如下有关debug_object_early_init()的代码：

```
void __init debug_objects_early_init(void)
{
	int i;

	for (i = 0; i < ODEBUG_HASH_SIZE; i++)       // ODEBUG_HASH_SIZE的取值为16384
		raw_spin_lock_init(&obj_hash[i].lock);

	for (i = 0; i < ODEBUG_POOL_SIZE; i++)       // ODEBUG_POOL_SIZE的取值为512
		hlist_add_head(&obj_static_pool[i].node, &obj_pool);
}
```

如果CONFIG_DEBUG_OBJECTS=n，则在include/linux/debugobjects.h中，debug_object_early_init()被定义为空函数：

```
static inline void debug_objects_early_init(void) { }
```

###### 4.3.4.1.4.3.3 parse_early_param() / parse_args()

###### 4.3.4.1.4.3.3.1 内核启动命令行

在启动内核时，可以传递一个命令行字符串(即内核参数及其取值)，来控制内核启动的过程，例如：

```
BOOT_IMAGE=/boot/vmlinuz-3.5.0-17-generic root=UUID=61b86fe4-41d9-4de3-a204-f64bf26eb02d ro quiet splash vt.handoff=7
```

可通过下列命令查看内核启动命令行：

```
chenwx proc # cat /proc/cmdline
BOOT_IMAGE=/boot/vmlinuz-3.15.0-eudyptula-00054-g783e9e8-dirty root=UUID=fe67c2d0-9b0f-4fd6-8e97-463ce95a7e0c ro quiet splash
```

在内核启动过程中，涉及到如下几个命令行字符串变量：

* builtin_cmdline
* boot_command_line
* saved_command_line
* command_line
* static_command_line

最初，在引导内核启动时，boot_command_line是由boot_loader传递给内核的，参见arch/x86/kernel/head_32.S中的函数ENTRY(startup_32)。此后，在如下函数调用中：

```
start_kernel()
-> setup_arch(&command_line)			// 参见init/main.c
-> setup_command_line(command_line)		// 参见init/main.c
```

根据编译条件的不同，boot_command_line的取值可能会发生变化，并将boot_command_line赋值给其他变量，参见:

![boot_command_line](/assets/boot_command_line.jpg)

###### 4.3.4.1.4.3.3.1.1 boot_command_line的配置方式

上文中的变量boot_command_line可以由如下两种方式配置：

1) 通过LILO或GRUB等引导加载程序进行配置(参见[配置引导加载程序GRUB(或LILO](#)节)。例如/boot/grub/grub.cfg中包含如下内容：

```
...
menuentry 'Linux Mint 14 MATE 32-bit, 3.5.0-17-generic (/dev/sda1)' --class linuxmint --class gnu-linux --class gnu --class os {
	recordfail
	gfxmode $linux_gfx_mode
	insmod gzio
	insmod part_msdos
	insmod ext2
	set root='hd0,msdos1'
	if [ x$feature_platform_search_hint = xy ]; then
	  search --no-floppy --fs-uuid --set=root --hint-bios=hd0,msdos1 --hint-efi=hd0,msdos1 --hint-baremetal=ahci0,msdos1  61b86fe4-41d9-4de3-a204-f64bf26eb02d
	else
	  search --no-floppy --fs-uuid --set=root 61b86fe4-41d9-4de3-a204-f64bf26eb02d
	fi
	linux	/boot/vmlinuz-3.5.0-17-generic root=UUID=61b86fe4-41d9-4de3-a204-f64bf26eb02d ro   quiet splash $vt_handoff
	initrd	/boot/initrd.img-3.5.0-17-generic
}
menuentry 'Linux Mint 14 MATE 32-bit, 3.5.0-17-generic (/dev/sda1) -- recovery mode' --class linuxmint --class gnu-linux --class gnu --class os {
	recordfail
	insmod gzio
	insmod part_msdos
	insmod ext2
	set root='hd0,msdos1'
	if [ x$feature_platform_search_hint = xy ]; then
	  search --no-floppy --fs-uuid --set=root --hint-bios=hd0,msdos1 --hint-efi=hd0,msdos1 --hint-baremetal=ahci0,msdos1  61b86fe4-41d9-4de3-a204-f64bf26eb02d
	else
	  search --no-floppy --fs-uuid --set=root 61b86fe4-41d9-4de3-a204-f64bf26eb02d
	fi
	echo	'Loading Linux 3.5.0-17-generic ...'
	linux	/boot/vmlinuz-3.5.0-17-generic root=UUID=61b86fe4-41d9-4de3-a204-f64bf26eb02d ro recovery nomodeset
	echo	'Loading initial ramdisk ...'
	initrd	/boot/initrd.img-3.5.0-17-generic
}
...
```

2) 在配置内核(make \*config)时，通过如下选项配置内核参数。这些内核参数被静态的编译进内核，此后通过变量builtin_cmdline访问，参见arch/x86/kernel/setup.c中的setup_arch()函数：

```
Processor type and features
[*] Built-in kernel command line							=> CONFIG_CMDLINE_BOOL
()    Built-in kernel command string						=> CONFIG_CMDLINE
[ ]   Built-in command line overrides boot loader arguments		=> CONFIG_CMDLINE_OVERRIDE
```

###### 4.3.4.1.4.3.3.2 注册内核参数的处理函数

内核参数的处理函数被放置到.init.setup段或__param段，分别由宏early_param()/\_\_setup()和\_\_module_param_call()来完成。参见如下文档：

* Documentation/kernel-parameters.txt
* Documentation/sysctl/kernel.txt

###### 4.3.4.1.4.3.3.2.1 early_param()/\_\_setup()

宏early_param()和__setup()用于注册内核参数的处理函数，这些处理函数被放置到.init.setup段，参见include/linux/init.h:

```
struct obs_kernel_param {
	const char *str;				// 内核参数名
	int (*setup_func)(char *);		// 内核参数的处理函数
	int early;					// 是否为宏early_param注册的
};

#define __setup(str, fn)								\
	__setup_param(str, fn, fn, 0)

#define early_param(str, fn)							\
	__setup_param(str, fn, fn, 1)

#define __setup_param(str, unique_id, fn, early)				\
	static const char __setup_str_##unique_id[] __initconst	\
		__aligned(1) = str; 							\
	static struct obs_kernel_param __setup_##unique_id		\
		__used __section(.init.setup)					\
		__attribute__((aligned((sizeof(long)))))			\
		= { __setup_str_##unique_id, fn, early }
```

这两个宏定义并初始化了类型为struct obs_kernel_param的对象，它被编译到.init.setup段。根据vmlinux.lds如何生成节，arch/x86/kernel/vmlinux.lds.S被扩展为vmliux.lds(详见错误：引用源未找到)，其中，.init.setup段包含如下内容：

```
.init.data : AT(ADDR(.init.data) - 0xC0000000) { *(.init.data) *(.cpuinit.data) *(.meminit.data) . = ALIGN(8); __ctors_start = .; *(.ctors) __ctors_end = .; *(.init.rodata) . = ALIGN(8); __start_ftrace_events = .; *(_ftrace_events) __stop_ftrace_events = .; *(.cpuinit.rodata) *(.meminit.rodata) . = ALIGN(32); __dtb_start = .; *(.dtb.init.rodata) __dtb_end = .; . = ALIGN(16); __setup_start = .; *(.init.setup) __setup_end = .; __initcall_start = .; *(.initcallearly.init) __early_initcall_end = .; *(.initcall0.init) *(.initcall0s.init) *(.initcall1.init) *(.initcall1s.init) *(.initcall2.init) *(.initcall2s.init) *(.initcall3.init) *(.initcall3s.init) *(.initcall4.init) *(.initcall4s.init) *(.initcall5.init) *(.initcall5s.init) *(.initcallrootfs.init) *(.initcall6.init) *(.initcall6s.init) *(.initcall7.init) *(.initcall7s.init) __initcall_end = .; __con_initcall_start = .; *(.con_initcall.init) __con_initcall_end = .; __security_initcall_start = .; *(.security_initcall.init) __security_initcall_end = .; }
```

因而，可通过__setup_start和__setup_end查询内核参数并调用其处理函数。

注：early_param()与__setup()的不同之处在于，early_param()注册的内核参数必须在其他内核参数之前被处理。以参数foo及处理函数foo_func()为例，这两个宏的扩展结果如下：

* early_param("foo", foo_func)

```
static const char __setup_str_foo_func[] __initconst __aligned(1) = "foo";
static struct obs_kernel_param __setup_foo_func
	__used __section(.init.setup) __attribute__((aligned((sizeof(long)))))
	= { __setup_str_foo_func, foo_func, 1 }
```

* \_\_setup("foo", foo_func)

```
static const char __setup_str_foo_func[] __initconst __aligned(1) = "foo";
static struct obs_kernel_param __setup_foo_func
	__used __section(.init.setup) __attribute__((aligned((sizeof(long)))))
	= { __setup_str_foo_func, foo_func, 0 }
```

在init/main.c中，包含如下定义：

```
early_param("debug", debug_kernel);
early_param("quiet", quiet_kernel);
early_param("loglevel", loglevel);

__setup("reset_devices", set_reset_devices);
__setup("init=", init_setup);
__setup("rdinit=", rdinit_setup);
```

###### 4.3.4.1.4.3.3.2.2 \_\_module_param_call()

宏__module_param_call()用于注册内核参数的处理函数，这些处理函数被放置到__param段，其定义于include/linux/moduleparam.h:

```
struct kernel_param {
	const char *name;
	const struct kernel_param_ops *ops;
	u16 perm;
	u16 flags;
	union {
		void *arg;
		const struct kparam_string *str;
		const struct kparam_array *arr;
	};
};

/* This is the fundamental function for registering boot/module parameters. */
#define __module_param_call(prefix, name, ops, arg, isbool, perm)				\
	/* Default value instead of permissions? */							\
	static int __param_perm_check_##name __attribute__((unused)) =			\
	BUILD_BUG_ON_ZERO((perm) < 0 || (perm) > 0777 || ((perm) & 2))			\
	+ BUILD_BUG_ON_ZERO(sizeof(""prefix) > MAX_PARAM_PREFIX_LEN);			\
	static const char __param_str_##name[] = prefix #name;					\
	static struct kernel_param __moduleparam_const __param_##name			\
	__used													\
	__attribute__ ((unused,__section__ ("__param"),aligned(sizeof(void *))))	\
	= { __param_str_##name, ops, perm, isbool ? KPARAM_ISBOOL : 0,	{ arg } }
```

该宏定义并初始化了类型为struct kernel_param的对象，它被编译到__param段。根据vmlinux.lds如何生成节，arch/x86/kernel/vmlinux.lds.S被扩展为vmliux.lds(详见错误：引用源未找到)，其中包含如下内容：

```
__param : AT(ADDR(__param) - 0xC0000000) { __start___param = .; *(__param) __stop___param = .; }
```

因而可以通过__start___param和__stop___param查询内核参数并调用其处理函数。

使用__module_param_call()的宏，参见[13.1.3.1 与模块参数有关的宏](#)节。

###### 4.3.4.1.4.3.3.3 内核参数处理函数的调用过程

函数start_kernel()调用parse_early_param()和parse_args()来分别处理early_param()和__setup()注册的内核参数处理函数：

```
asmlinkage void __init start_kernel(void)
{
	...
	printk(KERN_NOTICE "Kernel command line: %s\n", boot_command_line);
	/*
	 * 解析命令行boot_command_line中的内核参数，并在.init.setup段
	 * 查找其处理函数，参见parse_early_param()节
	 */
	parse_early_param();
	/*
	 * 解析命令行static_command_line中的内核参数，并在__param段或
	 * .init.setup段查找其处理函数，参见parse_args()节
	 */
	parse_args("Booting kernel", static_command_line, __start___param,
			__stop___param - __start___param, &unknown_bootoption);
	...
}
```

###### 4.3.4.1.4.3.3.3.1 parse_early_param()

该函数定义于init/main.c:

```
/* Arch code calls this early on, or if not, just before other parsing. */
void __init parse_early_param(void)
{
	static __initdata int done = 0;
	static __initdata char tmp_cmdline[COMMAND_LINE_SIZE];

	if (done)
		return;

	// 解析命令行boot_command_line中的内核参数
	/* All fall through to do_early_param. */
	strlcpy(tmp_cmdline, boot_command_line, COMMAND_LINE_SIZE);
	parse_early_options(tmp_cmdline);
	done = 1;
}

void __init parse_early_options(char *cmdline)
{
	// 函数do_early_param()参见下文
	parse_args("early options", cmdline, NULL, 0, do_early_param);
}
```

其中，函数parse_args()定义于kernel/params.c:

```
/* Args looks like "foo=bar,bar2 baz=fuz wiz". */
int parse_args(const char *name, char *args,
	       const struct kernel_param *params, unsigned num,
	       int (*unknown)(char *param, char *val))
{
	char *param, *val;

	DEBUGP("Parsing ARGS: %s\n", args);

	/* Chew leading spaces */
	args = skip_spaces(args);

	while (*args) {
		int ret;
		int irq_was_disabled;

		args = next_arg(args, &param, &val);				// 获取下一个参数param及其取值val
		irq_was_disabled = irqs_disabled();
		ret = parse_one(param, val, params, num, unknown);	// 解析这个参数
		if (irq_was_disabled && !irqs_disabled()) {
			printk(KERN_WARNING "parse_args(): option '%s' enabled irq's!\n", param);
		}
		switch (ret) {
		case -ENOENT:
			printk(KERN_ERR "%s: Unknown parameter `%s'\n", name, param);
			return ret;
		case -ENOSPC:
			printk(KERN_ERR "%s: `%s' too large for parameter `%s'\n", name, val ?: "", param);
			return ret;
		case 0:
			break;
		default:
			printk(KERN_ERR "%s: `%s' invalid for parameter `%s'\n", name, val ?: "", param);
			return ret;
		}
	}

	/* All parsed OK. */
	return 0;
}
```

其中，函数parse_one()定义于kernel/params.c:

```
static int parse_one(char *param, char *val,
		     const struct kernel_param *params, unsigned num_params,
		     int (*handle_unknown)(char *param, char *val))
{
	unsigned int i;
	int err;

	/*
	 * 在params中查找同名参数param，若存在，则调用其处理函数；
	 * start_kernel() -> parse_early_param() -> parse_early_options() -> parse_args(...) 不进入此循环；而
	 * start_kernel() -> parse_args("Booting kernel", ..., &unknown_bootoption) 要进入此循环，
	 * 其中，params为__start___param，num_params为__stop___param - __start___param，即__param段的内容，
	 * 参见__module_param_call()节
	 */
	/* Find parameter */
	for (i = 0; i < num_params; i++) {
		if (parameq(param, params[i].name)) {
			/* No one handled NULL, so do it here. */
			if (!val && params[i].ops->set != param_set_bool)
				return -EINVAL;
			DEBUGP("They are equal!  Calling %p\n", params[i].ops->set);
			mutex_lock(&param_lock);
			err = params[i].ops->set(val, &params[i]);
			mutex_unlock(&param_lock);
			return err;
		}
	}

	/*
	 * start_kernel() -> parse_early_param() -> parse_early_options() -> parse_args() 要进入此分支，
	 * 此时，handle_unknown取值为do_early_param，即调用do_early_param()，参见下文；
	 * start_kernel() -> parse_args("Booting kernel", ..., &unknown_bootoption) 可能会进入此分支，
	 * 此时，handle_unknown取值为unknown_bootoption，即调用unknown_bootoption()，参见parse_args()节
	 */
	if (handle_unknown) {
		DEBUGP("Unknown argument: calling %p\n", handle_unknown);
		return handle_unknown(param, val);
	}

	DEBUGP("Unknown argument `%s'\n", param);
	return -ENOENT;
}
```

函数do_early_param()定义于init/main.c:

```
/* Check for early params. */
static int __init do_early_param(char *param, char *val)
{
	const struct obs_kernel_param *p;

	/*
	 * 查找指定内核参数的处理函数，并调用之；
	 * 变量__setup_start和__setup_end参见early_param()/__setup()节
	 */
	for (p = __setup_start; p < __setup_end; p++) {
		if ((p->early && parameq(param, p->str)) ||
		     (strcmp(param, "console") == 0 && strcmp(p->str, "earlycon") == 0) ) {
			if (p->setup_func(val) != 0)
				printk(KERN_WARNING "Malformed early option '%s'\n", param);
		}
	}
	/* We accept everything at this stage. */
	return 0;
}
```

###### 4.3.4.1.4.3.3.3.2 parse_args()

当start_kernel()调用完parse_early_param()后，将调用如下函数解析命令行static_command_line中的内核参数：

```
parse_args("Booting kernel", static_command_line, __start___param,
		__stop___param - __start___param, &unknown_bootoption);
```

其执行过程与[parse_early_param()](#)节类似，不同之处在于，函数parse_one()调用handle_unknown()时，实际调用的是函数unknown_bootoption()而不是do_early_param()。

函数unknown_bootoption()定义于init/main.c:

```
/*
 * Unknown boot options get handed to init, unless they look like
 * unused parameters (modprobe will find them in /proc/cmdline).
 */
static int __init unknown_bootoption(char *param, char *val)
{
	/* Change NUL term back to "=", to make "param" the whole string. */
	if (val) {
		/* param=val or param="val"? */
		if (val == param+strlen(param)+1)
			val[-1] = '=';
		else if (val == param+strlen(param)+2) {
			val[-2] = '=';
			memmove(val-1, val, strlen(val)+1);
			val--;
		} else
			BUG();
	}

	/* Handle obsolete-style parameters */
	// 若parse_one()在__param段找不到对应参数的处理函数，则在.init.setup段重新查找
	if (obsolete_checksetup(param))
		return 0;

	/* Unused module parameter. */
	if (strchr(param, '.') && (!val || strchr(param, '.') < val))
		return 0;

	if (panic_later)
		return 0;

	if (val) {
		/* Environment option */
		unsigned int i;
		for (i = 0; envp_init[i]; i++) {
			if (i == MAX_INIT_ENVS) {
				panic_later = "Too many boot env vars at `%s'";
				panic_param = param;
			}
			if (!strncmp(param, envp_init[i], val - param))
				break;
		}
		envp_init[i] = param;
	} else {
		/* Command line option */
		unsigned int i;
		for (i = 0; argv_init[i]; i++) {
			if (i == MAX_INIT_ARGS) {
				panic_later = "Too many boot init vars at `%s'";
				panic_param = param;
			}
		}
		argv_init[i] = param;
	}
	return 0;
}

static int __init obsolete_checksetup(char *line)
{
	const struct obs_kernel_param *p;
	int had_early_param = 0;

	/*
	 * 查找指定内核参数的处理函数，并调用之；
	 * 其处理过程与函数do_early_param()类似，参见parse_early_param()节
	 */
	p = __setup_start;
	do {
		int n = strlen(p->str);
		if (parameqn(line, p->str, n)) {
			if (p->early) {
				/* Already done in parse_early_param?
				 * (Needs exact match on param part).
				 * Keep iterating, as we can have early
				 * params and __setups of same names 8( */
				if (line[n] == '\0' || line[n] == '=')
					had_early_param = 1;
			} else if (!p->setup_func) {
				printk(KERN_WARNING "Parameter %s is obsolete," ignored\n", p->str);
				return 1;
			} else if (p->setup_func(line + n))	// 调用内核参数的处理函数
				return 1;
		}
		p++;
	} while (p < __setup_end);

	return had_early_param;
}
```

###### 4.3.4.1.4.3.4 vfs_caches_init_early()

该函数用于初始化目录项哈希表(dentry_hashtable)和索引节点哈希表(inode_hashtable)，其定义于fs/dcache.c:

```
void __init vfs_caches_init_early(void)
{
	dcache_init_early();		// 参见dcache_init_early()节
	inode_init_early();		// 参见inode_init_early()节
}
```

###### 4.3.4.1.4.3.4.1 dcache_init_early()

该函数定义于fs/dcache.c:

```
static unsigned int d_hash_mask __read_mostly;
static unsigned int d_hash_shift __read_mostly;

static struct hlist_bl_head *dentry_hashtable __read_mostly;
static __initdata unsigned long dhash_entries;

static void __init dcache_init_early(void)
{
	int loop;

	// 如果本函数没有创建目录项哈希表，则在dcache_init()中创建，参见dcache_init()节
	/* If hashes are distributed across NUMA nodes, defer
	 * hash allocation until vmalloc space is available.
	 */
	if (hashdist)
		return;

	dentry_hashtable = alloc_large_system_hash("Dentry cache",
					sizeof(struct hlist_bl_head),
					dhash_entries,
					13,
					HASH_EARLY,
					&d_hash_shift,
					&d_hash_mask,
					0);

	for (loop = 0; loop < (1 << d_hash_shift); loop++)
		INIT_HLIST_BL_HEAD(dentry_hashtable + loop);
}
```

其中，变量hashdist定义于mm/page_alloc.c:

```
int hashdist = HASHDIST_DEFAULT;
```

而HASHDIST_DEFAULT与配置项CONFIG_NUMA和CONFIG_64BIT有关，其定义于include/linux/bootmem.h:

```
/* Only NUMA needs hash distribution. 64bit NUMA architectures have
 * sufficient vmalloc space.
 */
#if defined(CONFIG_NUMA) && defined(CONFIG_64BIT)
#define HASHDIST_DEFAULT 1
#else
#define HASHDIST_DEFAULT 0
#endif
```

###### 4.3.4.1.4.3.4.2 inode_init_early()

该函数定义于fs/inode.c:

```
static unsigned int i_hash_mask __read_mostly;
static unsigned int i_hash_shift __read_mostly;
static struct hlist_head *inode_hashtable __read_mostly;

static __initdata unsigned long ihash_entries;

/*
 * Initialize the waitqueues and inode hash table.
 */
void __init inode_init_early(void)
{
	int loop;

	/* If hashes are distributed across NUMA nodes, defer
	 * hash allocation until vmalloc space is available.
	 */
	if (hashdist)
		return;

	inode_hashtable = alloc_large_system_hash("Inode-cache",
					sizeof(struct hlist_head),
					ihash_entries,
					14,
					HASH_EARLY,
					&i_hash_shift,
					&i_hash_mask,
					0);

	for (loop = 0; loop < (1 << i_hash_shift); loop++)
		INIT_HLIST_HEAD(&inode_hashtable[loop]);
}
```

###### 4.3.4.1.4.3.5 trap_init()

该函数用于设置中断描述符表中前19个陷阱门所对应的处理程序(这些中断向量都是CPU保留用于异常处理的，参见[中断处理简介](#)节)，其定义于arch/x86/kernel/trap.c:

```
void __init trap_init(void)
{
	int i;

#ifdef CONFIG_EISA
	void __iomem *p = early_ioremap(0x0FFFD9, 4);

	if (readl(p) == 'E' + ('I'<<8) + ('S'<<16) + ('A'<<24))
		EISA_bus = 1;
	early_iounmap(p, 4);
#endif

	/*
	 * 数组idt_table[]定义于arch/x86/kernel/traps.c，其中，NR_VECTORS=256
	 *    gate_desc idt_table[NR_VECTORS];
	 */
	set_intr_gate(0, &divide_error); 					// 填充idt_table[0]
	set_intr_gate_ist(2, &nmi, NMI_STACK); 				// 填充idt_table[2]
	/* int4 can be called from all */
	set_system_intr_gate(4, &overflow); 					// 填充idt_table[4]
	set_intr_gate(5, &bounds); 							// 填充idt_table[5]
	set_intr_gate(6, &invalid_op); 						// 填充idt_table[6]
	set_intr_gate(7, &device_not_available); 				// 填充idt_table[7]
#ifdef CONFIG_X86_32
	set_task_gate(8, GDT_ENTRY_DOUBLEFAULT_TSS); 			// 填充idt_table[8]
#else
	set_intr_gate_ist(8, &double_fault, DOUBLEFAULT_STACK);
#endif
	set_intr_gate(9, &coprocessor_segment_overrun); 			// 填充idt_table[9]
	set_intr_gate(10, &invalid_TSS); 					// 填充idt_table[10]
	set_intr_gate(11, &segment_not_present);				// 填充idt_table[11]
	set_intr_gate_ist(12, &stack_segment, STACKFAULT_STACK); 	// 填充idt_table[12]
	set_intr_gate(13, &general_protection); 				// 填充idt_table[13]
	set_intr_gate(15, &spurious_interrupt_bug); 			// 填充idt_table[15]
	set_intr_gate(16, &coprocessor_error); 				// 填充idt_table[16]
	set_intr_gate(17, &alignment_check); 					// 填充idt_table[17]
#ifdef CONFIG_X86_MCE
	set_intr_gate_ist(18, &machine_check, MCE_STACK); 		// 填充idt_table[18]
#endif
	set_intr_gate(19, &simd_coprocessor_error); 			// 填充idt_table[19]

	/*
	 * FIRST_EXTERNAL_VECTOR定义于arch/x86/include/asm/irq_vectors.h，取值为0x20
	 * 即设置位图used_vectors的前32比特为1，表示中断向量表idt_table的占用情况
	 */
	/* Reserve all the builtin and the syscall vector: */
	for (i = 0; i < FIRST_EXTERNAL_VECTOR; i++)
		set_bit(i, used_vectors);

// CONFIG_IA32_EMULATION是否和下面的CONFIG_X86_32冲突？
#ifdef CONFIG_IA32_EMULATION
	set_system_intr_gate(IA32_SYSCALL_VECTOR, ia32_syscall); 	// 填充idt_table[128]
	// IA32_SYSCALL_VECTOR定义于arch/x86/include/asm/irq_vectors.h，取值为0x80
	set_bit(IA32_SYSCALL_VECTOR, used_vectors);
#endif

#ifdef CONFIG_X86_32
	/*
	 * SYSCALL_VECTOR定义于arch/x86/include/asm/irq_vectors.h，其取值为0x80
	 * system_call为系统调用总控程序，定义于arch/x86/kernel/entry_32.S，参见系统调用节
	 * 系统调用使用int 0x80中断，system_call根据系统调用号(保存于eax)查询sys_call_table，
	 * 找到对应的处理程序并执行
	 */
	set_system_trap_gate(SYSCALL_VECTOR, &system_call); 		// 填充idt_table[128]
	/*
	 * SYSCALL_VECTOR定义于arch/x86/include/asm/irq_vectors.h，其取值为0x80
	 * 在arch/x86/kernel/traps.c中，used_vectors通过宏DECLARE_BITMAP定义：
	 *     DECLARE_BITMAP(used_vectors, NR_VECTORS);
	 *     扩展后：unsigned long used_vectors[256];
	 */
	set_bit(SYSCALL_VECTOR, used_vectors);
#endif

	/*
	 * Should be a barrier for any external CPU state:
	 */
	cpu_init();				// 该函数定义于arch/x86/kernel/cpu/common.c

	x86_init.irqs.trap_init();		// x86_init定义于arch/x86/kernel/x86_init.c
}
```

idt_table[]示意图，参见Subjects/Chapter04_Boot/Figures/idt_table[].jpg

###### 4.3.4.1.4.3.5.1 设置idt_table表项

函数trap_init()调用如下函数设置idt_table表项，其定义于arch/x86/include/asm/desc.h：

* set_intr_gate()

```
static inline void set_intr_gate(unsigned int n, void *addr)
{
	BUG_ON((unsigned)n > 0xFF);
	_set_gate(n, GATE_INTERRUPT, addr, 0, 0, __KERNEL_CS);
}
```

* set_intr_gate_ist()

```
static inline void set_intr_gate_ist(int n, void *addr, unsigned ist)
{
	BUG_ON((unsigned)n > 0xFF);
	_set_gate(n, GATE_INTERRUPT, addr, 0, ist, __KERNEL_CS);
}

- set_system_intr_gate()
static inline void set_system_intr_gate(unsigned int n, void *addr)
{
	BUG_ON((unsigned)n > 0xFF);
	_set_gate(n, GATE_INTERRUPT, addr, 0x3, 0, __KERNEL_CS);
}
```

* set_task_gate()

```
static inline void set_intr_gate(unsigned int n, void *addr)
{
	BUG_ON((unsigned)n > 0xFF);
	_set_gate(n, GATE_INTERRUPT, addr, 0, 0, __KERNEL_CS);
}
```

* set_system_trap_gate()

```
static inline void set_system_trap_gate(unsigned int n, void *addr)
{
	BUG_ON((unsigned)n > 0xFF);
	_set_gate(n, GATE_TRAP, addr, 0x3, 0, __KERNEL_CS);
}
```

上述函数均通过调用_set_gate()来实现，其定义于arch/x86/include/asm/desc.h:

```
static inline void _set_gate(int gate, unsigned type, void *addr, unsigned dpl,
									unsigned ist, unsigned seg)
{
	gate_desc s;

	// 参见pack_gate()节
	pack_gate(&s, type, (unsigned long)addr, dpl, ist, seg);

	/*
	 * does not need to be atomic because it is only done once at
	 * setup time
	 */
	// 将描述符s拷贝到idt_table[gate]项中，参见write_idt_entry()节
	write_idt_entry(idt_table, gate, &s);
}
```

###### 4.3.4.1.4.3.5.1.1 pack_gate()

pack_gate()定义于arch/x86/include/asm/desc.h：

```
#ifdef CONFIG_X86_64

static inline void pack_gate(gate_desc *gate, unsigned type, unsigned long func,
					  unsigned dpl, unsigned ist, unsigned seg)
{
	gate->offset_low		= PTR_LOW(func);
	gate->segment		= __KERNEL_CS;
	gate->ist			= ist;
	gate->p			= 1;
	gate->dpl			= dpl;
	gate->zero0		= 0;
	gate->zero1		= 0;
	gate->type			= type;
	gate->offset_middle	= PTR_MIDDLE(func);
	gate->offset_high	= PTR_HIGH(func);
}

#else

/*
 * 给指定的描述符赋值，参见段描述符节
 * 注意：没有定义宏CONFIG_X86_64的情况下，入参flags是无效的
 */
static inline void pack_gate(gate_desc *gate, unsigned char type, unsigned long base,
					  unsigned dpl, unsigned flags, unsigned short seg)
{
	gate->a = (seg << 16) | (base & 0xffff);
	gate->b = (base & 0xffff0000) | (((0x80 | type | (dpl << 5)) & 0xff) << 8);
}

#endif
```

###### 4.3.4.1.4.3.5.1.2 write_idt_entry()

该函数定义于arch/x86/include/asm/desc.h：

```
#ifdef CONFIG_PARAVIRT
#include <asm/paravirt.h>
#else
#define write_idt_entry(dt, entry, g)		native_write_idt_entry(dt, entry, g)
#endif	/* CONFIG_PARAVIRT */

static inline void native_write_idt_entry(gate_desc *idt, int entry, const gate_desc *gate)
{
	memcpy(&idt[entry], gate, sizeof(*gate));
}
```

###### 4.3.4.1.4.3.6 mm_init()

该函数定义于init/main.c:

```
static void __init mm_init(void)
{
	/*
	 * page_cgroup requires countinous pages as memmap
	 * and it's bigger than MAX_ORDER unless SPARSEMEM.
	 */
	page_cgroup_init_flatmem();
	mem_init();				// 参见mem_init()节
	kmem_cache_init();			// 参见 General Cache/kmem_cache_init()节
	percpu_init_late();
	pgtable_cache_init();
	vmalloc_init();
}

4.3.4.1.4.3.6.1 mem_init()

该函数定义于arch/x86/mm/init_32.c:
void __init mem_init(void)
{
	int codesize, reservedpages, datasize, initsize;
	int tmp;

	pci_iommu_alloc();

#ifdef CONFIG_FLATMEM
	BUG_ON(!mem_map);
#endif
	/* this will put all low memory onto the freelists */
	// 参见free_all_bootmem()/free_all_bootmem_core()节
	totalram_pages += free_all_bootmem();

	reservedpages = 0;
	/*
	 * max_low_pfn的含义参见错误：引用源未找到，其取值被如下函数更新：
	 * start_kernel() -> setup_arch() -> find_low_pfn_range()
	 */
	for (tmp = 0; tmp < max_low_pfn; tmp++)
		/*
		 * Only count reserved RAM pages:
		 */
		if (page_is_ram(tmp) && PageReserved(pfn_to_page(tmp)))
			reservedpages++;

	// 将高端内存转入Buddy Allocator System中管理，参见set_highmem_pages_init()节
	set_highmem_pages_init();

	// 各变量的含义参见错误：引用源未找到
	codesize =  (unsigned long) &_etext - (unsigned long) &_text;
	datasize =  (unsigned long) &_edata - (unsigned long) &_etext;
	initsize =  (unsigned long) &__init_end - (unsigned long) &__init_begin;

	// 打印输出参见early_node_map[]=>node_data[]->node_zones[]节NOTE 13
	printk(KERN_INFO "Memory: %luk/%luk available "
		"(%dk kernel code, %dk reserved, %dk data, %dk init, %ldk highmem)\n",
		nr_free_pages() << (PAGE_SHIFT-10), num_physpages << (PAGE_SHIFT-10),
		codesize >> 10, reservedpages << (PAGE_SHIFT-10),
		datasize >> 10, initsize >> 10, totalhigh_pages << (PAGE_SHIFT-10));

	// 打印输出参见early_node_map[]=>node_data[]->node_zones[]节NOTE 13
	printk(KERN_INFO "virtual kernel memory layout:\n"
		"    fixmap  : 0x%08lx - 0x%08lx   (%4ld kB)\n"
#ifdef CONFIG_HIGHMEM
		"    pkmap   : 0x%08lx - 0x%08lx   (%4ld kB)\n"
#endif
		"    vmalloc : 0x%08lx - 0x%08lx   (%4ld MB)\n"
		"    lowmem  : 0x%08lx - 0x%08lx   (%4ld MB)\n"
		"      .init : 0x%08lx - 0x%08lx   (%4ld kB)\n"
		"      .data : 0x%08lx - 0x%08lx   (%4ld kB)\n"
		"      .text : 0x%08lx - 0x%08lx   (%4ld kB)\n",

		FIXADDR_START, FIXADDR_TOP,
		(FIXADDR_TOP - FIXADDR_START) >> 10,

#ifdef CONFIG_HIGHMEM
		PKMAP_BASE, PKMAP_BASE+LAST_PKMAP*PAGE_SIZE,
		(LAST_PKMAP*PAGE_SIZE) >> 10,
#endif

		VMALLOC_START, VMALLOC_END,
		(VMALLOC_END - VMALLOC_START) >> 20,

		(unsigned long)__va(0), (unsigned long)high_memory,
		((unsigned long)high_memory - (unsigned long)__va(0)) >> 20,

		(unsigned long)&__init_begin, (unsigned long)&__init_end,
		((unsigned long)&__init_end - (unsigned long)&__init_begin) >> 10,

		(unsigned long)&_etext, (unsigned long)&_edata,
		((unsigned long)&_edata - (unsigned long)&_etext) >> 10,

		(unsigned long)&_text, (unsigned long)&_etext,
		((unsigned long)&_etext - (unsigned long)&_text) >> 10);

	/*
	 * Check boundaries twice: Some fundamental inconsistencies can
	 * be detected at build time already.
	 */
#define __FIXADDR_TOP (-PAGE_SIZE)
#ifdef CONFIG_HIGHMEM
	BUILD_BUG_ON(PKMAP_BASE + LAST_PKMAP*PAGE_SIZE	> FIXADDR_START);
	BUILD_BUG_ON(VMALLOC_END > PKMAP_BASE);
#endif
#define high_memory (-128UL << 20)
	BUILD_BUG_ON(VMALLOC_START >= VMALLOC_END);
#undef high_memory
#undef __FIXADDR_TOP

#ifdef CONFIG_HIGHMEM
	BUG_ON(PKMAP_BASE + LAST_PKMAP*PAGE_SIZE > FIXADDR_START);
	BUG_ON(VMALLOC_END > PKMAP_BASE);
#endif
	BUG_ON(VMALLOC_START >= VMALLOC_END);
	BUG_ON((unsigned long)high_memory > VMALLOC_START);

	if (boot_cpu_data.wp_works_ok < 0)
		test_wp_bit();
}
```

###### 4.3.4.1.4.3.6.1.1 free_all_bootmem()/free_all_bootmem_core()

Once free_all_bootmem() returns, all the pages in ZONE_NORMAL have been given to the buddy allocator. See section early_node_map[]=>node_data[]->node_zones[].

函数free_all_bootmem()定义于mm/bootmem.c:

```
static struct list_head bdata_list __initdata = LIST_HEAD_INIT(bdata_list);

/**
 * free_all_bootmem - release free pages to the buddy allocator
* Returns the number of pages actually released.
 */
unsigned long __init free_all_bootmem(void)
{
	unsigned long total_pages = 0;
	bootmem_data_t *bdata;

	/*
	 * 类型bootmem_data_t参见Boot Memory Allocator/bootmem_data_t节，
	 * 变量bdata_list参见链表，函数free_all_bootmem_core()参见下文
	 */
	list_for_each_entry(bdata, &bdata_list, list)
		total_pages += free_all_bootmem_core(bdata);

	return total_pages;
}
```

其中，函数free_all_bootmem_core()定义于mm/bootmem.c:

```
static unsigned long __init free_all_bootmem_core(bootmem_data_t *bdata)
{
	int aligned;
	struct page *page;
	unsigned long start, end, pages, count = 0;

	if (!bdata->node_bootmem_map)
		return 0;

	start = bdata->node_min_pfn;
	end = bdata->node_low_pfn;

	/*
	 * If the start is aligned to the machines wordsize, we might
	 * be able to free pages in bulks of that order.
	 */
	aligned = !(start & (BITS_PER_LONG - 1));

	bdebug("nid=%td start=%lx end=%lx aligned=%d\n", bdata - bootmem_node_data, start, end, aligned);

	while (start < end) {
		unsigned long *map, idx, vec;

		map = bdata->node_bootmem_map;
		idx = start - bdata->node_min_pfn;
		vec = ~map[idx / BITS_PER_LONG];

		/*
		 * 根据起始页框号是否对齐、空闲页面的多少等因素释放页面到Buddy Allocator Sytem：
		 * 或者一次释放32个页面，或者一次释放一个页面
		 */
		if (aligned && vec == ~0UL && start + BITS_PER_LONG < end) {
			int order = ilog2(BITS_PER_LONG);
			__free_pages_bootmem(pfn_to_page(start), order);	// 每次释放32个页面，参见下文
			count += BITS_PER_LONG;
		} else {
			unsigned long off = 0;
			while (vec && off < BITS_PER_LONG) {
				if (vec & 1) {
					page = pfn_to_page(start + off);
					__free_pages_bootmem(page, 0);		// 每次释放1个页面，参见下文
					count++;
				}
				vec >>= 1;
				off++;
			}
		}
		start += BITS_PER_LONG;
	}

	/*
	 * 释放位图(bdata->node_bootmem_map)所占用的内存空间：
	 * 位图用来指示一个页面是否空闲，现在所有内存都归Buddy Allocator System管理，
	 * 该位图就没有存在的必要了
	 */
	page = virt_to_page(bdata->node_bootmem_map);
	pages = bdata->node_low_pfn - bdata->node_min_pfn;
	pages = bootmem_bootmap_pages(pages);
	count += pages;
	while (pages--)
		__free_pages_bootmem(page++, 0);					// 参见下文

	bdebug("nid=%td released=%lx\n", bdata - bootmem_node_data, count);

	return count;
}
```

其中，函数__free_pages_bootmem()定义于mm/page_alloc.c:

```
void __meminit __free_pages_bootmem(struct page *page, unsigned int order)
{
	if (order == 0) {				// 释放单个页面
		__ClearPageReserved(page);		// 复位page->flags中的标志位PG_reserved
		set_page_count(page, 0);		// page->_count = 0
		set_page_refcounted(page);		// page->_count = 1
		__free_page(page);			// 参见__free_page()/free_page()节
	} else {						// 释放2order个页面
		int loop;

		prefetchw(page);
		for (loop = 0; loop < BITS_PER_LONG; loop++) {
			struct page *p = &page[loop];

			if (loop + 1 < BITS_PER_LONG)
				prefetchw(p + 1);
			__ClearPageReserved(p); 	// 复位page->flags中的标志位PG_reserved
			set_page_count(p, 0);		// page->_count = 0
		}

		set_page_refcounted(page);		// page->_count = 1
		__free_pages(page, order);		// 参见free_pages()/__free_pages()节
	}
}
```

###### 4.3.4.1.4.3.6.1.2 set_highmem_pages_init()

函数set_highmem_pages_init()定义于arch/x86/mm/init_32.c:

```
void __init set_highmem_pages_init(void)
{
	struct zone *zone;
	int nid;

	for_each_zone(zone) {
		unsigned long zone_start_pfn, zone_end_pfn;

		if (!is_highmem(zone))
			continue;

		zone_start_pfn = zone->zone_start_pfn;
		zone_end_pfn = zone_start_pfn + zone->spanned_pages;

		nid = zone_to_nid(zone);
		printk(KERN_INFO "Initializing %s for node %d (%08lx:%08lx)\n",
				zone->name, nid, zone_start_pfn, zone_end_pfn);

		add_highpages_with_active_regions(nid, zone_start_pfn, zone_end_pfn);
	}
	totalram_pages += totalhigh_pages;
}
```

函数add_highpages_with_active_regions()定义于arch/x86/mm/init_32.c:

```
void __init add_highpages_with_active_regions(int nid, unsigned long start_pfn, unsigned long end_pfn)
{
	struct range *range;
	int nr_range;
	int i;

	// 获取页框数
	nr_range = __get_free_all_memory_range(&range, nid, start_pfn, end_pfn);

	for (i = 0; i < nr_range; i++) {
		struct page *page;
		int node_pfn;

		for (node_pfn = range[i].start; node_pfn < range[i].end; node_pfn++) {
			if (!pfn_valid(node_pfn))
				continue;
			page = pfn_to_page(node_pfn);
			/*
			 * 调用__free_page()将该页加入到Buddy Allocator System中
			 * 参见early_node_map[]=>node_data[]->node_zones[]节
			 * 和__free_page()/free_page()节
			 */
			add_one_highpage_init(page);
		}
	}
}
```

###### 4.3.4.1.4.3.7 sched_init()

该函数定义参见kernel/sched.c:

```
void __init sched_init(void)
{
	...

	/*
	 * Make us the idle thread. Technically, schedule() should not be
	 * called from this thread, however somewhere below it might be,
	 * but because we are the idle thread, we just pick up running again
	 * when this runqueue becomes "idle".
	 */
	/*
	 * 将当前进程作为idle进程，放置到当然CPU运行队列的rq->idle域，
	 * 参见运行队列结构/struct rq节
	 */
	init_idle(current, smp_processor_id());

	calc_load_update = jiffies + LOAD_FREQ;

	/*
	 * During early bootup we pretend to be a normal task:
	 */
	// 各调度类组成的链表参见pick_next_task()节和错误：引用源未找到节
	current->sched_class = &fair_sched_class;

	...
}
```

###### 4.3.4.1.4.3.8 early_irq_init()

该函数用于初始化数组irq_desc[](参见[struct irq_desc](#)节)，其定义于kernel/irq/irqdesc.c:

```
#ifdef CONFIG_SPARSE_IRQ
...
#else /* !CONFIG_SPARSE_IRQ */

int __init early_irq_init(void)
{
	int count, i, node = first_online_node;
	struct irq_desc *desc;

	init_irq_default_affinity();

	printk(KERN_INFO "NR_IRQS:%d\n", NR_IRQS);

	desc = irq_desc;
	count = ARRAY_SIZE(irq_desc);

	// 依次为数组irq_desc[]元素赋予默认值
	for (i = 0; i < count; i++) {
		desc[i].kstat_irqs = alloc_percpu(unsigned int);
		alloc_masks(&desc[i], GFP_KERNEL, node);
		raw_spin_lock_init(&desc[i].lock);
		lockdep_set_class(&desc[i].lock, &irq_desc_lock_class);
		desc_set_defaults(i, &desc[i], node, NULL);
	}
	return arch_early_irq_init();
}

#endif

static void desc_set_defaults(unsigned int irq, struct irq_desc *desc,
		int node, struct module *owner)
{
	int cpu;

	desc->irq_data.irq = irq;
	desc->irq_data.chip = &no_irq_chip;
	desc->irq_data.chip_data = NULL;
	desc->irq_data.handler_data = NULL;
	desc->irq_data.msi_desc = NULL;
	irq_settings_clr_and_set(desc, ~0, _IRQ_DEFAULT_INIT_FLAGS);
	irqd_set(&desc->irq_data, IRQD_IRQ_DISABLED);
	desc->handle_irq = handle_bad_irq;
	desc->depth = 1;
	desc->irq_count = 0;
	desc->irqs_unhandled = 0;
	desc->name = NULL;
	desc->owner = owner;
	for_each_possible_cpu(cpu)
		*per_cpu_ptr(desc->kstat_irqs, cpu) = 0;
	desc_smp_init(desc, node);
}
```

###### 4.3.4.1.4.3.9 init_IRQ()

该函数用于设置可屏蔽中断，其定义于arch/x86/kernel/irqinit.c:

```
void __init init_IRQ(void)
{
	int i;

	/*
	 * We probably need a better place for this, but it works for
	 * now ...
	 */
	x86_add_irq_domains();

	/*
	 * On cpu 0, Assign IRQ0_VECTOR..IRQ15_VECTOR's to IRQ 0..15.
	 * If these IRQ's are handled by legacy interrupt-controllers like PIC,
	 * then this configuration will likely be static after the boot. If
	 * these IRQ's are handled by more mordern controllers like IO-APIC,
	 * then this vector space can be freed and re-used dynamically as the
	 * irq's migrate etc.
	 */
	/*
	 * legacy_pic参见legacy_pic / x86_init节，其中legacy_pic->nr_legacy_irqs
	 * 取值为NR_IRQS_LEGACY，即16。因而，此处设置IRQ 0x30..0x3F，参见vector_irq节
	 * IRQ0_VECTOR定义于arch/x86/include/asm/irq_vectors.h，取值为48，参见中断处理简介节
	 */
	for (i = 0; i < legacy_pic->nr_legacy_irqs; i++)
		per_cpu(vector_irq, 0)[IRQ0_VECTOR + i] = i;

	/*
	 * x86_init参见legacy_pic / x86_init节，其中x86_init.irqs.intr_init
	 * 取值为native_init_IRQ。因而，如下语句调用函数native_init_IRQ()，
	 * 参见native_init_IRQ()节
	 */
	x86_init.irqs.intr_init();
}
```

###### 4.3.4.1.4.3.9.1 legacy_pic / x86_init

变量legacy_pic定义于arch/x86/kernel/i8259.c:

```
struct irq_chip i8259A_chip = {
	.name			= "XT-PIC",
	.irq_mask		= disable_8259A_irq,
	.irq_disable	= disable_8259A_irq,
	.irq_unmask	= enable_8259A_irq,
	.irq_mask_ack	= mask_and_ack_8259A,
};

struct legacy_pic default_legacy_pic = {
	// 取值为16，定义于arch/x86/include/asm/irq_vectors.h
	.nr_legacy_irqs	= NR_IRQS_LEGACY,
	.chip  		= &i8259A_chip,
	.mask 		= mask_8259A_irq,
	.unmask		= unmask_8259A_irq,
	.mask_all 		= mask_8259A,
	.restore_mask 	= unmask_8259A,
	.init 		= init_8259A,
	.irq_pending 	= i8259A_irq_pending,
	.make_irq 		= make_8259A_irq,
};

struct legacy_pic *legacy_pic = &default_legacy_pic;
```

变量x86_init定义于arch/x86/kernel/x86_init.c:

```
/*
 * The platform setup functions are preset with the default functions
 * for standard PC hardware.
 */
struct x86_init_ops x86_init __initdata = {

	.resources = {
		.probe_roms		= probe_roms,
		.reserve_resources	= reserve_standard_io_resources,
		.memory_setup		= default_machine_specific_memory_setup,
	},

	.mpparse = {
		.mpc_record		= x86_init_uint_noop,
		.setup_ioapic_ids	= x86_init_noop,
		.mpc_apic_id		= default_mpc_apic_id,
		.smp_read_mpc_oem	= default_smp_read_mpc_oem,
		.mpc_oem_bus_info	= default_mpc_oem_bus_info,
		.find_smp_config		= default_find_smp_config,
		.get_smp_config		= default_get_smp_config,
	},

	.irqs = {
		.pre_vector_init		= init_ISA_irqs,
		.intr_init			= native_init_IRQ,
		.trap_init			= x86_init_noop,
	},

	.oem = {
		.arch_setup		= x86_init_noop,
		.banner			= default_banner,
	},

	.mapping = {
		.pagetable_reserve	= native_pagetable_reserve,
	},

	.paging = {
		.pagetable_setup_start= native_pagetable_setup_start,
		.pagetable_setup_done	= native_pagetable_setup_done,
	},

	.timers = {
		.setup_percpu_clockev	= setup_boot_APIC_clock,
		.tsc_pre_init		= x86_init_noop,
		.timer_init		= hpet_time_init,
		.wallclock_init		= x86_init_noop,
	},

	.iommu = {
		.iommu_init		= iommu_init_noop,
	},

	.pci = {
		.init				= x86_default_pci_init,
		.init_irq			= x86_default_pci_init_irq,
		.fixup_irqs		= x86_default_pci_fixup_irqs,
	},
};
```

###### 4.3.4.1.4.3.9.2 native_init_IRQ()

该函数定义于arch/x86/kernel/irqinit.c:

```
void __init native_init_IRQ(void)
{
	int i;

	/*
	 * x86_init参见legacy_pic / x86_init节，其中x86_init.irqs.pre_vector_init
	 * 取值为init_ISA_irqs，因而如下语句调用函数init_ISA_irqs()，参见init_ISA_irqs()节
	 */
	/* Execute any quirks before the call gates are initialised: */
	x86_init.irqs.pre_vector_init();

	// 参见apic_intr_init()节
	apic_intr_init();

	/*
	 * Cover the whole vector space, no vector can escape
	 * us. (some of these will be overridden and become
	 * 'special' SMP interrupts)
	 */
	for (i = FIRST_EXTERNAL_VECTOR; i < NR_VECTORS; i++) {
		/*
		 * IA32_SYSCALL_VECTOR could be used in trap_init already.
		 * used_vectors定义于arch/x86/kernel/traps.c
		 * used_vectors is BITMAP for irq which is not managed by percpu vector_irq
		 * used_vectors中的前32个比特位在trap_init()中被赋值，参见trap_init()节
		 */
		if (!test_bit(i, used_vectors))
			// 数组interrupt[]参见interrupt[]节
			set_intr_gate(i, interrupt[i-FIRST_EXTERNAL_VECTOR]);
	}

	// IRQ2 is cascade interrupt to second interrupt controller
	if (!acpi_ioapic && !of_ioapic)
		setup_irq(2, &irq2);

#ifdef CONFIG_X86_32
	/*
	 * External FPU? Set up irq13 if so, for
	 * original braindamaged IBM FERR coupling.
	 */
	if (boot_cpu_data.hard_math && !cpu_has_fpu)
		setup_irq(FPU_IRQ, &fpu_irq); 		// FPU_IRQ取值为13

	irq_ctx_init(smp_processor_id());
#endif
}
```

###### 4.3.4.1.4.3.9.2.1 init_ISA_irqs()

该函数用于设置ISA中断(IRQ 0x30-0x3F，参见中断处理简介节)所对应的中断处理程序，其定义于arch/x86/kernel/irqinit.c:

```
void __init init_ISA_irqs(void)
{
	// 外部可屏蔽中断采用8259A中断控制器，参见legacy_pic / x86_init节和节
	struct irq_chip *chip = legacy_pic->chip;
	const char *name = chip->name;
	int i;

#if defined(CONFIG_X86_64) || defined(CONFIG_X86_LOCAL_APIC)
	init_bsp_APIC();
#endif
	// 调用init_8259A()初始化8259A中断控制器，参见legacy_pic / x86_init节
	legacy_pic->init(0);

	// 依次设置8259A控制的16个中断向量对应的中断处理函数，即handle_level_irq()
	for (i = 0; i < legacy_pic->nr_legacy_irqs; i++)
		irq_set_chip_and_handler_name(i, chip, handle_level_irq, name);
}
```

其中，irq_set_chip_and_handler_name()设置中断控制器芯片及中断处理函数，其定义于kernel/irq/chip.c:

```
void irq_set_chip_and_handler_name(unsigned int irq, struct irq_chip *chip,
			      irq_flow_handler_t handle, const char *name)
{
	irq_set_chip(irq, chip);
	__irq_set_handler(irq, handle, 0, name);
}

/**
 *	irq_set_chip - set the irq chip for an irq
 *	@irq:	irq number
 *	@chip:	pointer to irq chip description structure
 */
int irq_set_chip(unsigned int irq, struct irq_chip *chip)
{
	unsigned long flags;
	struct irq_desc *desc = irq_get_desc_lock(irq, &flags, 0);

	if (!desc)
		return -EINVAL;

	if (!chip)
		chip = &no_irq_chip;

	desc->irq_data.chip = chip;
	irq_put_desc_unlock(desc, flags);
	/*
	 * For !CONFIG_SPARSE_IRQ make the irq show up in
	 * allocated_irqs. For the CONFIG_SPARSE_IRQ case, it is
	 * already marked, and this call is harmless.
	 */
	irq_reserve_irq(irq);
	return 0;
}

void __irq_set_handler(unsigned int irq, irq_flow_handler_t handle, int is_chained, const char *name)
{
	unsigned long flags;
	struct irq_desc *desc = irq_get_desc_buslock(irq, &flags, 0);

	if (!desc)
		return;

	if (!handle) {
		handle = handle_bad_irq;
	} else {
		if (WARN_ON(desc->irq_data.chip == &no_irq_chip))
			goto out;
	}

	/* Uninstall? */
	if (handle == handle_bad_irq) {
		if (desc->irq_data.chip != &no_irq_chip)
			mask_ack_irq(desc);
		irq_state_set_disabled(desc);
		desc->depth = 1;
	}
	// 设置中断处理函数为handle_level_irq()，参见desc->handle_irq()/handle_level_irq()节
	desc->handle_irq = handle;
	desc->name = name;

	if (handle != handle_bad_irq && is_chained) {
		irq_settings_set_noprobe(desc);
		irq_settings_set_norequest(desc);
		irq_settings_set_nothread(desc);
		irq_startup(desc);
	}
out:
	irq_put_desc_busunlock(desc, flags);
}
```

###### 4.3.4.1.4.3.9.2.2 apic_intr_init()

该函数设置APIC可屏蔽中断所对应的中断处理程序，其定义于arch/x86/kernel/irqinit.c:

```
static void __init apic_intr_init(void)
{
	smp_intr_init();

#ifdef CONFIG_X86_THERMAL_VECTOR
	alloc_intr_gate(THERMAL_APIC_VECTOR, thermal_interrupt); 		// IRQ 0xFA
#endif
#ifdef CONFIG_X86_MCE_THRESHOLD
	alloc_intr_gate(THRESHOLD_APIC_VECTOR, threshold_interrupt); 	// IRQ 0xF9
#endif

#if defined(CONFIG_X86_64) || defined(CONFIG_X86_LOCAL_APIC)
	/* self generated IPI for local APIC timer */
	alloc_intr_gate(LOCAL_TIMER_VECTOR, apic_timer_interrupt); 		// IRQ 0xEF

	/* IPI for X86 platform specific use */
	alloc_intr_gate(X86_PLATFORM_IPI_VECTOR, x86_platform_ipi); 	// IRQ 0xF7

	/* IPI vectors for APIC spurious and error interrupts */
	alloc_intr_gate(SPURIOUS_APIC_VECTOR, spurious_interrupt); 		// IRQ 0xFF
	alloc_intr_gate(ERROR_APIC_VECTOR, error_interrupt); 			// IRQ 0xFE

	/* IRQ work interrupts: */
# ifdef CONFIG_IRQ_WORK
	alloc_intr_gate(IRQ_WORK_VECTOR, irq_work_interrupt); 			// IRQ 0xF6
# endif

#endif
}
```

###### 4.3.4.1.4.3.9.2.3 interrupt[]

数组interrupt[]定义于arch/x86/kernel/entry_32.S:

```
/*
 * Build the entry stubs and pointer table with some assembler magic.
 * We pack 7 stubs into a single 32-byte chunk, which will fit in a
 * single cache line on all modern x86 implementations.
 */
.section .init.rodata,"a"
ENTRY(interrupt)
.section .entry.text, "ax"
	.p2align 5
	.p2align CONFIG_X86_L1_CACHE_SHIFT
ENTRY(irq_entries_start)
	RING0_INT_FRAME
vector=FIRST_EXTERNAL_VECTOR					// FIRST_EXTERNAL_VECTOR取值为0x20
.rept (NR_VECTORS-FIRST_EXTERNAL_VECTOR+6)/7		// 外层循环，共32次
	.balign 32								// 32字节对齐
	.rept	7								// 内层循环，共7次
		.if vector < NR_VECTORS
			.if vector <> FIRST_EXTERNAL_VECTOR
				CFI_ADJUST_CFA_OFFSET -4		// 按照CFA规则修改前一个offset，以达到4字节对齐
			.endif
1: 			pushl_cfi $(~vector+0x80) 	/* Note: always in signed byte range */ // 占2字节
			.if ((vector-FIRST_EXTERNAL_VECTOR)%7) <> 6
				jmp 2f					// 跳转到2处，即long jmp，占5字节
			.endif
			.previous
			.long 1b						// 跳转到1处，即short jmp，占2字节
			.section .entry.text, "ax"
			vector=vector+1
		.endif
	.endr
2: 	jmp common_interrupt						// 跳转到common_interrupt执行
.endr
END(irq_entries_start)

.previous
END(interrupt)
.previous

/*
 * the CPU automatically disables interrupts when executing an IRQ vector,
 * so IRQ-flags tracing has to follow that:
 */
	.p2align CONFIG_X86_L1_CACHE_SHIFT
common_interrupt:
	addl $-0x80,(%esp)	/* Adjust vector into the [-256,-1] range */
	SAVE_ALL			// 保存中断处理程序可能用到的寄存器
	TRACE_IRQS_OFF
	movl %esp,%eax		// 把栈顶指针传给eax寄存器，该寄存器的内容将作为do_IRQ()函数的入参
	call do_IRQ		// 调用do_IRQ()处理中断，参见do_IRQ()节
	jmp ret_from_intr	// 参见ret_from_intr节
ENDPROC(common_interrupt)
	CFI_ENDPROC
```

ENTRY(interrupt)通过伪指令.rept和.endr，在代码段产生(NR_VECTORS - FIRST_EXTERNAL_VECTOR)个跳转到common_interrupt的汇编代码片段，起始地址为irq_entries_start；在数据段产生一个中断数组的符号interrupt，用于记录产生代码段中每个中断向量处理的汇编代码片段地址，在C语言中将interrupt符号作为中断数组变量导入(参见arch/x86/include/asm/hw_irq.h):

```
extern void (*__initconst interrupt[NR_VECTORS-FIRST_EXTERNAL_VECTOR])(void);
```

ENTRY(interrupt)编译之后，所生成的代码段和数据段内存布局如下：


ENTRIY(interrupt)汇编代码段主要由两个.rept构成，外层.rept循环(NR_VECTORS-FIRST_EXTERNAL_VECTOR+6)/7次，而其中每次内层.rept循环7次，内层循环所产生的代码以32字节对齐，内层.rept循环产生的代码如上图irq_entries_start中的粗黑方框所示。

以两层.rept循环生成jmp common_interrupt的目的在于：在内循环中，前6次循环产生的代码指令为push和short jmp，而第7次产生的代码指令为push和long jmp。push占2字节，short jmp占2字节，long jmp占5字节，故采取这种方式内层.rept循环7次产生的代码大小为：6 * (2 + 2) + 2 + 5 = 31 字节，而外层循环以32字节对齐。老版本每次都为push和long jmp，相对而言，这种新方法利用short jmp节省了内存开销。

每个中断门描述符将$(~vector+0x80)压栈后都会跳转到common_interrupt处执行。common_interrupt在保存中断现场之后，跳转到do_IRQ()进行中断处理，最后调用iret_from_intr进行中断返回、恢复中断上下文。

common_interrupt在调用do_IRQ()前，中断栈内存布局如下：


###### 4.3.4.1.4.3.10 softirq_init()

该函数用于初始化软中断相关数据结构，其定义于kernel/softirq.c:

```
void __init softirq_init(void)
{
	int cpu;

	for_each_possible_cpu(cpu) {
		int i;

		// 参见struct tasklet_节
		per_cpu(tasklet_vec, cpu).tail = &per_cpu(tasklet_vec, cpu).head;
		// 参见struct tasklet_节
		per_cpu(tasklet_hi_vec, cpu).tail = &per_cpu(tasklet_hi_vec, cpu).head;
		for (i = 0; i < NR_SOFTIRQS; i++)
			INIT_LIST_HEAD(&per_cpu(softirq_work_list[i], cpu));	// 参见softirq_work_list[]节
	}

	register_hotcpu_notifier(&remote_softirq_cpu_notifier);

	/*
	 * 设置软中断TASKLET_SOFTIRQ的服务程序为tasklet_action()，
	 * 参见struct softirq_节和tasklet_action()节
	 */
	open_softirq(TASKLET_SOFTIRQ, tasklet_action);
	/*
	 * 设置软中断HI_SOFTIRQ的服务程序为tasklet_hi_action()，
	 * 参见struct softirq_节和tasklet_hi_action()节
	 */
	open_softirq(HI_SOFTIRQ, tasklet_hi_action);
}

static struct notifier_block __cpuinitdata remote_softirq_cpu_notifier = {
	.notifier_call = remote_softirq_cpu_notify,
};
```

###### 4.3.4.1.4.3.10.1 tasklet_action()

该函数定义于kernel/softirq.c:

```
static void tasklet_action(struct softirq_action *a)
{
	struct tasklet_struct *list;

	// 从tasklet_vec中获取列表，参见struct tasklet_节
	local_irq_disable();
	list = __this_cpu_read(tasklet_vec.head);
	__this_cpu_write(tasklet_vec.head, NULL);
	__this_cpu_write(tasklet_vec.tail, &__get_cpu_var(tasklet_vec).head);
	local_irq_enable();

	while (list) {
		struct tasklet_struct *t = list;

		list = list->next;

		if (tasklet_trylock(t)) {
			if (!atomic_read(&t->count)) {
				if (!test_and_clear_bit(TASKLET_STATE_SCHED, &t->state))
					BUG();
				t->func(t->data); 	// 调用该tasklet的处理函数
				tasklet_unlock(t);
				continue;
			}
			tasklet_unlock(t);
		}

		local_irq_disable();
		t->next = NULL;
		*__this_cpu_read(tasklet_vec.tail) = t;
		__this_cpu_write(tasklet_vec.tail, &(t->next));
		// 将当前CPU对应的irq_stat变量中的域__softirq_pending中的标志位TASKLET_SOFTIRQ置位
		__raise_softirq_irqoff(TASKLET_SOFTIRQ);
		local_irq_enable();
	}
}
```

###### 4.3.4.1.4.3.10.2 tasklet_hi_action()

该函数定义于kernel/softirq.c:

```
static void tasklet_hi_action(struct softirq_action *a)
{
	struct tasklet_struct *list;

	local_irq_disable();
	list = __this_cpu_read(tasklet_hi_vec.head);
	__this_cpu_write(tasklet_hi_vec.head, NULL);
	__this_cpu_write(tasklet_hi_vec.tail, &__get_cpu_var(tasklet_hi_vec).head);
	local_irq_enable();

	while (list) {
		struct tasklet_struct *t = list;

		list = list->next;

		if (tasklet_trylock(t)) {
			if (!atomic_read(&t->count)) {
				if (!test_and_clear_bit(TASKLET_STATE_SCHED, &t->state))
					BUG();
				t->func(t->data); 	// 调用该tasklet的处理函数
				tasklet_unlock(t);
				continue;
			}
			tasklet_unlock(t);
		}

		local_irq_disable();
		t->next = NULL;
		*__this_cpu_read(tasklet_hi_vec.tail) = t;
		__this_cpu_write(tasklet_hi_vec.tail, &(t->next));
		__raise_softirq_irqoff(HI_SOFTIRQ);
		local_irq_enable();
	}
}
```

###### 4.3.4.1.4.3.11 vfs_caches_init()

该函数定义于fs/dcache.c:

```
struct kmem_cache *names_cachep __read_mostly;

void __init vfs_caches_init(unsigned long mempages)
{
	unsigned long reserve;

	/* Base hash sizes on available memory, with a reserve equal to
        150% of current kernel size */
	reserve = min((mempages - nr_free_pages()) * 3/2, mempages - 1);
	mempages -= reserve;

	// 参见Create a Specific Cache/kmem_cache_create()节
	names_cachep = kmem_cache_create("names_cache", PATH_MAX, 0,
			SLAB_HWCACHE_ALIGN|SLAB_PANIC, NULL);

	dcache_init();			// 参见dcache_init()节
	inode_init();			// 参见inode_init()节
	files_init(mempages); 	// 参见files_init()节
	mnt_init();			// 参见mnt_init()节
	bdev_cache_init();		// 参见bdev_cache_init()节
	chrdev_init();			// 参见chrdev_init()节
}
```

###### 4.3.4.1.4.3.11.1 dcache_init()

该函数与dcache_init_early()类似，如果在dache_init_early()已经创建了目录项哈希表，则dcache_init()不再创建；否则，创建目录项哈希表。因此，调用了本函数后，目录项哈希表(dentry_hashtable)一定存在了。其定义于fs/dcache.c:

```
static unsigned int d_hash_mask __read_mostly;
static unsigned int d_hash_shift __read_mostly;

static struct hlist_bl_head *dentry_hashtable __read_mostly;
static __initdata unsigned long dhash_entries;

static void __init dcache_init(void)
{
	int loop;

	/*
	 * A constructor could be added for stable state like the lists,
	 * but it is probably not worth it because of the cache nature
	 * of the dcache.
	 */
	dentry_cache = KMEM_CACHE(dentry, SLAB_RECLAIM_ACCOUNT|SLAB_PANIC|SLAB_MEM_SPREAD);

	// 参见dcache_init_early()节
	/* Hash may have been set up in dcache_init_early */
	if (!hashdist)
		return;

	dentry_hashtable = alloc_large_system_hash("Dentry cache",
					sizeof(struct hlist_bl_head),
					dhash_entries,
					13,
					0,
					&d_hash_shift,
					&d_hash_mask,
					0);

	for (loop = 0; loop < (1 << d_hash_shift); loop++)
		INIT_HLIST_BL_HEAD(dentry_hashtable + loop);
}
```

###### 4.3.4.1.4.3.11.2 inode_init()

该函数与inode_init_early()类似，如果在inode_init_early()已经创建了索引节点哈希表，则inode_init()不再创建；否则，创建索引节点哈希表。因此，调用了本函数后，索引节点哈希表(inode_hashtable)一定存在了。其定义于fs/inode.c:

```
static unsigned int i_hash_mask __read_mostly;
static unsigned int i_hash_shift __read_mostly;
static struct hlist_head *inode_hashtable __read_mostly;

static __initdata unsigned long ihash_entries;

void __init inode_init(void)
{
	int loop;

	/* inode slab cache */
	// 参见Create a Specific Cache/kmem_cache_create()节
	inode_cachep = kmem_cache_create("inode_cache",
					 sizeof(struct inode),
					 0,
					 (SLAB_RECLAIM_ACCOUNT|SLAB_PANIC|
					 SLAB_MEM_SPREAD),
					 init_once);

	// 参见inode_init_early()节
	/* Hash may have been set up in inode_init_early */
	if (!hashdist)
		return;

	inode_hashtable = alloc_large_system_hash("Inode-cache",
					sizeof(struct hlist_head),
					ihash_entries,
					14,
					0,
					&i_hash_shift,
					&i_hash_mask,
					0);

	for (loop = 0; loop < (1 << i_hash_shift); loop++)
		INIT_HLIST_HEAD(&inode_hashtable[loop]);
}
```

###### 4.3.4.1.4.3.11.3 files_init()

该函数定义于fs/fs_table.c:

```
/* sysctl tunables... */
struct files_stat_struct files_stat = {
	.max_files = NR_FILE
};

DECLARE_LGLOCK(files_lglock);
DEFINE_LGLOCK(files_lglock);

static struct percpu_counter nr_files __cacheline_aligned_in_smp;

void __init files_init(unsigned long mempages)
{
	unsigned long n;

	// 参见Create a Specific Cache/kmem_cache_create()节
	filp_cachep = kmem_cache_create("filp", sizeof(struct file), 0,
			SLAB_HWCACHE_ALIGN | SLAB_PANIC, NULL);

	/*
	 * One file with associated inode and dcache is very roughly 1K.
	 * Per default don't use more than 10% of our memory for files.
	 */
	n = (mempages * (PAGE_SIZE / 1024)) / 10;
	/*
	 * NR_FILE定义于include/linux/fs.h，取值为8192
	 * max_t()取n和NR_FILE中的最大值
	 */
	files_stat.max_files = max_t(unsigned long, n, NR_FILE);
	files_defer_init();				// 参见files_defer_init()节
	lg_lock_init(files_lglock);			// 实际调用函数files_lglock_lock_init()
	percpu_counter_init(&nr_files, 0);	// 将nr_files.count设置为0
}
```

###### 4.3.4.1.4.3.11.3.1 files_defer_init()

该函数定义于fs/file.c:

```
int sysctl_nr_open_max = 1024 * 1024; /* raised later */

void __init files_defer_init(void)
{
	int i;
	for_each_possible_cpu(i)
		fdtable_defer_list_init(i);
	sysctl_nr_open_max = min((size_t)INT_MAX, ~(size_t)0/sizeof(void *)) & -BITS_PER_LONG;
}
```

其中，fdtable_defer_list_init()用于初始化指定CPU的变量fdtable_defer_list，参见fs/file.c:

```
/*
 * We use this list to defer free fdtables that have vmalloced
 * sets/arrays. By keeping a per-cpu list, we avoid having to embed
 * the work_struct in fdtable itself which avoids a 64 byte (i386) increase in
 * this per-task structure.
 */
static DEFINE_PER_CPU(struct fdtable_defer, fdtable_defer_list);

static void __devinit fdtable_defer_list_init(int cpu)
{
	struct fdtable_defer *fddef = &per_cpu(fdtable_defer_list, cpu);
	spin_lock_init(&fddef->lock);
	// 将fddef->wq.func赋值为free_fdtable_work，用于释放该wq
	INIT_WORK(&fddef->wq, free_fdtable_work);
	fddef->next = NULL;	// 初始化fdtable链表
}
```

变量fdtable_defer_list的结构，参见Subjects/Chapter04_Boot/Figures/fdtable_defer_list.jpg

###### 4.3.4.1.4.3.11.4 mnt_init()

该函数定义于fs/namespace.c:

```
static struct list_head *mount_hashtable __read_mostly;
static struct kmem_cache *mnt_cache __read_mostly;
static struct rw_semaphore namespace_sem;

/* /sys/fs */
struct kobject *fs_kobj;

DEFINE_BRLOCK(vfsmount_lock);

void __init mnt_init(void)
{
	unsigned u;
	int err;

	init_rwsem(&namespace_sem);

	// 参见Create a Specific Cache/kmem_cache_create()节
	mnt_cache = kmem_cache_create("mnt_cache", sizeof(struct vfsmount),
						   0, SLAB_HWCACHE_ALIGN | SLAB_PANIC, NULL);

	mount_hashtable = (struct list_head *)__get_free_page(GFP_ATOMIC);

	if (!mount_hashtable)
		panic("Failed to allocate mount hash table\n");

	printk(KERN_INFO "Mount-cache hash table entries: %lu\n", HASH_SIZE);

	for (u = 0; u < HASH_SIZE; u++)
		INIT_LIST_HEAD(&mount_hashtable[u]);

	br_lock_init(vfsmount_lock);

	/*
	 * 注册并安装sysfs文件系统，参见sysfs_init()节:
	 * - 注册sysfs文件系统后，file_systems链表中添加了一个新元素sysfs_fs_type；
	 * - 安装sysfs文件系统后，生成了sysfs_mnt。通常，sysfs文件系统被安装在/sys下，
	 *   可通过 # mount 命令查看
	 */
	err = sysfs_init();
	if (err)
		printk(KERN_WARNING "%s: sysfs_init error: %d\n", __func__, err);

	// 创建sysfs文件系统的fs子目录，参见kobject_create_and_add()节
	fs_kobj = kobject_create_and_add("fs", NULL);
	if (!fs_kobj)
		printk(KERN_WARNING "%s: kobj create error\n", __func__);

	// 注册rootfs文件系统，参见init_rootfs()节
	init_rootfs();

	// 安装rootfs文件系统，生成系统根目录/，参见init_mount_tree()节和[NOTE]
	init_mount_tree();
}
```

**为什么不直接把真实的文件系统设置为根文件系统?**

答案很简单，因为内核中没有根文件系统的设备驱动，如USB等存放根文件系统的设备驱动，而且即便将根文件系统的设备驱动编译到内核中，此时这些设备驱动还尚未加载，其实所有的设备驱动都是由后面的kernel_init线程加载的，所以需要CPIO initrd, initrd和RAMDisk initrd。另外，root设备都是以设备文件的方式指定的，如果没有根文件系统，设备文件怎么可能存在呢?

```
start_kernel()
-> vfs_caches_init()
   -> mnt_init()
      -> init_mount_tree()			// 设置根文件系统
-> rest_init()
   -> kernel_init()
      -> do_pre_smp_initcalls()		// 加载设备驱动
      -> do_basic_setup()			// 加载设备驱动
```

###### 4.3.4.1.4.3.11.4.1 sysfs_init()

该函数定义于fs/sysfs/mount.c:

```
static struct vfsmount *sysfs_mnt;
struct kmem_cache *sysfs_dir_cachep;

int __init sysfs_init(void)
{
	int err = -ENOMEM;

	// 参见Create a Specific Cache/kmem_cache_create()节
	sysfs_dir_cachep = kmem_cache_create("sysfs_dir_cache", sizeof(struct sysfs_dirent), 0, 0, NULL);
	if (!sysfs_dir_cachep)
		goto out;

	// 调用bdi_init(&sysfs_backing_dev_info)初始化变量sysfs_backing_dev_info，其定义于fs/sysfs/inode.c
	err = sysfs_inode_init();
	if (err)
		goto out_err;

	// 将sysfs文件系统注册到file_systems中，参见注册/注销文件系统节和11.3.5.2 Sysfs的编译及初始化节
	err = register_filesystem(&sysfs_fs_type);
	if (!err) {
		// 安装sysfs文件系统，参见安装文件系统节
		sysfs_mnt = kern_mount(&sysfs_fs_type);
		if (IS_ERR(sysfs_mnt)) {
			printk(KERN_ERR "sysfs: could not mount!\n");
			err = PTR_ERR(sysfs_mnt);
			sysfs_mnt = NULL;
			// 若出错，则从file_systems中注销文件系统sysfs_fs_type，参见注册/注销文件系统节
			unregister_filesystem(&sysfs_fs_type);
			goto out_err;
		}
	} else
		goto out_err;
out:
	return err;
out_err:
	kmem_cache_destroy(sysfs_dir_cachep);
	sysfs_dir_cachep = NULL;
	goto out;
}
```

###### 4.3.4.1.4.3.11.4.2 init_rootfs()

关于rootfs的介绍，参见Documentation/filesystems/ramfs-rootfs-initramfs.txt:

> What is rootfs?
>
> Rootfs is a special instance of ramfs (or tmpfs, if that's enabled), which is always present in 2.6 systems. You can't unmount rootfs for approximately the same reason you can't kill the init process; rather than having special code to check for and handle an empty list, it's smaller and simpler for the kernel to just make sure certain lists can't become empty.
>
> Most systems just mount another filesystem over rootfs and ignore it. The amount of space an empty instance of ramfs takes up is tiny.

一般文件系统的注册都是通过宏module_init以及函数do_initcalls()来完成的，但是rootfs的注册却是通过函数init_rootfs()来完成的，这意味着rootfs的注册过程是Linux内核初始化阶段不可分割的一部分！

函数init_rootfs()定义于fs/ramfs/inode.c:

```
int __init init_rootfs(void)
{
	int err;

	// 初始化变量ramfs_backing_dev_info
	err = bdi_init(&ramfs_backing_dev_info);
	if (err)
		return err;

	/*
	 * 将rootfs文件系统注册到file_systems中，
	 * 参见注册/注销文件系统节和11.3.2.2 Rootfs编译与初始化及安装过程节
	 */
	err = register_filesystem(&rootfs_fs_type);
	if (err)
		bdi_destroy(&ramfs_backing_dev_info);

	return err;
}
```

###### 4.3.4.1.4.3.11.4.3 init_mount_tree()

该函数定义于fs/namespace.c:

```
static void __init init_mount_tree(void)
{
	struct vfsmount *mnt;
	struct mnt_namespace *ns;
	struct path root;

	/*
	 * 文件系统rootfs的注册，参见do_kern_mount()节；
	 * 此过程中调用了rootfs_mount()，参见rootfs_mount()节
	 */
	mnt = do_kern_mount("rootfs", 0, "rootfs", NULL);
	if (IS_ERR(mnt))
		panic("Can't create rootfs");

	// 创建新的namespace，并与mnt链接起来，参见Filesystem_20.jpg中红色框
	ns = create_mnt_ns(mnt);
	if (IS_ERR(ns))
		panic("Can't allocate initial namespace");

	init_task.nsproxy->mnt_ns = ns;		// 设置INIT进程的根目录
	get_mnt_ns(ns);					// 增加ns->count计数

	root.mnt = ns->root;
	root.dentry = ns->root->mnt_root;

	// 设置当前进程的current->fs->pwd
	set_fs_pwd(current->fs, &root);

	// 设置当前进程的current->fs->root, 即将当前的文件系统设置为根文件系统
	set_fs_root(current->fs, &root);
}
```

函数init_mount_tree()执行后的数据结构，参见Subjects/Chapter04_Boot/Figures/init_mount_tree().jpg

此后，由init_task进程fork出来的子进程也继承了init_task->nsproxy->mnt_ns信息，参见copy_process()节中的:

```
retval = copy_fs(clone_flags, p);
```

综上，函数init_mount_tree()为VFS建立了根目录"/"，而一旦有了根目录，那么这棵树就可以发展壮大，例如: 可以通过系统调用sys_mkdir在这棵树上建立新的叶子节点等。

###### 4.3.4.1.4.3.11.5 bdev_cache_init()

* 字符设备初始化函数之一：bdev_cache_init()，参见本节
* 字符设备初始化函数之二：genhd_device_init()，参见【[10.4.2 块设备的初始化/genhd_device_init()](#)节

该函数定义于fs/block_dev.c:

```
static  __cacheline_aligned_in_smp DEFINE_SPINLOCK(bdev_lock);
static struct kmem_cache * bdev_cachep __read_mostly;

static struct file_system_type bd_type = {
	.name		= "bdev",
	/*
	 * bd_mount()通过如下函数被调用，参见节：
	 * bdev_cache_init()->kern_mount()->kern_mount_data()
	 * ->vfs_kern_mount()->mount_fs()中的type->mount()
	 */
	.mount	= bd_mount,
	.kill_sb	= kill_anon_super,
};

struct super_block *blockdev_superblock __read_mostly;

void __init bdev_cache_init(void)
{
	int err;
	struct vfsmount *bd_mnt;

	// 创建块设备缓存，参见Create a Specific Cache/kmem_cache_create()节
	bdev_cachep = kmem_cache_create("bdev_cache", sizeof(struct bdev_inode), 0,
 			 (SLAB_HWCACHE_ALIGN|SLAB_RECLAIM_ACCOUNT|SLAB_MEM_SPREAD|SLAB_PANIC), init_once);

	// 注册bdev文件系统到file_systems，参见注册/注销文件系统节
	err = register_filesystem(&bd_type);
	if (err)
		panic("Cannot register bdev pseudo-fs");

	// 安装bdev文件系统，参见安装文件系统节
	bd_mnt = kern_mount(&bd_type);
	if (IS_ERR(bd_mnt))
		panic("Cannot create bdev pseudo-fs");
	/*
	 * This vfsmount structure is only used to obtain the
	 * blockdev_superblock, so tell kmemleak not to report it.
	 */
	kmemleak_not_leak(bd_mnt);
	blockdev_superblock = bd_mnt->mnt_sb;	/* For writeback */
}
```

###### 4.3.4.1.4.3.11.6 chrdev_init()

* 字符设备初始化函数之一：chrdev_init()，参见本节
* 字符设备初始化函数之二：chr_dev_init()，参见[10.3.2 字符设备的初始化/chr_dev_init()](#)节

该函数定义于fs/char_dev.c:

```
struct backing_dev_info directly_mappable_cdev_bdi = {
	.name = "char",
	.capabilities	= (
#ifdef CONFIG_MMU
		/* permit private copies of the data to be taken */
		BDI_CAP_MAP_COPY |
#endif
		/* permit direct mmap, for read, write or exec */
		BDI_CAP_MAP_DIRECT |
		BDI_CAP_READ_MAP | BDI_CAP_WRITE_MAP | BDI_CAP_EXEC_MAP |
		/* no writeback happens */
		BDI_CAP_NO_ACCT_AND_WRITEBACK),
};

static struct kobj_map *cdev_map;
static DEFINE_MUTEX(chrdevs_lock);

void __init chrdev_init(void)
{
	/*
	 * 变量cdev_map在如下函数中被访问：
	 * - cdev_map(), 参见10.3.3.3.3.1 cdev_add()节
	 * - kobj_lookup(), 参见10.3.3.3.4.1 kobj_lookup()节
	 */
	cdev_map = kobj_map_init(base_probe, &chrdevs_lock);
	bdi_init(&directly_mappable_cdev_bdi);
}
```

初始化完成后的数据结构:

Subjects/Chapt er10_Device_Driver/01_Char_Device/Figures/chrdevs[]\_2.jpg

图中struct probe的域data指向struct cdev类型的对象，由函数cdev_add()设置，参见[10.3.3.3.3.1 cdev_add()](#)节。

###### 4.3.4.1.4.3.12 proc_root_init()

该函数定义于fs/proc/root.c:

```
static struct file_system_type proc_fs_type = {
	.name		= "proc",
	.mount	= proc_mount,	// 参见proc_mount()节
	.kill_sb	= proc_kill_sb,
};

struct pid_namespace init_pid_ns = {
	.kref = {
		.refcount	= ATOMIC_INIT(2),
	},
	.pidmap = {
		[ 0 ... PIDMAP_ENTRIES-1] = { ATOMIC_INIT(BITS_PER_PAGE), NULL }
	},
	.last_pid		= 0,
	.level		= 0,
	// 初始化进程描述符，参见进程0/swapper, swapper/0, swapper/1, ...节
	.child_reaper	= &init_task,
};

void __init proc_root_init(void)
{
	int err;

	// 分配缓存空间proc_inode_cachep
	proc_init_inodecache();
	// 注册proc文件系统，参见注册/注销文件系统节
	err = register_filesystem(&proc_fs_type);
	if (err)
		return;
	/*
	 * 通过调用kern_mount_data()来安装proc文件系统，
	 * 参见安装文件系统(1)/kern_mount()节和proc_mount()节
	 */
	err = pid_ns_prepare_proc(&init_pid_ns);
	if (err) {
		unregister_filesystem(&proc_fs_type);
		return;
	}

	/*
	 * 创建/proc/mounts到/proc/self/mounts的链接，即/proc/mounts -> /proc/self/mounts
	 * 通过命令 # cat /proc/mounts，可以查看当前系统中安装的文件系统，参见具体的文件系统节
	 */
	proc_symlink("mounts", NULL, "self/mounts");

	// 创建/proc/net到/proc/self/net的链接，即/proc/net -> /proc/self/net
	proc_net_init();

#ifdef CONFIG_SYSVIPC
	proc_mkdir("sysvipc", NULL);	// 创建/proc/sysvipc目录
#endif
	proc_mkdir("fs", NULL);		// 创建/proc/fs目录
	proc_mkdir("driver", NULL);		// 创建/proc/drivers目录
	// 创建/proc/fs/nfsd目录
	proc_mkdir("fs/nfsd", NULL);	/* somewhere for the nfsd filesystem to be mounted */
#if defined(CONFIG_SUN_OPENPROMFS) || defined(CONFIG_SUN_OPENPROMFS_MODULE)
	/* just give it a mountpoint */
	proc_mkdir("openprom", NULL);	// 创建/proc/openprom目录
#endif
	/*
	 * 创建目录: /proc/tty, /proc/tty/ldisc, /proc/tty/driver,
	 * /proc/tty/ldiscs, /proc/tty/drivers
	 */
	proc_tty_init();
#ifdef CONFIG_PROC_DEVICETREE
	proc_device_tree_init();		// 创建/proc/device-tree目录
#endif
	proc_mkdir("bus", NULL);		// 创建/proc/bus目录
	proc_sys_init();				// 创建/proc/sys目录
}
```

###### 4.3.4.1.4.3.13 rest_init()

在start_kernel()函数的最后，调用rest_init()函数进行后续的初始化。在init/main.c中，包含如下有关rest_init()的代码：

```
static __initdata DECLARE_COMPLETION(kthreadd_done);     // 定义并初始化变量kthreadd_done

...
static noinline void __init_refok rest_init(void)
{
	int pid;

	rcu_scheduler_starting();
	/*
	 * We need to spawn init first so that it obtains pid 1, however
	 * the init task will end up wanting to create kthreads, which, if
	 * we schedule it before we create kthreadd, will OOPS.
	 */
	/*
	 * 调用kernel_thread()创建pid=1的内核线程，即init线程，参见kernel_thread()节。
	 * 该线程将执行kernel_init()函数，参见kernel_init()节
	 */
	kernel_thread(kernel_init, NULL, CLONE_FS | CLONE_SIGHAND);
	numa_default_policy();
	/*
	 * 调用kernel_thread()创建pid=2的内核线程，即kthreadd线程，参见kernel_thread()节。
	 * 该线程执行kthreadd()函数，参见kthreadd()节。
	 * 对于全局链表kthread_create_list中的每一项，执行函数kthread()
	 */
	pid = kernel_thread(kthreadd, NULL, CLONE_FS | CLONE_FILES);
	rcu_read_lock();
	// kthreadd_task的定义参见kernel/kthread.c: struct task_struct *kthreadd_task;
	kthreadd_task = find_task_by_pid_ns(pid, &init_pid_ns);
	rcu_read_unlock();
	// 通知kernel_init进程，kthreadd已经完成，参见kernel_init()节
	complete(&kthreadd_done);

	/*
	 * The boot idle thread must execute schedule()
	 * at least once to get things moving:
	 */
	init_idle_bootup_task(current);
	preempt_enable_no_resched();	// 与配置CONFIG_PREEMPT_COUNT有关，参见include/linux/preempt.h
	schedule();				// 如果存在一个准备好的进程，则运行它；否则，调用下面的cpu_init()函数

	/* Call into cpu_idle with preempt disabled */
	preempt_disable();			// 与配置CONFIG_PREEMPT_COUNT有关，参见preempt_disable()节
	cpu_idle();				// 参见cpu_idle()节
}
```

###### 4.3.4.1.4.3.13.1 kernel_init()

该函数定义于init/main.c：

```
static int __init kernel_init(void * unused)
{
	/*
	 * Wait until kthreadd is all set-up.
	 */
	// 等待kthreadd完成，参见rest_init()节中的语句rest_init() -> complete(&kthreadd_done)
	wait_for_completion(&kthreadd_done);
	/*
	 * init can allocate pages on any node
	 */
	set_mems_allowed(node_states[N_HIGH_MEMORY]);
	/*
	 * init can run on any cpu.
	 */
	set_cpus_allowed_ptr(current, cpu_all_mask);

	cad_pid = task_pid(current);

	smp_prepare_cpus(setup_max_cpus);

	do_pre_smp_initcalls();		// 参见do_pre_smp_initcalls()节
	lockup_detector_init();

	smp_init();
	sched_init_smp();

	do_basic_setup();			// 参见do_basic_setup()节

	/* Open the /dev/console on the rootfs, this should never fail */
	if (sys_open((const char __user *) "/dev/console", O_RDWR, 0) < 0)
		printk(KERN_WARNING "Warning: unable to open an initial console.\n");

	(void) sys_dup(0);
	(void) sys_dup(0);

	/*
	 * check if there is an early userspace init.  If yes, let it do all
	 * the work
	 */
	/*
	 * 内核参数"rdinit="用于设置ramdisk_execute_command，
	 * 参见init/main.c:rdinit_setup();
	 * 若无内核参数"rdinit="，则设置ramdisk_execute_command="/init"，
	 * 即initrd.img中的init，参见init_post()节
	 */
	if (!ramdisk_execute_command)
		ramdisk_execute_command = "/init";

	/*
	 * 若ramdisk_execute_command指定的初始化程序不存在，
	 * 则复位ramdisk_execute_command = NULL;
	 * 以避免init_post()执行该初始化程序，参见init_post()节
	 */
	if (sys_access((const char __user *) ramdisk_execute_command, 0) != 0) {
		ramdisk_execute_command = NULL;
		prepare_namespace();		// 参见prepare_namespace()节
	}

	/*
	 * Ok, we have completed the initial bootup, and
	 * we're essentially up and running. Get rid of the
	 * initmem segments and start the user-mode stuff..
	 */
	init_post();				// 参见init_post()节
	return 0;
}
```

###### 4.3.4.1.4.3.13.1.1 do_pre_smp_initcalls()

该函数定义于init/main.c:

```
extern initcall_t __initcall_start[], __initcall_end[], __early_initcall_end[];

static void __init do_pre_smp_initcalls(void)
{
	initcall_t *fn;

	for (fn = __initcall_start; fn < __early_initcall_end; fn++)
		do_one_initcall(*fn);		// 参见do_one_initcall()节
}
```

###### 4.3.4.1.4.3.13.1.2 do_basic_setup()

该函数定义于init/main.c:

```
static void __init do_basic_setup(void)
{
	cpuset_init_smp();
	usermodehelper_init();	// 参见13.3.2.2.1 khelper_wq节
	shmem_init();
	driver_init();			// 参见10.2.1 设备驱动程序的初始化/driver_init()节
	init_irq_proc();
	do_ctors();
	usermodehelper_enable();
	do_initcalls();			// 参见module被编译进内核时的初始化过程节
}
```

###### 4.3.4.1.4.3.13.1.3 prepare_namespace()

该函数定义于init/do_mounts.c:

```
/*
 * Prepare the namespace - decide what/where to mount, load ramdisks, etc.
 */
void __init prepare_namespace(void)
{
	int is_floppy;

	/*
	 * 对于将根文件系统存放到USB或者SCSI设备上的情况，Kernel需要
	 * 等待这些耗时比较久的设备驱动加载完毕，故此处存在一个root_delay
	 *
	 * 内核参数"rootdelay="设置root_delay，
	 * 参见init/do_mounts.c中的root_delay_setup()
	 */
	if (root_delay) {
		printk(KERN_INFO "Waiting %dsec before mounting root device...\n", root_delay);
		ssleep(root_delay);
	}

	/*
	 * wait for the known devices to complete their probing
	 *
	 * Note: this is a potential source of long boot delays.
	 * For example, it is not atypical to wait 5 seconds here
	 * for the touchpad of a laptop to initialize.
	 */
	wait_for_device_probe();

	md_run_setup();

	/*
	 * 根据内核参数"root="来设置saved_root_name[]，
	 * 参见init/do_mounts.c中的root_dev_setup().
	 *
	 * Set the root_device_name variable with the device
	 * filename obtained from the "root" boot parameter.
	 * Also, sets the ROOT_DEV variable with the major
	 * and minor numbers of the same device file.
	 *
	 * 内核参数"root="可通过下列命令查看：
	 * chenwx@chenwx ~ $ cat /proc/cmdline
	 * BOOT_IMAGE=/boot/vmlinuz-3.11.0-12-generic     \
	 * root=UUID=fe67c2d0-9b0f-4fd6-8e97-463ce95a7e0c \
	 * ro quiet splash vt.handoff=7
	 */
	if (saved_root_name[0]) {
		root_device_name = saved_root_name;
		/*
		 * Try #1: 若内核参数"root="代表的字符串以"mtd"或"ubi"开头，
		 * 则调用mount_block_root()解析该内核参数
		 */
		if (!strncmp(root_device_name, "mtd", 3) || !strncmp(root_device_name, "ubi", 3)) {
			mount_block_root(root_device_name, root_mountflags);
			goto out;
		}
		/*
		 * Try #2: 若内核参数"root="代表的字符串以"/dev/"或"PARTUUID="开头，
		 * 则将/dev/<disk_name>转换为Device Number.
		 * 参见源代码中对函数name_to_dev_t()的注释。
		 */
		ROOT_DEV = name_to_dev_t(root_device_name);
		if (strncmp(root_device_name, "/dev/", 5) == 0)
			root_device_name += 5;
	}

	/*
	 * Try #3: 若内核参数"root="代表的字符串以"UUID=<uuid>"开头，
	 * 则需要加载initrd.image，并由其中的init程序(参见kernel_init()节)
	 * 负责解析"root="字符串"UUID=<uuid>"，并挂在相应的设备。
	 *
	 * 函数initrd_load()用于加载映像/boot/initrd.img-3.11.0-12-generic，
	 * 参见CONFIG_BLK_DEV_INITRD=y节
	 *
	 * 可通过下列命令解压initrd.img映像：
	 *   $ mv initrd.img-3.11.0-12-generic initrd.img.gz
	 *   $ gunzip initrd.img.gz
	 *   $ cpio -i -d < initrd.img
	 *   $ rm -rf initrd.img
	 *   $ ls
	 *   bin  conf  etc  init  lib  run  sbin  scripts  usr  var
	 */
	if (initrd_load())
		goto out;

	/*
	 * 内核参数"rootwait"设置root_wait，
	 * 参见init/do_mounts.c中的rootwait_setup()
	 */
	/* wait for any asynchronous scanning to complete */
	if ((ROOT_DEV == 0) && root_wait) {
		printk(KERN_INFO "Waiting for root device %s...\n", saved_root_name);
		while (driver_probe_done() != 0 || (ROOT_DEV = name_to_dev_t(saved_root_name)) == 0)
			msleep(100);
		async_synchronize_full();
	}

	is_floppy = MAJOR(ROOT_DEV) == FLOPPY_MAJOR;

	if (is_floppy && rd_doload && rd_load_disk(0))
		ROOT_DEV = Root_RAM0;

	// 根据ROOT_DEV挂载根文件系统，参见下文；
	mount_root();
out:
	// 挂载devtmpfs文件系统，参见Devtmpfs的安装节
	devtmpfs_mount("dev");
	/*
	 * Moves the mount point of the mounted filesystem on the root
	 * directory of the rootfs filesystem.
	 * Notice that the rootfs special filesystem cannot be unmounted,
	 * it's only hidden under the disk-based root filesystem.
	 * 系统调用sys_mount()参见安装文件系统(2)/sys_mount()节，
	 * 文件系统rootfs参见虚拟文件系统(VFS)的初始化节
	 */
	sys_mount(".", "/", NULL, MS_MOVE, NULL);
	sys_chroot((const char __user __force *)".");
}
```

其中，函数mount_root()定义于init/do_mounts.c:

```
void __init mount_root(void)
{
#ifdef CONFIG_ROOT_NFS
	if (MAJOR(ROOT_DEV) == UNNAMED_MAJOR) {
		if (mount_nfs_root())
			return;

		printk(KERN_ERR "VFS: Unable to mount root fs via NFS, trying floppy.\n");
		ROOT_DEV = Root_FD0;
	}
#endif
#ifdef CONFIG_BLK_DEV_FD
	if (MAJOR(ROOT_DEV) == FLOPPY_MAJOR) {
		/* rd_doload is 2 for a dual initrd/ramload setup */
		if (rd_doload==2) {
			if (rd_load_disk(1)) {
				ROOT_DEV = Root_RAM1;
				root_device_name = NULL;
			}
		} else
			change_floppy("root floppy");
	}
#endif
#ifdef CONFIG_BLOCK
	create_dev("/dev/root", ROOT_DEV);
	mount_block_root("/dev/root", root_mountflags);
#endif
}
```

###### 4.3.4.1.4.3.13.1.4 init_post()

该函数定义于init/main.c:

```
/* This is a non __init function. Force it to be noinline otherwise gcc
 * makes it inline to init() and it becomes part of init.text section
 */
static noinline int init_post(void)
{
	/* need to finish all async __init code before freeing the memory */
	async_synchronize_full();
	free_initmem();		// 参见early_node_map[]=>node_data[]->node_zones[]节
	mark_rodata_ro();
	system_state = SYSTEM_RUNNING;
	numa_default_policy();

	current->signal->flags |= SIGNAL_UNKILLABLE;

	/*
	 * Try #1: 启动ramdisk_execute_command指定的初始化程序，
	 *         其取值参见kernel_init()节的kernel_init():
	 *         a) 由内核参数"rdinit="指定，或者
	 *         b) /init(此初始化程序由initrd.img加载而来，
	 *            参见prepare_namespace()节的Try #3)
	 * 若该进程成功，则转到用户空间；否则，继续尝试
	 */
	if (ramdisk_execute_command) {
		run_init_process(ramdisk_execute_command);
		printk(KERN_WARNING "Failed to execute %s\n", ramdisk_execute_command);
	}

	/*
	 * We try each of these until one succeeds.
	 *
	 * The Bourne shell can be used instead of init if we are
	 * trying to recover a really broken machine.
	 */
	// Try #2: 启动execute_command指定的初始化程序：
	// 由内核参数"init="指定，参见init/main.c中的函数init_setup()
	if (execute_command) {
		run_init_process(execute_command);
		printk(KERN_WARNING "Failed to execute %s.  Attempting defaults...\n", execute_command);
	}
	/*
	 * 依次尝试启动如下初始化程序。若其中之一启动成功，
	 * 则转到用户空间；否则，继续尝试。参见init节
	 */
	run_init_process("/sbin/init");	// Try #3: /sbin/init
	run_init_process("/etc/init");	// Try #4: /etc/init
	run_init_process("/bin/init");	// Try #5: /bin/init
	/*
	 * 若上述init均不存在或无法启动，则启动shell，即/bin/sh;
	 * 若成功，则转到用户空间；否则，终止启动内核
	 */
	run_init_process("/bin/sh");	// Try #6: /bin/sh

	panic("No init found.  Try passing init= option to kernel. "
		   "See Linux Documentation/init.txt for guidance.");
}
```

###### 4.3.4.1.4.3.13.2 kthreadd()

在kernel/kthread.c中，包含如下有关kthreadd()的代码(另参见[kthreadd进程](#)节)：

```
static LIST_HEAD(kthread_create_list);

int kthreadd(void *unused)
{
	struct task_struct *tsk = current;

	/* Setup a clean context for our children to inherit. */
	set_task_comm(tsk, "kthreadd");				// 设置程序名称为kthreadd，参见程序名称节
	ignore_signals(tsk); 						// 忽略所有信号
	set_cpus_allowed_ptr(tsk, cpu_all_mask);
	set_mems_allowed(node_states[N_HIGH_MEMORY]);

	current->flags |= PF_NOFREEZE | PF_FREEZER_NOSIG;

	for (;;) {
		set_current_state(TASK_INTERRUPTIBLE); 	// 设置当前进程为可中断睡眠状态
		// 如果链表kthread_create_list为空，则当前进程进入睡眠状态
		if (list_empty(&kthread_create_list))
			schedule();
		// 如果链表kthread_create_list不为空，则设置当前进程为运行状态
		__set_current_state(TASK_RUNNING);

		spin_lock(&kthread_create_lock);
		while (!list_empty(&kthread_create_list)) {
			struct kthread_create_info *create;

			// 获取链表kthread_create_list中的一项，用于创建指定的内核线程
			create = list_entry(kthread_create_list.next, struct kthread_create_info, list);
			list_del_init(&create->list);
			spin_unlock(&kthread_create_lock);

			// 创建指定的内核线程，该线程执行函数kthread()，参见kthread_run()节
			create_kthread(create);

			spin_lock(&kthread_create_lock);
		}
		spin_unlock(&kthread_create_lock);
	}

	return 0;
}
```

###### 4.3.4.1.4.3.13.3 cpu_idle()

在arch/x86/kernel/process_32.c中，包含如下有关cpu_idle()的代码：

```
/*
 * The idle thread. There's no useful work to be
 * done, so just try to conserve power and have a
 * low exit latency (ie sit in a loop waiting for
 * somebody to say that they'd like to reschedule)
 */
void cpu_idle(void)
{
	int cpu = smp_processor_id();

	/*
	 * If we're the non-boot CPU, nothing set the stack canary up
	 * for us.  CPU0 already has it initialized but no harm in
	 * doing it again.  This is a good place for updating it, as
	 * we wont ever return from this function (so the invalid
	 * canaries already on the stack wont ever trigger).
	 */
	boot_init_stack_canary();

	current_thread_info()->status |= TS_POLLING;

	/* endless idle loop with no priority at all */
	while (1) {
		tick_nohz_stop_sched_tick(1);
		while (!need_resched()) {

			check_pgt_cache();
			rmb();

			if (cpu_is_offline(cpu))
				play_dead();

			local_touch_nmi();
			local_irq_disable();
			/* Don't trace irqs off for idle */
			stop_critical_timings();
			if (cpuidle_idle_call())
				pm_idle();
			start_critical_timings();
		}
		tick_nohz_restart_sched_tick();
		// 与rest_init()中调用schedule()函数的方式类似，参见rest_init()节
		preempt_enable_no_resched();
		// 如果存在一个准备好的进程，则运行它
		schedule();
		// 与rest_init()中调用schedule()函数的方式类似，参见rest_init()节
		preempt_disable();
	}
}
```

### 4.3.5 init

当内核被引导并进行初始化之后，内核就可以启动第一个用户级进程：init进程，其进程号为1。这是系统第一个调用的、使用标准C库编译的程序。此前，还没有执行任何标准的C应用程序。

init进程的启动过程参见[init_post()](#)节。若无法启动init进程，则打印错误信息：```No init found```. 参见Documentation/init.txt。

调用函数run_init_process()来启动init进程，其定义于init/main.c:

```
static const char * argv_init[MAX_INIT_ARGS+2] = { "init", NULL, };
const char * envp_init[MAX_INIT_ENVS+2] = { "HOME=/", "TERM=linux", NULL, };

// 由init_post()节可知，入参init_filename依次为"/sbin/init", "/etc/init", "/bin/init"
static void run_init_process(const char *init_filename)
{
	argv_init[0] = init_filename;
	kernel_execve(init_filename, argv_init, envp_init);
}
```

其中，函数kernel_execve()定义于arch/x86/kernel/sys_i386_32.c:

```
/*
 * Do a system call from kernel instead of calling sys_execve so we
 * end up with proper pt_regs.
 */
int kernel_execve(const char *filename,
		  const char *const argv[],
		  const char *const envp[])
{
	long __res;
	asm volatile ("int $0x80"
		: "=a" (__res)
		: "0" (__NR_execve), "b" (filename), "c" (argv), "d" (envp) : "memory");
	return __res;
}
```

函数kernel_execve()调用系统调用sys_execve()，参见[sys_execve()/exec()](#)节；另参见<<Linux Device Drivers, 2nd Edition>>第16章:

> The final call to execve finalizes the transition to user space. There is no magic involved in this transition. As with any execve call in Unix, this one replaces the memory maps of the current process with new memory maps defined by the binary file being executed. It doesn’t matter that, in this case, the calling process is running in kernel space. That’s transparent to the implementation of execve, which just finds that there are no previous memory maps to release before activating the new ones.
>
> Whatever the system setup or command line, the init process is now executing in user space and any further kernel operation takes place in response to system calls coming from init itself or from the processes it forks out.

#### 4.3.5.1 init的种类

The design of init has diverged in Unix systems, such as System III and System V, from the functionality provided by the init in Research Unix and its BSD derivatives. The usage on most Linux distributions is somewhat compatible with System V, but some distributions, such as Slackware, use a BSD-style and others, such as Gentoo, have their own customized version.

Several replacement init implementations have been written with attempt to address design limitations in the standard versions. These include systemd and Upstart, the latter being used by Ubuntu and some other Linux distributions.

Refer to article "init system other points, and conclusion" in:

* [https://lwn.net/Articles/578209/](https://lwn.net/Articles/578209/)
* [https://lwn.net/Articles/578210/](https://lwn.net/Articles/578210/)

##### 4.3.5.1.1 SysV-style init

System V init examines the /etc/inittab file for an :initdefault: entry, which defines any default runlevel. If there is no default runlevel, then init dumps the user to a system console for manual entry of a runlevel.

/etc/inittab用于设定runlevel，例如:

```
#
# inittab     This file describes how the INIT process should set up
#               the system in a certain run-level.
#
# Author:     Miquel van Smoorenburg, <miquels@drinkel.nl.mugnet.org>
#               Modified for RHS Linux by Marc Ewing and Donnie Barnes
#

# Default runlevel. The runlevels used by RHS are:
#   0 - halt (Do NOT set initdefault to this)
#   1 - Single user mode
#   2 - Multiuser, without NFS (The same as 3, if you do not have networking)
#   3 - Full multiuser mode
#   4 - unused
#   5 - X11
#   6 - reboot (Do NOT set initdefault to this)
#
id:3:initdefault:

# System initialization.
si::sysinit:/etc/rc.d/rc.sysinit

l0:0:wait:/etc/rc.d/rc 0
l1:1:wait:/etc/rc.d/rc 1
l2:2:wait:/etc/rc.d/rc 2
l3:3:wait:/etc/rc.d/rc 3
l4:4:wait:/etc/rc.d/rc 4
l5:5:wait:/etc/rc.d/rc 5
l6:6:wait:/etc/rc.d/rc 6

# Trap CTRL-ALT-DELETE
#ca::ctrlaltdel:/sbin/shutdown -t3 -r now
# s+c disable_abort_keys_tdsc start
ca::ctrlaltdel:/usr/local/etc/acknowledge_ctrlaltdel
# s+c disable_abort_keys_tdsc end

# When our UPS tells us power has failed, assume we have a few minutes
# of power left.  Schedule a shutdown for 2 minutes from now.
# This does, of course, assume you have powerd installed and your
# UPS connected and working correctly.  
pf::powerfail:/sbin/shutdown -f -h +2 "Power Failure; System Shutting Down"

# If power was restored before the shutdown kicked in, cancel it.
pr:12345:powerokwait:/sbin/shutdown -c "Power Restored; Shutdown Cancelled"

# Run gettys in standard runlevels
1:2345:respawn:/sbin/mingetty tty1
2:2345:respawn:/sbin/mingetty tty2
3:2345:respawn:/sbin/mingetty tty3
4:2345:respawn:/sbin/mingetty tty4
5:2345:respawn:/sbin/mingetty tty5
6:2345:respawn:/sbin/mingetty tty6

# Run xdm in runlevel 5
x:5:respawn:/etc/X11/prefdm -nodaemon
```

###### 4.3.5.1.1.1 runlevel

Linux的runlevel取值如下：

| runlevel | Note |
| :------: | :--- |
| 0 | 关机 |
| 1 | 单用户模式 |
| 2 | 无网络支持的多用户模式 |
| 3 | 有网络支持的多用户模式 |
| 4 | 保留 |
| 5 | 有网络支持的X-Window支持的多用户模式 |
| 6 | 重新引导系统，即重启 |

<p/>

Default runlevels for different Linux distributions:

| Operating System | Default runlevel |
| :--------------- | :--------------- |
| AIX | 2 |
| CentOS | 3 (console/server), or<br>5 (graphical/desktop) |
| Debian | 2 |
| Gentoo Linux | 3 |
| HP-UX | 3 (console/server/multiuser), or<br>4 (graphical) |
| Mac OS X | 3 |
| Mandriva Linux | 3 (console/server), or<br>5 (graphical/desktop) |
| Red Hat Enterprise Linux / Fedora | 3 (console/server), or<br>5 (graphical/desktop) |
| Slackware Linux | 3 |
| Solaris | 3 |
| SUSE Linux Enterprise/openSUSE Linux | 3 (console/server), or<br>5 (graphical/desktop) |
| Ubuntu (Server and Desktop) | 2 |

<p/>

On most systems users can check the current runlevel with either of the following commands:

```
chenwx@chenwx ~ $ runlevel
N 2
chenwx@chenwx ~ $ who -r
         run-level 2  2014-04-14 21:26
```

##### 4.3.5.1.2 BSD-style init

BSD init runs the initialization shell script located in /etc/rc, then launches getty on text-based terminals or a windowing system such as X on graphical terminals under the control of /etc/ttys. There are no runlevels; the /etc/rc file determines what programs are run by init. The advantage of this system is that it is simple and easy to edit manually. However, new software added to the system may require changes to existing files that risk producing an unbootable system. To mitigate this, BSD variants have long supported a site-specific /etc/rc.local file that is run in a sub-shell near the end of the boot sequence.

A fully modular system was introduced with NetBSD 1.5 and ported to FreeBSD 5.0 and successors. This system executes scripts in the /etc/rc.d directory. Unlike System V's script ordering, which is derived from the filename of each script, this system uses explicit dependency tags placed within each script. The order in which scripts are executed is determined by the rcorder script based on the requirements stated in these tags.

init进程是非内核进程中第一个被启动运行的，因此它的进程号pid总是1。init读取配置文件/etc/inittab，决定需要启动的运行级别。从根本上说，运行级别规定了整个系统的行为，每个级别满足特定的目的。如果定义了initdefault级别，则直接使用该值，否则需要由用户输入一个代表运行级别的数值。

输入代表运行级别的数字之后，init根据/etc/inittab中的定义执行一个命令脚本程序。缺省的运行级别取决于安装阶段对登录程序的选择：是使用基于文本的，还是使用基于X-Window的登录程序。

当运行级别发生改变时，将根据/etc/inittab中的定义运行一个命令脚本程序。这些命令脚本程序负责启动或者停止该运行级别特定的各种服务。由于需要管理的服务数量很多，因此需要使用rc命令脚本程序。其中，最主要的一个是/etc/rc.d/rc，它负责为每一个运行级别按照正确的顺序调用相应的命令脚本程序。可以想象，这样一个命令脚本程序很容易变得难以控制！为了防止这类事件的发生，需要使用精心设计的方案。

```
/etc/rc.d
|-- rc.sysinit
|-- rc
|-- init.d
|-- nohup.out
|-- rc.local
|-- rc0.d
|-- rc1.d
|-- rc2.d
|-- rc3.d
|-- rc4.d
|-- rc5.d
`-- rc6.d
```

对每一个运行级别来说，在/etc/rc.d子目录中都有一个对应的下级目录。这些运行级别的下级子目录的命名方法是rcX.d，其中X代表运行级别。例如：运行级别3的全部命令脚本程序都保存在/etc/rc.d/rc3.d子目录中。在各个运行级别的子目录中，都建立有到/etc/rc.d/init.d子目录中命令脚本程序的符号链接，但是，这些符号链接并不使用命令脚本程序在/etc/rc.d/init.d子目录中原来的名字。如果命令脚本程序是用来启动一个服务的，其符号链接的名字就以字母S打头；如果命令脚本程序是用来关闭一个服务的，其符号链接的名字就以字母K打头。

许多情况下，这些命令脚本程序的执行顺序都很重要。如果没有先配置网络接口，就没有办法使用DNS服务解析主机名！为了安排它们的执行顺序，在字母S或者K的后面紧跟着两位数字，数值小的在数值大的前面执行。如：/etc/rc.d/rc3.d/S50inet就会在/etc/rc.d/rc3.d/S55named之前执行(S50inet配置网络设置，55named启动DNS服务器)。存放在/etc/rc.d/init.d子目录中的、被符号链接上的命令脚本程序是真正的实干家，是它们完成了启动或者停止各种服务的操作过程。当/etc/rc.d/rc运行通过每个特定的运行级别子目录的时候，它会根据数字的顺序依次调用各个命令脚本程序执行。它先运行以字母K打头的命令脚本程序，然后再运行以字母S打头的命令脚本程序。对以字母K打头的命令脚本程序来说，会传递stop参数；类似地对以字母S打头的命令脚本程序来说，会传递start参数。编写自己的rc命令脚本在维护Linux系统运转时，肯定会遇到需要系统管理员对开机或者关机命令脚本进行修改的情况。

##### 4.3.5.1.3 Replacements for init

**Rational**

The traditional init process was originally only responsible for bringing the computer into a normal running state after power-on, or gracefully shutting down services prior to shutdown. As a result, the design is strictly synchronous, blocking future tasks until the current one has completed. Its tasks must also be defined in advance, as they are limited to this prep or cleanup function. This leaves it unable to handle various non-startup-tasks on a modern desktop computer elegantly, including:

* The addition or removal of USB pen drives and other portable storage / network devices while the machine is running;
* The discovery and scanning of new storage devices, without locking the system, especially when a disk may not even power on until it is scanned;
* The loading of firmware for a device, which may need to occur after it is detected but before it is usable.

**Description of replacements for init**

Traditionally, one of the major drawbacks of init is that it starts tasks serially, waiting for each to finish loading before moving on to the next. When startup processes end up I/O blocked, this can result in long delays during boot.

Various efforts have been made to replace the traditional init daemons to address this and other design problems, including:

| Replacements for init | Notes |
| :-------------------- | :---- |
| BootScripts | used in GoboLinux |
| busybox-init | suited embedded operating systems, employed by OpenWrt before it was replaced with procd |
| DEMONS | a modification of the init start process by KahelOS, where daemons are started only when the DE (desktop environment) started |
| eINIT | a full replacement of init designed to start processes asynchronously, but with the potential of doing it without shell scripts |
| Initng | a full replacement of init designed to start processes asynchronously |
| launchd | a replacement for init introduced in Mac OS X v10.4 (it launches SystemStarter to run old-style 'rc.local' and SystemStarter processes) |
| Mudur | an init replacement written in Python and designed to start process asynchronously in use by the Pardus Linux distribution |
| runit | a cross-platform full replacement for init with parallel starting of services |
| s6 | another cross-platform full replacement for init, similar to runit |
| Service Management Facility | a complete full replacement/redesign of init from the ground up in Solaris starting with Solaris 10 |
| systemd | a full replacement for init with parallel starting of services and other features, used by many distributions |
| SystemStarter | a process spawner started by the BSD-style init in Mac OS X prior to Mac OS X v10.4 |
| Upstart | a full replacement of init designed to start processes asynchronously initiated by Ubuntu |

<p/>

###### 4.3.5.1.3.1 upstart

[upstart](http://upstart.ubuntu.com) is an event-based replacement for the /sbin/init daemon which handles starting of tasks and services during boot, stopping them during shutdown and supervising them while the system is running. That means upstart operates asynchronously.

It was originally developed for the Ubuntu distribution, but is intended to be suitable for deployment in all Linux distributions as a replacement for the venerable System-V init.

Easy transition and perfect backwards compatibility with sysvinit were the explicit design goals; accordingly, Upstart can run unmodified sysvinit scripts. In this way it differs from most other init replacements (beside systemd and OpenRC), which usually assume and require complete transition to run properly, and do not support a mixed environment of traditional and new startup methods.

Upstart allows for extensions to its event model through the use of initctl to input custom, single events, or event bridges to integrate many or more-complicated events. By default, Upstart includes bridges for socket, dbus, udev, file, and dconf events; additionally, more bridges (for example, a Mach ports bridge, or a devd (found on FreeBSD systems) bridge) are possible.

UpStart提供了一系列命令来完成管理系统服务的工作，其中的核心命令是initctl，这是一个带子命令风格的命令行工具：

```
chenwx@chenwx ~ $ initctl version
init (upstart 1.12.1)
chenwx@chenwx ~ $ initctl list
avahi-cups-reload stop/waiting
avahi-daemon start/running, process 532
mountall-net stop/waiting
mountnfs-bootclean.sh start/running
nmbd start/running, process 1637
passwd stop/waiting
rc stop/waiting
rsyslog start/running
...
```

###### 4.3.5.1.3.2 systemd

[systemd](http://freedesktop.org/wiki/Software/systemd/) is a suite of basic building blocks for a Linux system. It provides a system and service manager that runs as PID 1 and starts the rest of the system. systemd provides aggressive parallelization capabilities, uses socket and D-Bus activation for starting services, offers on-demand starting of daemons, keeps track of processes using Linux control groups, supports snapshotting and restoring of the system state, maintains mount and automount points and implements an elaborate transactional dependency-based service control logic. systemd supports SysV and LSB init scripts and works as a replacement for sysvinit. Other parts include a logging daemon, utilities to control basic system configuration like the hostname, date, locale, maintain a list of logged-in users and running containers and virtual machines, system accounts, runtime directories and settings, and daemons to manage simple network configuration, network time synchronization, log forwarding, and name resolution.

Because it's a system daemon, and under Unix/Linux those are in lower case, and get suffixed with a lower case d. And since systemd manages the system, it's called systemd.

systemd software architecture:

* Subjects/Chapter04_Boot/Figures/systemd_components.png
* Subjects/Chapter04_Boot/Figures/unified_hierarchy_cgroups_and_systemd.png

Git repository

* git://anongit.freedesktop.org/systemd/systemd
* ssh://git.freedesktop.org/git/systemd/systemd

###### 4.3.5.1.3.2.1 systemd的配置文件

systemd的配置文件放置在下面的目录中：

/usr/lib/systemd/system/: 每个服务最主要的启动脚本设置，类似于以前的/etc/init.d下的文件。

/run/systemd/system/: 系统执行过程中所产生的服务脚本，这些脚本的执行优先级要比/usr/lib/systemd/system/目录下的高。

/etc/systemd/system/: 系统管理员根据系统的需求所创建的执行脚本，类似于以前的/etc/rd.d/rc5.d/Sxx之类的功能。这些脚本的执行优先级要比/run/systemd/system/目录下的高。

故，系统开机时会不会执行某些服务其实是看/etc/systemd/system/下面的设置，所以该目录下是一些链接文件，而实际执行的配置文件都放置在/usr/lib/systemd/system/目录下面。

#### 4.3.5.2 查看当前系统使用的init

执行下列命令查看当前子系统中的init进程：
chenwx@chenwx ~ $ init --version
init (upstart 1.12.1)
Copyright (C) 2006-2014 Canonical Ltd., 2011 Scott James Remnant

This is free software; see the source for copying conditions.  There is NO warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

或者，init进程更改为systemd:

```
chenwx@chenwx ~ $ lsb_release -a
No LSB modules are available.
Distributor ID:	LinuxMint
Description:	Linux Mint 18 Sarah
Release:	18
Codename:	sarah

chenwx@chenwx ~ $ ll /sbin/init
lrwxrwxrwx 1 root root 20 Jul 13 00:28 /sbin/init -> /lib/systemd/systemd

chenwx@chenwx ~ $ systemd --version
systemd 229
+PAM +AUDIT +SELINUX +IMA +APPARMOR +SMACK +SYSVINIT +UTMP +LIBCRYPTSETUP +GCRYPT +GNUTLS +ACL +XZ -LZ4 +SECCOMP +BLKID +ELFUTILS +KMOD -IDN
```

# Appendixes

## Appendix A: make -f scripts/Makefile.build obj=列表

```
// Refer to target scripts_basic in top Makefile
make -f scripts/Makefile.build obj=scripts/basic
// Refer to target prepare0 in top Makefile
make -f scripts/Makefile.build obj=.
// Refer to target scripts in top Makefile
make -f scripts/Makefile.build obj=scripts
make -f scripts/Makefile.build obj=scripts/mod
// Refer to $(init-y) in top Makefile
make -f scripts/Makefile.build obj=init
// Refer to $(core-y) in top Makefile
make -f scripts/Makefile.build obj=usr
make -f scripts/Makefile.build obj=arch/x86
make -f scripts/Makefile.build obj=arch/x86/crypto
make -f scripts/Makefile.build obj=arch/x86/kernel
make -f scripts/Makefile.build obj=arch/x86/kernel/acpi
make -f scripts/Makefile.build obj=arch/x86/kernel/apic
make -f scripts/Makefile.build obj=arch/x86/kernel/cpu
make -f scripts/Makefile.build obj=arch/x86/kernel/cpu/mtrr
make -f scripts/Makefile.build obj=arch/x86/mm
make -f scripts/Makefile.build obj=arch/x86/net
make -f scripts/Makefile.build obj=arch/x86/platform
make -f scripts/Makefile.build obj=arch/x86/platform/ce4100
make -f scripts/Makefile.build obj=arch/x86/platform/efi
make -f scripts/Makefile.build obj=arch/x86/platform/geode
make -f scripts/Makefile.build obj=arch/x86/platform/iris
make -f scripts/Makefile.build obj=arch/x86/platform/mrst
make -f scripts/Makefile.build obj=arch/x86/platform/olpc
make -f scripts/Makefile.build obj=arch/x86/platform/scx200
make -f scripts/Makefile.build obj=arch/x86/platform/sfi
make -f scripts/Makefile.build obj=arch/x86/platform/uv
make -f scripts/Makefile.build obj=arch/x86/platform/visws
make -f scripts/Makefile.build obj=arch/x86/vdso
make -f scripts/Makefile.build obj=kernel
make -f scripts/Makefile.build obj=kernel/events
make -f scripts/Makefile.build obj=kernel/irq
make -f scripts/Makefile.build obj=kernel/time
make -f scripts/Makefile.build obj=mm
make -f scripts/Makefile.build obj=fs
make -f scripts/Makefile.build obj=fs/devpts
make -f scripts/Makefile.build obj=fs/exofs
make -f scripts/Makefile.build obj=fs/nls
make -f scripts/Makefile.build obj=fs/notify
make -f scripts/Makefile.build obj=fs/notify/dnotify
make -f scripts/Makefile.build obj=fs/notify/fanotify
make -f scripts/Makefile.build obj=fs/notify/inotify
make -f scripts/Makefile.build obj=fs/partitions
make -f scripts/Makefile.build obj=fs/proc
make -f scripts/Makefile.build obj=fs/quota
make -f scripts/Makefile.build obj=fs/ramfs
make -f scripts/Makefile.build obj=fs/sysfs
make -f scripts/Makefile.build obj=ipc
make -f scripts/Makefile.build obj=security
make -f scripts/Makefile.build obj=crypto
make -f scripts/Makefile.build obj=block
// Refer to $(drivers-y) in top Makefile
make -f scripts/Makefile.build obj=drivers
make -f scripts/Makefile.build obj=drivers/auxdisplay
make -f scripts/Makefile.build obj=drivers/base
make -f scripts/Makefile.build obj=drivers/base/power
make -f scripts/Makefile.build obj=drivers/block
make -f scripts/Makefile.build obj=drivers/cdrom
make -f scripts/Makefile.build obj=drivers/char
make -f scripts/Makefile.build obj=drivers/clk
make -f scripts/Makefile.build obj=drivers/clocksource
make -f scripts/Makefile.build obj=drivers/firewire
make -f scripts/Makefile.build obj=drivers/firmware
make -f scripts/Makefile.build obj=drivers/gpio
make -f scripts/Makefile.build obj=drivers/gpu
make -f scripts/Makefile.build obj=drivers/gpu/drm
make -f scripts/Makefile.build obj=drivers/gpu/drm/i2c
make -f scripts/Makefile.build obj=drivers/gpu/stub
make -f scripts/Makefile.build obj=drivers/gpu/vga
make -f scripts/Makefile.build obj=drivers/i2c
make -f scripts/Makefile.build obj=drivers/i2c/algos
make -f scripts/Makefile.build obj=drivers/i2c/busses
make -f scripts/Makefile.build obj=drivers/i2c/muxes
make -f scripts/Makefile.build obj=drivers/idle
make -f scripts/Makefile.build obj=drivers/ieee802154
make -f scripts/Makefile.build obj=drivers/input
make -f scripts/Makefile.build obj=drivers/input/keyboard
make -f scripts/Makefile.build obj=drivers/input/serio
make -f scripts/Makefile.build obj=drivers/leds
make -f scripts/Makefile.build obj=drivers/lguest
make -f scripts/Makefile.build obj=drivers/macintosh
make -f scripts/Makefile.build obj=drivers/media
make -f scripts/Makefile.build obj=drivers/media/common
make -f scripts/Makefile.build obj=drivers/media/common/tuners
make -f scripts/Makefile.build obj=drivers/media/rc
make -f scripts/Makefile.build obj=drivers/media/rc/keymaps
make -f scripts/Makefile.build obj=drivers/media/video
make -f scripts/Makefile.build obj=drivers/media/video/davinci
make -f scripts/Makefile.build obj=drivers/mfd
make -f scripts/Makefile.build obj=drivers/misc
make -f scripts/Makefile.build obj=drivers/misc/carma
make -f scripts/Makefile.build obj=drivers/misc/cb710
make -f scripts/Makefile.build obj=drivers/misc/eeprom
make -f scripts/Makefile.build obj=drivers/misc/lis3lv02d
make -f scripts/Makefile.build obj=drivers/misc/ti-st
make -f scripts/Makefile.build obj=drivers/net
make -f scripts/Makefile.build obj=drivers/nfc
make -f scripts/Makefile.build obj=drivers/pinctrl
make -f scripts/Makefile.build obj=drivers/platform
make -f scripts/Makefile.build obj=drivers/platform/x86
make -f scripts/Makefile.build obj=drivers/tty
make -f scripts/Makefile.build obj=drivers/tty/ipwireless
make -f scripts/Makefile.build obj=drivers/tty/serial
make -f scripts/Makefile.build obj=drivers/tty/vt
make -f scripts/Makefile.build obj=drivers/video
make -f scripts/Makefile.build obj=drivers/video/backlight
make -f scripts/Makefile.build obj=drivers/video/console
make -f scripts/Makefile.build obj=drivers/video/display
make -f scripts/Makefile.build obj=drivers/video/omap2
make -f scripts/Makefile.build obj=drivers/video/omap2/displays
make -f scripts/Makefile.build obj=sound
make -f scripts/Makefile.build obj=firmware
// Refer to $(net-y) in top Makefile
make -f scripts/Makefile.build obj=net
// Refer to $(libs-y) in top Makefile
make -f scripts/Makefile.build obj=lib
make -f scripts/Makefile.build obj=arch/x86/lib
```

## Appendix B: Makefile Tree

```
linux-3.2/Makefile
+- include scripts/Kbuild.include
|  +- build := -f $(srctree)/scripts/Makefile.build obj	// 参见下文
+- include arch/$(SRCARCH)/Makefile					// 以x86为例，即include linux-3.2/arch/x86/Makefile
|  +- include $(srctree)/arch/x86/Makefile_32.cpu
```

```
linux-3.2/scripts/Makefile.build
+- -include include/config/auto.conf
+- include scripts/Kbuild.include
+- include $(kbuild-file)			// 包含指定目录下的Kbuild，或者Makefile(若不存在Kbuild的话)
+- include scripts/Makefile.lib
+- include scripts/Makefile.host
+- include $(cmd_files)
```

可运行下列命令查看完成的Makefile/Kbuild的包含关系:

```
chenwx@chenwx ~/linux $ make -d O=../linux-build/ -n bzImage > ../linux-build/build.log

chenwx@chenwx ~/linux $ grep "Reading makefile" ../linux-build/build.log
Reading makefiles...
Reading makefile 'Makefile'...
Reading makefiles...
Reading makefile '/home/chenwx/linux/Makefile'...
Reading makefile 'scripts/Kbuild.include' (search path) (no ~ expansion)...
Reading makefile 'include/config/auto.conf' (search path) (don't care) (no ~ expansion)...
Reading makefile 'include/config/auto.conf.cmd' (search path) (don't care) (no ~ expansion)...
Reading makefile 'arch/x86/Makefile' (search path) (no ~ expansion)...
Reading makefile 'arch/x86/Makefile_32.cpu' (search path) (no ~ expansion)...
Reading makefile 'scripts/Makefile.gcc-plugins' (search path) (no ~ expansion)...
Reading makefile 'scripts/Makefile.kasan' (search path) (no ~ expansion)...
Reading makefile 'scripts/Makefile.extrawarn' (search path) (no ~ expansion)...
Reading makefile 'scripts/Makefile.ubsan' (search path) (no ~ expansion)...
Reading makefile '.vmlinux.cmd' (search path) (no ~ expansion)...
Reading makefiles...
Reading makefile '/home/chenwx/linux/scripts/Makefile.build'...
Reading makefile 'include/config/auto.conf' (search path) (don't care) (no ~ expansion)...
Reading makefile 'scripts/Kbuild.include' (search path) (no ~ expansion)...
Reading makefile '/home/chenwx/linux/arch/x86/entry/syscalls/Makefile' (search path) (no ~ expansion)...
Reading makefile 'scripts/Makefile.lib' (search path) (no ~ expansion)...
...
```

## Appendix C: Kconfig tree

```
linux-3.2/Kconfig
+- source "arch/$(SRCARCH)/Kconfig"	// 此处以x86体系为例，即source "arch/x86/Kconfig"
|  +- source "init/Kconfig"
|  |  +- source "kernel/irq/Kconfig"
|  |  +- source "usr/Kconfig"
|  |  +- source "arch/Kconfig"
|  |  |  +- source "kernel/gcov/Kconfig"
|  |  +- source "block/Kconfig"
|  |  |  +- source block/Kconfig.iosched
|  |  +- source "kernel/Kconfig.locks"
|  +- source "kernel/Kconfig.freezer"
|  +- source "kernel/time/Kconfig"
|  +- source "arch/x86/xen/Kconfig"
|  +- source "arch/x86/lguest/Kconfig"
|  +- source "arch/x86/Kconfig.cpu"
|  +- source "kernel/Kconfig.preempt"
|  +- source "mm/Kconfig"
|  +- source kernel/Kconfig.hz
|  +- source "kernel/power/Kconfig"
|  +- source "drivers/acpi/Kconfig"
|  +- source "drivers/sfi/Kconfig"
|  +- source "drivers/cpufreq/Kconfig"
|  +- source "drivers/cpuidle/Kconfig"
|  +- source "drivers/idle/Kconfig"
|  +- source "drivers/pci/pcie/Kconfig"
|  +- source "drivers/pci/Kconfig"
|  +- source "drivers/eisa/Kconfig"
|  +- source "drivers/mca/Kconfig"
|  +- source "drivers/pcmcia/Kconfig"
|  +- source "drivers/pci/hotplug/Kconfig"
|  +- source "drivers/rapidio/Kconfig"
|  +- source "fs/Kconfig.binfmt"
|  +- source "net/Kconfig"
|  |  +- source "net/packet/Kconfig"
|  |  +- source "net/unix/Kconfig"
|  |  +- source "net/xfrm/Kconfig"
|  |  +- source "net/iucv/Kconfig"
|  |  +- source "net/ipv4/Kconfig"
|  |  +- source "net/ipv6/Kconfig"
|  |  +- source "net/netlabel/Kconfig"
|  |  +- source "net/netfilter/Kconfig"
|  |  +- source "net/ipv4/netfilter/Kconfig"
|  |  +- source "net/ipv6/netfilter/Kconfig"
|  |  +- source "net/decnet/netfilter/Kconfig"
|  |  +- source "net/bridge/netfilter/Kconfig"
|  |  +- source "net/dccp/Kconfig"
|  |  +- source "net/sctp/Kconfig"
|  |  +- source "net/rds/Kconfig"
|  |  +- source "net/tipc/Kconfig"
|  |  +- source "net/atm/Kconfig"
|  |  +- source "net/l2tp/Kconfig"
|  |  +- source "net/802/Kconfig"
|  |  +- source "net/bridge/Kconfig"
|  |  +- source "net/dsa/Kconfig"
|  |  +- source "net/8021q/Kconfig"
|  |  +- source "net/decnet/Kconfig"
|  |  +- source "net/llc/Kconfig"
|  |  +- source "net/ipx/Kconfig"
|  |  +- source "drivers/net/appletalk/Kconfig"
|  |  +- source "net/x25/Kconfig"
|  |  +- source "net/lapb/Kconfig"
|  |  +- source "net/econet/Kconfig"
|  |  +- source "net/wanrouter/Kconfig"
|  |  +- source "net/phonet/Kconfig"
|  |  +- source "net/ieee802154/Kconfig"
|  |  +- source "net/sched/Kconfig"
|  |  +- source "net/dcb/Kconfig"
|  |  +- source "net/dns_resolver/Kconfig"
|  |  +- source "net/batman-adv/Kconfig"
|  |  +- source "net/ax25/Kconfig"
|  |  +- source "net/can/Kconfig"
|  |  +- source "net/irda/Kconfig"
|  |  +- source "net/bluetooth/Kconfig"
|  |  +- source "net/rxrpc/Kconfig"
|  |  +- source "net/wireless/Kconfig"
|  |  +- source "net/mac80211/Kconfig"
|  |  +- source "net/wimax/Kconfig"
|  |  +- source "net/rfkill/Kconfig"
|  |  +- source "net/9p/Kconfig"
|  |  +- source "net/caif/Kconfig"
|  |  +- source "net/ceph/Kconfig"
|  |  +- source "net/nfc/Kconfig"
|  +- source "drivers/Kconfig"
|  |  +- source "drivers/base/Kconfig"
|  |  +- source "drivers/connector/Kconfig"
|  |  +- source "drivers/mtd/Kconfig"
|  |  +- source "drivers/of/Kconfig"
|  |  +- source "drivers/parport/Kconfig"
|  |  +- source "drivers/pnp/Kconfig"
|  |  +- source "drivers/block/Kconfig"
|  |  +- source "drivers/misc/Kconfig"
|  |  +- source "drivers/ide/Kconfig"
|  |  +- source "drivers/scsi/Kconfig"
|  |  +- source "drivers/ata/Kconfig"
|  |  +- source "drivers/md/Kconfig"
|  |  +- source "drivers/target/Kconfig"
|  |  +- source "drivers/message/fusion/Kconfig"
|  |  +- source "drivers/firewire/Kconfig"
|  |  +- source "drivers/message/i2o/Kconfig"
|  |  +- source "drivers/macintosh/Kconfig"
|  |  +- source "drivers/net/Kconfig"
|  |  +- source "drivers/isdn/Kconfig"
|  |  +- source "drivers/telephony/Kconfig"
|  |  +- source "drivers/input/Kconfig"
|  |  +- source "drivers/char/Kconfig"
|  |  +- source "drivers/i2c/Kconfig"
|  |  +- source "drivers/spi/Kconfig"
|  |  +- source "drivers/pps/Kconfig"
|  |  +- source "drivers/ptp/Kconfig"
|  |  +- source "drivers/pinctrl/Kconfig"
|  |  +- source "drivers/gpio/Kconfig"
|  |  +- source "drivers/w1/Kconfig"
|  |  +- source "drivers/power/Kconfig"
|  |  +- source "drivers/hwmon/Kconfig"
|  |  +- source "drivers/thermal/Kconfig"
|  |  +- source "drivers/watchdog/Kconfig"
|  |  +- source "drivers/ssb/Kconfig"
|  |  +- source "drivers/bcma/Kconfig"
|  |  +- source "drivers/mfd/Kconfig"
|  |  +- source "drivers/regulator/Kconfig"
|  |  +- source "drivers/media/Kconfig"
|  |  +- source "drivers/video/Kconfig"
|  |  +- source "sound/Kconfig"
|  |  +- source "drivers/hid/Kconfig"
|  |  +- source "drivers/usb/Kconfig"
|  |  +- source "drivers/uwb/Kconfig"
|  |  +- source "drivers/mmc/Kconfig"
|  |  +- source "drivers/memstick/Kconfig"
|  |  +- source "drivers/leds/Kconfig"
|  |  +- source "drivers/accessibility/Kconfig"
|  |  +- source "drivers/infiniband/Kconfig"
|  |  +- source "drivers/edac/Kconfig"
|  |  +- source "drivers/rtc/Kconfig"
|  |  +- source "drivers/dma/Kconfig"
|  |  +- source "drivers/dca/Kconfig"
|  |  +- source "drivers/auxdisplay/Kconfig"
|  |  +- source "drivers/uio/Kconfig"
|  |  +- source "drivers/vlynq/Kconfig"
|  |  +- source "drivers/virtio/Kconfig"
|  |  +- source "drivers/xen/Kconfig"
|  |  +- source "drivers/staging/Kconfig"
|  |  +- source "drivers/platform/Kconfig"
|  |  +- source "drivers/clk/Kconfig"
|  |  +- source "drivers/hwspinlock/Kconfig"
|  |  +- source "drivers/clocksource/Kconfig"
|  |  +- source "drivers/iommu/Kconfig"
|  |  +- source "drivers/virt/Kconfig"
|  |  +- source "drivers/hv/Kconfig"
|  |  +- source "drivers/devfreq/Kconfig"
|  +- source "drivers/firmware/Kconfig"
|  +- source "fs/Kconfig"
|  |  +- source "fs/ext2/Kconfig"
|  |  +- source "fs/ext3/Kconfig"
|  |  +- source "fs/ext4/Kconfig"
|  |  +- source "fs/jbd/Kconfig"
|  |  +- source "fs/jbd2/Kconfig"
|  |  +- source "fs/reiserfs/Kconfig"
|  |  +- source "fs/jfs/Kconfig"
|  |  +- source "fs/xfs/Kconfig"
|  |  +- source "fs/gfs2/Kconfig"
|  |  +- source "fs/ocfs2/Kconfig"
|  |  +- source "fs/btrfs/Kconfig"
|  |  +- source "fs/nilfs2/Kconfig"
|  |  +- source "fs/notify/Kconfig"
|  |  +- source "fs/quota/Kconfig"
|  |  +- source "fs/autofs4/Kconfig"
|  |  +- source "fs/fuse/Kconfig"
|  |  +- source "fs/fscache/Kconfig"
|  |  +- source "fs/cachefiles/Kconfig"
|  |  +- source "fs/isofs/Kconfig"
|  |  +- source "fs/udf/Kconfig"
|  |  +- source "fs/fat/Kconfig"
|  |  +- source "fs/ntfs/Kconfig"
|  |  +- source "fs/proc/Kconfig"
|  |  +- source "fs/sysfs/Kconfig"
|  |  +- source "fs/configfs/Kconfig"
|  |  +- source "fs/adfs/Kconfig"
|  |  +- source "fs/affs/Kconfig"
|  |  +- source "fs/ecryptfs/Kconfig"
|  |  +- source "fs/hfs/Kconfig"
|  |  +- source "fs/hfsplus/Kconfig"
|  |  +- source "fs/befs/Kconfig"
|  |  +- source "fs/bfs/Kconfig"
|  |  +- source "fs/efs/Kconfig"
|  |  +- source "fs/jffs2/Kconfig"
|  |  +- source "fs/ubifs/Kconfig"
|  |  +- source "fs/logfs/Kconfig"
|  |  +- source "fs/cramfs/Kconfig"
|  |  +- source "fs/squashfs/Kconfig"
|  |  +- source "fs/freevxfs/Kconfig"
|  |  +- source "fs/minix/Kconfig"
|  |  +- source "fs/omfs/Kconfig"
|  |  +- source "fs/hpfs/Kconfig"
|  |  +- source "fs/qnx4/Kconfig"
|  |  +- source "fs/romfs/Kconfig"
|  |  +- source "fs/pstore/Kconfig"
|  |  +- source "fs/sysv/Kconfig"
|  |  +- source "fs/ufs/Kconfig"
|  |  +- source "fs/exofs/Kconfig"
|  |  +- source "fs/nfs/Kconfig"
|  |  +- source "fs/nfsd/Kconfig"
|  |  +- source "net/sunrpc/Kconfig"
|  |  +- source "fs/ceph/Kconfig"
|  |  +- source "fs/cifs/Kconfig"
|  |  +- source "fs/ncpfs/Kconfig"
|  |  +- source "fs/coda/Kconfig"
|  |  +- source "fs/afs/Kconfig"
|  |  +- source "fs/9p/Kconfig"
|  |  +- source "fs/partitions/Kconfig"
|  |  +- source "fs/nls/Kconfig"
|  |  +- source "fs/dlm/Kconfig"
|  +- source "arch/x86/Kconfig.debug"
|  |  +- source "lib/Kconfig.debug"
|  +- source "security/Kconfig"
|  |  +- source security/selinux/Kconfig
|  |  +- source security/smack/Kconfig
|  |  +- source security/tomoyo/Kconfig
|  |  +- source security/apparmor/Kconfig
|  |  +- source security/integrity/Kconfig
|  +- source "crypto/Kconfig"
|  |  +- source "crypto/async_tx/Kconfig"
|  |  +- source "drivers/crypto/Kconfig"
|  +- source "arch/x86/kvm/Kconfig"
|  |  +- source "virt/kvm/Kconfig"
|  |  +- source drivers/vhost/Kconfig
|  |  +- source drivers/lguest/Kconfig
|  +- source "lib/Kconfig"
|  |  +- source "lib/xz/Kconfig"
```

## Appendix D: make -f scripts/Makefile.modbuiltin obj=列表

```
make -f scripts/Makefile.modbuiltin obj=init
make -f scripts/Makefile.modbuiltin obj=usr
make -f scripts/Makefile.modbuiltin obj=arch/x86
make -f scripts/Makefile.modbuiltin obj=arch/x86/crypto
make -f scripts/Makefile.modbuiltin obj=arch/x86/kernel
make -f scripts/Makefile.modbuiltin obj=arch/x86/kernel/acpi
make -f scripts/Makefile.modbuiltin obj=arch/x86/kernel/apic
make -f scripts/Makefile.modbuiltin obj=arch/x86/kernel/cpu
make -f scripts/Makefile.modbuiltin obj=arch/x86/kernel/cpu/mcheck
make -f scripts/Makefile.modbuiltin obj=arch/x86/kernel/cpu/mtrr
make -f scripts/Makefile.modbuiltin obj=arch/x86/mm
make -f scripts/Makefile.modbuiltin obj=arch/x86/net
make -f scripts/Makefile.modbuiltin obj=arch/x86/platform
make -f scripts/Makefile.modbuiltin obj=arch/x86/platform/ce4100
make -f scripts/Makefile.modbuiltin obj=arch/x86/platform/efi
make -f scripts/Makefile.modbuiltin obj=arch/x86/platform/geode
make -f scripts/Makefile.modbuiltin obj=arch/x86/platform/iris
make -f scripts/Makefile.modbuiltin obj=arch/x86/platform/mrst
make -f scripts/Makefile.modbuiltin obj=arch/x86/platform/olpc
make -f scripts/Makefile.modbuiltin obj=arch/x86/platform/scx200
make -f scripts/Makefile.modbuiltin obj=arch/x86/platform/sfi
make -f scripts/Makefile.modbuiltin obj=arch/x86/platform/uv
make -f scripts/Makefile.modbuiltin obj=arch/x86/platform/visws
make -f scripts/Makefile.modbuiltin obj=arch/x86/vdso
make -f scripts/Makefile.modbuiltin obj=kernel
make -f scripts/Makefile.modbuiltin obj=kernel/events
make -f scripts/Makefile.modbuiltin obj=kernel/gcov
make -f scripts/Makefile.modbuiltin obj=kernel/irq
make -f scripts/Makefile.modbuiltin obj=kernel/power
make -f scripts/Makefile.modbuiltin obj=kernel/time
make -f scripts/Makefile.modbuiltin obj=kernel/trace
make -f scripts/Makefile.modbuiltin obj=mm
make -f scripts/Makefile.modbuiltin obj=fs
make -f scripts/Makefile.modbuiltin obj=fs/debugfs
make -f scripts/Makefile.modbuiltin obj=fs/devpts
make -f scripts/Makefile.modbuiltin obj=fs/exofs
make -f scripts/Makefile.modbuiltin obj=fs/exportfs
make -f scripts/Makefile.modbuiltin obj=fs/nls
make -f scripts/Makefile.modbuiltin obj=fs/notify
make -f scripts/Makefile.modbuiltin obj=fs/notify/dnotify
make -f scripts/Makefile.modbuiltin obj=fs/notify/fanotify
make -f scripts/Makefile.modbuiltin obj=fs/notify/inotify
make -f scripts/Makefile.modbuiltin obj=fs/partitions
make -f scripts/Makefile.modbuiltin obj=fs/proc
make -f scripts/Makefile.modbuiltin obj=fs/quota
make -f scripts/Makefile.modbuiltin obj=fs/ramfs
make -f scripts/Makefile.modbuiltin obj=fs/sysfs
make -f scripts/Makefile.modbuiltin obj=ipc
make -f scripts/Makefile.modbuiltin obj=security
make -f scripts/Makefile.modbuiltin obj=crypto
make -f scripts/Makefile.modbuiltin obj=block
make -f scripts/Makefile.modbuiltin obj=drivers
make -f scripts/Makefile.modbuiltin obj=drivers/accessibility
make -f scripts/Makefile.modbuiltin obj=drivers/accessibility/braille
make -f scripts/Makefile.modbuiltin obj=drivers/acpi
make -f scripts/Makefile.modbuiltin obj=drivers/acpi/acpica
make -f scripts/Makefile.modbuiltin obj=drivers/auxdisplay
make -f scripts/Makefile.modbuiltin obj=drivers/base
make -f scripts/Makefile.modbuiltin obj=drivers/base/power
make -f scripts/Makefile.modbuiltin obj=drivers/base/regmap
make -f scripts/Makefile.modbuiltin obj=drivers/block
make -f scripts/Makefile.modbuiltin obj=drivers/cdrom
make -f scripts/Makefile.modbuiltin obj=drivers/char
make -f scripts/Makefile.modbuiltin obj=drivers/clk
make -f scripts/Makefile.modbuiltin obj=drivers/clocksource
make -f scripts/Makefile.modbuiltin obj=drivers/cpufreq
make -f scripts/Makefile.modbuiltin obj=drivers/cpuidle
make -f scripts/Makefile.modbuiltin obj=drivers/cpuidle/governors
make -f scripts/Makefile.modbuiltin obj=drivers/crypto
make -f scripts/Makefile.modbuiltin obj=drivers/dma
make -f scripts/Makefile.modbuiltin obj=drivers/edac
make -f scripts/Makefile.modbuiltin obj=drivers/firewire
make -f scripts/Makefile.modbuiltin obj=drivers/firmware
make -f scripts/Makefile.modbuiltin obj=drivers/firmware/google
make -f scripts/Makefile.modbuiltin obj=drivers/gpio
make -f scripts/Makefile.modbuiltin obj=drivers/gpu
make -f scripts/Makefile.modbuiltin obj=drivers/gpu/drm
make -f scripts/Makefile.modbuiltin obj=drivers/gpu/drm/i2c
make -f scripts/Makefile.modbuiltin obj=drivers/gpu/stub
make -f scripts/Makefile.modbuiltin obj=drivers/gpu/vga
make -f scripts/Makefile.modbuiltin obj=drivers/i2c
make -f scripts/Makefile.modbuiltin obj=drivers/i2c/algos
make -f scripts/Makefile.modbuiltin obj=drivers/i2c/busses
make -f scripts/Makefile.modbuiltin obj=drivers/i2c/muxes
make -f scripts/Makefile.modbuiltin obj=drivers/idle
make -f scripts/Makefile.modbuiltin obj=drivers/ieee802154
make -f scripts/Makefile.modbuiltin obj=drivers/input
make -f scripts/Makefile.modbuiltin obj=drivers/input/joystick
make -f scripts/Makefile.modbuiltin obj=drivers/input/keyboard
make -f scripts/Makefile.modbuiltin obj=drivers/input/misc
make -f scripts/Makefile.modbuiltin obj=drivers/input/serio
make -f scripts/Makefile.modbuiltin obj=drivers/iommu
make -f scripts/Makefile.modbuiltin obj=drivers/isdn
make -f scripts/Makefile.modbuiltin obj=drivers/isdn/hardware
make -f scripts/Makefile.modbuiltin obj=drivers/isdn/hardware/avm
make -f scripts/Makefile.modbuiltin obj=drivers/isdn/hardware/eicon
make -f scripts/Makefile.modbuiltin obj=drivers/leds
make -f scripts/Makefile.modbuiltin obj=drivers/lguest
make -f scripts/Makefile.modbuiltin obj=drivers/macintosh
make -f scripts/Makefile.modbuiltin obj=drivers/md
make -f scripts/Makefile.modbuiltin obj=drivers/media
make -f scripts/Makefile.modbuiltin obj=drivers/media/common
make -f scripts/Makefile.modbuiltin obj=drivers/media/common/tuners
make -f scripts/Makefile.modbuiltin obj=drivers/media/rc
make -f scripts/Makefile.modbuiltin obj=drivers/media/rc/keymaps
make -f scripts/Makefile.modbuiltin obj=drivers/media/video
make -f scripts/Makefile.modbuiltin obj=drivers/media/video/davinci
make -f scripts/Makefile.modbuiltin obj=drivers/mfd
make -f scripts/Makefile.modbuiltin obj=drivers/misc
make -f scripts/Makefile.modbuiltin obj=drivers/misc/carma
make -f scripts/Makefile.modbuiltin obj=drivers/misc/cb710
make -f scripts/Makefile.modbuiltin obj=drivers/misc/eeprom
make -f scripts/Makefile.modbuiltin obj=drivers/misc/lis3lv02d
make -f scripts/Makefile.modbuiltin obj=drivers/misc/ti-st
make -f scripts/Makefile.modbuiltin obj=drivers/net
make -f scripts/Makefile.modbuiltin obj=drivers/net/ethernet
make -f scripts/Makefile.modbuiltin obj=drivers/net/ethernet/3com
make -f scripts/Makefile.modbuiltin obj=drivers/net/ethernet/8390
make -f scripts/Makefile.modbuiltin obj=drivers/net/ethernet/amd
make -f scripts/Makefile.modbuiltin obj=drivers/net/ethernet/atheros
make -f scripts/Makefile.modbuiltin obj=drivers/net/ethernet/broadcom
make -f scripts/Makefile.modbuiltin obj=drivers/net/ethernet/fujitsu
make -f scripts/Makefile.modbuiltin obj=drivers/net/ethernet/intel
make -f scripts/Makefile.modbuiltin obj=drivers/net/ethernet/marvell
make -f scripts/Makefile.modbuiltin obj=drivers/net/ethernet/mellanox
make -f scripts/Makefile.modbuiltin obj=drivers/net/ethernet/natsemi
make -f scripts/Makefile.modbuiltin obj=drivers/net/ethernet/oki-semi
make -f scripts/Makefile.modbuiltin obj=drivers/net/ethernet/packetengines
make -f scripts/Makefile.modbuiltin obj=drivers/net/ethernet/qlogic
make -f scripts/Makefile.modbuiltin obj=drivers/net/ethernet/racal
make -f scripts/Makefile.modbuiltin obj=drivers/net/ethernet/rdc
make -f scripts/Makefile.modbuiltin obj=drivers/net/ethernet/sis
make -f scripts/Makefile.modbuiltin obj=drivers/net/ethernet/smsc
make -f scripts/Makefile.modbuiltin obj=drivers/net/ethernet/stmicro
make -f scripts/Makefile.modbuiltin obj=drivers/net/ethernet/sun
make -f scripts/Makefile.modbuiltin obj=drivers/net/ethernet/ti
make -f scripts/Makefile.modbuiltin obj=drivers/net/ethernet/via
make -f scripts/Makefile.modbuiltin obj=drivers/net/wan
make -f scripts/Makefile.modbuiltin obj=drivers/net/wireless
make -f scripts/Makefile.modbuiltin obj=drivers/nfc
make -f scripts/Makefile.modbuiltin obj=drivers/pci
make -f scripts/Makefile.modbuiltin obj=drivers/pci/pcie
make -f scripts/Makefile.modbuiltin obj=drivers/pci/pcie/aer
make -f scripts/Makefile.modbuiltin obj=drivers/pinctrl
make -f scripts/Makefile.modbuiltin obj=drivers/platform
make -f scripts/Makefile.modbuiltin obj=drivers/platform/x86
make -f scripts/Makefile.modbuiltin obj=drivers/pnp
make -f scripts/Makefile.modbuiltin obj=drivers/pnp/isapnp
make -f scripts/Makefile.modbuiltin obj=drivers/pnp/pnpacpi
make -f scripts/Makefile.modbuiltin obj=drivers/tty
make -f scripts/Makefile.modbuiltin obj=drivers/tty/ipwireless
make -f scripts/Makefile.modbuiltin obj=drivers/tty/serial
make -f scripts/Makefile.modbuiltin obj=drivers/tty/vt
make -f scripts/Makefile.modbuiltin obj=drivers/usb
make -f scripts/Makefile.modbuiltin obj=drivers/usb/early
make -f scripts/Makefile.modbuiltin obj=drivers/usb/host
make -f scripts/Makefile.modbuiltin obj=drivers/video
make -f scripts/Makefile.modbuiltin obj=drivers/video/backlight
make -f scripts/Makefile.modbuiltin obj=drivers/video/console
make -f scripts/Makefile.modbuiltin obj=drivers/video/display
make -f scripts/Makefile.modbuiltin obj=drivers/video/omap2
make -f scripts/Makefile.modbuiltin obj=drivers/video/omap2/displays
make -f scripts/Makefile.modbuiltin obj=drivers/watchdog
make -f scripts/Makefile.modbuiltin obj=sound
make -f scripts/Makefile.modbuiltin obj=firmware
make -f scripts/Makefile.modbuiltin obj=arch/x86/pci
make -f scripts/Makefile.modbuiltin obj=arch/x86/power
make -f scripts/Makefile.modbuiltin obj=arch/x86/video
make -f scripts/Makefile.modbuiltin obj=net
make -f scripts/Makefile.modbuiltin obj=net/802
make -f scripts/Makefile.modbuiltin obj=net/8021q
make -f scripts/Makefile.modbuiltin obj=net/core
make -f scripts/Makefile.modbuiltin obj=net/ethernet
make -f scripts/Makefile.modbuiltin obj=net/ipv6
make -f scripts/Makefile.modbuiltin obj=net/ipv6/netfilter
make -f scripts/Makefile.modbuiltin obj=net/netfilter
make -f scripts/Makefile.modbuiltin obj=net/netlink
make -f scripts/Makefile.modbuiltin obj=net/sched
make -f scripts/Makefile.modbuiltin obj=net/wireless
make -f scripts/Makefile.modbuiltin obj=net/xfrm
make -f scripts/Makefile.modbuiltin obj=lib
make -f scripts/Makefile.modbuiltin obj=lib/lzo
make -f scripts/Makefile.modbuiltin obj=arch/x86/lib
```

## Appendix E: arch目录下处理器体系架构介绍

**alpha处理器**

Alpha处理器最早由美国DEC公司设计制造，在Compaq(康柏)公司收购DEC之后，Alpha处理器继续得到发展，并且应用于许多高档的Compaq服务器上，HP(惠普)收购Compaq后，Alpha便为HP(惠普)所有，不过HP(惠普)已经放弃发展alpha 处理器。

**arm处理器**

Arm系列处理器是英国Arm公司设计的主流嵌入式32位RISC处理器，Arm公司不直接生产Arm处理器，而是采用IP授权的方式由第三方开发生产，著名的公司如Ti、Samsung等都有出品Arm处理器。目前在手机领域广泛应用。

**avr32处理器**

Avr32处理器是美国Atmel公司设计开发的32位RISC处理器，设计目的是在每一个时钟周期内完成更多处理工作，从而在较低的时钟频率下实现相同的吞吐量。适合在工业控制、汽车电子等嵌入式设备领域中使用。Avr32属于MCU(Micro Control Unit)型处理器。

**blackfin处理器**

Blackfin处理器是美国ADI公司开发的具有DSP能力的32位RISC处理器，Blackfin处理器基于由ADI和Intel公司联合开发的微信号架构(MSA)，适用于嵌入式音频、视频和通信应用等领域。

**cris处理器**

Cris处理器是瑞典Axis通信公司开发的32位RISC处理器，主要用于网络设备，属于比较专业的应用领域。因为Axis通信公司主要开发网络监控设备，所以Cris处理器在其网络监控设备中应用广泛。

**frv处理器**

Frv处理器是日本富士通开发的32位高性能RISC处理器，采用VLIW(Very Long Instruction Word)构架，具备良好的多媒体处理能力，在机顶盒(STB)、数码刻录机(DVR)、数码相机(DSC)等嵌入式领域应用广泛。

**h8300处理器**

H8300处理器是日本瑞萨科技开发的32位高性能RISC处理器，具有强大的位操作指令，最适于实时控制应用如汽车电子、家用电器、医疗器械等领域。H8300属于MCU型的处理器。

**hexagon处理器**

Hexagon is a DSP based CPU architecture developed by Qualcomm. It uses VLIW and is capable of dispatching up to 4 instructions to 4 Execution Units every clock. The Hexagon architecture is designed to deliver performance with low power over a variety of applications. It has features such as multithreading, privilege levels, VLIW, SIMD, and instructions geared toward efficient signal processing. The port of Linux for Hexagon runs under a hypervisor layer and was merged with the 3.2 release of the kernel. Support for Hexagon was added in 3.1 release of LLVM by Tony Linthicum. There is also a non-FSF maintained branch of GCC. Hexagon DSPs are included in Snapdragon SoC since 2006. In Snapdragon S4 (MSM8960 and newer) there are three Hexagon cores, two in the Modem subsystem and one in the Multimedia subsystem. There are four generations of DSP architecture: H1 (2006), H2 (2007-2008), H3 (2009), H4 (2010-2011). H4 has 20 DMIPS per milliwatt, works with frequency 500 MHz. Clock speed of Hexagon varies in 400-600 MHz for QDSP6 and in 256-350 MHz for QDSP5.

**ia64处理器**

Ia64处理器是美国英特尔开发的面向服务器应用的64位处理器，由于具有64位寻址能力，它能够使用100万TB的地址空间，足以运算企业级或超大规模的数据库任务；64位宽的寄存器可以使CPU浮点运算达到非常高的精度。

**m32r处理器**

M32r处理器是日本瑞萨科技开发的32位高性能RISC处理器，内置大容量存储器，适用于车载系统、数字AV设备、数字成像设备等产品领域。属于MCU型的处理器。

**m68k处理器**

M68k处理器是美国Motorola公司开发的高性能处理器，具有高性价比、高集成度等特点，在工业自动化设备、控制设备、医疗仪器系统、安全系统等领域多有应用。现在为Freescale公司所有，风头已不敌PowerPC处理器。

**microblaze处理器**

Microblaze处理器是美国Xilinx公司提供的嵌入在其FPGA芯片上的32位RISC软核。 它具有运算能力强、外围接口配置灵活等特点，集成在FPGA中，可以和FPGA实现协同设计，具备软硬件可配置的灵活性。

**mips处理器**

Mips处理器是由美国斯坦福大学Hennessy教授领导的研究小组研制出来，现为Mips公司拥有，和Arm处理器一样采用IP授权的方式由第三方开发生产。著名的公司如Broadcom、Nxp等都有出品Mips处理器。我国的龙芯CPU也是采用Mips体系结构。

**mn10300处理器**

Mn10300处理器是日本松下开发的32位多媒体处理器。

**openrisc处理器**

OpenRisc是OpenCores组织提供的基于GPL协议的开放源代码的RISC(精简指令集计算机)处理器。有人认为其性能介于ARM7和ARM9之间，适合一般的嵌入式系统使用。最重要的一点是OpenCores组织提供了大量的开放源代码IP核供研究人员使用，因此对于一般的开发单位具有很大的吸引力。

**parisc处理器**

Parisc处理器是由HP(惠普)开发设计的处理器，主要用于HP(惠普)公司的服务器中，目前HP(惠普)已经放弃Parisc处理器的开发，不过一些Parisc处理器技术已经融合到ia64处理器之中。

**powerpc处理器**

Powerpc处理器是由美国IBM、Apple、Motorola联合开发的处理器，Powepc处理器在IBM的服务器、Apple的MAC电脑中都有应用。不过现在多应用在网络设备、视频系统、工业系统等领域。Sony PS3游戏机的Cell处理器也是Powerpc体系结构。

**s390处理器**

S390处理器是由美国IBM开发的面向大型机应用的处理器。

**score处理器**

Score处理器是由台湾凌阳开发的32位RISC处理器。Score属于MCU型处理器。

**sh处理器**

Sh处理器又称SuperH处理器，最先由日本Hitachi公司开发，后由Hitachi及ST Microelectronics两家公司共同开发，2003年瑞萨科技从Hitachi公司继承到拥有权。Sh属于MCU型处理器。

**sparc处理器**

Sparc处理器是由美国SUN和TI公司共同开发的RISC微处理器，最突出的特点是它的可扩展性。SUN公司将它做为高端处理器应用到服务器产品。

**tile处理器**

-

**um处理器**

-

**unicore32处理器**

-

**x86处理器**

X86处理器是由美国Intel推出的复杂指令集(cisc)处理器，广泛应用在PC电脑领域和服务器领域，在工业控制领域也有应用。目前主要是Intel、AMD、VIA在开发x86体系结构的处理器。

**xtensa处理器**

Xtensa处理器是由美国Tensilica(泰思立达)公司开发的可配置及可扩展的微处理器。

## Appendix F: vmlinux.lds.S

```
/*
 * ld script for the x86 kernel
 *
 * Historic 32-bit version written by Martin Mares <mj@atrey.karlin.mff.cuni.cz>
 *
 * Modernisation, unification and other changes and fixes:
 *   Copyright (C) 2007-2009  Sam Ravnborg <sam@ravnborg.org>
 *
 *
 * Don't define absolute symbols until and unless you know that symbol
 * value is should remain constant even if kernel image is relocated
 * at run time. Absolute symbols are not relocated. If symbol value should
 * change if kernel is relocated, make the symbol section relative and
 * put it inside the section definition.
 */

#ifdef CONFIG_X86_32
/*
 * See arch/x86/include/asm/page_32_types.h for __PAGE_OFFSET,
 * 由.config中的配置CONFIG_PAGE_OFFSET有关
 */
#define LOAD_OFFSET __PAGE_OFFSET
#else
/*
 * See arch/x86/include/asm/page_64_types.h
 * for __START_KERNEL_map
 */
#define LOAD_OFFSET __START_KERNEL_map
#endif

#include <asm-generic/vmlinux.lds.h>
#include <asm/asm-offsets.h>
#include <asm/thread_info.h>
#include <asm/page_types.h>
#include <asm/cache.h>
#include <asm/boot.h>

#undef i386     /* in case the preprocessor is a 32bit one */

// Alex Note: See .config for CONFIG_OUTPUT_FORMAT
OUTPUT_FORMAT(CONFIG_OUTPUT_FORMAT, CONFIG_OUTPUT_FORMAT, CONFIG_OUTPUT_FORMAT)

#ifdef CONFIG_X86_32
OUTPUT_ARCH(i386)		// Refer to output of linux command 'objdump -i'
ENTRY(phys_startup_32)	// See System.map
jiffies = jiffies_64;
#else
OUTPUT_ARCH(i386:x86-64)
ENTRY(phys_startup_64)
jiffies_64 = jiffies;
#endif

#if defined(CONFIG_X86_64) && defined(CONFIG_DEBUG_RODATA)
/*
 * On 64-bit, align RODATA to 2MB so that even with CONFIG_DEBUG_RODATA
 * we retain large page mappings for boundaries spanning kernel text, rodata
 * and data sections.
 *
 * However, kernel identity mappings will have different RWX permissions
 * to the pages mapping to text and to the pages padding (which are freed) the
 * text section. Hence kernel identity mappings will be broken to smaller
 * pages. For 64-bit, kernel text and kernel identity mappings are different,
 * so we can enable protection checks that come with CONFIG_DEBUG_RODATA,
 * as well as retain 2MB large page mappings for kernel text.
 */
#define X64_ALIGN_DEBUG_RODATA_BEGIN	. = ALIGN(HPAGE_SIZE);

#define X64_ALIGN_DEBUG_RODATA_END		\
		. = ALIGN(HPAGE_SIZE);		\
		__end_rodata_hpage_align = .;

#else

#define X64_ALIGN_DEBUG_RODATA_BEGIN
#define X64_ALIGN_DEBUG_RODATA_END

#endif

PHDRS {
	text PT_LOAD FLAGS(5);          /* R_E */	// 代码段配置，可加载，可读可执行
	data PT_LOAD FLAGS(6);          /* RW_ */	// 数据段配置，可加载，可读可写
#ifdef CONFIG_X86_64
#ifdef CONFIG_SMP
	percpu PT_LOAD FLAGS(6);        /* RW_ */
#endif
	init PT_LOAD FLAGS(7);          /* RWE */
#endif
	note PT_NOTE FLAGS(0);          /* ___ */	// 注释段配置
}

SECTIONS
{
#ifdef CONFIG_X86_32
		/*
		 * LOAD_PHYSICAL_ADDR定义于arch\x86\include\asm\boot.h
		 * 由CONFIG_PHYSICAL_START、CONFIG_PHYSICAL_ALIGN计算而来
		 */
        . = LOAD_OFFSET + LOAD_PHYSICAL_ADDR;
        phys_startup_32 = startup_32 - LOAD_OFFSET;
#else
        . = __START_KERNEL;
        phys_startup_64 = startup_64 - LOAD_OFFSET;
#endif

	/* Text and read-only data */
	.text :  AT(ADDR(.text) - LOAD_OFFSET) {
		_text = .;
		/* bootstrapping code */
		HEAD_TEXT				// See include/asm-generic/vmlinux.lds.h
#ifdef CONFIG_X86_32
		. = ALIGN(PAGE_SIZE);		// See arch/x86/include/asm/page_types.h
		*(.text..page_aligned)
#endif
		. = ALIGN(8);
		_stext = .;
		TEXT_TEXT				// See include/asm-generic/vmlinux.lds.h
		SCHED_TEXT				// See include/asm-generic/vmlinux.lds.h
		LOCK_TEXT				// See include/asm-generic/vmlinux.lds.h
		KPROBES_TEXT			// See include/asm-generic/vmlinux.lds.h
		ENTRY_TEXT				// See include/asm-generic/vmlinux.lds.h
		IRQENTRY_TEXT			// See include/asm-generic/vmlinux.lds.h
		*(.fixup)
		*(.gnu.warning)
		/* End of text section */
		_etext = .;
	} :text = 0x9090

	NOTES :text :note

	EXCEPTION_TABLE(16) :text = 0x9090

#if defined(CONFIG_DEBUG_RODATA)
	/* .text should occupy whole number of pages */
	. = ALIGN(PAGE_SIZE);
#endif
	X64_ALIGN_DEBUG_RODATA_BEGIN
	RO_DATA(PAGE_SIZE)
	X64_ALIGN_DEBUG_RODATA_END

	/* Data */
	.data : AT(ADDR(.data) - LOAD_OFFSET) {
		/* Start of data section */
		_sdata = .;

		/* init_task */
		INIT_TASK_DATA(THREAD_SIZE)

#ifdef CONFIG_X86_32
		/* 32 bit has nosave before _edata */
		NOSAVE_DATA
#endif

		PAGE_ALIGNED_DATA(PAGE_SIZE)

		CACHELINE_ALIGNED_DATA(L1_CACHE_BYTES)

		DATA_DATA
		CONSTRUCTORS

		/* rarely changed data like cpu maps */
		READ_MOSTLY_DATA(INTERNODE_CACHE_BYTES)

		/* End of data section */
		_edata = .;
	} :data

#ifdef CONFIG_X86_64

	. = ALIGN(PAGE_SIZE);
	__vvar_page = .;

	.vvar : AT(ADDR(.vvar) - LOAD_OFFSET) {
		/* work around gold bug 13023 */
		__vvar_beginning_hack = .;

		/* Place all vvars at the offsets in asm/vvar.h. */
#define EMIT_VVAR(name, offset) 				\
		. = __vvar_beginning_hack + offset;	\
		*(.vvar_ ## name)
#define __VVAR_KERNEL_LDS
#include <asm/vvar.h>
#undef __VVAR_KERNEL_LDS
#undef EMIT_VVAR

	} :data

       . = ALIGN(__vvar_page + PAGE_SIZE, PAGE_SIZE);

#endif /* CONFIG_X86_64 */

	/* Init code and data - will be freed after init */
	. = ALIGN(PAGE_SIZE);
	.init.begin : AT(ADDR(.init.begin) - LOAD_OFFSET) {
		__init_begin = .; /* paired with __init_end */
	}

#if defined(CONFIG_X86_64) && defined(CONFIG_SMP)
	/*
	 * percpu offsets are zero-based on SMP.  PERCPU_VADDR() changes the
	 * output PHDR, so the next output section - .init.text - should
	 * start another segment - init.
	 */
	PERCPU_VADDR(INTERNODE_CACHE_BYTES, 0, :percpu)
#endif

	INIT_TEXT_SECTION(PAGE_SIZE)
#ifdef CONFIG_X86_64
	:init
#endif

	INIT_DATA_SECTION(16)

	.x86_cpu_dev.init : AT(ADDR(.x86_cpu_dev.init) - LOAD_OFFSET) {
		__x86_cpu_dev_start = .;
		*(.x86_cpu_dev.init)
		__x86_cpu_dev_end = .;
	}

	/*
	 * start address and size of operations which during runtime
	 * can be patched with virtualization friendly instructions or
	 * baremetal native ones. Think page table operations.
	 * Details in paravirt_types.h
	 */
	. = ALIGN(8);
	.parainstructions : AT(ADDR(.parainstructions) - LOAD_OFFSET) {
		__parainstructions = .;
		*(.parainstructions)
		__parainstructions_end = .;
	}

	/*
	 * struct alt_inst entries. From the header (alternative.h):
	 * "Alternative instructions for different CPU types or capabilities"
	 * Think locking instructions on spinlocks.
	 */
	. = ALIGN(8);
	.altinstructions : AT(ADDR(.altinstructions) - LOAD_OFFSET) {
		__alt_instructions = .;
		*(.altinstructions)
		__alt_instructions_end = .;
	}

	/*
	 * And here are the replacement instructions. The linker sticks
	 * them as binary blobs. The .altinstructions has enough data to
	 * get the address and the length of them to patch the kernel safely.
	 */
	.altinstr_replacement : AT(ADDR(.altinstr_replacement) - LOAD_OFFSET) {
		*(.altinstr_replacement)
	}

	/*
	 * struct iommu_table_entry entries are injected in this section.
	 * It is an array of IOMMUs which during run time gets sorted depending
	 * on its dependency order. After rootfs_initcall is complete
	 * this section can be safely removed.
	 */
	.iommu_table : AT(ADDR(.iommu_table) - LOAD_OFFSET) {
		__iommu_table = .;
		*(.iommu_table)
		__iommu_table_end = .;
	}

	. = ALIGN(8);
	.apicdrivers : AT(ADDR(.apicdrivers) - LOAD_OFFSET) {
		__apicdrivers = .;
		*(.apicdrivers);
		__apicdrivers_end = .;
	}

	. = ALIGN(8);
	/*
	 * .exit.text is discard at runtime, not link time, to deal with
	 *  references from .altinstructions and .eh_frame
	 */
	.exit.text : AT(ADDR(.exit.text) - LOAD_OFFSET) {
		EXIT_TEXT
	}

	.exit.data : AT(ADDR(.exit.data) - LOAD_OFFSET) {
		EXIT_DATA
	}

#if !defined(CONFIG_X86_64) || !defined(CONFIG_SMP)
	PERCPU_SECTION(INTERNODE_CACHE_BYTES)
#endif

	. = ALIGN(PAGE_SIZE);

	/* freed after init ends here */
	.init.end : AT(ADDR(.init.end) - LOAD_OFFSET) {
		__init_end = .;
	}

	/*
	 * smp_locks might be freed after init
	 * start/end must be page aligned
	 */
	. = ALIGN(PAGE_SIZE);
	.smp_locks : AT(ADDR(.smp_locks) - LOAD_OFFSET) {
		__smp_locks = .;
		*(.smp_locks)
		. = ALIGN(PAGE_SIZE);
		__smp_locks_end = .;
	}

#ifdef CONFIG_X86_64
	.data_nosave : AT(ADDR(.data_nosave) - LOAD_OFFSET) {
		NOSAVE_DATA
	}
#endif

	/* BSS */
	. = ALIGN(PAGE_SIZE);
	.bss : AT(ADDR(.bss) - LOAD_OFFSET) {
		__bss_start = .;
		*(.bss..page_aligned)
		*(.bss)
		. = ALIGN(PAGE_SIZE);
		__bss_stop = .;
	}

	. = ALIGN(PAGE_SIZE);
	.brk : AT(ADDR(.brk) - LOAD_OFFSET) {
		__brk_base = .;
		. += 64 * 1024;		/* 64k alignment slop space */
		*(.brk_reservation)	/* areas brk users have reserved */
		__brk_limit = .;
	}

	_end = .;

     STABS_DEBUG
     DWARF_DEBUG

	/* Sections to be discarded */
	DISCARDS
	/DISCARD/ : { *(.eh_frame) }
}


#ifdef CONFIG_X86_32
/*
 * The ASSERT() sink to . is intentional, for binutils 2.14 compatibility:
 */
. = ASSERT((_end - LOAD_OFFSET <= KERNEL_IMAGE_SIZE),
	   "kernel image bigger than KERNEL_IMAGE_SIZE");
#else
/*
 * Per-cpu symbols which need to be offset from __per_cpu_load
 * for the boot processor.
 */
#define INIT_PER_CPU(x) init_per_cpu__##x = x + __per_cpu_load
INIT_PER_CPU(gdt_page);
INIT_PER_CPU(irq_stack_union);

/*
 * Build-time check on the image size:
 */
. = ASSERT((_end - _text <= KERNEL_IMAGE_SIZE),
	   "kernel image bigger than KERNEL_IMAGE_SIZE");

#ifdef CONFIG_SMP
. = ASSERT((irq_stack_union == 0),
           "irq_stack_union is not at start of per-cpu area");
#endif

#endif /* CONFIG_X86_32 */

#ifdef CONFIG_KEXEC
#include <asm/kexec.h>

. = ASSERT(kexec_control_code_size <= KEXEC_CONTROL_CODE_MAX_SIZE,
           "kexec control code size is too big");
#endif
```

## Appendix G: vmlinux.lds

vmlinux.lds是由vmlinux.lds.S经过预处理而生成的文件，用于链接器ld连接.o文件时的link script。该文件的生成过程参见$(vmlinux-lds)节，其具体内容如下：

```
OUTPUT_FORMAT("elf32-i386", "elf32-i386", "elf32-i386")
OUTPUT_ARCH(i386)
ENTRY(phys_startup_32)
jiffies = jiffies_64;

PHDRS {
  text PT_LOAD FLAGS(5); /* R_E */
  data PT_LOAD FLAGS(6); /* RW_ */
  note PT_NOTE FLAGS(0); /* ___ */
}

SECTIONS
{
  . = 0xC0000000 + ((0x1000000 + (0x1000000 - 1)) & ~(0x1000000 - 1));
  phys_startup_32 = startup_32 - 0xC0000000;

  /* Text and read-only data */
  .text : AT(ADDR(.text) - 0xC0000000) {
    _text = .;
    /* bootstrapping code */
    *(.head.text)
    . = ALIGN((1 << 12));
    *(.text..page_aligned)
    . = ALIGN(8);
    _stext = .;
    . = ALIGN(8); *(.text.hot) *(.text) *(.ref.text) *(.devinit.text) *(.devexit.text) *(.text.unlikely)
    . = ALIGN(8); __sched_text_start = .; *(.sched.text) __sched_text_end = .;
    . = ALIGN(8); __lock_text_start = .; *(.spinlock.text) __lock_text_end = .;
    . = ALIGN(8); __kprobes_text_start = .; *(.kprobes.text) __kprobes_text_end = .;
    . = ALIGN(8); __entry_text_start = .; *(.entry.text) __entry_text_end = .;

    *(.fixup)
    *(.gnu.warning)
    /* End of text section */
    _etext = .;
  } :text = 0x9090

  .notes : AT(ADDR(.notes) - 0xC0000000) { __start_notes = .; *(.note.*) __stop_notes = .; } :text :note
  . = ALIGN(16); __ex_table : AT(ADDR(__ex_table) - 0xC0000000) { __start___ex_table = .; *(__ex_table) __stop___ex_table = .; } :text = 0x9090

  . = ALIGN(((1 << 12))); .rodata : AT(ADDR(.rodata) - 0xC0000000) { __start_rodata = .; *(.rodata) *(.rodata.*) *(__vermagic) . = ALIGN(8); __start___tracepoints_ptrs = .; *(__tracepoints_ptrs) __stop___tracepoints_ptrs = .; *(__tracepoints_strings) } .rodata1 : AT(ADDR(.rodata1) - 0xC0000000) { *(.rodata1) } . = ALIGN(8); __bug_table : AT(ADDR(__bug_table) - 0xC0000000) { __start___bug_table = .; *(__bug_table) __stop___bug_table = .; } .pci_fixup : AT(ADDR(.pci_fixup) - 0xC0000000) { __start_pci_fixups_early = .; *(.pci_fixup_early) __end_pci_fixups_early = .; __start_pci_fixups_header = .; *(.pci_fixup_header) __end_pci_fixups_header = .; __start_pci_fixups_final = .; *(.pci_fixup_final) __end_pci_fixups_final = .; __start_pci_fixups_enable = .; *(.pci_fixup_enable) __end_pci_fixups_enable = .; __start_pci_fixups_resume = .; *(.pci_fixup_resume) __end_pci_fixups_resume = .; __start_pci_fixups_resume_early = .; *(.pci_fixup_resume_early) __end_pci_fixups_resume_early = .; __start_pci_fixups_suspend = .; *(.pci_fixup_suspend) __end_pci_fixups_suspend = .; } .builtin_fw : AT(ADDR(.builtin_fw) - 0xC0000000) { __start_builtin_fw = .; *(.builtin_fw) __end_builtin_fw = .; } .rio_ops : AT(ADDR(.rio_ops) - 0xC0000000) { __start_rio_switch_ops = .; *(.rio_switch_ops) __end_rio_switch_ops = .; } . = ALIGN(4); .tracedata : AT(ADDR(.tracedata) - 0xC0000000) { __tracedata_start = .; *(.tracedata) __tracedata_end = .; } __ksymtab : AT(ADDR(__ksymtab) - 0xC0000000) { __start___ksymtab = .; *(SORT(___ksymtab+*)) __stop___ksymtab = .; } __ksymtab_gpl : AT(ADDR(__ksymtab_gpl) - 0xC0000000) { __start___ksymtab_gpl = .; *(SORT(___ksymtab_gpl+*)) __stop___ksymtab_gpl = .; } __ksymtab_unused : AT(ADDR(__ksymtab_unused) - 0xC0000000) { __start___ksymtab_unused = .; *(SORT(___ksymtab_unused+*)) __stop___ksymtab_unused = .; } __ksymtab_unused_gpl : AT(ADDR(__ksymtab_unused_gpl) - 0xC0000000) { __start___ksymtab_unused_gpl = .; *(SORT(___ksymtab_unused_gpl+*)) __stop___ksymtab_unused_gpl = .; } __ksymtab_gpl_future : AT(ADDR(__ksymtab_gpl_future) - 0xC0000000) { __start___ksymtab_gpl_future = .; *(SORT(___ksymtab_gpl_future+*)) __stop___ksymtab_gpl_future = .; } __kcrctab : AT(ADDR(__kcrctab) - 0xC0000000) { __start___kcrctab = .; *(SORT(___kcrctab+*)) __stop___kcrctab = .; } __kcrctab_gpl : AT(ADDR(__kcrctab_gpl) - 0xC0000000) { __start___kcrctab_gpl = .; *(SORT(___kcrctab_gpl+*)) __stop___kcrctab_gpl = .; } __kcrctab_unused : AT(ADDR(__kcrctab_unused) - 0xC0000000) { __start___kcrctab_unused = .; *(SORT(___kcrctab_unused+*)) __stop___kcrctab_unused = .; } __kcrctab_unused_gpl : AT(ADDR(__kcrctab_unused_gpl) - 0xC0000000) { __start___kcrctab_unused_gpl = .; *(SORT(___kcrctab_unused_gpl+*)) __stop___kcrctab_unused_gpl = .; } __kcrctab_gpl_future : AT(ADDR(__kcrctab_gpl_future) - 0xC0000000) { __start___kcrctab_gpl_future = .; *(SORT(___kcrctab_gpl_future+*)) __stop___kcrctab_gpl_future = .; } __ksymtab_strings : AT(ADDR(__ksymtab_strings) - 0xC0000000) { *(__ksymtab_strings) } __init_rodata : AT(ADDR(__init_rodata) - 0xC0000000) { *(.ref.rodata) *(.devinit.rodata) *(.devexit.rodata) } __param : AT(ADDR(__param) - 0xC0000000) { __start___param = .; *(__param) __stop___param = .; } __modver : AT(ADDR(__modver) - 0xC0000000) { __start___modver = .; *(__modver) __stop___modver = .; . = ALIGN(((1 << 12))); __end_rodata = .; } . = ALIGN(((1 << 12)));

  /* Data */
  .data : AT(ADDR(.data) - 0xC0000000) {
    /* Start of data section */
    _sdata = .;
    /* init_task */
    . = ALIGN(((1 << 12) << 1)); *(.data..init_task)
    /* 32 bit has nosave before _edata */
    . = ALIGN((1 << 12)); __nosave_begin = .; *(.data..nosave) . = ALIGN((1 << 12)); __nosave_end = .;
    . = ALIGN((1 << 12)); *(.data..page_aligned)
    . = ALIGN((1 << (5))); *(.data..cacheline_aligned)
    *(.data) *(.ref.data) *(.data..shared_aligned) *(.devinit.data) *(.devexit.data) . = ALIGN(32); *(__tracepoints) . = ALIGN(8); __start___jump_table = .; *(__jump_table) __stop___jump_table = .; . = ALIGN(8); __start___verbose = .; *(__verbose) __stop___verbose = .; __start_annotated_branch_profile = .; *(_ftrace_annotated_branch) __stop_annotated_branch_profile = .; __start___trace_bprintk_fmt = .; *(__trace_printk_fmt) __stop___trace_bprintk_fmt = .;
    CONSTRUCTORS
    /* rarely changed data like cpu maps */
    . = ALIGN((1 << 5)); *(.data..read_mostly) . = ALIGN((1 << 5));
    /* End of data section */
    _edata = .;
  } :data

  /* Init code and data - will be freed after init */
  . = ALIGN((1 << 12));
  .init.begin : AT(ADDR(.init.begin) - 0xC0000000) {
    __init_begin = .; /* paired with __init_end */
  }
  . = ALIGN((1 << 12)); .init.text : AT(ADDR(.init.text) - 0xC0000000) { _sinittext = .; *(.init.text) *(.cpuinit.text) *(.meminit.text) _einittext = .; }
  .init.data : AT(ADDR(.init.data) - 0xC0000000) { *(.init.data) *(.cpuinit.data) *(.meminit.data) . = ALIGN(8); __ctors_start = .; *(.ctors) __ctors_end = .; *(.init.rodata) . = ALIGN(8); __start_ftrace_events = .; *(_ftrace_events) __stop_ftrace_events = .; *(.cpuinit.rodata) *(.meminit.rodata) . = ALIGN(32); __dtb_start = .; *(.dtb.init.rodata) __dtb_end = .; . = ALIGN(16); __setup_start = .; *(.init.setup) __setup_end = .; __initcall_start = .; *(.initcallearly.init) __early_initcall_end = .; *(.initcall0.init) *(.initcall0s.init) *(.initcall1.init) *(.initcall1s.init) *(.initcall2.init) *(.initcall2s.init) *(.initcall3.init) *(.initcall3s.init) *(.initcall4.init) *(.initcall4s.init) *(.initcall5.init) *(.initcall5s.init) *(.initcallrootfs.init) *(.initcall6.init) *(.initcall6s.init) *(.initcall7.init) *(.initcall7s.init) __initcall_end = .; __con_initcall_start = .; *(.con_initcall.init) __con_initcall_end = .; __security_initcall_start = .; *(.security_initcall.init) __security_initcall_end = .; }
 /*
  * Code and data for a variety of lowlevel trampolines, to be
  * copied into base memory (< 1 MiB) during initialization.
  * Since it is copied early, the main copy can be discarded
  * afterwards.
  */
  .x86_trampoline : AT(ADDR(.x86_trampoline) - 0xC0000000) {
    x86_trampoline_start = .;
    *(.x86_trampoline)
    x86_trampoline_end = .;
  }
  .x86_cpu_dev.init : AT(ADDR(.x86_cpu_dev.init) - 0xC0000000) {
    __x86_cpu_dev_start = .;
    *(.x86_cpu_dev.init)
    __x86_cpu_dev_end = .;
  }
  /*
   * start address and size of operations which during runtime
   * can be patched with virtualization friendly instructions or
   * baremetal native ones. Think page table operations.
   * Details in paravirt_types.h
   */
  . = ALIGN(8);
  .parainstructions : AT(ADDR(.parainstructions) - 0xC0000000) {
    __parainstructions = .;
    *(.parainstructions)
    __parainstructions_end = .;
  }
  /*
   * struct alt_inst entries. From the header (alternative.h):
   * "Alternative instructions for different CPU types or capabilities"
   * Think locking instructions on spinlocks.
   */
  . = ALIGN(8);
  .altinstructions : AT(ADDR(.altinstructions) - 0xC0000000) {
    __alt_instructions = .;
    *(.altinstructions)
    __alt_instructions_end = .;
  }
  /*
   * And here are the replacement instructions. The linker sticks
   * them as binary blobs. The .altinstructions has enough data to
   * get the address and the length of them to patch the kernel safely.
   */
  .altinstr_replacement : AT(ADDR(.altinstr_replacement) - 0xC0000000) {
    *(.altinstr_replacement)
  }
  /*
   * struct iommu_table_entry entries are injected in this section.
   * It is an array of IOMMUs which during run time gets sorted depending
   * on its dependency order. After rootfs_initcall is complete
   * this section can be safely removed.
   */
  .iommu_table : AT(ADDR(.iommu_table) - 0xC0000000) {
    __iommu_table = .;
    *(.iommu_table)
    __iommu_table_end = .;
  }
  . = ALIGN(8);
  .apicdrivers : AT(ADDR(.apicdrivers) - 0xC0000000) {
    __apicdrivers = .;
    *(.apicdrivers);
    __apicdrivers_end = .;
  }
  . = ALIGN(8);
  /*
   * .exit.text is discard at runtime, not link time, to deal with
   *  references from .altinstructions and .eh_frame
   */
  .exit.text : AT(ADDR(.exit.text) - 0xC0000000) {
    *(.exit.text) *(.cpuexit.text) *(.memexit.text)
  }
  .exit.data : AT(ADDR(.exit.data) - 0xC0000000) {
    *(.exit.data) *(.cpuexit.data) *(.cpuexit.rodata) *(.memexit.data) *(.memexit.rodata)
  }
  . = ALIGN((1 << 12)); .data..percpu : AT(ADDR(.data..percpu) - 0xC0000000) { __per_cpu_load = .; __per_cpu_start = .; *(.data..percpu..first) . = ALIGN((1 << 12)); *(.data..percpu..page_aligned) . = ALIGN((1 << 5)); *(.data..percpu..readmostly) . = ALIGN((1 << 5)); *(.data..percpu) *(.data..percpu..shared_aligned) __per_cpu_end = .; }
  . = ALIGN((1 << 12));
  /* freed after init ends here */
  .init.end : AT(ADDR(.init.end) - 0xC0000000) {
    __init_end = .;
  }
  /*
   * smp_locks might be freed after init
   * start/end must be page aligned
   */
  . = ALIGN((1 << 12));
  .smp_locks : AT(ADDR(.smp_locks) - 0xC0000000) {
    __smp_locks = .;
    *(.smp_locks)
    . = ALIGN((1 << 12));
    __smp_locks_end = .;
  }
  /* BSS */
  . = ALIGN((1 << 12));
  .bss : AT(ADDR(.bss) - 0xC0000000) {
    __bss_start = .;
    *(.bss..page_aligned)
    *(.bss)
    . = ALIGN((1 << 12));
    __bss_stop = .;
  }
  . = ALIGN((1 << 12));
  .brk : AT(ADDR(.brk) - 0xC0000000) {
    __brk_base = .;
    . += 64 * 1024; /* 64k alignment slop space */
    *(.brk_reservation) /* areas brk users have reserved */
    __brk_limit = .;
  }
  _end = .;

  .stab 0 : { *(.stab) } .stabstr 0 : { *(.stabstr) } .stab.excl 0 : { *(.stab.excl) } .stab.exclstr 0 : { *(.stab.exclstr) } .stab.index 0 : { *(.stab.index) } .stab.indexstr 0 : { *(.stab.indexstr) } .comment 0 : { *(.comment) }
  .debug 0 : { *(.debug) } .line 0 : { *(.line) } .debug_srcinfo 0 : { *(.debug_srcinfo) } .debug_sfnames 0 : { *(.debug_sfnames) } .debug_aranges 0 : { *(.debug_aranges) } .debug_pubnames 0 : { *(.debug_pubnames) } .debug_info 0 : { *(.debug_info .gnu.linkonce.wi.*) } .debug_abbrev 0 : { *(.debug_abbrev) } .debug_line 0 : { *(.debug_line) } .debug_frame 0 : { *(.debug_frame) } .debug_str 0 : { *(.debug_str) } .debug_loc 0 : { *(.debug_loc) } .debug_macinfo 0 : { *(.debug_macinfo) } .debug_weaknames 0 : { *(.debug_weaknames) } .debug_funcnames 0 : { *(.debug_funcnames) } .debug_typenames 0 : { *(.debug_typenames) } .debug_varnames 0 : { *(.debug_varnames) }

  /* Sections to be discarded */
  /DISCARD/ : { *(.exit.text) *(.cpuexit.text) *(.memexit.text) *(.exit.data) *(.cpuexit.data) *(.cpuexit.rodata) *(.memexit.data) *(.memexit.rodata) *(.exitcall.exit) *(.discard) *(.discard.*) }
  /DISCARD/ : { *(.eh_frame) }
}

  /*
   * The ASSERT() sink to . is intentional, for binutils 2.14 compatibility:
   */
  . = ASSERT((_end - 0xC0000000 <= (512 * 1024 * 1024)), "kernel image bigger than KERNEL_IMAGE_SIZE");
```
命令readelf -a vmlinux的输出参见文档vmlinux.lds/vmlinux.readelf -a.txt.zip

## Appendix H: scripts/module-common.lds

script/module-common.lds是生成*.ko文件的链接脚本文件，其包含如下内容，引用参见[3.4.3.4.2.3 $(modules)](#3-4-3-4-2-3-modules-)节:

```
/*
 * Common module linker script, always used when linking a module.
 * Archs are free to supply their own linker scripts.  ld will
 * combine them automatically.
 */
SECTIONS {
	/DISCARD/ : { *(.discard) }

	__ksymtab			: { *(SORT(___ksymtab+*)) }
	__ksymtab_gpl		: { *(SORT(___ksymtab_gpl+*)) }
	__ksymtab_unused		: { *(SORT(___ksymtab_unused+*)) }
	__ksymtab_unused_gpl	: { *(SORT(___ksymtab_unused_gpl+*)) }
	__ksymtab_gpl_future	: { *(SORT(___ksymtab_gpl_future+*)) }

	__kcrctab			: { *(SORT(___kcrctab+*)) }
	__kcrctab_gpl		: { *(SORT(___kcrctab_gpl+*)) }
	__kcrctab_unused		: { *(SORT(___kcrctab_unused+*)) }
	__kcrctab_unused_gpl	: { *(SORT(___kcrctab_unused_gpl+*)) }
	__kcrctab_gpl_future	: { *(SORT(___kcrctab_gpl_future+*)) }
}
```

## Appendix I: Targets Tree

![Targets_Tree](/assets/Targets_Tree.jpg)

# References

* [The Linux Kernel Documentation](https://www.kernel.org/doc/html/latest/index.html)