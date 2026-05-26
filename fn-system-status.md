# 飞牛 OS (FnOS) 系统功能状态报告

## 系统概述

- 操作系统：Debian GNU/Linux 12
- 飞牛OS系统版本：fnOS 1.1.18
- 当前添加：EVE-NG 5.0.1-24 (网络仿真平台)
- 服务器 IP：192.168.48.192

## 服务端口

| 服务 | 端口 | 状态 |
|------|------|------|
| Apache2 (EVE-NG) | 80 | ✓ 运行中 |
| trim_nginx (FnOS) | 5666, 5667 | ✓ 运行中 |

## 功能状态

### ✓ 正常运行

| 功能模块 | 服务名称 | 状态 |
|----------|----------|------|
| 文件共享 | smbd.service | ✓ 运行中 |
| NFS 服务 | nfs-mountd.service | ✓ 运行中 |
| Web 服务 | trim_nginx.service | ✓ 运行中 |
| 媒体服务 | mediasrv.service | ✓ 运行中 |
| 云存储 | cloud_storage_dav.service | ✓ 运行中 |
| 下载中心 | dlcenter.service | ✓ 运行中 |
| 备份服务 | backup_service.service | ✓ 运行中 |
| 用户服务 | usersrv.service | ✓ 运行中 |
| 应用中心 | trim_app_center.service | ✓ 运行中 |
| 文件存储 | filestor_service.service | ✓ 运行中 |
| 文件搜索 | finder_service.service | ✓ 运行中 |

### ✗ 受影响

| 功能模块 | 说明 |
|----------|------|
| Docker | 已移除 docker-ce (与 EVE-NG 冲突) |

## 访问地址

- **飞牛 OS 管理界面**：http://192.168.48.192:5666 或 http://192.168.48.192:5667
- **EVE-NG**：http://192.168.48.192 (端口 80)

## 共存说明

Apache2 (EVE-NG) 使用端口 80，trim_nginx (飞牛 OS) 使用端口 8000/8001，两个 Web 服务运行在不同端口，互不冲突。

## Docker 说明

由于 EVE-NG 官方包声明与 docker-ce 冲突，docker-ce 已被移除以安装 EVE-NG。

- 原 Docker 容器可能无法运行
- 如需 Docker 功能，建议：
  1. 在另一台机器上运行 Docker
  2. 或使用飞牛 OS 的虚拟机功能

## 关键服务列表

```
avahi.service                    - trim avahi service
backup_service.service           - Basic Backup Service
cloud_storage_dav.service        - Trim Cloud Storage Dav
dlcenter.service                 - trim download center Service
dockermgr.service                - trim docker management service
dsmgr.service                    - trim docker stream service
filestor_service.service         - trim file storage service
finder_service.service           - trim file finder service
mediasrv.service                 - Mediasrv Service
network_service.service         - trim network service
smbd.service                     - Samba SMB/CIFS daemon
trim_main.service                - trim main service
trim_nginx.service               - trim nginx service
usersrv.service                  - trim user service
```

## 验证命令

```bash
# 检查所有 trim 服务状态
systemctl list-units --type=service --state=running | grep trim

# 检查端口占用
ss -tlnp | grep -E "80|8000|8001|5666"

# 检查原系统 Nginx 状态
systemctl status trim_nginx

# 检查 EVE-NG 状态
systemctl status apache2
