<div align="center">

# MosDNS

一个插件化、可编程的 DNS 转发器。

[![Build](https://github.com/OVINC-CN/MosDNS/actions/workflows/test.yml/badge.svg)](https://github.com/OVINC-CN/MosDNS/actions/workflows/test.yml)
[![Go Version](https://img.shields.io/github/go-mod/go-version/OVINC-CN/MosDNS?logo=go)](https://go.dev/)
[![License](https://img.shields.io/github/license/OVINC-CN/MosDNS)](LICENSE)
[![Container](https://img.shields.io/badge/GHCR-amd64%20%7C%20arm64-blue?logo=docker)](https://github.com/OVINC-CN/MosDNS/pkgs/container/mosdns)

[使用文档](https://irine-sistiana.gitbook.io/mosdns-wiki/) ·
[容器镜像](https://github.com/OVINC-CN/MosDNS/pkgs/container/mosdns) ·
[问题反馈](https://github.com/OVINC-CN/MosDNS/issues)

</div>

## 简介

MosDNS 通过插件和执行序列组合 DNS 处理流程，可用于构建灵活的本地或服务器端 DNS 服务。它支持按域名、IP、查询类型等条件匹配请求，并按需完成转发、缓存、重定向和响应处理。

## 特性

- 插件化架构，可组合和复用 DNS 处理逻辑
- 支持 UDP、TCP、DoT、DoH 和 DoQ 等上游协议
- 支持缓存、Hosts、ECS、TTL、限速及 IPSet/NFTSet 等常用能力
- 提供 HTTP API、Prometheus 指标和 pprof 调试端点
- 单文件静态编译，适合容器及轻量化部署
- 提供 `linux/amd64`、`linux/arm64` 多架构容器镜像

## 快速开始

### 1. 创建配置

保存以下内容为 `config.yaml`：

```yaml
log:
  level: info

plugins:
  - tag: forward_google
    type: forward
    args:
      upstreams:
        - addr: https://8.8.8.8/dns-query

  - tag: udp_server
    type: udp_server
    args:
      entry: forward_google
      listen: 0.0.0.0:53

  - tag: tcp_server
    type: tcp_server
    args:
      entry: forward_google
      listen: 0.0.0.0:53
```

### 2. 使用 Docker 运行

从 [Packages](https://github.com/OVINC-CN/MosDNS/pkgs/container/mosdns) 选择版本标签，并替换命令中的 `<version>`：

```bash
docker run -d \
  --name mosdns \
  --restart unless-stopped \
  -p 53:53/udp \
  -p 53:53/tcp \
  -v "$(pwd)/config.yaml:/etc/mosdns/config.yaml:ro" \
  ghcr.io/ovinc-cn/mosdns:<version>
```

查看运行日志：

```bash
docker logs -f mosdns
```

## 从源码构建

需要 Go 1.25 或更高版本：

```bash
git clone https://github.com/OVINC-CN/MosDNS.git
cd MosDNS
go build -trimpath -o mosdns .
```

生成配置模板并启动：

```bash
./mosdns config gen config.yaml
./mosdns start -c config.yaml
```

## 文档

- [完整使用文档与配置教程](https://irine-sistiana.gitbook.io/mosdns-wiki/)
- [容器镜像及可用版本](https://github.com/OVINC-CN/MosDNS/pkgs/container/mosdns)
- [GitHub Issues](https://github.com/OVINC-CN/MosDNS/issues)

## 许可证

本项目基于 [GNU General Public License v3.0](LICENSE) 发布。
