```
Yii2.0  定时发送邮件：
1.web配置：
    host 和 port 适用于扣扣企业邮箱
    'mailer' => [
        'class' => 'yii\swiftmailer\Mailer',
        'useFileTransport' =>false,//这句一定有，false发送邮件，true只是生成邮件在runtime文件夹下，不发邮件
        'transport' => [
            'class' => 'Swift_SmtpTransport',
            'host' => 'smtp.exmail.qq.com',  //每种邮箱的host配置不一样
            'username' => 'datacenter@checheng.com',
            'password' => 'Datacenter2017',
            'port' => '587',     //寻找到合适的端口号
            'encryption' => 'tls',
    
        ],
        'messageConfig'=>[
            'charset'=>'UTF-8',
            'from'=>['datacenter@checheng.com'=>'统计中心']
        ],
    ],

    /**
     * @title 发送邮件
     * @date 2017-09-04
     * @author CF
     */

    public function actionSendEmail(){

        $path_file = InfoSendEmail::getExcel(); //获取文件路径
		
        $mail = Yii::$app->mailer->compose();

        fopen($path_file, 'r+');    //对文件进行操作前 赋予操作权限
		
        $mail->attach($path_file); //本地附件
        
		$email = CarTjSendConfig::getEmail(); //获取邮箱账号 数组
        
		$mail->setTo($email);
		
        $mail->setSubject("车城网运营日报-".date('Ymd',time()-86400));

		$mail->setTextBody('这是正文');
		
        if($mail->send()){
            unlink($path_file); //删除文件
        }

        die();
    }
```