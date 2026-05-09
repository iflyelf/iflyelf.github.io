# Docker镜像加速器


Docker镜像加速器

<!--more-->

## 准备工作
* 域名一个,推荐[namesilo](https://www.namesilo.com)域名注册平台。<br/>
* [Cloudflare](https://www.cloudflare-cn.com)账号<br/>   
* [Github](https://github.com)代码仓库<br/>

## 克隆代码到自己的仓库
fork [cloudflare-docker-proxy](https://github.com/iflyelf/cloudflare-docker-proxy)仓库到自己账号下<br/>

## 修改配置文件
* 修改 src/index.js,修改内容如下:
```
const routes = {
 // production
  "docker.iflyelf.com": dockerHub,
  "quay.iflyelf.com": "https://quay.io",
  "gcr.iflyelf.com": "https://gcr.io",
  "k8s-gcr.iflyelf.com": "https://k8s.gcr.io",
  "k8s.iflyelf.com": "https://registry.k8s.io",
  "ghcr.iflyelf.com": "https://ghcr.io",
  "cloudsmith.iflyelf.com": "https://docker.cloudsmith.io",
  "ecr.iflyelf.com": "https://public.ecr.aws",

  // staging
  "docker-staging.iflyelf.com": dockerHub,
};
```
> 将其中iflyelf.com根域名更换为自己的域名

* 修改 wrangler.toml,修改内容如下:
```
[env.production]
name = "cloudflare-docker-proxy"
routes = [
   { pattern = "docker.iflyelf.com", custom_domain = true },
   { pattern = "quay.iflyelf.com", custom_domain = true },
   { pattern = "gcr.iflyelf.com", custom_domain = true },
   { pattern = "k8s-gcr.iflyelf.com", custom_domain = true },
   { pattern = "k8s.iflyelf.com", custom_domain = true },
   { pattern = "ghcr.iflyelf.com", custom_domain = true },
   { pattern = "cloudsmith.iflyelf.com", custom_domain = true },
]

[env.production.vars]
MODE = "production"
TARGET_UPSTREAM = ""

[env.staging]
name = "cloudflare-docker-proxy-staging"
route = { pattern = "docker-staging.iflyelf.com", custom_domain = true }
```
> 将其中iflyelf.com根域名更换为自己的域名

* 修改README.md文件中的[![Deploy to Cloudflare Workers](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/iflyelf/cloudflare-docker-proxy)更改为自己的仓库地址。

## 点击部署
[![Deploy to Cloudflare Workers](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/iflyelf/cloudflare-docker-proxy)

---

> 作者: [小诺](https://www.iflyelf.com)  
> URL: https://www.iflyelf.com/posts/docker_mirror/  

