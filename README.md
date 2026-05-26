# 飞牛_EVE-NG
#在飞牛os的底层上原生安装EVE-NG模拟器

## 系统环境
- 操作系统: Debian GNU/Linux 12 (bookworm)
- 原系统: 飞牛OS (fnOS 1.1.18)
- EVE-NG 版本: 5.0.1-24
- IP: 192.168.48.192
- EVE登录: admin / unl
- Web: http://192.168.48.192/
- 飞牛OS webUI: http://192.168.48.192:5666/
- 飞牛OS登录: admin / Qwer1234.

<img width="1920" height="1030" alt="PixPin_2026-05-26_22-09-18" src="https://github.com/user-attachments/assets/2b1eb072-2610-43a1-b841-55e3c6af9699" />

<img width="1920" height="1021" alt="PixPin_2026-05-26_22-09-31" src="https://github.com/user-attachments/assets/8d16b4aa-a486-422b-a660-d69f19613ac0" />

## 安装的组件

| 组件 | 版本 | 状态 |
|------|------|------|
| eve-ng | 5.0.1-24 | ✓ 已安装 |
| eve-ng-qemu | 5.0.1-1 | ✓ 已安装 |
| eve-ng-dynamips | 6.0.1-5 | ✓ 已安装 |
| eve-ng-schema | 6.0.1-0 | ✓ 已安装 |
| eve-ng-vpcs | 6.1-eve-ng | ✓ 已安装 |
| eve-ng-pro-guacamole | 5.0.1-1 | ✓ 已安装(Mock API) |
| PHP YAML | 2.2.2 | ✓ 已安装 |
| MariaDB | (替代MySQL) | ✓ 已安装 |
| Slim Framework | - | ✓ 已修复(PHP 8兼容) |

## 已解决的问题

### 1. 添加 EVE-NG 官方仓库
- 添加 GPG 密钥并配置 jammy 仓库
- `apt-get update` 完成

### 2. 安装 MariaDB (替代 MySQL)
- `apt-get install -y mariadb-server`
- 创建了 `mysql-server`, `python2`, `libjpeg8`, `ipxe-qemu-256k-compat-efi-roms`, `lib32gcc1` 等假包

### 3. 安装 eve-ng 核心依赖
- 使用 `apt-get install` 安装了 apache2, php, lib32gcc-s1, libc6-i386 等依赖
- 部分包使用 `--force-depends` 和 `--force-all` 强制安装
- 移除了 docker-ce 以解决冲突

### 4. PHP YAML 扩展
- `apt-get download php8.2-yaml` + `dpkg --force-depends -i`

### 5. Slim 框架 PHP 8 兼容性
- `sed -i 's/get_magic_quotes_gpc()/false/' /opt/unetlab/html/includes/Slim/Http/Util.php`

### 6. Guacamole Mock API
- 创建 `/opt/unetlab/html/html5/api/tokens.php` 提供 mock 认证
- 修改 Apache 配置，`<Location /html5/> ProxyPass !`
- `sed -i "s|http://127.0.0.1/html5/api/tokens|http://127.0.0.1/html5/api/tokens/|" /opt/unetlab/html/includes/functions.php`

### 7. 数据库初始化
- `mysql -u root eve_ng_db < /opt/unetlab/schema/unetlab-001-create-schema.sql`
- 插入 admin 用户 (密码: unl)

### 8. IOL 节点修复
- **libcrypto.so.1.1**: 添加 Debian 11 bullseye 仓库，安装 libssl1.1
- **AF_UNIX socket**: `chattr -i /opt/unetlab/jail`，创建 jail/0 和 /var/run/unetlab 目录
- **IOU 许可证**: 生成 iourc 文件，但最初使用了主机名 `fons-mini`

### 9. IOL 主机名/许可证问题 (最终修复)
- **发现**: hostname 从 `fons-mini` 变为 `fnos-EVE`，iourc 许可证仍用旧主机名
- **修复**: 重新用 `python3 CiscoIOUKeygen.py` 生成新密钥
- **更新**: iourc 文件改为 `fnos-EVE = 8dfdbb08089e2198;`
- **重启**: 使用 `unl_wrapper -a stop/wipe/start -T 0 -F /opt/unetlab/labs/1.unl -D 1`
- **验证**: IOL 进程正常运行，wrapper.txt 为空（无错误）

### 10. 开机自启动
- 创建 `/etc/systemd/system/eve-ng.service`
- `systemctl enable eve-ng.service`

### 11. QEMU 节点修复 (H3C S9850 等)
- **libnettle.so.7**: 从 Ubuntu 20.04 (focal) 下载并安装 `libnettle7_3.5.1`（提供 `NETTLE_7` 符号）
- **libbrlapi.so.0.7**: 从 focal 下载并安装 `libbrlapi0.7`
- **libjpeg.so.8**: 从 focal 下载 `libjpeg-turbo8`，提取 `libjpeg.so.8` 到 `/opt/qemu/lib/`
- 添加 `/opt/qemu/lib` 到 `/etc/ld.so.conf.d/qemu.conf` 并运行 `ldconfig`
- **验证**: IOL 节点和 QEMU 节点 (H3C S9850) 均能正常启动和连接

## 已解决的问题

- IOL 节点: libcrypto.so.1.1 / AF_UNIX socket / 许可证 → 全部修复
- QEMU 节点: libnettle.so.7 / libbrlapi.so.0.7 / libjpeg.so.8 → 全部修复
- H3C S9850 非官方镜像 → 正常运行，console 端口 32770

### 已知限制
- Guacamole 远程桌面服务不可用 (使用 Mock API)
- 自定义 EVE-NG 内核不可用
- VM console 连接需要完整 Guacamole

## 原始飞牛OS 系统状态
- fnOS web 在 http://192.168.48.192:5666 或 5667
- 大部分原始服务仍在运行（除 docker-ce 被移除）
- 详情见 /root/fn-system-status.md

## 关键文件
- `/root/eve-ng-install-guide.md` - 完整安装指南
- `/root/fn-system-status.md` - 原始系统状态报告
- `/opt/unetlab/html/includes/Slim/Http/Util.php` - PHP 8 兼容修复
- `/opt/unetlab/html/html5/api/tokens.php` - Guacamole Mock API
- `/opt/unetlab/addons/iol/bin/iourc` - IOL 许可证文件
- `/etc/systemd/system/eve-ng.service` - 开机自启动服务
