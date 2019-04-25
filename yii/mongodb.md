```
    $res = TotalFeedsAll::updateAll(
        ['adclick' => $adclick],
        ['_id' => $id]
    );

根据id修改adclick 如果该字段不存在则新增 存在则修改

示例：
    public static function updateInfo($adclick,$id){
        //返回更新的条数
        $res = TotalFeedsAll::updateAll(
            ['adclick' => $adclick],
            ['_id' => $id]
        );
        return $res;
    }

    //将当天total_feeds的点击量 计算并存储到total_feeds_all
    public function actionFeedsToday(){

        $date = date('Y-m-d',time());
        $media_all = TotalFeedsAll::find()->where(['date' => $date])->asArray()->all();
        foreach ($media_all as $k => $val) {
            $media_all[$k]['adclick'] = 0;
        }
        //点击量
        $kwid = TotalFeeds::find()->where(['date' => $date])->asArray()->all();
        foreach ($kwid as $key => $value) {
            foreach ($media_all as $k => $v) {
                if (($value['date'] == $v['date']) && ($value['name'] == $v['name']) && ($value['request_type'] == $v['request_type']) && ($value['category'] == $v['category'])) {
                    $media_all[$k]['adclick'] += $value['adclick'];
                    continue;
                }
            }
        }

        foreach ($media_all as $k => $val){
            self::updateInfo($val['adclick'],$val['_id']);
        }
        unset($media_all);
        unset($kwid);
        echo "success";
    }
```