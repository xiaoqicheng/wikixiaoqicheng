## php array 的操作
```angular2html 
 <p style='colour:red';>符号规则：*必选，^可选</p>
```

### 1.urlencode(string str)
```
此函数便于将字符串编码并将其用于 URL 的请求部分，同时它还便于将变量传递给下一页
```
### 2. array_slice(array*,start*,length^,preserve^)   
```
定义：函数在数组中根据条件取出一段值，并返回
length：返回数组的长度。preserve：true 保留键名 false 重置键名
example:
    <?php
        $a=array("red","green","blue","yellow","brown");
        print_r(array_slice($a,2));
    ?>
```

### 3.array_splice(array*,start*,length^,array^)
```
定义：函数从数组中移除选定的元素，并用新元素取代它。该函数也将返回包含被移除元素的数组
start:0,第一位开始 -2，倒数第二位开始，或者其他数值
example:
<?php
    $a1=array("a"=>"red","b"=>"green","c"=>"blue","d"=>"yellow");
    $a2=array("a"=>"purple","b"=>"orange");
    array_splice($a1,0,2,$a2);
    print_r($a1);
?>
```

### 4.reset(array*)
```
定义：输出数组中的当前元素和下一个元素的值，然后把数组的内部指针重置到数组中的第一个元素
eample:
    <?php
        $people = array("Bill", "Steve", "Mark", "David");
        echo current($people) . "<br>";
        echo next($people) . "<br>";
        echo reset($people);
    ?>
```
