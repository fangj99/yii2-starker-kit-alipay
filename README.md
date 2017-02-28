# yii2-starker-kit-alipay

Integration of alipay to yii2-starter-kit/advanced yii2 template

##1. Put folder Alipay under vendor folder
##2. Setup your own alipay information in the vendor/Alipay/AlipayPay.php
```
 public $partner = '2088xxxxxxxxxxx';
 public $seller_email = 'yyyy@xxxxxxxxx.com';
 public $key = 'sdgstxxxxxxxxxxxxxxxx';
 public $notify_url = 'https://xxxxxxxxx.com/cart/notifycall';
 public $return_url = 'https://xxxxxxxxx.com/cart/returncall';
```

##3 In controller, this sample is CartController, create two actions 
###public function actionNotifycall(): 
This one need to deal with all the transactions with the order, alipay server will retry this url couple of times

###public function actionReturncall(): 
this one just for redirection, for the result normally will be fail, alipay server will retry this url only one time

After creat these two action, you can test with the url directly, it will show fail and redirection

####Show Fail: https://xxxxxxxxx.com/cart/notifycall

####Redirection: https://xxxxxxxxx.com/cart/returncall


```
use AlipayPay;

	
	public function actionNotifycall(){
		
		$alipay = new AlipayPay();
		$verify_result = $alipay->verifyNotify();
				
		//\Yii::info($verify_result, 'cart');
		
        if ($verify_result) {//验证成功
            //商户订单号
            $out_trade_no = \Yii::$app->request->post('out_trade_no');
            //交易状态
            $trade_status = \Yii::$app->request->post('trade_status');
			//\Yii::info($trade_status, 'cart');

            if($trade_status == 'TRADE_FINISHED'||$trade_status == 'TRADE_SUCCESS') 
            {
                //\Yii::info($out_trade_no, 'cart');
				$order_model = Order::findOne($out_trade_no);
				$order_model->status = Order::STATUS_PAID;
				$order_model->save();  // equivalent to $model->update();
				
            }
            
            //返回状态
            return "success";
        } else {
            //验证失败
            return "fail";
        }
		
	}
		
	public function actionReturncall(){
		//跳转到不同页面 for this is normally fail

		$this->redirect(['order/query']);
	}
```

