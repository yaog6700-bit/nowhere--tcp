# Nowhere Portal Server

基于 QUIC 协议的高性能网络代理服务端，一键部署，自动配置。

---

## 环境要求

- 系统：Linux x86_64
- 权限：root
- 端口：任意一个 UDP 端口（默认 11111）

---

## 一键安装

```bash
curl -sL https://raw.githubusercontent.com/yaog6700-bit/nowhere--tcp/main/install.sh | bash

```

安装过程中会交互式输入以下参数（全部支持回车跳过使用默认值或自动生成）：

| 参数 | 说明 | 默认 |
|------|------|------|
| 监听端口 | UDP 端口号 | `11111` |
| 认证 Key | 连接密钥 | 自动生成 |
| etar | 带宽限制（Mbps） | `1000` |
| Spec | 协议规范字符串 | 自动生成 |
| ALPN | TLS ALPN 标识 | 自动生成 |
| 节点名称 | 连接串备注名 | `My-Node` |

安装完成后会输出连接串，发给客户端即可使用：

```
nowhere://your-key@your-ip:your-port?spec=xxx&alpn=xxx#My-Node
```

---

## 一键卸载

```bash
bash <(curl -sL https://raw.githubusercontent.com/yaog6700-bit/nowhere--tcp/main/install.sh) uninstall
```

卸载会移除：服务进程、systemd 配置、二进制文件，并询问是否删除日志。

---

## 服务管理

```bash
# 查看实时日志
tail -f /var/log/nowhere.log

# 重启服务
systemctl restart nowhere

# 停止服务
systemctl stop nowhere

# 查看服务状态
systemctl status nowhere
```

---

## 高级配置

支持通过环境变量调整服务行为，在 `/etc/systemd/system/nowhere.service` 的 `[Service]` 段添加：

```ini
Environment=NOW_TCP_DIAL_TIMEOUT=10s
Environment=NOW_UDP_IDLE_TIMEOUT=60s
Environment=NOW_HANDSHAKE_TIMEOUT=5s
Environment=NOW_QUIC_MAX_STREAMS=256
Environment=NOW_TCP_DATA_BUF_SIZE=65536
Environment=NOW_UDP_DATA_BUF_SIZE=65536
```

修改后执行 `systemctl daemon-reload && systemctl restart nowhere` 生效。

---

## 常见问题

**日志中出现大量 `dial timeout`？**

通常由以下原因导致，安装脚本已自动检测并修复：
- 服务器配置了 IPv6 但出网不通 → 自动禁用 IPv6
- Docker 遗留路由干扰出站绑定 → 自动清理（不影响运行中的容器）

**手动修复 IPv6 问题：**

```bash
sysctl -w net.ipv6.conf.all.disable_ipv6=1
sysctl -w net.ipv6.conf.default.disable_ipv6=1
echo "net.ipv6.conf.all.disable_ipv6 = 1" >> /etc/sysctl.conf
echo "net.ipv6.conf.default.disable_ipv6 = 1" >> /etc/sysctl.conf
systemctl restart nowhere
```

---

## 客户端

IOS&MAC:使用 [Anywhere](https://apps.apple.com/app/anywhere) iOS 客户端连接，将安装完成后输出的连接串导入即可。
Windows :使用 Swell Proxy for Windows 
---

## License

MIT
