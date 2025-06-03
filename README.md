# RustDesk Server Docker 部署文档

> 📌 本项目用于通过 Docker Compose 快速部署 RustDesk 自建中继和 ID 注册服务器。  
> 支持平台：Linux（推荐 Ubuntu 18+/Debian/CentOS 7+）。

---

## 📁 项目结构

```
rustdesk-server-docker/
├── docker-compose.yml   # Docker Compose 文件
└── data/                # RustDesk 配置/密钥目录（自动生成）
```

---

## 🚀 快速部署步骤

### 1. 克隆本项目

```bash
git clone https://github.com/Besson1412/rustdesk-server-docker.git
cd rustdesk-server-docker
```

> 📝 请替换为你自己的 GitHub 地址

---

### 2. 启动容器

```bash
docker compose up -d
```

启动后会自动创建 `data/` 目录，内部会生成密钥文件：

- `id_ed25519`（私钥）
- `id_ed25519.pub`（公钥，用于客户端配置）

---

### 3. 开放防火墙端口

请确保服务器已开放以下端口（如使用云服务器安全组，请在后台配置）：

| 端口   | 协议 | 用途             |
|--------|------|------------------|
| 21115  | TCP  | NAT类型探测       |
| 21116  | TCP/UDP | 客户端注册和连接 |
| 21117  | TCP  | 中继转发服务       |

> ❗ 如果需要支持网页客户端，还需开放 21118(TCP) 和 21119(TCP)

---

### 4. 客户端设置 ID/中继服务器地址

在 RustDesk 客户端：
- 打开主界面 → 菜单 → “ID/中继服务器” 设置
- 输入你的服务器 IP 或域名，例如：

```
id.example.com
或
id.example.com:21116
```

- 不需要填写中继地址，RustDesk 会自动推导。

---

## 🔐 查看公钥（用于客户端手动配置）

```bash
cat data/id_ed25519.pub
```

> 如果客户端无法加密连接，可以将该内容复制粘贴到客户端配置的 “Key” 项。

---

## 🔁 重置 Key

如需更换密钥，删除原始文件并重启容器：

```bash
rm -f data/id_ed25519*
docker compose restart
```

---

## 🧯 停止与清理

```bash
docker compose down
```

（不加 `-v` 参数不会删除挂载目录下的数据）

---

## 💡 其他说明

- 本项目未使用 `--net=host`，保持 Docker 默认桥接网络
- 若你需要通过域名访问，建议配合 Nginx 做 TCP 端口转发（非 HTTP Proxy）

---

## 🛠️ 技术参考

- 官方文档：https://rustdesk.com/docs/zh/self-host/install/
- Docker 镜像：https://hub.docker.com/r/rustdesk/rustdesk-server
- GitHub 源码：https://github.com/rustdesk/rustdesk-server

---
