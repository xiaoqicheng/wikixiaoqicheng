### GUZZLE
```
try {
        $config = ['base_uri'=>'https://api.weixin.qq.com/cgi-bin/token'];
        $client = new Client($config);
        $query = [
            'appid' => config('weapp')['app_id'],
            'secret' => config('weapp')['app_secret'],
            'grant_type' => 'client_credential',
        ];

        $response = $client->get('', ['query' => $query]);
        $data = json_decode($response->getBody()->getContents(), true);
        Cache::store('redis')->put('access_token', $data['access_token'], ($data['expires_in']-300)/60);
        return $data['access_token'];
    } catch (\Exception $exception) {
        \Log::error('Get access_token error:' . $exception->getMessage());
        throw new ServiceUnavailableHttpException($exception->getMessage());
    }

利用Guzzle发送post请求 并接收返回数据

   try {
    $config = ['base_uri'=>'https://api.weixin.qq.com/datacube/getweanalysisappiddailysummarytrend?access_token='.$access_token,
                'headers'=>['Content-Type'=>'application/json']];
    $client = new Client($config);
    $query = [
        'begin_date' => date("Ymd",strtotime("-1 day")),
        'end_date' => date("Ymd",strtotime("-1 day")),
    ];
  
    $response = $client->post('', ['json'=>$query]);   键要写json
    return json_decode($response->getBody()->getContents(), true);
} catch (\Exception $exception) {
    \Log::error('Get datacube error:' . $exception->getMessage());
    throw new ServiceUnavailableHttpException($exception->getMessage());
}
```
