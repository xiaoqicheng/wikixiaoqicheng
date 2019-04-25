### 生成ssk
```
1.在GitHub上注册账号

2.使用 git config --list 查看配置是否存在  
若无则使用以下命令添加吧
git config --global user.name"名称"
git config --global user.email "邮箱"

3.生成ssh key
 ssh-keygen -r rsa -c "邮箱"（windows）
 ssh-keygen -t rsa -C "邮箱"(Linux)
按三次回车 ，不输入密码

4.复制出.ssh文件夹下的id_rsa.pub文件内容加入到GitHub中
```

### 基本操作
```
1.创建本地分支
git branch + 分支名

2.切换新分支
git checkout product
git pull origin product
git checkout -b zdjc origin/product
.git config 里 配置更改
rebase = true origin指向
更改代码 commit push 到新分支

```
### 合并分支
```
将两个分支拉到最新代码到本地
git merge test --no-ff -m 'merge test'
git push  

解决冲突后需要添加 和 提交
``` 

### 比较本地和远程分支的不同
```
git diff origin/master..HEAD --stat （查看不同的文件）
git diff origin/master..HEAD （查看不同的文件的内容）
```

### 代码回滚
```
git reset --hard commit_id
```

### 查看文件每个部分修改历史
```
git blame 文件名 
```