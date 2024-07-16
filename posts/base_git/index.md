# Git基本操作


Git基本操作

&lt;!--more--&gt;

## 查看配置
### 查看全局配置
```
git config --global --list
```
### 查看当前配置
```
git config --list
```
### 配置个人的用户名称和电子邮件地址
```
git config --global user.name &#34;iflyelf&#34;
git config --global user.email iflyelf@gmail.com
```

## 生成、添加 SSH 公钥
### 生成 SSH 公钥
```
ssh-keygen -t ed25519 -C &#39;邮箱&#39; -f ~/.ssh/gitee_id_rsa
-t key 类型
-C 注释
ssh-keygen -t ed25519 -C &#39;iflyelf@gmail.com&#39; -f ~/.ssh/gitee_id_rsa
```

## 创建Git仓库
### 创建仓库目录文件并进入目录
```
mkdir -pv git-me
cd git-me
```
### 初始化Git仓库
```
git init
```
### 创建README文件并编辑
```
touch README.md
echo &#34;学习git&#34; &gt;&gt; README.md
```
### 增加文件到缓存区
#### 增加所有修改的文件到缓存区(不包括删除的文件到缓存区)
```
git add .
```
#### 增加所有修改的文件包含删除的文件到缓存区
```
git add -A
```
### 将暂存区内容添加到仓库中
```
git commit -am &#34;创建README.md文件&#34;
-a 类似 git add . 但不包含新增加的文件
-m 注释内容
```
### 增加远程仓库
```
git remote add &lt;remote_name&gt; &lt;remote_url&gt;
remote_name 远程仓库的名称
remote_url 远程仓库的 URL
git remote add origin https://gitee.com/iflyelf/git-me.git
```
###  将本地仓库的代码上传至远程仓库
```
git push &lt;远程主机名&gt; &lt;本地分支名&gt;:&lt;远程分支名&gt;
git push -u origin &#34;master&#34;
```
### 从远程获取代码并合并本地的版本
```
git pull &lt;远程主机名&gt; &lt;远程分支名&gt;:&lt;本地分支名&gt;
git pull origin master
```

# Git 分支管理
## 创建分支
```
git branch 分支名称
git branch dev
```
## 切换分支
```
git checkout 分支名称
git checkout dev
```
## 创建分支并切换至新分支
```
git checkout -b 分支名称
git checkout -b test
or
git switch -c 分支名称
git switch -c test
```
## 查看分支
### 查看本地仓库所有分支
```
git branch
```
### 查看远程仓库所有分支
```
git branch -r
```
### 查看本地和远程仓库所有分支
```
git branch -a
```

## 推送本地分支到远程仓库分支
```
git push origin 本地分支名称
git push origin test
```

## 获取远程仓库代码到本地仓库分支
```
git fetch -p
```

## 合并分支
```
git merge 分支名称
```

## 删除分支
### 删除分支
```
git branch -d 分支名称
```
### 强制删除分支
```
git branch -D 分支名称
```
### 删除远程分支
```
git push origin --delete test
```

## Git 标签
### 创建标签(git commit之后操作)
```
git tag -a 版本号 -m &#34;版本描述&#34;
git tag -a v1.0 -m &#34;v1.0&#34;
```
### 查看标签
```
git tag
```
### 查看该版本所修改的内容
```
git show 版本号
git show v1.1
```
### 删除标签
```
git tag -d 版本号
```

## Git 版本管理
### 查看当前仓库状态
```
git status -s
M 被修改
A 增加
D 删除
R 重命名
C 被拷贝
U 被更新但是未合并
```
### 查看代码提交记录
#### 查看历史提交记录
```
git log --oneline
```
#### 以列表形式查看指定文件的历史修改记录
```
git blame 文件名
```
### 对比文件内容差异
```
git diff 文件名
```

## 恢复或撤销文件的更改
### 撤销单个文件的修改(丢弃所有未提交的更改)
```
git restore 文件名称
git checkout -- 文件名称
```
### 还原文件到暂存区的状态
```
git restore --staged 文件名称
```
### 还原所有未提交的更改(包括工作目录和暂存区的更改)
```
git restore .
```
### 还原文件到指定提交的状态
```
git restore --source=&lt;commit&gt; &lt;file&gt;
```

## 回退版本
### 回退所有内容到上一个版本
```
git reset HEAD^
```
### 回退到某个版本
```
git reset --soft HEAD
```
### 回退到某个版本回退点之前的所有信息
```
git reset --hard 版本号
git reset --hard v1.0
```
### 将本地的状态回退到和远程的一样
```
git reset --hard origin/master
```


---

> 作者: [小精灵](https://www.iflyelf.com)  
> URL: https://www.iflyelf.com/posts/base_git/  

