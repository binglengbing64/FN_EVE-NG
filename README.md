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

<img width="1920" height="1028" alt="PixPin_2026-05-26_22-11-16" src="https://github.com/user-attachments/assets/7ac986c5-57c2-4b8a-a112-6dbc65d13a4b" />


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
- `/root/fn-system-status.md` - 原始系统状态报告
- `/opt/unetlab/html/includes/Slim/Http/Util.php` - PHP 8 兼容修复
- `/opt/unetlab/html/html5/api/tokens.php` - Guacamole Mock API
- `/opt/unetlab/addons/iol/bin/iourc` - IOL 许可证文件
- `/etc/systemd/system/eve-ng.service` - 开机自启动服务
