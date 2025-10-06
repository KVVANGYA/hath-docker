# hath-docker

使用 Disappear9 的 Docker 镜像快速部署 H@H 客户端

## 项目信息

- **Docker 镜像**: `ghcr.io/disappear9/hentaiathome:latest`
- **H@H 版本**: 1.6.3+
- **支持平台**: AMD64, ARM64, ARM
- **镜像下载量**: 500K+

---

## 快速开始

### 前置要求

✅ 已安装 Docker 和 Docker Compose  
✅ 已在 [E-Hentai H@H 页面](https://e-hentai.org/hentaiathome.php) 注册并获得 Client ID 和 Key  
✅ 服务器防火墙开放了设定的端口（默认 8443）  
✅ 服务器有足够的硬盘空间（建议 20GB 以上）

### 30秒快速部署

```
# 1. 创建项目目录
mkdir ~/hath && cd ~/hath

# 2. 创建配置文件
cat > docker-compose.yml << 'EOF'
version: '3.8'

services:
  hath:
    image: ghcr.io/disappear9/hentaiathome:latest
    container_name: hath
    restart: unless-stopped
    ports:
      - "8443:8443"
    volumes:
      - ./data:/hath/data
      - ./download:/hath/download
      - ./cache:/hath/cache
      - ./log:/hath/log
      - ./tmp:/hath/tmp
    environment:
      - HatH_KEY=你的ID-你的KEY
      - TZ=America/New_York
EOF

# 3. 创建必要目录
mkdir -p data download cache log tmp

# 4. 启动容器
docker-compose up -d

# 5. 查看日志
docker-compose logs -f hath
```

---

## 详细配置说明

### 1. 获取认证信息

访问 https://e-hentai.org/hentaiathome.php

填写申请表单：
- **Max Outgoing Speed**: 根据你的测速结果填写（单位：KB/s）
- **Max Disk Cache**: 根据硬盘空间填写（单位：GB，建议 20-100）
- **Network Test**: 提供 speedtest.net 的测速链接

提交后获得：
- **Client ID**: 例如 `12345`
- **Client Key**: 例如 `LBVANWQ7bSsKuLZVpeoG`

### 2. 配置 docker-compose.yml

#### 基础配置（推荐）

```
version: '3.8'

services:
  hath:
    image: ghcr.io/disappear9/hentaiathome:latest
    container_name: hath
    restart: unless-stopped
    ports:
      - "8443:8443"  # 改为你在 E-Hentai 设定的端口
    volumes:
      - ./data:/hath/data
      - ./download:/hath/download
      - ./cache:/hath/cache
      - ./log:/hath/log
      - ./tmp:/hath/tmp
    environment:
      # 格式: ID-KEY (中间用减号连接)
      - HatH_KEY=12345-LBVANWQ7bSsKuLZVpeoG
      - TZ=America/New_York  # 时区设置
```

#### 进阶配置（带代理）

```
version: '3.8'

services:
  hath:
    image: ghcr.io/disappear9/hentaiathome:latest
    container_name: hath
    restart: unless-stopped
    ports:
      - "8443:8443"
    volumes:
      - ./data:/hath/data
      - ./download:/hath/download
      - ./cache:/hath/cache
      - ./log:/hath/log
      - ./tmp:/hath/tmp
    environment:
      - HatH_KEY=12345-LBVANWQ7bSsKuLZVpeoG
      - TZ=America/New_York
      # 代理设置（可选）
      - HTTP_PROXY=http://proxy.example.com:8080
      - HTTPS_PROXY=http://proxy.example.com:8080
```

### 3. 环境变量说明

| 变量名 | 必需 | 说明 | 示例 |
|--------|------|------|------|
| `HatH_KEY` | ✅ | 认证密钥，格式为 `ID-KEY` | `12345-abcdef123456` |
| `TZ` | ⭐ | 服务器时区 | `America/New_York` |
| `HTTP_PROXY` | ❌ | HTTP 代理地址 | `http://proxy:8080` |
| `HTTPS_PROXY` | ❌ | HTTPS 代理地址 | `http://proxy:8080` |

### 4. 目录说明

```
~/hath/
├── docker-compose.yml    # Docker Compose 配置文件
├── data/                 # H@H 客户端数据（包含 client_login）
├── download/             # 临时下载目录
├── cache/               # 图片缓存目录（占用空间最大）
├── log/                 # 日志文件
└── tmp/                 # 临时文件
```

---

## 时区设置参考

| 服务器位置 | TZ 值 |
|-----------|-------|
| 🇺🇸 美国东部（纽约、新泽西） | `America/New_York` |
| 🇺🇸 美国中部（芝加哥、达拉斯） | `America/Chicago` |
| 🇺🇸 美国西部（洛杉矶） | `America/Los_Angeles` |
| 🇬🇧 英国（伦敦） | `Europe/London` |
| 🇫🇷 法国（巴黎） | `Europe/Paris` |
| 🇩🇪 德国（法兰克福） | `Europe/Berlin` |
| 🇳🇱 荷兰（阿姆斯特丹） | `Europe/Amsterdam` |
| 🇯🇵 日本（东京） | `Asia/Tokyo` |
| 🇭🇰 香港 | `Asia/Hong_Kong` |
| 🇹🇼 台湾 | `Asia/Taipei` |
| 🇸🇬 新加坡 | `Asia/Singapore` |

---

## 部署步骤

### 步骤 1：创建项目目录

```
mkdir -p ~/hath
cd ~/hath
```

### 步骤 2：创建 docker-compose.yml

```
nano docker-compose.yml
```

粘贴配置内容（参考上面的配置示例），记得修改：
- `HatH_KEY` 为你的实际 ID 和 Key
- `ports` 为你在 E-Hentai 设定的端口
- `TZ` 为你的服务器时区

保存退出（Ctrl+O，回车，Ctrl+X）

### 步骤 3：创建目录结构

```
mkdir -p data download cache log tmp
```

### 步骤 4：启动容器

```
docker-compose up -d
```

### 步骤 5：查看日志

```
docker-compose logs -f hath
```

按 `Ctrl+C` 退出日志查看（不会停止容器）

---

## 验证部署成功

### ✅ 检查日志输出

成功启动应该看到：

```
hath  | [info] Hentai@Home 1.6.3 starting up
hath  | [info] Loaded login settings from client_login
hath  | [info] Starting RPC server (你的IP:8443)
hath  | [info] Connecting to RPC server...
hath  | [info] Successfully connected to RPC server
hath  | [info] Client 12345 initialized successfully
hath  | [info] Fetching file list...
```

### ✅ 检查容器状态

```
docker ps | grep hath
```

应该显示 `Up` 状态：
```
CONTAINER ID   IMAGE                                      STATUS
abc123def456   ghcr.io/disappear9/hentaiathome:latest    Up 2 minutes
```

### ✅ 检查端口监听

```
netstat -tulpn | grep 8443
```

或

```
ss -tulpn | grep 8443
```

### ✅ 检查 E-Hentai 后台

访问：https://e-hentai.org/hentaiathome.php

确认显示：
- ✅ **Status**: Online（绿色）
- ✅ **Last Seen**: 几秒前
- ✅ **Files Cached**: 开始增长
- ✅ **Trust**: 逐渐累积

---

## 日常管理

### 查看实时日志

```
docker-compose logs -f hath
```

### 查看最近日志（最后 100 行）

```
docker-compose logs --tail=100 hath
```

### 重启容器

```
docker-compose restart
```

### 停止容器

```
docker-compose stop
```

### 启动容器

```
docker-compose start
```

### 停止并删除容器

```
docker-compose down
```

### 更新镜像

```
# 拉取最新镜像
docker-compose pull

# 重新创建容器
docker-compose up -d

# 清理旧镜像（可选）
docker image prune -f
```

### 查看缓存大小

```
du -sh cache/
```

### 查看文件数量

```
find cache/ -type f | wc -l
```

### 备份配置

```
tar -czf hath-backup-$(date +%Y%m%d).tar.gz docker-compose.yml data/
```

---

## 故障排查

### ❌ 问题：容器不断重启

**症状**：
```
docker ps -a
# 显示 Restarting 状态
```

**解决方法**：

1. 查看详细错误日志
```
docker-compose logs hath
```

2. 检查 HatH_KEY 格式是否正确
```
cat docker-compose.yml | grep HatH_KEY
# 格式应为: HatH_KEY=12345-abcdefg123456
# 不能有空格，ID 和 KEY 用减号连接
```

3. 检查端口是否被占用
```
netstat -tulpn | grep 8443
# 如果已被占用，修改 docker-compose.yml 中的端口
```

### ❌ 问题：无法连接到 RPC 服务器

**症状**：
```
[error] Failed to connect to RPC server
```

**解决方法**：

1. **检查防火墙**
```
# Ubuntu/Debian
sudo ufw allow 8443/tcp
sudo ufw status

# CentOS/RHEL
sudo firewall-cmd --add-port=8443/tcp --permanent
sudo firewall-cmd --reload
```

2. **检查云服务商安全组**  
   确保在云服务商控制面板开放了 8443 端口

3. **验证端口可访问性**  
   从外部测试：`telnet 你的公网IP 8443`

### ❌ 问题：E-Hentai 显示 Offline

**可能原因**：
- 容器未正常运行
- 防火墙阻止连接
- 端口配置不一致
- 网络问题

**解决步骤**：

```
# 1. 确认容器运行
docker ps | grep hath

# 2. 查看日志
docker-compose logs --tail=50 hath

# 3. 测试端口
netstat -tulpn | grep 8443

# 4. 重启容器
docker-compose restart

# 5. 等待 1-2 分钟后刷新 E-Hentai 页面
```

### ❌ 问题：Trust 分数不增长

**原因**：需要时间累积，通常需要：
- 24 小时稳定运行后开始增长
- 7 天达到 100 Trust
- 持续稳定运行很重要

**建议**：
- 保持 24/7 运行
- 不要频繁重启
- 确保足够的磁盘空间

### ❌ 问题：磁盘空间不足

**查看磁盘使用**：
```
df -h
du -sh ~/hath/cache/
```

**清理空间**：
```
# 清理 Docker 未使用的资源
docker system prune -a

# 减少 H@H 缓存大小
# 需要在 E-Hentai 后台修改 Max Disk Cache 配置
```

### ❌ 问题：忘记了 Client ID 或 Key

**解决方法**：

1. 访问 https://e-hentai.org/hentaiathome.php
2. 在 "Manage Clients" 部分查看现有客户端
3. 如果找不到，需要重新注册一个新的客户端

---

## 性能优化

### 硬件配置建议

| 配置项 | 最低要求 | 推荐配置 | 高性能配置 |
|--------|----------|----------|-----------|
| **CPU** | 1核 | 2核 | 4核+ |
| **内存** | 512 MB | 2 GB | 4 GB+ |
| **硬盘** | 20 GB | 100 GB | 500 GB - 2 TB |
| **带宽** | 2.5 MB/s | 50 MB/s | 100 MB/s+ |
| **上传** | 2.5 MB/s | 50 MB/s | 100 MB/s+ |

### 带宽要求

```
最低要求：2500 KB/s (20 Mbps)
推荐配置：50000 KB/s (400 Mbps)
理想配置：100000+ KB/s (800+ Mbps)
```

### 收益优化

**提高 Hath 收益的方法**：

1. **增加存储空间** → 获得更多 Static Ranges
2. **提高带宽** → 增加 Hit Rate
3. **保持稳定运行** → 提高 Trust 分数
4. **选择合适地理位置** → 靠近用户群体（欧美）

**收益公式**：
```
每日 Hath = 1 + 0.15 × hitrate + 0.01 × static_ranges
```

---

## 监控和统计

### 实时监控脚本

创建监控脚本：

```
cat > ~/monitor-hath.sh << 'EOF'
#!/bin/bash
echo "=== H@H 运行状态 ==="
echo
echo "容器状态:"
docker ps | grep hath
echo
echo "磁盘使用:"
du -sh ~/hath/cache/
echo
echo "文件数量:"
find ~/hath/cache/ -type f | wc -l
echo
echo "最近日志:"
docker-compose -f ~/hath/docker-compose.yml logs --tail=5 hath
EOF

chmod +x ~/monitor-hath.sh
```

运行监控：
```
~/monitor-hath.sh
```

### 设置定时任务

```
# 编辑 crontab
crontab -e

# 添加每小时检查一次
0 * * * * cd ~/hath && docker-compose restart > /dev/null 2>&1
```

---

## 迁移和备份

### 备份数据

```
# 完整备份
tar -czf hath-backup-$(date +%Y%m%d).tar.gz ~/hath/

# 仅备份配置和数据
tar -czf hath-config-$(date +%Y%m%d).tar.gz ~/hath/docker-compose.yml ~/hath/data/
```

### 恢复数据

```
# 解压备份
tar -xzf hath-backup-20251006.tar.gz

# 启动容器
cd ~/hath
docker-compose up -d
```

### 迁移到新服务器

```
# 旧服务器：停止并备份
cd ~/hath
docker-compose down
tar -czf hath-migrate.tar.gz ~/hath/

# 传输到新服务器
scp hath-migrate.tar.gz user@new-server:/root/

# 新服务器：解压并启动
tar -xzf hath-migrate.tar.gz
cd ~/hath
docker-compose up -d

# 在 E-Hentai 后台更新 IP 地址
```

---

## 安全建议

✅ 定期更新镜像  
✅ 使用防火墙限制访问  
✅ 不要在公共环境暴露日志  
✅ 定期备份配置文件  
✅ 监控异常流量  

---

## 常见问题 FAQ

**Q: 为什么选择 Disappear9 的镜像？**  
A: 经过充分测试（500K+ 下载量），支持多平台，自动处理配置，持续维护更新。

**Q: 可以同时运行多个客户端吗？**  
A: 可以，但每个客户端需要独立的 Client ID/Key 和端口。

**Q: 需要多久才能获得收益？**  
A: 通常 24 小时后开始产生稳定收益，7 天后达到最佳状态。

**Q: 如何提高收益？**  
A: 增加存储空间、提高带宽、保持 24/7 稳定运行。

**Q: 可以随时停止吗？**  
A: 可以，但频繁停止会降低 Trust 分数，影响长期收益。

**Q: 需要什么样的网络环境？**  
A: 最低 20 Mbps 上传和下载，推荐 400+ Mbps，需要公网 IP。

**Q: Docker 版本和原生版本有区别吗？**  
A: 功能完全相同，Docker 版本更易于部署和管理。

---

## 相关资源

- 📦 **Docker Hub**: https://github.com/Disappear9/H-at-H-docker
- 🌐 **E-Hentai H@H**: https://e-hentai.org/hentaiathome.php
- 📚 **H@H Wiki**: https://ehwiki.org/wiki/Hentai@Home
- 💬 **E-Hentai 论坛**: https://forums.e-hentai.org/

---

## 许可证

本文档遵循 MIT License。

H@H 客户端版权归 E-Hentai.org 所有。

---

## 更新日志

- **2025-10-06**: 初始版本，基于 Disappear9/hentaiathome:latest

---

**祝您运行愉快！Your business is appreciated. 🎉**

