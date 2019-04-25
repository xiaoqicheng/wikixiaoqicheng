### laravel5.5定时任务
```
1.创建artisan命令
php artisan make:command Test

2.修改Test文件
<?php
namespace App\Console\Commands;

use Illuminate\Console\Command;
class Test extends Command
{
    //命令名称
    protected $signature = 'test:test1';
    //命令说明
    protected $description = '测试定时任务';

    /**
     * Create a new command instance.
     *
     * @return void
     */
    public function __construct()
    {
        parent::__construct();
    }

    //执行内容
    public function handle()
    {
        //
        echo date('Y-m-d H:i:s').'每分钟执行一次';
    }
}

3.修改kernel.php文件
<?php

namespace App\Console;

use Illuminate\Console\Scheduling\Schedule;
use Illuminate\Foundation\Console\Kernel as ConsoleKernel;

class Kernel extends ConsoleKernel
{

    //加载命令类
    protected $commands = [
        //
        Commands\Test::class,
    ];

    //设置执行限制
    protected function schedule(Schedule $schedule)
    {

        $schedule->command('test:test1')->timezone('Asia/Shanghai');
    }
}

4.服务器上编辑crontab

> crontab -e

# m h  dom mon dow   command
*/1 * * * * php /home/test/work/xiaoqicheng/artisan test:test1 >> /var/log/ping.log/test.log 2>&1
```