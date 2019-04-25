#### laravel中时区的更改
```
设置时区：在config/app.php 下 修改参数‘timezone’='UTC' 为 ‘timezone’='Asia/Sahnghai'.
```

#### redis的使用设置缓存
```
设置缓存
Cache::store('redis')->put('bar', 'baz', 10);

获取缓存：
Cache::get('bar');
```