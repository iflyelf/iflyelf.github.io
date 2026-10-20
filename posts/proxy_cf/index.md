# 多功能加速代理 - Cloudflare Workers


一个基于 Cloudflare Workers 的多功能加速代理，支持 GitHub、Docker、SourceForge、Google Sheets 等资源的加速访问。只需要一个域名和一个 Worker，就能把这些常用资源全部代理起来。

<!--more-->

## 项目简介

- 在线演示：<https://proxy.xiaonuo.live>
- 项目地址：<https://github.com/iflyelf/proxy-cf>

早期的 [cloudflare-docker-proxy](https://github.com/iflyelf/cloudflare-docker-proxy) 只能代理 Docker 镜像，而且每个镜像仓库都要单独配置一个子域名。现在的 `proxy-cf` 做了整合：

- **单域名**：所有容器注册表通过路径前缀区分，不用再为每个仓库配置子域名
- **多功能**：GitHub、Docker、SourceForge、Google Sheets、普通直链一并支持
- **零成本**：运行在 Cloudflare Workers 免费额度内，无需服务器
- **可视化**：主页自带加速链接和 Git Clone 命令生成器

## 功能特性

### GitHub 加速

- Raw 文件加速
- Release 下载加速
- 仓库源码包（Archive）下载加速
- Git Clone、Pull、Fetch 等完整 Git 操作
- 完整的 CORS 支持

### Docker 镜像加速

支持主流容器注册表，通过路径前缀访问：

| 注册表 | 路径前缀 | 拉取示例 |
|--------|---------|----------|
| Docker Hub | 默认（无需前缀） | 配置 `registry-mirrors` 后 `docker pull nginx:alpine` |
| GitHub Packages | `/ghcr.io` | `docker pull proxy.xiaonuo.live/ghcr.io/owner/image:tag` |
| Google GCR | `/gcr.io` | `docker pull proxy.xiaonuo.live/gcr.io/project/image:tag` |
| Kubernetes | `/registry.k8s.io` | `docker pull proxy.xiaonuo.live/registry.k8s.io/pause:3.9` |
| Kubernetes（旧） | `/k8s.gcr.io` | `docker pull proxy.xiaonuo.live/k8s.gcr.io/pause:3.5` |
| Red Hat Quay | `/quay.io` | `docker pull proxy.xiaonuo.live/quay.io/prometheus/node-exporter:latest` |
| Microsoft MCR | `/mcr.microsoft.com` | `docker pull proxy.xiaonuo.live/mcr.microsoft.com/dotnet/runtime:7.0-alpine` |
| NVIDIA NGC | `/nvcr.io` | `docker pull proxy.xiaonuo.live/nvcr.io/nvidia/cuda:11.8.0-base-ubuntu22.04` |
| AWS ECR Public | `/public.ecr.aws` | `docker pull proxy.xiaonuo.live/public.ecr.aws/amazonlinux/amazonlinux:latest` |
| Cloudsmith | `/docker.cloudsmith.io` | `docker pull proxy.xiaonuo.live/docker.cloudsmith.io/org/repo/image:tag` |

特性：

- 完整的 Docker Registry API v2 支持
- 自动重写认证头 `WWW-Authenticate` 和重定向地址 `Location`，避免连接被重置
- 支持多架构镜像
- 单域名访问所有注册表

### 其他功能

- **SourceForge 加速**：文件下载加速
- **Google Sheets 代理**：无需 API Key 读取表格数据
- **直链下载加速**：任意 HTTP/HTTPS 文件，支持 Range 断点续传

## 准备工作

- 一个域名，推荐在 [namesilo](https://www.namesilo.com) 注册
- 一个 [Cloudflare](https://www.cloudflare.com) 账号，域名需托管在 Cloudflare
- （可选）[GitHub](https://github.com) 账号，用于 Fork 项目

## 部署

### Cloudflare Dashboard 部署（推荐）

1. 登录 [Cloudflare Dashboard](https://dash.cloudflare.com/)
2. 进入 **Workers & Pages** → **Create Application** → **Create Worker**
3. 点击 **Edit Code**，删除默认代码
4. 复制 [worker.js](https://github.com/iflyelf/proxy-cf/blob/main/worker.js) 的全部内容，粘贴到编辑器
5. 修改 `CONFIG` 中的域名为自己的域名：

```javascript
const CONFIG = {
  domain: 'proxy.xiaonuo.live',  // 改为你的域名

  // Docker 镜像仓库路径前缀映射
  dockerRegistries: {
    'docker.io': 'https://docker.1panel.live',
    'registry-1.docker.io': 'https://docker.1panel.live',
    'ghcr.io': 'https://ghcr.io',
    'gcr.io': 'https://gcr.io',
    'k8s.gcr.io': 'https://k8s.gcr.io',
    'registry.k8s.io': 'https://registry.k8s.io',
    'quay.io': 'https://quay.io',
    'mcr.microsoft.com': 'https://mcr.microsoft.com',
    'nvcr.io': 'https://nvcr.io',
    'public.ecr.aws': 'https://public.ecr.aws',
    'docker.cloudsmith.io': 'https://docker.cloudsmith.io',
  },

  // 默认 Docker Hub 上游
  defaultDockerUpstream: 'https://docker.1panel.live',
};
```

6. 点击 **Save and Deploy**
7. 绑定自定义域名：**Settings** → **Triggers** → **Add Custom Domain**，输入自己的域名（如 `proxy.example.com`）

> 提示：`worker.js` 文件较大（约 69KB），请确保完整复制。建议直接使用 Dashboard 的代码编辑器粘贴部署。

### 修改配置

部署完成后，主要改两个地方：

- `domain`：改成自己的自定义域名，主页展示的示例链接会同步更新
- `dockerRegistries`：按需增删容器注册表。比如想换一个 Docker Hub 上游，修改 `docker.io` 和 `defaultDockerUpstream` 即可

## 使用说明

### GitHub 文件加速

推荐使用 URL 参数格式，兼容性最好：

```bash
# Raw 文件
# 原地址
https://raw.githubusercontent.com/kubernetes/kubernetes/master/README.md

# 加速地址
https://proxy.xiaonuo.live/proxy?url=https://raw.githubusercontent.com/kubernetes/kubernetes/master/README.md

# Release 下载
# 原地址
https://github.com/junegunn/fzf/releases/download/v0.54.0/fzf-0.54.0-linux_amd64.tar.gz

# 加速地址
https://proxy.xiaonuo.live/proxy?url=https://github.com/junegunn/fzf/releases/download/v0.54.0/fzf-0.54.0-linux_amd64.tar.gz

# 仓库源码包
https://proxy.xiaonuo.live/proxy?url=https://github.com/golang/go/archive/refs/tags/go1.21.0.tar.gz
```

也可以直接把原始 URL 拼在域名后面（部分环境可能被 Cloudflare 阻止，不推荐）：

```bash
https://proxy.xiaonuo.live/https://raw.githubusercontent.com/username/repo/main/file.txt
```

主页还提供了在线生成器，输入原始链接即可得到加速地址。

### Git Clone 加速

**方式一：单次克隆，无需配置**

```bash
git clone https://proxy.xiaonuo.live/proxy?url=https://github.com/torvalds/linux.git
```

**方式二：全局配置（推荐，所有仓库自动生效）**

```bash
# 配置代理
git config --global url."https://proxy.xiaonuo.live/proxy?url=https://github.com/".insteadOf "https://github.com/"

# 验证配置
git config --get-regexp url

# 之后正常的 clone、pull、fetch 都会自动走代理
git clone https://github.com/kubernetes/kubernetes.git
git pull
git fetch

# 取消配置
git config --global --unset url."https://proxy.xiaonuo.live/proxy?url=https://github.com/".insteadOf
```

**方式三：仅对当前仓库生效**

```bash
cd your-repo
git config url."https://proxy.xiaonuo.live/proxy?url=https://github.com/".insteadOf "https://github.com/"
```

支持 `clone`、`pull`、`fetch` 等所有 Git 操作，包括 `/info/refs`、`/git-upload-pack` 等协议端点。

### Docker Hub 镜像加速

编辑 `/etc/docker/daemon.json`：

```json
{
    "registry-mirrors": ["https://proxy.xiaonuo.live"]
}
```

重启 Docker：

```bash
sudo systemctl restart docker
```

然后正常拉取镜像即可：

```bash
docker pull nginx:alpine
docker pull mysql:8.0
```

> Docker Hub 的请求会转发到 `docker.1panel.live` 上游。如果该上游不可用，修改 `CONFIG.dockerRegistries['docker.io']` 和 `defaultDockerUpstream` 即可。

### 其他容器注册表

通过路径前缀访问，Docker 会自动补上 `/v2`，所以直接按下面的格式拉取：

```bash
# Google GCR（公开镜像）
docker pull proxy.xiaonuo.live/gcr.io/google-containers/pause:3.2

# Kubernetes（公开镜像）
docker pull proxy.xiaonuo.live/registry.k8s.io/pause:3.9
docker pull proxy.xiaonuo.live/k8s.gcr.io/pause:3.5

# Quay.io（公开镜像）
docker pull proxy.xiaonuo.live/quay.io/prometheus/node-exporter:latest

# Microsoft MCR（公开镜像）
docker pull proxy.xiaonuo.live/mcr.microsoft.com/dotnet/runtime:7.0-alpine

# GitHub Packages（部分镜像需要认证）
docker pull proxy.xiaonuo.live/ghcr.io/owner/image:tag
```

对于 Kubernetes 节点上的 containerd，需要额外配置 `certs.d` 并开启 `override_path`，详见下一节。

### Containerd 配置（Kubernetes 用户）

#### 方法一：Containerd v1

编辑 `/etc/containerd/config.toml`：

```toml
[plugins."io.containerd.grpc.v1.cri".registry]
  [plugins."io.containerd.grpc.v1.cri".registry.mirrors]
    [plugins."io.containerd.grpc.v1.cri".registry.mirrors."docker.io"]
      endpoint = ["https://proxy.xiaonuo.live"]
    [plugins."io.containerd.grpc.v1.cri".registry.mirrors."gcr.io"]
      endpoint = ["https://proxy.xiaonuo.live/gcr.io"]
    [plugins."io.containerd.grpc.v1.cri".registry.mirrors."registry.k8s.io"]
      endpoint = ["https://proxy.xiaonuo.live/registry.k8s.io"]
    [plugins."io.containerd.grpc.v1.cri".registry.mirrors."k8s.gcr.io"]
      endpoint = ["https://proxy.xiaonuo.live/k8s.gcr.io"]
    [plugins."io.containerd.grpc.v1.cri".registry.mirrors."quay.io"]
      endpoint = ["https://proxy.xiaonuo.live/quay.io"]
    [plugins."io.containerd.grpc.v1.cri".registry.mirrors."ghcr.io"]
      endpoint = ["https://proxy.xiaonuo.live/ghcr.io"]
    [plugins."io.containerd.grpc.v1.cri".registry.mirrors."mcr.microsoft.com"]
      endpoint = ["https://proxy.xiaonuo.live/mcr.microsoft.com"]
    [plugins."io.containerd.grpc.v1.cri".registry.mirrors."nvcr.io"]
      endpoint = ["https://proxy.xiaonuo.live/nvcr.io"]
    [plugins."io.containerd.grpc.v1.cri".registry.mirrors."public.ecr.aws"]
      endpoint = ["https://proxy.xiaonuo.live/public.ecr.aws"]
    [plugins."io.containerd.grpc.v1.cri".registry.mirrors."docker.cloudsmith.io"]
      endpoint = ["https://proxy.xiaonuo.live/docker.cloudsmith.io"]
```

重启 containerd：

```bash
sudo systemctl restart containerd
```

#### 方法二：Containerd v2（推荐）

1. 在 `/etc/containerd/config.toml` 中启用 `certs.d` 目录：

```toml
[plugins."io.containerd.cri.v1.images".registry]
  config_path = "/etc/containerd/certs.d"
```

2. 为各注册表创建 hosts 配置：

```bash
# Docker Hub
sudo mkdir -p /etc/containerd/certs.d/docker.io
sudo tee /etc/containerd/certs.d/docker.io/hosts.toml << EOF
server = "https://docker.io"

[host."https://proxy.xiaonuo.live"]
  capabilities = ["pull", "resolve"]
  skip_verify = false
EOF

# Google GCR
sudo mkdir -p /etc/containerd/certs.d/gcr.io
sudo tee /etc/containerd/certs.d/gcr.io/hosts.toml << EOF
server = "https://gcr.io"

[host."https://proxy.xiaonuo.live/gcr.io"]
  capabilities = ["pull", "resolve"]
  override_path = true
EOF

# Kubernetes Registry
sudo mkdir -p /etc/containerd/certs.d/registry.k8s.io
sudo tee /etc/containerd/certs.d/registry.k8s.io/hosts.toml << EOF
server = "https://registry.k8s.io"

[host."https://proxy.xiaonuo.live/registry.k8s.io"]
  capabilities = ["pull", "resolve"]
  override_path = true
EOF

# Kubernetes 旧版
sudo mkdir -p /etc/containerd/certs.d/k8s.gcr.io
sudo tee /etc/containerd/certs.d/k8s.gcr.io/hosts.toml << EOF
server = "https://k8s.gcr.io"

[host."https://proxy.xiaonuo.live/k8s.gcr.io"]
  capabilities = ["pull", "resolve"]
  override_path = true
EOF
```

其余注册表按同样格式创建即可：`quay.io`、`ghcr.io`、`mcr.microsoft.com`、`nvcr.io`、`public.ecr.aws`、`docker.cloudsmith.io`。

3. 重启并测试：

```bash
sudo systemctl restart containerd

sudo crictl pull nginx:alpine
sudo crictl pull registry.k8s.io/pause:3.9
```

配置项说明：

- `server`：原始注册表地址
- `capabilities`：支持的操作（`pull`、`resolve`、`push`）
- `override_path`：保留完整路径，路径前缀代理必须开启
- `skip_verify`：是否跳过 TLS 校验，保持 `false` 更安全

### SourceForge 加速

```bash
# 原地址
https://sourceforge.net/projects/projectname/files/file.zip

# 加速地址
https://proxy.xiaonuo.live/proxy?url=https://sourceforge.net/projects/projectname/files/file.zip
```

### Google Sheets 代理

```bash
# 访问格式
https://proxy.xiaonuo.live/sheet/YOUR_SHEET_ID

# 示例
https://proxy.xiaonuo.live/sheet/1BxiMVs0XRA5nFMdKvBdBZjgmUUqptlbs74OgvE2upms
```

返回 JSON 格式数据，无需申请 API Key。注意表格需要设置为「任何人都可以查看」。

### 直链下载加速

```bash
# 方式1：/proxy?url= 格式（推荐）
https://proxy.xiaonuo.live/proxy?url=https://example.com/file.zip

# 方式2：根路径参数
https://proxy.xiaonuo.live/?url=https://example.com/file.zip

# 方式3：直接拼接（部分环境可能被阻止）
https://proxy.xiaonuo.live/https://example.com/file.zip
```

支持 Range 请求，可断点续传。

## 验证部署

部署完成后，可以用下面的命令逐项验证：

```bash
# 主页
curl -I https://proxy.xiaonuo.live/

# GitHub Raw 文件
curl -s 'https://proxy.xiaonuo.live/proxy?url=https://raw.githubusercontent.com/octocat/Hello-World/master/README'

# GitHub Release 下载
curl -I 'https://proxy.xiaonuo.live/proxy?url=https://github.com/junegunn/fzf/releases/download/v0.54.0/fzf-0.54.0-linux_amd64.tar.gz'

# Git Clone
git clone https://proxy.xiaonuo.live/proxy?url=https://github.com/octocat/Hello-World.git

# Docker Hub API
curl -s https://proxy.xiaonuo.live/v2/

# 其他注册表 API
curl -s https://proxy.xiaonuo.live/v2/ghcr.io/linuxserver/baseimage-alpine/tags/list

# Google Sheets
curl -s https://proxy.xiaonuo.live/sheet/1BxiMVs0XRA5nFMdKvBdBZjgmUUqptlbs74OgvE2upms

# 查看调试信息
curl -s https://proxy.xiaonuo.live/debug
```

推荐用公开镜像测试 Docker 拉取：

```bash
docker pull nginx:alpine
docker pull proxy.xiaonuo.live/registry.k8s.io/pause:3.9
docker pull proxy.xiaonuo.live/quay.io/prometheus/node-exporter:latest
```

## 故障排查

### Docker 拉取失败

**unauthorized 错误**

部分注册表需要先认证：

```bash
# GitHub Packages 需要 GitHub Token（read:packages 权限）
echo YOUR_GITHUB_TOKEN | docker login ghcr.io -u YOUR_USERNAME --password-stdin

# NVIDIA NGC 需要 NVIDIA 账号
docker login nvcr.io

# 然后通过代理拉取
docker pull proxy.xiaonuo.live/ghcr.io/owner/image:tag
```

代理会转发认证令牌，但个别注册表可能因域名不匹配拒绝认证，建议优先用公开镜像测试。

**连接被重置**

项目已经通过自动重写认证头和切换 Docker Hub 上游解决。若仍然出现，可尝试更换 `defaultDockerUpstream`。

**daemon.json 不生效**

```bash
# 检查配置
cat /etc/docker/daemon.json

# 校验 JSON 格式
python3 -m json.tool /etc/docker/daemon.json

# 重启并查看日志
sudo systemctl restart docker
sudo journalctl -u docker -f
```

**路径前缀访问失败**

确认格式为 `域名/注册表/镜像名:标签`，例如 `proxy.xiaonuo.live/ghcr.io/owner/image:tag`，不要手动加 `/v2`。

### GitHub 下载慢

可能原因：

- Cloudflare Workers 到 GitHub 的网络延迟
- GitHub 自身限速
- Workers 免费版请求额度用尽

可以稍后重试，或升级 Cloudflare Workers 付费版。

## 性能与限制

Cloudflare Workers 免费额度：

| 项目 | 免费版 | 付费版 |
|------|--------|--------|
| 每天请求次数 | 100,000 | 按用量计费 |
| CPU 时间 | 10ms/请求 | 默认 30s/请求，可配置 |
| 响应大小 | 流式响应无硬性限制 | 流式响应无硬性限制 |

> 具体额度以 Cloudflare 官方文档为准。

使用建议：

- 镜像层下载速度取决于上游注册表和 Cloudflare 网络状况
- 超大文件（100MB 以上）可能受上游速度影响
- 避免短时间大量并发请求

## 安全说明

- 全程 HTTPS 加密
- 不记录用户日志
- 不存储任何数据
- 代码开源透明
- 支持 CORS 跨域

> 不要通过代理传输密码等敏感数据。

## 总结

从单一的 Docker 镜像加速，升级成了一个多功能加速代理：

- 一个 Worker + 一个域名，搞定 GitHub、Docker、SourceForge、Google Sheets 和直链下载
- Docker 支持单域名路径前缀访问所有主流注册表
- 支持 Git Clone 加速和断点续传
- 部署简单，免费额度足够个人使用

项目地址：<https://github.com/iflyelf/proxy-cf>

如果觉得有用，欢迎点个 Star。


---

> 作者: [小诺](https://www.iflyelf.com)  
> URL: https://www.iflyelf.com/posts/proxy_cf/  

