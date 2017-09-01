#### 发布任务接口 api

- 发起订单

```js
let url = `域名/app/index.php?i={$_W['uniacid']}&c=entry&m=imeepos_runner&do=open&__do=paylog.postOrder`;
let params = {};
params['desc'] = '下单留言';
params['serverId'] = '微信语音文件 ID, 由前端js录音并上传获得';
params['voice_time'] = '录音时长';
params['src'] = '微信录音上传服务器保存地址';
params['type'] = '用户自定义类型编号,系统默认0-1帮我送,2-3帮我买,4-5帮帮忙,6-7虚拟任务';
params['payType'] = ' wechat微信支付 credit余额支付 divider货到付款';
params['duration'] = ' 预计送达描述,文字描述如: **小时**分钟';
params['duration_value'] = ' 预计送达时间(分钟)';
params['total'] = '总金额(包含商品价格,即需要支付的金额)';
params['price'] = ' 帮买商品价格';
params['goods'] = ' 商品名称/任务标签/用户自定义';
params['end'] = array('lat'=>'精度','lng'=>'维度','realname'=>'姓名','mobile'=>'电话','poiname'=>'街道','detail'=>'详细地址'); // 目的地
params['start'] = array('lat'=>'精度','lng'=>'维度','realname'=>'姓名','mobile'=>'电话','poiname'=>'街道','detail'=>'详细地址');//起始地
params['baojia'] = array('value'=>'保额');// 保价
params['value'] = array('value'=>'****年**月**日 **:**');//预约
params['routeLen'] = '距离';//任务路程[起点-终点]
params['action'] = 'task'; //发布任务
params['tid'] = '订单编号'; //订单编号,提交后差重复,如果重复请更换订单号

var d = JSON.stringify(params);
var encrypted = Base64.encode(d);
let post['__input'] = {encrypted: encrypted};
$.post(url,post,(res)=>{
    //返回值
    console.log(res)// 返回结果
    //成功
    if(res.code === 1){
        // to do
    }else{
        // to do res.msg 失败原因
        weui.toast(res.msg)
    }
},'json');

```

### php处理支付成功,插入任务表
```php

$site = WeUtility::createModuleSite('imeepos_runner');
$site->payResult($params);

```