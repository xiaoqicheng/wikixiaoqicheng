# docker-compose 安装`

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