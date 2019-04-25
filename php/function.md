### 标量的判断
```
is_scalar() 判断变量是否是标量  -----  是的话返回true 不是的话返回false

什么是标量： 布尔、整数、浮点数、字符串型的数据属于标量
```

###  CURL 的使用
```
发送post请求并接受返回数据
```
example:
```
post_data ：json格式
public function curl($url,$post_data){
        $ch = curl_init();
        curl_setopt($ch, CURLOPT_URL, $url);
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
        // post数据
        curl_setopt($ch, CURLOPT_POST, 1);
       // post的变量
        curl_setopt($ch, CURLOPT_POSTFIELDS, $post_data);
        $output = curl_exec($ch);
        curl_close($ch);
        return $output;
    }
```

### instanceof 和 is_subclass_of()的区别

```
1. instdnceof关键字。使用这个关键字可以确定一个对象是类的实例、类的子类，还是实现了某个特定接口，并进行相应的操作
2. bool is_subclass_of ( object $object , string $class_name )
如果对象 object所属类是类 class_name的子类，则返回 TRUE ，否则返回 FALSE 。
```
eaxmple:
```
    <?php
        class Foo {
           public $foobar = 'Foo';
           public function test() {
             echo $this->foobar . "\n";
           }
         }
         class Bar extends Foo {
           public $foobar = 'Bar';
         }
        $a = new Foo();
        $b = new Bar();
        echo "use of test() method\n";
        $a->test();
        $b->test();
        echo "instanceof Foo\n";
        var_dump($a instanceof Foo); // TRUE
        var_dump($b instanceof Foo); // TRUE
        echo "instanceof Bar\n";
        var_dump($a instanceof Bar); // FALSE
        var_dump($b instanceof Bar); // TRUE
        echo "subclass of Foo\n";
        var_dump(is_subclass_of($a, 'Foo')); // FALSE
        var_dump(is_subclass_of($b, 'Foo')); // TRUE
        echo "subclass of Bar\n";
        var_dump(is_subclass_of($a, 'Bar')); // FALSE
        var_dump(is_subclass_of($b, 'Bar')); // FALSE
    ?>
```