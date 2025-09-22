# GitHub相关操作

## 如何将本地项目上传到GitHub

### git配置

* 安装git 
* 安装之后配置用户名和邮箱

```bash
git config --global user.name "您的用户名"
git config --global user.email "您的邮箱"
```

* 设置ssh密钥

![image-20250922165031855](https://xubenshan-pic.oss-cn-beijing.aliyuncs.com/img/image-20250922165031855.png)

之后会在.ssh文件夹生成两个文件。

![image-20250922165112630](https://xubenshan-pic.oss-cn-beijing.aliyuncs.com/img/image-20250922165112630.png)

复制id_rsa.pub文件中的内容。将公钥添加到 GitHub：Settings → SSH and GPG keys → New SSH key

### GitHub配置

* 新建仓库，注意不要初始化README文件。生成以下界面。

![](https://xubenshan-pic.oss-cn-beijing.aliyuncs.com/img/image-20250922165339201.png)

### 初次上传

```bash
# 初始化git仓库（本地仓库）
git init

# 把所有文件添加到暂存区
git add .

# 提交更改
git commit -m "first"

# 链接本地仓库和GitHub远程仓库

# 将本地分支重命名分支为main
git branch -M main

# 添加远程仓库
git remote add origin git@github.com:username/repositoryname.git

# 推送代码 main是本地分支 origin是远程仓库的别名
git push -u origin main 
```

本地仓库 远程仓库

### 日常使用

```bash
git add .

git commit -m "first"

git push 
```

分支：

```bash
# 删除远程的master分支
git push origin --delete master
```

