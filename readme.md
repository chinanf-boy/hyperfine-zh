# hyperfine [![translate-svg]][translate-list]

[translate-svg]: http://llever.com/translate.svg
[translate-list]: https://github.com/chinanf-boy/chinese-translate-list
    
「 命令行基准测试工具 」

[中文](./readme.md) | [english](https://github.com/sharkdp/hyperfine)


---

## 校对 ✅

<!-- doc-templite START generated -->
<!-- repo = 'sharkdp/hyperfine' -->
<!-- commit = 'bdccc10a61d65b0d1e59e98bf95a0ca50edc2d76' -->
<!-- time = '2018 8.26' -->
翻译的原文 | 与日期 | 最新更新 | 更多
---|---|---|---
[commit] | ⏰ 2018 8.26 | ![last] | [中文翻译][translate-list]

[last]: https://img.shields.io/github/last-commit/sharkdp/hyperfine.svg
[commit]: https://github.com/sharkdp/hyperfine/tree/bdccc10a61d65b0d1e59e98bf95a0ca50edc2d76

<!-- doc-templite END generated -->


### 贡献

欢迎 👏 勘误/校对/更新贡献 😊 [具体贡献请看](https://github.com/chinanf-boy/chinese-translate-list#贡献)

## 生活

[help me live , live need money 💰](https://github.com/chinanf-boy/live-need-money)

---

### 目录

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [hyperfine](#hyperfine)
  - [特征](#%E7%89%B9%E5%BE%81)
  - [用法](#%E7%94%A8%E6%B3%95)
    - [基本基准](#%E5%9F%BA%E6%9C%AC%E5%9F%BA%E5%87%86)
    - [I / O重程序](#i--o%E9%87%8D%E7%A8%8B%E5%BA%8F)
    - [参数化基准](#%E5%8F%82%E6%95%B0%E5%8C%96%E5%9F%BA%E5%87%86)
    - [导出结果](#%E5%AF%BC%E5%87%BA%E7%BB%93%E6%9E%9C)
  - [安装](#%E5%AE%89%E8%A3%85)
    - [在macOS上](#%E5%9C%A8macos%E4%B8%8A)
    - [在Ubuntu上](#%E5%9C%A8ubuntu%E4%B8%8A)
    - [在Arch Linux上](#%E5%9C%A8arch-linux%E4%B8%8A)
    - [在Void Linux上](#%E5%9C%A8void-linux%E4%B8%8A)
    - [货物 (Linux,macOS,Windows)](#%E8%B4%A7%E7%89%A9-linuxmacoswindows)
    - [从二进制文件 (Linux,macOS)](#%E4%BB%8E%E4%BA%8C%E8%BF%9B%E5%88%B6%E6%96%87%E4%BB%B6-linuxmacos)
  - [名称的由来](#%E5%90%8D%E7%A7%B0%E7%9A%84%E7%94%B1%E6%9D%A5)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->


# hyperfine

[![Build Status](https://travis-ci.org/sharkdp/hyperfine.svg?branch=master)](https://travis-ci.org/sharkdp/hyperfine)
[![Build status](https://ci.appveyor.com/api/projects/status/pdqq5frgkcj0smrs?svg=true)](https://ci.appveyor.com/project/sharkdp/hyperfine)
[![Version info](https://img.shields.io/crates/v/hyperfine.svg)](https://crates.io/crates/hyperfine)

命令行基准测试工具 (*灵感来自[长凳](https://github.com/Gabriel439/bench)*) . 

**演示**: [`fd`](https://github.com/sharkdp/fd)和[`find`](https://www.gnu.org/software/findutils/) 的 基准测试:

![hyperfine](https://i.imgur.com/5OqrGWe.gif)

## 特征

-   多次运行的统计分析. 
-   支持任意shell命令. 
-   关于基准进展和当前估计的持续反馈. 
-   可以在实际基准之前,预热执行. 
-   可以在每次定时运行之前设置高速缓存清除命令. 
-   统计异常值检测. 
-   将结果导出为各种格式: CSV,JSON,Markdown. 
-   参数化基准. 
-   跨平台

## 用法

### 基本基准

要运行基准测试,您只需调用即可`hyperfine <command>...`. 参数可以是任何shell命令. 例如: 

```bash
hyperfine 'sleep 0.3'
```

Hyperfine将自动确定要为每个命令执行的运行次数. 默认情况下,它将执行*至少*10个基准测试运行. 要更改此设置,您可以使用`-m`/`--min-runs`选项: 

```bash
hyperfine --min-runs 5 'sleep 0.2' 'sleep 3.2'
```

### 注重 I/O 的程序

如果程序执行时间受磁盘 I/O 限制,则基准测试结果可能会受到磁盘高速缓存以及它们是冷还是热的严重影响. 

如果要在热缓存上运行基准测试,可以使用`-w`/`--warmup`在实际基准测试之前,执行一定数量的程序执行的选项: 

```bash
hyperfine --warmup 3 'grep -R TODO *'
```

相反,如果要运行冷缓存的基准测试,可以使用这个`-p`/`--prepare`选项,在*每次*计时运行之前运行特殊命令. 例如,要清除Linux上的硬盘缓存,可以运行

```bash
sync; echo 3 | sudo tee /proc/sys/vm/drop_caches
```

要在 Hyperfine 中使用此特定命令,请`sudo -v`暂时获得sudo权限,然后调用: 

```bash
hyperfine --prepare 'sync; echo 3 | sudo tee /proc/sys/vm/drop_caches' 'grep -R TODO *'
```

### 参数化基准

如果你想运行只有一个参数变化的基准 (比如线程数) ,你可以使用`-P`/`--parameter-scan`选项: 

```bash
hyperfine --prepare 'make clean' --parameter-scan num_threads 1 12 'make -j {num_threads}'
```

### 导出结果

Hyperfine有多种导出基准测试结果的选项: CSV,JSON,Markdown (参见`--help`文字详情) . 例如,要将结果导出到Markdown,您可以使用`--export-markdown`,那将创建这样的表: 

| Command | Mean [ms] | Min…Max [ms] |
|:---|---:|---:|
| `find . -iregex '.*[0-9]\.jpg$'` | 506.0 ± 8.1 | 495.4…518.6 |
| `find . -iname '*[0-9].jpg'` | 304.9 ± 3.1 | 299.8…309.3 |
| `fd -HI '.*[0-9]\.jpg$'` | 66.2 ± 5.8 | 62.5…86.3 |

## 安装

### 在macOS上

Hyperfine可以通过安装[brew](https://brew.sh): 

    brew install hyperfine

### 在Ubuntu上

下载相应的`.deb`包,在[发布页面](https://github.com/sharkdp/hyperfine/releases)并通过`dpkg`安装它: 

    wget https://github.com/sharkdp/hyperfine/releases/download/v1.2.0/hyperfine_1.2.0_amd64.deb
    sudo dpkg -i hyperfine_1.2.0_amd64.deb

### 在Arch Linux上

在Arch Linux上,可以安装hyperfine[通过AUR](https://aur.archlinux.org/packages/hyperfine): 

    yaourt -S hyperfine

### 在Void Linux上

Hyperfine可以通过xbps安装

    xbps-install -S hyperfine

### Cargo (Linux,macOS,Windows) 

Hyperfine可以通过安装[cargo](https://doc.rust-lang.org/cargo/): 

    cargo install hyperfine

确保使用Rust 1.24或更高版本. 

### 二进制文件 (Linux,macOS) 

从中下载相应的存档[发布页面](https://github.com/sharkdp/hyperfine/releases). 

## 名称的由来

名字*hyperfine*是根据 铯133 的 hyperfine 水平来选择的,铯133在铯中[定义我们的基本时间单位](https://en.wikipedia.org/wiki/Second#Based_on_caesium_microwave_atomic_clock)起着至关重要的作用- 第二. 
