# 安装 docker 

```
安装必要的系统工具
#: sudo yum install -y yum-utils device-mapper-persistent-data lvm2

#: sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
#: sudo yum makecache fast
#: sudo yum -y install docker-ce
#: sudo systemctl start docker
#: sudo docker version

普通用户使用docker
#: usermod -aG docker $user
```

# 安装 docker-compose 

### 使用二进制包安装

##### 在github上 https://github.com/docker/compose/releases/ 寻找版本
```
#: sudo curl -L https://github.com/docker/compose/releases/download/1.22.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
#: sudo chmod +x /usr/local/bin/docker-compose
```

### 使用pip安装
```
#: sudo yum -y install epel-release.noarch 
#: sudo yum -y install python-pip
#: sudo pip install --upgrade pip
#: sudo pip install docker-compose
```