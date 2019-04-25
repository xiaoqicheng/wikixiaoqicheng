### 导出excel表格
example：
```
<?php
/**
 * Created by PhpStorm.
 * User: Admin
 * Date: 2017/7/3
 * Time: 17:10
 */
namespace app\models;

class InfoSendEmail extends \yii\db\ActiveRecord
{
    const NUM = 2;
    const NUM_I = 3;
    const NUM_I_TWO = 9;
    /**
     * @title 获取日报数据
     * @date 2017-09-04
     * @author CF
     */
    public static function getData(){
        $data['web'] = self::getWbeData();
        $data['app'] = self::getAppData();

        return $data;
    }

    /**
     * @title 获取Web日报数据
     * @date 2017-09-04
     * @author CF
     */
    public static function getWbeData(){
        $date = date('Y-m-d',time()-86400);
        $data['yes'] = self::getWebByDate($date);
        $data['one_diff'] = self::getWebByDateDiff($date,date('Y-m-d',strtotime($date . '-1 day')));
        $data['sev_diff'] = self::getWebByDateDiff($date,date('Y-m-d',strtotime($date . '-7 day')));
        $data['Fourteen_diff'] = self::getWebByDateDiff($date,date('Y-m-d',strtotime($date . '-14 day')));
        $data['Tw_eig_diff'] = self::getWebByDateDiff($date,date('Y-m-d',strtotime($date . '-28 day')));
        $data['sev_avg'] = self::getWebDataByDate($date,date('Y-m-d',strtotime($date . '-7 day')));
        $data['Tw_eig_avg'] = self::getWebDataByDate($date,date('Y-m-d',strtotime($date . '-28 day')));
        return $data;
    }

    /**
     * @Param $date 某天的时间
     * @title 获取某天的web数据
     * @date 2017-09-04
     * @author CF
     */
    public static function getWebByDate($date){
        $info = Total::find()->where(['date'=> $date])->asArray()->all();

        if ($info)
            foreach ($info as $k => $val){
                if (!isset($val['type'])){
                    $xs_pc = self::getXs($date,1);
                    $data['pc']['pv'] = $val['pv_pc'] ?? 0;
                    $data['pc']['uv'] = $val['uv_pc'] ?? 0;
                    $data['pc']['avg_p'] = (isset($val['uv_pc']) && $val['uv_pc'] > 0) ? sprintf('%.2f',($val['pv_pc']/$val['uv_pc'])) : 0;
                    $data['pc']['xs'] = $xs_pc['xs'];
                    $data['pc']['xs_H'] = $xs_pc['xs_H'];
                    $data['pc']['zhl'] = (isset($val['uv_pc']) && $val['uv_pc'] > 0) ? sprintf('%.2f',($xs_pc['xs_H']/$val['uv_pc'])*100).'%' : 0;

                    $xs_m = self::getXs($date,2);
                    $data['m']['pv'] = $val['pv_m'] ?? 0;
                    $data['m']['uv'] = $val['uv_m'] ?? 0;
                    $data['m']['avg_p'] = (isset($val['uv_m']) && $val['uv_m'] > 0) ? sprintf('%.2f',($val['pv_m']/$val['uv_m'])) : 0;
                    $data['m']['xs'] = $xs_m['xs'];
                    $data['m']['xs_H'] = $xs_m['xs_H'];
                    $data['m']['zhl'] = (isset($val['uv_m']) && $val['uv_m'] > 0) ? sprintf('%.2f',($xs_m['xs_H']/$val['uv_m'])*100).'%' : 0;
                }else{
                    if ($val['type'] == 1){
                        $xs = self::getXs($date,$val['type']);
                        $data['pc']['pv'] = $val['pv'] ?? 0;
                        $data['pc']['uv'] = $val['uv'] ?? 0;
                        $data['pc']['avg_p'] = (isset($val['uv']) && $val['uv'] > 0) ? sprintf('%.2f',($val['pv']/$val['uv'])) : 0;
                        $data['pc']['xs'] = $xs['xs'];
                        $data['pc']['xs_H'] = $xs['xs_H'];
                        $data['pc']['zhl'] = (isset($val['uv']) && $val['uv'] > 0) ? sprintf('%.2f',($xs['xs_H']/$val['uv'])*100).'%' : 0;
                    }elseif ($val['type'] == 2){
                        $xs = self::getXs($date,$val['type']);
                        $data['m']['pv'] = $val['pv'] ?? 0;
                        $data['m']['uv'] = $val['uv'] ?? 0;
                        $data['m']['avg_p'] = (isset($val['uv']) && $val['uv'] > 0) ? sprintf('%.2f',($val['pv']/$val['uv'])) : 0;
                        $data['m']['xs'] = $xs['xs'];
                        $data['m']['xs_H'] = $xs['xs_H'];
                        $data['m']['zhl'] = (isset($val['uv']) && $val['uv'] > 0) ? sprintf('%.2f',($xs['xs_H']/$val['uv'])*100).'%' : 0;
                    }
                }
            }

        return $data ?? [];
    }


    /**
     * @Param $date 某天的时间 $request 来源
     * @title 获取某天的线索数
     * @date 2017-09-04
     * @author CF
     */
    public static function getXs($date,$request){
        $info['xs'] = CarAskPrice::find()->where(['between', 'addtime', $date, date('Y-m-d 23:59:59',strtotime($date))])->andWhere(['request_type' => $request])->count();
        $info['xs_H'] = CarAskPrice::find()->where(['between', 'addtime', $date, date('Y-m-d 23:59:59',strtotime($date))])->andWhere(['request_type' => $request])->groupBy('phone')->count();
        return $info;
    }

    /**
     * @Param $start 开始的时间 $end 结束时间
     * @title 获取某天的web数据
     * @date 2017-09-04
     * @author CF
     */
    public static function getWebByDateDiff($start,$end){
        $start_data = self::getWebByDate($start);
        $end_data = self::getWebByDate($end);

        $data['pc']['pv'] = ($start_data['pc']['pv'] ?? 0) - ($end_data['pc']['pv'] ?? 0);
        $data['pc']['uv'] = ($start_data['pc']['uv'] ?? 0) - ($end_data['pc']['uv'] ?? 0);
        $data['pc']['avg_p'] = (($start_data['pc']['avg_p'] ?? 0) - ($end_data['pc']['avg_p'] ?? 0));
        $data['pc']['xs'] = ($start_data['pc']['xs'] ?? 0) - ($end_data['pc']['xs'] ?? 0);
        $data['pc']['xs_H'] = ($start_data['pc']['xs_H'] ?? 0) - ($end_data['pc']['xs_H'] ?? 0);
        $data['pc']['zhl'] = sprintf("%.2f", (((float)$start_data['pc']['zhl']/100 ?? 0) - ((float)$end_data['pc']['zhl']/100 ?? 0))*100) . '%';
        $data['m']['pv'] = ($start_data['m']['pv'] ?? 0) - ($end_data['m']['pv'] ?? 0);
        $data['m']['uv'] = ($start_data['m']['uv'] ?? 0) - ($end_data['m']['uv'] ?? 0);
        $data['m']['avg_p'] = (($start_data['m']['avg_p'] ?? 0) - ($end_data['m']['avg_p'] ?? 0));
        $data['m']['xs'] = ($start_data['m']['xs'] ?? 0) - ($end_data['m']['xs'] ?? 0);
        $data['m']['xs_H'] = ($start_data['m']['xs_H'] ?? 0) - ($end_data['m']['xs_H'] ?? 0);
        $data['m']['zhl'] = sprintf("%.2f", (((float)$start_data['m']['zhl']/100 ?? 0) - ((float)$end_data['m']['zhl']/100 ?? 0))*100) . '%';

        return $data;
    }

    /**
     * @Param $begin 开始时间
     * @Param $end   结束时间
     * @title 获取Web某段时间的平均差额
     * @date  2017-09-05
     * @author CF
     */
    public static function getWebDataByDate($begin,$end){
        $xAxis_data = $data = $info = [];
        $day = (strtotime($begin)-strtotime($end))/86400;
        for ($i=strtotime($end);$i <= strtotime($begin);$i+=86400){
            $xAxis_data[] = self::getWebByDate(date("Y-m-d",$i));
        }

        foreach ($xAxis_data as $key => $value){
            foreach ($value as $k => $val){
                if ($k == 'pc'){
                    $data['pc']['pv'] = ($data['pc']['pv'] ?? 0) + $val['pv'];
                    $data['pc']['uv'] = ($data['pc']['uv'] ?? 0) + $val['uv'];
                    $data['pc']['avg_p'] = ($data['pc']['avg_p'] ?? 0) + $val['avg_p'];
                    $data['pc']['xs'] = ($data['pc']['xs'] ?? 0) + $val['xs'];
                    $data['pc']['xs_H'] = ($data['pc']['xs_H'] ?? 0) + $val['xs_H'];
                }elseif($k == 'm'){
                    $data['m']['pv'] = ($data['m']['pv'] ?? 0) + $val['pv'];
                    $data['m']['uv'] = ($data['m']['uv'] ?? 0) + $val['uv'];
                    $data['m']['avg_p'] = ($data['m']['avg_p'] ?? 0) + $val['avg_p'];
                    $data['m']['xs'] = ($data['m']['xs'] ?? 0) + $val['xs'];
                    $data['m']['xs_H'] = ($data['m']['xs_H'] ?? 0) + $val['xs_H'];
                }
            }
        }

        $data_now = self::getWebByDate(date('Y-m-d',time()-86400));

        foreach ($data as $ke =>$v){
            $info[$ke]['pv'] = $data_now[$ke]['pv'] - sprintf("%.2f", $v['pv']/$day);
            $info[$ke]['uv'] = $data_now[$ke]['uv'] - sprintf("%.2f", $v['uv']/$day);
            $info[$ke]['avg_p'] =  $data_now[$ke]['avg_p'] - sprintf("%.2f", $v['avg_p']/$day);
            $info[$ke]['xs'] = $data_now[$ke]['xs'] - sprintf("%.2f", $v['xs']/$day);
            $info[$ke]['xs_H'] = $data_now[$ke]['xs_H'] - sprintf("%.2f", $v['xs_H']/$day);

            $info[$ke]['zhl'] = sprintf("%.2f", (((float)$data_now[$ke]['zhl']/100 ?? 0) - ((float)($v['uv'] > 0 ? sprintf("%.2f", $v['xs_H']/$v['uv']*100). '%' : 0)/100 ?? 0))*100) . '%';
        }

        return $info;
    }

    /**
     * @title 获取app日报数据
     * @date 2017-09-04
     * @author CF
     */
    public static function getAppData(){
        $date = date('Y-m-d',time()-86400);
        $data['yes'] = self::getAppByDate($date);
        $data['one_diff'] = self::getAppByDateDiff($date,date('Y-m-d',strtotime($date . '-1 day')));
        $data['sev_diff'] = self::getAppByDateDiff($date,date('Y-m-d',strtotime($date . '-7 day')));
        $data['Fourteen_diff'] = self::getAppByDateDiff($date,date('Y-m-d',strtotime($date . '-14 day')));
        $data['Tw_eig_diff'] = self::getAppByDateDiff($date,date('Y-m-d',strtotime($date . '-28 day')));
        $data['sev_avg'] = self::getAppDataByDate($date,date('Y-m-d',strtotime($date . '-7 day')));
        $data['Tw_eig_avg'] = self::getAppDataByDate($date,date('Y-m-d',strtotime($date . '-28 day')));

        return $data;
    }


    /**
     * @Param $date  time
     * @title 根据时间获取APP每日数据
     * @date 2017-09-05
     * @author CF
     */

    public static function getAppByDate($date){
        if ($date > '2017-08-17'){
            $info = AppTotalNew::find()->where(['date' => $date])->asArray()->all();
            foreach ($info as $key => $value){
                if ($value['platform'] == 3){
                    $xs = self::getXs($date,$value['platform']);
                    $data['android']['pv'] = $value['pv'] ?? 0;
                    $data['android']['uv'] = $value['uv'] ?? 0;
                    $data['android']['avg_p'] = (isset($value['uv']) && $value['uv'] > 0) ? sprintf('%.2f',($value['pv']/$value['uv'])) : 0;
                    $data['android']['avg_time'] = (isset($value['uv']) && $value['uv'] > 0) ? sprintf('%.2f',($value['total_time']/$value['uv']/60)) : 0;
                    $data['android']['fv'] = self::getFvByDate($date,$value['platform']);
                    $data['android']['xs'] = $xs['xs'];
                    $data['android']['xs_H'] = $xs['xs_H'];
                    $data['android']['zhl'] = (isset($value['uv']) && $value['uv'] > 0) ? sprintf('%.2f',($xs['xs_H']/$value['uv'])*100).'%' : 0;
                }elseif ($value['platform'] == 4){
                    $xs = self::getXs($date,$value['platform']);
                    $data['ios']['pv'] = $value['pv'] ?? 0;
                    $data['ios']['uv'] = $value['uv'] ?? 0;
                    $data['ios']['avg_p'] = (isset($value['uv']) && $value['uv'] > 0) ? sprintf('%.2f',($value['pv']/$value['uv'])) : 0;
                    $data['ios']['avg_time'] = (isset($value['uv']) && $value['uv'] > 0) ? sprintf('%.2f',($value['total_time']/$value['uv']/60)) : 0;
                    $data['ios']['fv'] = self::getFvByDate($date,$value['platform']);
                    $data['ios']['xs'] = $xs['xs'];
                    $data['ios']['xs_H'] = $xs['xs_H'];
                    $data['ios']['zhl'] = (isset($value['uv']) && $value['uv'] > 0) ? sprintf('%.2f',($xs['xs_H']/$value['uv'])*100).'%' : 0;
                }
            }

            //总数
            $total = [];
            foreach ($info as $k => $val){
                if ($val['platform'] == 3 || $val['platform'] == 4){
                    $total['pv'] = ($total['pv'] ?? 0) + ($val['pv'] ?? 0);
                    $total['uv'] = ($total['uv'] ?? 0) + ($val['uv'] ?? 0);
                    $total['total_time'] = ($total['total_time'] ?? 0) + ($val['total_time'] ?? 0);
                }
            }

            $data['all']['pv'] = $total['pv'] ?? 0;
            $data['all']['uv'] = $total['uv'] ?? 0;
            $data['all']['avg_p'] = (isset($total['uv']) && $total['uv'] > 0) ? sprintf('%.2f',($total['pv']/$total['uv'])) : 0;
            $data['all']['avg_time'] = (isset($total['uv']) && $total['uv'] > 0) ? sprintf('%.2f',($total['total_time']/$total['uv']/60)) : 0;
            $data['all']['fv'] = ($data['android']['fv'] ?? 0) + ($data['ios']['fv'] ?? 0);
            $data['all']['xs'] = ($data['android']['xs'] ?? 0) + ($data['ios']['xs'] ?? 0);
            $data['all']['xs_H'] = ($data['android']['xs_H'] ?? 0) + ($data['ios']['xs_H'] ?? 0);
            $data['all']['zhl'] = (isset($total['uv']) && $total['uv'] > 0) ? sprintf('%.2f',($data['all']['xs_H']/$total['uv'])*100).'%' : 0;

        }else{
            $info = AppTotal::find()->where(['date' => $date])->asArray()->all();
            foreach ($info as $key => $value){
                $xs_android = self::getXs($date,3);
                $data['android']['pv'] = $value['pv_a'] ?? 0;
                $data['android']['uv'] = $value['uv_a'] ?? 0;
                $data['android']['avg_p'] = (isset($value['uv_a']) && $value['uv_a'] > 0) ? sprintf('%.2f',($value['pv_a']/$value['uv_a'])) : 0;
                $data['android']['avg_time'] = (isset($value['uv_a']) && $value['uv_a'] > 0) ? sprintf('%.2f',($value['total_time_a']/$value['uv_a']/60)) : 0;
                $data['android']['fv'] = self::getFvByDate($date,3);
                $data['android']['xs'] = $xs_android['xs'];
                $data['android']['xs_H'] = $xs_android['xs_H'];
                $data['android']['zhl'] = (isset($value['uv_a']) && $value['uv_a'] > 0) ? sprintf('%.2f',($xs_android['xs_H']/$value['uv_a'])*100).'%' : 0;

                $xs_ios = self::getXs($date,4);
                $data['ios']['pv'] = $value['pv_i'] ?? 0;
                $data['ios']['uv'] = $value['uv_i'] ?? 0;
                $data['ios']['avg_p'] = (isset($value['uv_i']) && $value['uv_i'] > 0) ? sprintf('%.2f',($value['pv_i']/$value['uv_i'])) : 0;
                $data['ios']['avg_time'] = (isset($value['uv_i']) && $value['uv_i'] > 0) ? sprintf('%.2f',($value['total_time_i']/$value['uv_i']/60)) : 0;
                $data['ios']['fv'] = self::getFvByDate($date,4);
                $data['ios']['xs'] = $xs_ios['xs'];
                $data['ios']['xs_H'] = $xs_ios['xs_H'];
                $data['ios']['zhl'] = (isset($value['uv_i']) && $value['uv_i'] > 0) ? sprintf('%.2f',($xs_ios['xs_H']/$value['uv_i'])*100).'%' : 0;


                $data['all']['pv'] = ($value['pv_i'] ?? 0) + ($value['pv_a'] ?? 0);
                $data['all']['uv'] = ($value['uv_i'] ?? 0) + ($value['uv_a'] ?? 0);
                $data['all']['avg_p'] = ($data['all']['uv'] > 0) ? sprintf('%.2f',($data['all']['pv']/$data['all']['uv'])) : 0;
                $data['all']['avg_time'] = ($data['all']['uv'] > 0) ? sprintf('%.2f',(($value['total_time_i'] ?? 0 + $value['total_time_a'] ?? 0)/$data['all']['uv']/60)) : 0;
                $data['all']['fv'] = $data['android']['fv'] + $data['ios']['fv'];
                $data['all']['xs'] = $data['android']['xs'] + $data['ios']['xs'];
                $data['all']['xs_H'] = $data['android']['xs_H'] + $data['ios']['xs_H'];
                $data['all']['zhl'] = ($data['all']['xs_H'] > 0) ? sprintf('%.2f',($data['all']['xs_H']/$data['all']['uv'])*100).'%' : 0;

            }
        }

        return $data ?? [];

    }

    /**
     * @param $start
     * @param $end
     * @return mixed
     * @title 获取App某段时间的平均差额
     * @date 2017-09-05
     * @author CF
     */
    public static function getAppByDateDiff($start,$end){
        $start_data = self::getAppByDate($start);
        $end_data = self::getAppByDate($end);

        $data['android']['pv'] = ($start_data['android']['pv'] ?? 0) - ($end_data['android']['pv'] ?? 0);
        $data['android']['uv'] = ($start_data['android']['uv'] ?? 0) - ($end_data['android']['uv'] ?? 0);
        $data['android']['avg_p'] = (($start_data['android']['avg_p'] ?? 0) - ($end_data['android']['avg_p'] ?? 0));
        $data['android']['avg_time'] = ($start_data['android']['avg_time'] ?? 0) - ($end_data['android']['avg_time'] ?? 0);
        $data['android']['fv'] = ($start_data['android']['fv'] ?? 0) - ($end_data['android']['fv'] ?? 0);
        $data['android']['xs'] = ($start_data['android']['xs'] ?? 0) - ($end_data['android']['xs'] ?? 0);
        $data['android']['xs_H'] = ($start_data['android']['xs_H'] ?? 0) - ($end_data['android']['xs_H'] ?? 0);
        $data['android']['zhl'] = sprintf("%.2f", (((float)$start_data['android']['zhl']/100 ?? 0) - ((float)$end_data['android']['zhl']/100 ?? 0))*100) . '%';

        $data['ios']['pv'] = ($start_data['ios']['pv'] ?? 0) - ($end_data['ios']['pv'] ?? 0);
        $data['ios']['uv'] = ($start_data['ios']['uv'] ?? 0) - ($end_data['ios']['uv'] ?? 0);
        $data['ios']['avg_p'] = (($start_data['ios']['avg_p'] ?? 0) - ($end_data['ios']['avg_p'] ?? 0));
        $data['ios']['avg_time'] = ($start_data['ios']['avg_time'] ?? 0) - ($end_data['ios']['avg_time'] ?? 0);
        $data['ios']['fv'] = ($start_data['ios']['fv'] ?? 0) - ($end_data['ios']['fv'] ?? 0);
        $data['ios']['xs'] = ($start_data['ios']['xs'] ?? 0) - ($end_data['ios']['xs'] ?? 0);
        $data['ios']['xs_H'] = ($start_data['ios']['xs_H'] ?? 0) - ($end_data['ios']['xs_H'] ?? 0);
        $data['ios']['zhl'] = sprintf("%.2f", (((float)$start_data['ios']['zhl']/100 ?? 0) - ((float)$end_data['ios']['zhl']/100 ?? 0))*100) . '%';

        $data['all']['pv'] = ($start_data['all']['pv'] ?? 0) - ($end_data['all']['pv'] ?? 0);
        $data['all']['uv'] = ($start_data['all']['uv'] ?? 0) - ($end_data['all']['uv'] ?? 0);
        $data['all']['avg_p'] = (($start_data['all']['avg_p'] ?? 0) - ($end_data['all']['avg_p'] ?? 0));
        $data['all']['avg_time'] = ($start_data['all']['avg_time'] ?? 0) - ($end_data['all']['avg_time'] ?? 0);
        $data['all']['fv'] = ($start_data['all']['fv'] ?? 0) - ($end_data['all']['fv'] ?? 0);
        $data['all']['xs'] = ($start_data['all']['xs'] ?? 0) - ($end_data['all']['xs'] ?? 0);
        $data['all']['xs_H'] = ($start_data['all']['xs_H'] ?? 0) - ($end_data['all']['xs_H'] ?? 0);
        $data['all']['zhl'] = sprintf("%.2f", (((float)$start_data['all']['zhl']/100 ?? 0) - ((float)$end_data['all']['zhl']/100 ?? 0))*100) . '%';

        return $data;
    }

    /**
     * @param $begin
     * @param $end
     * @return array
     * @title 获取App某段时间的平均差额
     * @date  2017-09-05
     * @author CF
     */
    public static function getAppDataByDate($begin,$end){
        $xAxis_data = $data = $info = [];
        $day = (strtotime($begin)-strtotime($end))/86400;
        for ($i=strtotime($end);$i <= strtotime($begin);$i+=86400){
            $xAxis_data[] = self::getAppByDate(date("Y-m-d",$i));
        }

        foreach ($xAxis_data as $key => $value){
            foreach ($value as $k => $val){
                if ($k == 'android'){
                    $data['android']['pv'] = ($data['android']['pv'] ?? 0) + $val['pv'];
                    $data['android']['uv'] = ($data['android']['uv'] ?? 0) + $val['uv'];
                    $data['android']['avg_p'] = ($data['android']['avg_p'] ?? 0) + $val['avg_p'];
                    $data['android']['avg_time'] = ($data['android']['avg_time'] ?? 0) + $val['avg_time'];
                    $data['android']['fv'] = ($data['android']['fv'] ?? 0) + $val['fv'];
                    $data['android']['xs'] = ($data['android']['xs'] ?? 0) + $val['xs'];
                    $data['android']['xs_H'] = ($data['android']['xs_H'] ?? 0) + $val['xs_H'];
                }elseif($k == 'ios'){
                    $data['ios']['pv'] = ($data['ios']['pv'] ?? 0) + $val['pv'];
                    $data['ios']['uv'] = ($data['ios']['uv'] ?? 0) + $val['uv'];
                    $data['ios']['avg_p'] = ($data['ios']['avg_p'] ?? 0) + $val['avg_p'];
                    $data['ios']['avg_time'] = ($data['ios']['avg_time'] ?? 0) + $val['avg_time'];
                    $data['ios']['fv'] = ($data['ios']['fv'] ?? 0) + $val['fv'];
                    $data['ios']['xs'] = ($data['ios']['xs'] ?? 0) + $val['xs'];
                    $data['ios']['xs_H'] = ($data['ios']['xs_H'] ?? 0) + $val['xs_H'];
                }elseif($k == 'all'){
                    $data['all']['pv'] = ($data['all']['pv'] ?? 0) + $val['pv'];
                    $data['all']['uv'] = ($data['all']['uv'] ?? 0) + $val['uv'];
                    $data['all']['avg_p'] = ($data['all']['avg_p'] ?? 0) + $val['avg_p'];
                    $data['all']['avg_time'] = ($data['all']['avg_time'] ?? 0) + $val['avg_time'];
                    $data['all']['fv'] = ($data['all']['fv'] ?? 0) + $val['fv'];
                    $data['all']['xs'] = ($data['all']['xs'] ?? 0) + $val['xs'];
                    $data['all']['xs_H'] = ($data['all']['xs_H'] ?? 0) + $val['xs_H'];
                }
            }
        }

        $data_now = self::getAppByDate(date("Y-m-d",time()-86400));;

        foreach ($data as $ke =>$v){
            $info[$ke]['pv'] = $data_now[$ke]['pv'] - sprintf("%.2f", $v['pv']/$day);
            $info[$ke]['uv'] = $data_now[$ke]['uv'] - sprintf("%.2f", $v['uv']/$day);
            $info[$ke]['avg_p'] =  $data_now[$ke]['avg_p'] - sprintf("%.2f", $v['avg_p']/$day);
            $info[$ke]['avg_time'] = $data_now[$ke]['avg_time'] - sprintf("%.2f", $v['avg_time']/$day);
            $info[$ke]['fv'] = $data_now[$ke]['fv'] - sprintf("%.2f", $v['fv']/$day);
            $info[$ke]['xs'] = $data_now[$ke]['xs'] - sprintf("%.2f", $v['xs']/$day);
            $info[$ke]['xs_H'] = $data_now[$ke]['xs_H'] - sprintf("%.2f", $v['xs_H']/$day);


            $info[$ke]['zhl'] = sprintf("%.2f", (((float)$data_now[$ke]['zhl']/100 ?? 0) - ((float)($v['uv'] > 0 ? sprintf("%.2f", $v['xs_H']/$v['uv']*100) . '%' : 0)/100 ?? 0))*100) . '%';
        }

        return $info;
    }

    /**
     * @Param $date $type
     * @title 根据时间获取APP每日新增用户
     * @date 2017-09-05
     * @author CF
     */
    public static function getFvByDate($date, $type){
        return AppUser::find()->where(['platform' => $type])->andWhere(['date' => $date])->count();
    }

    /**
     * @return string
     * @title 生成文件 返回文件名称
     * @date 2017-09-05
     * @author CF
     */
    public static function getExcel(){
        $data = self::getData();
        $table = ['', '', '当日', '昨日差额', '7日环比差额', '14日环比差额', '28日环比差额', '近7日平均差额', '近28日平均差额'];
        $excelFileName = 'excel/daily-' . date('Ymd',time()-86400) . '.xls';
        $title_web = 'WEB日报表：' . date('Y-m-d',time()-86400);
        $title_app = 'APP日报表：' . date('Y-m-d',time()-86400);
        $tltle_web_letter = ['PV', 'UV', '人均访问页数', '线索数', '独立号码数', '转化率'];
        $tltle_app_letter = ['访问页数', '活跃用户', '人均访问页数', '人均访问时长', '新增用户', '线索数', '独立号码数', '转化率'];
        self::exportExcel($data,$table,$excelFileName,$title_web,$title_app,$tltle_web_letter,$tltle_app_letter);

        return $excelFileName;
    }

    /**
     * @param $data  数据
     * @param $tableheader 表头
     * @param $excelFileName 生成的文件名称
     * @param $title_web web工作薄名称
     * @param $title_app APP工作簿名称
     * @param $tltle_web_letter web端的列名
     * @param $tltle_app_letter APP端的列名
     * @title  生成要发送的文件名称
     * @date 2017-09-05
     * @author CF
     */

public static function exportExcel($data,$tableheader,$excelFileName,$title_web,$title_app,$tltle_web_letter,$tltle_app_letter)
    {
        include dirname(dirname(__FILE__)) . '/vendor/phpoffice/phpexcel/Classes/PHPExcel.php';
        include dirname(dirname(__FILE__)) . '/vendor/phpoffice/phpexcel/Classes/PHPExcel/Style.php';
        include dirname(dirname(__FILE__)) .'/vendor/phpoffice/phpexcel/Classes/PHPExcel/Writer/Excel5.php';
        include dirname(dirname(__FILE__)) .'/vendor/phpoffice/phpexcel/Classes/PHPExcel/Style/Alignment.php';
        include dirname(dirname(__FILE__)) .'/vendor/phpoffice/phpexcel/Classes/PHPExcel/IOFactory.php';
        //创建对象
        $excel = new \PHPExcel();
        $tableCount = count($tableheader);
        //Excel表格式,这里简略写了8列
        $letter = ['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z'];
        /* sheet标题 */
        $excel->setActiveSheetIndex(0);
        $excel->getActiveSheet(0)->setTitle('WEB端日报表');
        $excel->setActiveSheetIndex(0)->setCellValue('A1', $title_web);

        //合并单元格
        $excel->getActiveSheet(0)->mergeCells('A1:'.$letter[$tableCount-1].'1');
        $excel->getActiveSheet(0)->mergeCells('A2:B2');
        //设置水平居中
        $excelA = $excel->getActiveSheet()->getStyle('A1:'.$letter[$tableCount-1].'1');
        $excelA->getAlignment()->setHorizontal(\PHPExcel_Style_Alignment::HORIZONTAL_CENTER);
        //单元格加边框
        $excelA->getBorders()->getAllBorders()->setBorderStyle(\PHPExcel_Style_Border::BORDER_THIN);

        //填充表头信息
        for ($i = 0; $i < $tableCount; $i++) {
            $excel->getActiveSheet()->setCellValue("$letter[$i]2", "$tableheader[$i]");
            //单元格加边框
            $excelB = $excel->getActiveSheet()->getStyle("$letter[$i]2");
            $excelB->getBorders()->getAllBorders()->setBorderStyle(\PHPExcel_Style_Border::BORDER_THIN);
            $excelB->getAlignment()->setHorizontal(\PHPExcel_Style_Alignment::HORIZONTAL_CENTER);
        }

        $excel->getActiveSheet(0)->setCellValue("A3", 'PC');
        $excel->getActiveSheet(0)->mergeCells('A3:A8');
        $excel->setActiveSheetIndex(0)->getStyle('A3')->getAlignment()->setVertical(\PHPExcel_Style_Alignment::VERTICAL_CENTER);
        $excel->setActiveSheetIndex(0)->getStyle('A3:A8')->getBorders()->getAllBorders()->setBorderStyle(\PHPExcel_Style_Border::BORDER_THIN);
        $excel->getActiveSheet(0)->setCellValue("A9", 'M');
        $excel->getActiveSheet(0)->mergeCells('A9:A14');
        $excel->setActiveSheetIndex(0)->getStyle('A9')->getAlignment()->setVertical(\PHPExcel_Style_Alignment::VERTICAL_CENTER);
        $excel->setActiveSheetIndex(0)->getStyle('A9:A14')->getBorders()->getAllBorders()->setBorderStyle(\PHPExcel_Style_Border::BORDER_THIN);

        for ( $m=0; $m < 2; $m++){
            $m == 0 ? $web_letter_i = self::NUM_I : $web_letter_i = self::NUM_I_TWO;
            foreach ($tltle_web_letter as $ke => $v) {
                $excel->getActiveSheet()->setCellValue("$letter[1]$web_letter_i", "$v");
                $excel->setActiveSheetIndex(0)->getStyle("B3:$letter[1]$web_letter_i")->getBorders()->getAllBorders()->setBorderStyle(\PHPExcel_Style_Border::BORDER_THIN);
                $web_letter_i++;
            }
        }


        $j = self::NUM;
        foreach ($data['web'] as $key => $item) {
            if ($j< count($data['web']) + self::NUM) {
                foreach ($item as $k => $val) {
                    if ($k == 'pc') {
                        $i = self::NUM_I;
                        foreach ($val as $ke => $v) {
                            $excel->getActiveSheet()->setCellValue("$letter[$j]$i", "$v");
                            $excel->getActiveSheet()->getStyle("$letter[$j]$i")->getBorders()->getAllBorders()->setBorderStyle(\PHPExcel_Style_Border::BORDER_THIN);
                            if ($v < 0){
                                $excel->getActiveSheet()->getStyle("$letter[$j]$i")->getFont()->setColor( new \PHPExcel_Style_Color(\PHPExcel_Style_Color::COLOR_RED ));
                            }
                            $i++;
                        }
                    } elseif ($k == 'm') {
                        $i = self::NUM_I_TWO;
                        foreach ($val as $ke => $v) {
                            $excel->getActiveSheet()->setCellValue("$letter[$j]$i", "$v");
                            $excel->getActiveSheet()->getStyle("$letter[$j]$i")->getBorders()->getAllBorders()->setBorderStyle(\PHPExcel_Style_Border::BORDER_THIN);
                            if ($v < 0){
                                $excel->getActiveSheet()->getStyle("$letter[$j]$i")->getFont()->setColor( new \PHPExcel_Style_Color(\PHPExcel_Style_Color::COLOR_RED ));
                            }
                            $i++;
                        }
                    }
                }
            }
            $j++;
        }


        //创建一个新的工作空间(sheet)
        $excel->createSheet();
        $excel->setactivesheetindex(1);
        /* sheet标题 */
        $excel->setActiveSheetIndex(1);
        $excel->getActiveSheet(1)->setTitle('APP端日报表');
        $excel->setActiveSheetIndex(1)->setCellValue('A1', $title_app);

        //合并单元格
        $excel->getActiveSheet(1)->mergeCells('A1:'.$letter[$tableCount-1].'1');
        $excel->getActiveSheet(1)->mergeCells('A2:B2');
        //设置水平居中
        $excelA_1 = $excel->getActiveSheet(1)->getStyle('A1:'.$letter[$tableCount-1].'1');
        $excelA_1->getAlignment()->setHorizontal(\PHPExcel_Style_Alignment::HORIZONTAL_CENTER);
        //单元格加边框
        $excelA_1->getBorders()->getAllBorders()->setBorderStyle(\PHPExcel_Style_Border::BORDER_THIN);

        //填充表头信息
        for ($i = 0; $i < $tableCount; $i++) {
            $excel->getActiveSheet(1)->setCellValue("$letter[$i]2", "$tableheader[$i]");
            //单元格加边框
            $excelB_1 = $excel->getActiveSheet(1)->getStyle("$letter[$i]2");
            $excelB_1->getBorders()->getAllBorders()->setBorderStyle(\PHPExcel_Style_Border::BORDER_THIN);
            $excelB_1->getAlignment()->setHorizontal(\PHPExcel_Style_Alignment::HORIZONTAL_CENTER);
        }

        $excel->getActiveSheet(1)->setCellValue("A3", '全部');
        $excel->getActiveSheet(1)->mergeCells('A3:A10');
        $excel->setActiveSheetIndex(1)->getStyle('A3')->getAlignment()->setVertical(\PHPExcel_Style_Alignment::VERTICAL_CENTER);
        $excel->setActiveSheetIndex(1)->getStyle('A3:A10')->getBorders()->getAllBorders()->setBorderStyle(\PHPExcel_Style_Border::BORDER_THIN);
        $excel->getActiveSheet(1)->setCellValue("A11", '安卓');
        $excel->getActiveSheet(1)->mergeCells('A11:A18');
        $excel->setActiveSheetIndex(1)->getStyle('A11')->getAlignment()->setVertical(\PHPExcel_Style_Alignment::VERTICAL_CENTER);
        $excel->setActiveSheetIndex(1)->getStyle('A11:A18')->getBorders()->getAllBorders()->setBorderStyle(\PHPExcel_Style_Border::BORDER_THIN);
        $excel->getActiveSheet(1)->setCellValue("A19", 'ios');
        $excel->getActiveSheet(1)->mergeCells('A19:A26');
        $excel->setActiveSheetIndex(1)->getStyle('A19')->getAlignment()->setVertical(\PHPExcel_Style_Alignment::VERTICAL_CENTER);
        $excel->setActiveSheetIndex(1)->getStyle('A19:A26')->getBorders()->getAllBorders()->setBorderStyle(\PHPExcel_Style_Border::BORDER_THIN);

        for ( $m=0; $m < 3; $m++){
            $m == 0 ? $web_letter_i = 3 : ($m == 1 ? $web_letter_i = 11 : $web_letter_i = 19);
            foreach ($tltle_app_letter as $ke => $v) {
                $excel->getActiveSheet()->setCellValue("$letter[1]$web_letter_i", "$v");
                $excel->setActiveSheetIndex(1)->getStyle("B3:$letter[1]$web_letter_i")->getBorders()->getAllBorders()->setBorderStyle(\PHPExcel_Style_Border::BORDER_THIN);
                $web_letter_i++;
            }
        }


        $j = self::NUM;
        foreach ($data['app'] as $key => $item) {
            if ($j< count($data['app']) + self::NUM) {
                foreach ($item as $k => $val) {
                    if ($k == 'all') {
                        $i = 3;
                        foreach ($val as $ke => $v) {
                            $excel->getActiveSheet(1)->setCellValue("$letter[$j]$i", "$v");
                            $excel->getActiveSheet(1)->getStyle("$letter[$j]$i")->getBorders()->getAllBorders()->setBorderStyle(\PHPExcel_Style_Border::BORDER_THIN);
                            if ($v < 0){
                                $excel->getActiveSheet(1)->getStyle("$letter[$j]$i")->getFont()->setColor( new \PHPExcel_Style_Color(\PHPExcel_Style_Color::COLOR_RED ));
                            }
                            $i++;
                        }
                    }elseif ($k == 'android') {
                        $i = 11;
                        foreach ($val as $ke => $v) {
                            $excel->getActiveSheet(1)->setCellValue("$letter[$j]$i", "$v");
                            $excel->getActiveSheet(1)->getStyle("$letter[$j]$i")->getBorders()->getAllBorders()->setBorderStyle(\PHPExcel_Style_Border::BORDER_THIN);
                            if ($v < 0){
                                $excel->getActiveSheet(1)->getStyle("$letter[$j]$i")->getFont()->setColor( new \PHPExcel_Style_Color(\PHPExcel_Style_Color::COLOR_RED ));
                            }
                            $i++;
                        }
                    } elseif ($k == 'ios') {
                        $i = 19;
                        foreach ($val as $ke => $v) {
                            $excel->getActiveSheet(1)->setCellValue("$letter[$j]$i", "$v");
                            $excel->getActiveSheet(1)->getStyle("$letter[$j]$i")->getBorders()->getAllBorders()->setBorderStyle(\PHPExcel_Style_Border::BORDER_THIN);
                            if ($v < 0){
                                $excel->getActiveSheet(1)->getStyle("$letter[$j]$i")->getFont()->setColor( new \PHPExcel_Style_Color(\PHPExcel_Style_Color::COLOR_RED ));
                            }
                            $i++;
                        }
                    }
                }
            }
            $j++;
        }


        //创建Excel输入对象
        ob_end_clean();
        $write = new \PHPExcel_Writer_Excel2007($excel);

/*     //直接导出
	header("Pragma: public");
        header("Expires: 0");
        header("Cache-Control:must-revalidate, post-check=0, pre-check=0");
        header("Content-Type:application/force-download");
        header("Content-Type:application/vnd.ms-execl");
        header("Content-Type:application/octet-stream");
        header("Content-Type:application/download");
        header('Content-Disposition:attachment;filename="' . $excelFileName.'-'.date('YmdHis') . '.xlsx"');
        header("Content-Transfer-Encoding:binary");
        $write->save('php://output');
*/


        $write->save($excelFileName); //保存服务器

    }

}
```