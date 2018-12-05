---
title: "Docker笔记"
date: 2018-11-26T17:05:22+08:00
draft: true
---

> 参考书籍： Docker 从入门到实战

## 容器的原理
容器本质上是宿主机上的进程。容器的核心技术是Cgroups和namespace, 容器技术通过namespace实现资源隔离，通过Cgroups实现资源控制, 通过rootfs实现文件系统隔离<br>
LXC是Cgroups的管理工具，Cgroups是namespace的用户空间管理接口。namespace是Linux内核在task_struct中对进程组管理的基础机制。<br>

### Cgroups

Cgroups是Linux内核提供的一种可以限制、记录、隔离进程组所使用的物理资源（如CPU，内存，I/O等）的机制。

### namespace
chroot是一个实现资源隔离的命令，它可以实现文件系统隔离，这是最早的容器技术。
一个容器要做到6项基本隔离，也就是Linux内核中提供的6种namespace隔离<br>

 namespace  |   	  隔离内容
----------- | --------------------------- 
IPC 		| 信号量、消息队列和共享内存
Network		| 网络资源
Mount		| 文件系统挂载点
PID			| 进程🆔
UTS			| 主机名和域名
User		| 用户🆔和组🆔

namespace 主要是通过以下3个函数来完成的<br>
clone() -- 创建新的namespace
setns() -- 将进程关联到一个已经存在的namespace
unshare() -- 在已有进程上进行namespace隔离

## Docker 命令简介

### docker attach
```
docker attach --help

Usage:	docker attach [OPTIONS] CONTAINER

Attach local standard input, output, and error streams to a running container

Options:
      --detach-keys string   Override the key sequence for detaching a container
      --no-stdin             Do not attach STDIN
      --sig-proxy            Proxy all received signals to the process (default true)
```

```
  ~ 
   docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                      PORTS                NAMES
ccc2ae1bdca4        ubuntu              "/bin/bash"              3 minutes ago       Up 3 minutes                                     modest_babbage

  ~ 
   docker attach ccc2ae1bdca4
root@ccc2ae1bdca4:/# cat /proc/version
Linux version 4.9.125-linuxkit (root@659b6d51c354) (gcc version 6.4.0 (Alpine 6.4.0) ) #1 SMP Fri Sep 7 08:20:28 UTC 2018
root@ccc2ae1bdca4:/# date
Mon Nov 26 14:49:32 UTC 2018
root@ccc2ae1bdca4:/# Ctrl + P; Ctrl + Q
bash: Ctrl: command not found
bash: Ctrl: command not found
root@ccc2ae1bdca4:/# read escape sequence

  ~ 
```

### docker build
* -c: 控制CPU使用
* -f: 选择Dockerfile名称
* -m: 设置构建内存上限
* -q: 不显示构建过程的一些信息
* -t: 为构建的镜像打上标签

```
   docker build --help

Usage:	docker build [OPTIONS] PATH | URL | -

Build an image from a Dockerfile

Options:
      --add-host list           Add a custom host-to-IP mapping (host:ip)
      --build-arg list          Set build-time variables
      --cache-from strings      Images to consider as cache sources
      --cgroup-parent string    Optional parent cgroup for the container
      --compress                Compress the build context using gzip
      --cpu-period int          Limit the CPU CFS (Completely Fair Scheduler) period
      --cpu-quota int           Limit the CPU CFS (Completely Fair Scheduler) quota
  -c, --cpu-shares int          CPU shares (relative weight)
      --cpuset-cpus string      CPUs in which to allow execution (0-3, 0,1)
      --cpuset-mems string      MEMs in which to allow execution (0-3, 0,1)
      --disable-content-trust   Skip image verification (default true)
  -f, --file string             Name of the Dockerfile (Default is 'PATH/Dockerfile')
      --force-rm                Always remove intermediate containers
      --iidfile string          Write the image ID to the file
      --isolation string        Container isolation technology
      --label list              Set metadata for an image
  -m, --memory bytes            Memory limit
      --memory-swap bytes       Swap limit equal to memory plus swap: '-1' to enable unlimited swap
      --network string          Set the networking mode for the RUN instructions during build (default "default")
      --no-cache                Do not use cache when building the image
      --platform string         Set platform if server is multi-platform capable
      --pull                    Always attempt to pull a newer version of the image
  -q, --quiet                   Suppress the build output and print image ID on success
      --rm                      Remove intermediate containers after a successful build (default true)
      --security-opt strings    Security options
      --shm-size bytes          Size of /dev/shm
      --squash                  Squash newly built layers into a single new layer
      --stream                  Stream attaches to server to negotiate build context
  -t, --tag list                Name and optionally a tag in the 'name:tag' format
      --target string           Set the target build stage to build.
      --ulimit ulimit           Ulimit options (default [])

  ~/work/Docker 
  

```


### Docker system prune清理容器

```
docker system prune
WARNING! This will remove:
        - all stopped containers
        - all networks not used by at least one container
        - all dangling images
        - all dangling build cache
Are you sure you want to continue? [y/N] y
Deleted Containers:
4dcf245cb8fef2feace594f2adc0c8fe78676d8ed765645c50633b25001e62a5
46d1df1ca1a093022e742b435da9d529acfb906597d7d720acd83574ef1be872
41400f28267358aabac261c36a5224fcea9bd532d0033fcbf663ad4c4ae64e3c
c190ba0e6075a61a9a724533231ce43b4d8c548c81657658700d1d32e230ca2d
940df5ec9ba4648c542108a8c0efc47667cbe81db32b3d5b2872dc27d03b82c5
87b99fc55b3032fbba0066d3829aadbe4ad3e1fb52fa057389aad67d0a09a2e8
ec98a5b1971f4696d9e366007648cdb2f0006ad8a0c9d85b4a3f5842f9cb45cd
3003122bff768f434c996aa5e3ff19f7accf7d66f26a1bcd820b08bce3aff424
a4887f39a890c8fa0b24a93b2eaf4c42d52816710822a85f16cba1c05c070809
aed397abcd0d6b3e896df517837bd94f48620cce87e961d74daf8a7cf814c2b4
837d62f8febe76b6f65072b65a0bc7049667e63509f59bb27ca82d90921dcc80
9b7770990eaeab14a1a3109e27d07211fd6ce2718d445b0f8259c4d409f33580
1da92d1678abf6fd72979a908e25a4a591ce339ad10b53b183375e2a01cf6cc9
42ce1d798558ffc05281e6aa6c24c495ebc7b4c7ebc27b5b42ee2d6b90c0c06f
6e62cd0e41cfd838d68d23f2f6d2bb9a60918ba10e2d7622ed6be4510d490302
7fea23f4764c70913a22f69dc805b6c61bb7594cd1a406c9638e41abedb3974b
19f2bb9d6ff2ce303d421b34bb46a2658bc063c3f220a9070b79282cd55fe291

Total reclaimed space: 177B
```
