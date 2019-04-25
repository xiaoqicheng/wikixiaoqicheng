### composer 更新超出字节数报错
```
提示content-length mismatch composer
https://pkg.phpcomposer.com/
方法1： 修改当前项目的 composer.json 配置文件：
打开命令行窗口（windows用户）或控制台（Linux、Mac 用户），进入你的项目的根目录（也就是 composer.json 文件所在目录），执行如下命令：
复制
composer config repo.packagist composer https://packagist.phpcomposer.com
上述命令将会在当前项目中的 composer.json 文件的末尾自动添加镜像的配置信息（你也可以自己手工添加）：
复制
"repositories": {
    "packagist": {
        "type": "composer",
        "url": "
https://packagist.phpcomposer.com"
    }
}
OK，一切搞定！试一下 composer install 来体验飞一般的速度吧！


镜像原理：

一般情况下，安装包的数据（主要是 zip 文件）一般是从 github.com 上下载的，安装包的元数据是从 packagist.org 上下载的。

然而，由于众所周知的原因，国外的网站连接速度很慢，并且随时可能被“墙”甚至“不存在”。
方法一： 修改 composer 的全局配置文件（推荐方式）改成国内地址
先执行：
composer config -g repo.packagist composer https://packagist.phpcomposer.com
在执行：composer update
```