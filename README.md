# 米波文档

#### 服务器及环境要求
- php > 5.3
- mysql > 5.6
- 微擎 > 0.8

#### 如果页面打开较慢,请开启gzip
* [nginx/gizp 开启方法](nginx-gizp.md)

#### 支付配置位置
* 微擎0.8 - 1.0 : 功能选项/支付参数/微信支付/
* 微擎1.0 - ~ : 公众号/支付参数/微信支付/修改

#### 阿里大于短信
* 老版大于短信: 需新建应用获取 appkey 和 appsecret
* 新版大于短信: 登陆阿里云/右上角(鼠标放在账号上)/accesskeys/创建即可获得 appkey 和 appsecret

#### 模板消息
* 微信公众平台/模板消息/添加模板即可获取模板编号/填写到对应位置


#### 老版本用户升级新版流程说明
* 微擎上找到自己所在版本-更新到最新
* 打开更新链接 域名+/addons/imeepos_runner/install/download.php?module=imeepos_runner
* 点击立即更新,更新结束后清理缓存
* 运行升级脚本, 域名+web/index.php?c=site&a=entry&&do=clear&m=imeepos_runner
* 升级脚本,仅用于老版本升级新版本使用,这个脚本运营过程中切勿尝试!
s
#### 更新升级及安装方法
* 域名+/addons/imeepos_runner/install/download.php?module=imeepos_runner 
* [imeepos_runner]为对应模块代号

#### 更新链接打开提示未授权
* 进商业群-改昵称-看文档-联系群主私聊域名-授权通过-刷新页面即可
* 更换域名,老域名由于某些原因不用了,可以更换么? 可以更换,群红包50元,份数随意!

#### 更新链接打开404了
*  [下载此文件上传到addons/imeepos_runner/](http://meepo.com.cn/attachment/install.zip)
* 注意解压后检查文件有无嵌套,window下解压有时候会出现install/install的嵌套情况,请上传内部install文件夹

#### 更新过程中出现 -- 暂无更新文件 
* 原因: 文件检查过程中途终止
* 解决方法: 域名+/addons/imeepos_runner/install/initFiles.php?module=imeepos_runner 
* 如提示404或空白请重复上一步
* [imeepos_runner]为对应模块代号

#### 模块代号说明
* imeepos_runner - 小明跑腿
* imeepos_runner_plugin_im - 跑腿 IM 插件
* imeepos_runner_plugin_tixian - 跑腿提现插件
* imeepos_runner_plugin_bbs - 同城圈子
* imeepos_coach - 健身房


#### 配置管理说明
* 登陆模块后台/角色管理/设置自己微信账号为管理员 OR 站长
* 进入前台页面/清理缓存/即可看到管理员 OR 站长特权
* 设置及配置口诀: 想改哪里在哪里点[快捷菜单/装修]
* 前台管理入口: [快捷菜单/管理]


#### 前台文件说明 [2017/8/15日以后更新文件]
* 文件命名 ***.版本号.min.js,文件目录 [模块目录/template/mobile/***.版本号.min.js]
* 如更新不满意,手工修改[模块目录/template/mobile/index.html]中***.版本号.min.js文件到对应版本号,即可还原!