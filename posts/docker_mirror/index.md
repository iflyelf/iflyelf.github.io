# Docker镜像加速器


Docker镜像加速器

&lt;!--more--&gt;

## 准备工作
* 域名一个,推荐[namesilo](https://www.namesilo.com)域名注册平台。&lt;br/&gt;
* [Cloudflare](https://www.cloudflare-cn.com)账号&lt;br/&gt;   
* [Github](https://github.com)代码仓库&lt;br/&gt;

## 克隆代码到自己的仓库
fork [cloudflare-docker-proxy](https://github.com/iflyelf/cloudflare-docker-proxy)仓库到自己账号下&lt;br/&gt;

## 修改配置文件
* 修改 src/index.js,修改内容如下:
```
const routes = {
 // production
  &#34;docker.iflyelf.com&#34;: dockerHub,
  &#34;quay.iflyelf.com&#34;: &#34;https://quay.io&#34;,
  &#34;gcr.iflyelf.com&#34;: &#34;https://gcr.io&#34;,
  &#34;k8s-gcr.iflyelf.com&#34;: &#34;https://k8s.gcr.io&#34;,
  &#34;k8s.iflyelf.com&#34;: &#34;https://registry.k8s.io&#34;,
  &#34;ghcr.iflyelf.com&#34;: &#34;https://ghcr.io&#34;,
  &#34;cloudsmith.iflyelf.com&#34;: &#34;https://docker.cloudsmith.io&#34;,
  &#34;ecr.iflyelf.com&#34;: &#34;https://public.ecr.aws&#34;,

  // staging
  &#34;docker-staging.iflyelf.com&#34;: dockerHub,
};
```
&gt; 将其中iflyelf.com根域名更换为自己的域名

* 修改 wrangler.toml,修改内容如下:
```
[env.production]
name = &#34;cloudflare-docker-proxy&#34;
routes = [
   { pattern = &#34;docker.iflyelf.com&#34;, custom_domain = true },
   { pattern = &#34;quay.iflyelf.com&#34;, custom_domain = true },
   { pattern = &#34;gcr.iflyelf.com&#34;, custom_domain = true },
   { pattern = &#34;k8s-gcr.iflyelf.com&#34;, custom_domain = true },
   { pattern = &#34;k8s.iflyelf.com&#34;, custom_domain = true },
   { pattern = &#34;ghcr.iflyelf.com&#34;, custom_domain = true },
   { pattern = &#34;cloudsmith.iflyelf.com&#34;, custom_domain = true },
]

[env.production.vars]
MODE = &#34;production&#34;
TARGET_UPSTREAM = &#34;&#34;

[env.staging]
name = &#34;cloudflare-docker-proxy-staging&#34;
route = { pattern = &#34;docker-staging.iflyelf.com&#34;, custom_domain = true }
```
&gt; 将其中iflyelf.com根域名更换为自己的域名

* 修改README.md文件中的[![Deploy to Cloudflare Workers](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/iflyelf/cloudflare-docker-proxy)更改为自己的仓库地址。

## 点击部署
[![Deploy to Cloudflare Workers](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/iflyelf/cloudflare-docker-proxy)

---

> 作者: [小精灵](https://www.iflyelf.com)  
> URL: https://www.iflyelf.com/posts/docker_mirror/  

