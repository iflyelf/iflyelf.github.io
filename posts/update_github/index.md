# 上传博客至github仓库


# 上传博客至github仓库

## 配置个人的用户名称和电子邮件地址
git config --global user.name &#34;iflyelf&#34;
git config --global user.email iflyelf@gmail.com

## 查看全局配置
git config --global --list

参考地址:
https://docs.github.com/zh/get-started/getting-started-with-git/setting-your-username-in-git

## 为硬件安全密钥生成新的 SSH 密钥
ssh-keygen -t ed25519 -C &#34;iflyelf@gmail.com&#34; -f ~/.ssh/github_id_rsa

参考地址:
https://docs.github.com/zh/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent

## 获取SSH 公钥
cat /root/.ssh/github_id_rsa.pub

## 将 SSH 密钥添加到 ssh-agent
1&gt; 在后台启动 ssh 代理
eval &#34;$(ssh-agent -s)&#34;
2&gt; 将 SSH 私钥添加到 ssh-agent
ssh-add /root/.ssh/github_id_rsa

参考地址:
https://docs.github.com/zh/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account

## 测试 SSH 连接
ssh -T git@github.com
https://docs.github.com/zh/authentication/connecting-to-github-with-ssh/testing-your-ssh-connection


## 创建README文件并编辑
touch README.md
echo &#34;# 小精灵笔记&#34; &gt; README.md

## 增加所有修改的文件到缓存区
git add .

## 将暂存区内容添加到仓库中
git commit -am &#34;创建README.md文件&#34;

## 重命名当前分支
git branch -M main

## 增加远程仓库
git remote add origin https://github.com/iflyelf/blog.git

## 将本地仓库的代码上传至远程仓库
git push -u origin main

## 管理个人访问令牌
创建个人访问令牌地址
https://github.com/settings/tokens/new

参考地址:
https://docs.github.com/zh/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens




---

> 作者: [小精灵](https://www.iflyelf.com)  
> URL: https://www.iflyelf.com/posts/update_github/  

