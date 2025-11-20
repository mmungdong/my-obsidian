# 1. 工具下载

## 1.1. 代码阅读工具 - CLion

- 下载地址：[下载 CLion：用于 C/C++ 开发的智能跨平台 IDE](https://www.jetbrains.com.cn/clion/download/)

- 安装 & 配置 CLion：[CLion安装、配置、使用、调试（完全小白向）-CSDN博客](https://blog.csdn.net/annesede/article/details/133940779)

# 2. code

## 2.1. 下载源码

> 源码： [GitHub - redis/redis: For developers, who are building real-time data-driven applications, Redis is the preferred, fastest, and most feature-rich cache, data structure server, and document and vector query engine.](https://github.com/redis/redis)

```shell
git clone https://github.com/redis/redis.git
```

这里我们以 `7.0.5` 的版本来编译并查看源码的目录结构。

切换分支到 `7.0.5`:

```shell
git checkout tags/7.0.5 -b 7.0.5
```

## 2.2. 编译

前提：如果编译环境没有 gcc 编译器，检查：

```shell
gcc -v
```

用 CLion 打开 redis，这里我是 windows，推荐使用 WSL 来对源码进行编译，在代码的根目录执行下面命令来编译：

```shell
 make CFLAGS="-g -O0" MALLOC=jemalloc
```

如果编译失败了，执行下面命令清理编译残留后再编译：

```bash
make distclean
```

问题：

1. 报错：

```shell
release.c:37:10: fatal error: release.h: No such file or directory 37 | #include "release.h" | ^~~~~~~~~~~ compilation terminated. make[1]: *** [Makefile:403: release.o] Error 1 make[1]: Leaving directory '/mnt/d/code/redis/src' make: *** [Makefile:6: all] Error 2
```

