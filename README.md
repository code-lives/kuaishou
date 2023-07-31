<p align="center">
<a href="https://packagist.org/packages/code-lives/kuaishou" target="_blank"><img src="https://img.shields.io/packagist/v/code-lives/kuaishou?include_prereleases" alt="GitHub forks"></a>
<a href="https://packagist.org/packages/code-lives/kuaishou" target="_blank"><img src="https://img.shields.io/github/stars/code-lives/kuaishou?style=social" alt="GitHub forks"></a>
<a href="https://github.com/code-lives/kuaishou/fork" target="_blank"><img src="https://img.shields.io/github/forks/code-lives/kuaishou?style=social" alt="GitHub forks"></a>

</p>

|                                         第三方                                          | token | openid | 支付 | 回调 | 退款 | 订单查询 | 解密手机号 | 分账 | 模版消息 |
| :-------------------------------------------------------------------------------------: | :---: | :----: | :--: | :--: | :--: | :------: | :--------: | :--: | :------: |
| [快手小程序](https://mp.kuaishou.com/docs/develop/server/epay/interfaceDefinition.html) |   ✓   |   ✓    |  ✓   |  ✓   |  ✓   |    ✓     |     ✓      |  ✓   |    ✓     |

## 安装

```php
composer require code-lives/kuaishou 1.0.0
```

### ⚠️ 注意

> 金额单位分 100=1 元

> 返回结果 array 由开发者自行判断

# 预下单

```php

//引入命名空间
use Applet\Assemble\Kuaishou;

// 小程序下单
$pay= Kuaishou::init($config)->set("订单号","金额","描述",'openid', 'access_token')->getParam();

```

### Token

```php
$code="";
$data= Kuaishou::init($config)->getToken();
```

### Config

| 参数名字   | 类型   | 必须 | 说明                                |
| ---------- | ------ | ---- | ----------------------------------- |
| app_id     | int    | 是   | 小程序 appid                        |
| app_secret | int    | 是   | 小程序 secret                       |
| notify_url | string | 是   | 回调地址                            |
| settle_url | string | 是   | 结算回调地址，没有就默认 notify_url |
| type       | int    | 是   | 类目                                |

### Openid

```php
$code="";
$data= Kuaishou::init($config)->getOpenid($code);
```

| 返回参数    | 类型   | 必须 | 说明          |
| ----------- | ------ | ---- | ------------- |
| session_key | string | 是   | session_key   |
| openid     | string | 是   | 用户 open_id  |
| result      | string | 是   | 状态 1 是成功 |

### 解密手机号

```php
$data= Kuaishou::init($config)->decryptPhone($session_key, $iv, $encryptedData);
echo $phone['phoneNumber'];
```

### 订单查询

```php
$data = Kuaishou::init($config)->findOrder("订单号",$access_token);
```

### 退款

| 参数名字      | 类型    | 必须 | 说明         |
| ------------- | ------- | ---- | ------------ |
| out_trade_no  | string  | 是   | 平台订单号   |
| out_refund_no | strging | 是   | 自定义订单号 |
| refund_amount | int     | 是   | 退款金额     |
| reason        | string  | 是   | 退款原因     |
| access_token  | string  | 是   | access_token |
| attach        | string  | 否   | 自定义       |

```php
$orders = [
        'out_order_no' => $order['out_order_no'],
        'out_refund_no' => $order['out_refund_no'],
        'reason' => $order['reason'],
        'attach' => $order['attach'],
    ];
$data= Kuaishou::init($config)->applyOrderRefund($order);
```

### 结算

| 参数名字      | 类型   | 必须 | 说明         |
| ------------- | ------ | ---- | ------------ |
| out_order_no  | string | 是   | 平台订单号   |
| out_settle_no | string | 是   | 自定义订单号 |
| reason        | string | 是   | 退款原因     |
| access_token  | string | 是   | access_token |
| attach        | string | 否   | 自定义       |

```php
//注意 需要设置回调 notify_url  在config 设置 settle_url 如果没有 默认为 notify_url
$orders = [
        'out_order_no' => $order['out_order_no'],
        'out_settle_no' => $order['out_settle_no'],
        'reason' => $order['reason'],
        'attach' => $order['attach'],
    ];
$data= Kuaishou::init($config)->settle($order,$access_token);
```

### 订单信息同步

| 参数名字             | 类型   | 必须 | 说明                     |
| -------------------- | ------ | ---- | ------------------------ |
| out_biz_order_no     | string | 是   | 展示在用户端的唯一订单号 |
| out_order_no         | string | 是   | 小程序预下单支付订单号   |
| open_id              | string | 是   | 订单对应的用户 open id   |
| order_create_time    | string | 是   | 订单创建时间             |
| order_status         | string | 否   | 订单状态                 |
| order_path           | string | 是   | 小程序路径               |
| product_cover_img_id | string | 是   | 图片 id                  |

```php

$order = [
        'out_biz_order_no' => '',
        'out_order_no' => '',
        'open_id' => '',
        'order_create_time' => time(),
        'order_status' => 6,
        'order_path' => '',
        'product_cover_img_id' =>'',
    ];
$data = Kuaishou::init($config)->synchronousOrder($order, $token);
```

### 图片上传(订单信息同步)

```php
$data = Kuaishou::init($config)->imgUpload('图片路径', $token);
```

### 模版消息

```php
$data = [
    "open_id" => "",
    "tpl_id" => "",
    "page" => "pages/index/index",
    "data" => [
        'key1' =>  "第一个",
        'key2' =>  "第二个",
        'key3' =>  "第三个",
    ]
];
$data= Kuaishou::init($config)->sendMsg($data,$token);
$data=[
    "err_no" => 1001,
    "err_tips" => "该用户未订阅"
]
```

### 支付回调

```php
$pay = Kuaishou::init($config);
$status = $pay->notifyCheck(); //验证
if ($status) {
    $order = $pay->getNotifyOrder(); //订单数据
    //$order['data']['out_order_no']//平台订单号
    echo json_encode(['result' => 1, 'message_id' => $order['message_id']]);exit;
}
```
