---
title: "Dart"
date: 2019-09-24T08:52:37+08:00
draft: true
---

# dart 安装与更新

[参考链接🔗](https://dart.dev/get-dart)

dart的发行版本有2种，一种是stable releases(稳定版)，一种是pre-releases（预发版）。<br>

**稳定版**：最多6周更新一次。稳定版的版本号用.分隔数字，不带连接符(-)或字母，比如2.5.0。<br>
**预发版**：通常1周更新一次。预发版的版本号有额外的连接符(-)和字母(dev)，比如2.6.0-dev.1.0。<br>
<br>
安装稳定版
```shell
brew tap dart-lang/dart
brew install dart
```

安装预发版

```shell
brew install dart -- --devel
```

查看dart版本号（注意第2行的`stable 2.5.0, devel 2.6.0-dev.1.0`版本号表示方式）
```shell
[zhuwenjun@bogon ~ ]$ brew info dart
dart-lang/dart/dart: stable 2.5.0, devel 2.6.0-dev.1.0
The Dart SDK
https://www.dartlang.org/
/usr/local/Cellar/dart/2.5.0 (395 files, 559.1MB) *
  Built from source on 2019-09-23 at 18:43:38
From: https://github.com/dart-lang/homebrew-dart/blob/master/dart.rb
==> Options
--devel
	Install development version 2.6.0-dev.1.0
==> Caveats
Please note the path to the Dart SDK:
  /usr/local/opt/dart/libexec
[zhuwenjun@bogon ~ ]$
```

更新dart
```shell
brew upgrade dart
```

如果当前使用的是稳定版，但是要安装预发版：
```shell
brew upgrade --force dart -- --devel
```

如果当前使用的是预发版，但是要安装稳定版：
```shell
brew unlink dart
brew install dart
```

切换已安装好的dart版本`brew switch dart <version>`(TODO：确认是否只是能切换稳定版本)
```shell
[zhuwenjun@bogon ~ ]$ brew switch dart 2.5.0
Cleaning /usr/local/Cellar/dart/2.5.0
9 links created for /usr/local/Cellar/dart/2.5.0
[zhuwenjun@bogon ~ ]$ brew switch dart 2.6.0-dev.1.0
Error: dart does not have a version "2.6.0-dev.1.0" in the Cellar.
```
 
