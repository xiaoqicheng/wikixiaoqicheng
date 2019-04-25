### 搭建好lnmp环境后，只显示首页页面跳转报错

```
在nginx 配置中

location / {
        try_files $uri $uri/ /index.php?$query_string;
        if (!-e $request_filename){
           rewrite ^/(.*) /index.php last;
        }
    }
```