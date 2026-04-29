# URL Schemes & Intent收集与整理

## 更新时间 2026.04.29

## 来源与相关文章：

   [小贝塔教程资源导航：安卓软件界面Shell](https://xbta.cc/shell)
   
   酷安[Anywhere-](https://www.coolapk.com/apk/com.absinthe.anywhere_)与[快捷方式](https://www.coolapk.com/apk/com.syyf.quickpay)的评论区
   
   [少数派：入门 iOS 自动化：读懂 URL Schemes](https://sspai.com/post/44591)
   
   [少数派：URL Schemes 使用详解](https://sspai.com/post/31500)
   
   [云端筑梦师的博客：完整参数URL Scheme大全查询](https://www.ydzms.com/archives/58/)

   自己摸索与收集

### 1. 小米系统

可作为磁贴使用，如比较常用的小爱同学

**小米妙播：** 包名`miui.systemui.plugin`，类名`miui.systemui.miplay.MiPlayDetailActivity`

**小米妙享：** 包名`com.milink.service`，类名`com.miui.circulate.world.CirculateWorldActivity`

**小爱同学：** 包名`com.miui.voiceassist`，类名`com.xiaomi.voiceassistant.CTAAlertActivity`，IntentAction为`android.intent.action.ASSIST`

**垃圾清理：** `#Intent;action=miui.intent.action.GARBAGE_CLEANUP;package=com.miui.cleanmaster;end`

**应用数据清理：** `#Intent;component=com.miui.cleanmaster/com.miui.optimizecenter.deepclean.appdata.AppDataActivity;package=com.miui.cleanmaster;S.enter_homepage_way=phone_manage;end`

**原生电池优化界面：** 包名`com.android.settings`，类名`.Settings$HighPowerApplicationsActivity`

**最近任务：** 包名`com.miui.home`，类名`.recents.RecentsActivity`

**原生系统内存显示：** 包名`com.android.settings`，类名`.Settings$MemorySettingsActivity`

**设置默认应用：** 包名`com.android.settings`，类名`.Settings$AdvancedAppsActivity` 或者使用`#Intent;action=android.settings.MANAGE_DEFAULT_APPS_SETTINGS;package=com.android.settings;component=com.android.settings/.Settings%24AdvancedAppsActivity;end`

**手机激活信息：** 包名`com.miui.cloudservice`，类名`.ui.DeviceActivationInfoActivity`

**手机管家的网络助手：** #Intent;action=miui.intent.action.NETWORKASSISTANT_ENTRANCE;component=com.miui.securitycenter/com.miui.networkassistant.ui.NetworkAssistantActivity;end

**打开某个应用的应用信息设置：** #Intent;component=com.miui.securitycenter/com.miui.appmanager.ApplicationsDetailsActivity;S.package_name=应用包名;end

**小米社区 每日积分签到：** `mio://web.vip.miui.com/page/info/mio/mio/checkIn?ref=longpressshortcuts`

**直接拨打电话号码** Shell命令 `am start -a android.intent.action.CALL -d tel:电话号码`

**按出拨号盘暗语** Shell命令，比如在root设备上按出Sui的管理界面 `am broadcast -a android.provider.Telephony.SECRET_CODE -d "android_secret_code://784784"`

**直接打开开发者选项中的无线调试页面：** `#Intent;action=android.intent.action.MAIN;component=com.android.settings/.SubSettings;i.%3Asettings%3Asource_metrics=39;S.%3Asettings%3Ashow_fragment_title=%E6%97%A0%E7%BA%BF%E8%B0%83%E8%AF%95;S.%3Asettings%3Ashow_fragment=com.android.settings.development.WirelessDebuggingFragment;B.%3Asettings%3Ais_second_layer_page=false;i.%3Asettings%3Ashow_fragment_title_resid=-1;i.page_transition_type=0;end`

**原生文件管理App：** 包名`com.google.android.documentsui`，类名`com.android.documentsui.LauncherActivity`

**原生语言设置界面：** 包名`com.android.settings`，类名`.Settings$SystemDashboardActivity`

### 2. 支付宝 `com.eg.android.AlipayGphone`
  常用小程序一般为`alipays://platformapi/startapp?appId=数字`
  
  可能后面会跟一些参数

  或是扫一扫`alipayqr://platformapi/startapp?saId=10000007&qrcode=淘宝或支付宝相关的网址`，比如下面的快递取件码

  #### 数字来源1：
  小程序若能分享，则右上角分享出去，得到链接后，在浏览器里打开，得到一串网址后，使用URLdecode解码后可得到appid（通用方法）

  #### 数字来源2：
  小程序若不能分享，且设备能root，可去`/data/data/com.eg.android.AlipayGphone/files/nebulaInstallApps/`路径下，打开每个文件夹里的Manifest.xml（可得到名称和数字），或去查看`/data/data/com.eg.android.AlipayGphone/databases/nebulax_app.db`数据库文件（2021年8月尝试有效，现可能已经失效）
  
  #### 数字来源3：
  他人的分享，比如上面的来源与相关文章

  #### 常用：
|名称|URL Schemes|
|-------------|-------------|
|扫一扫|alipays://platformapi/startapp?appId=10000007|
|付钱/收钱|alipays://platformapi/startapp?appId=20000056|
|出行|alipays://platformapi/startapp?appId=20002047|
|付款码|alipays://platformapi/startapp?appId=20000056|
|收款码|alipays://platformapi/startapp?appId=20000123|
|乘车码|alipays://platformapi/startapp?appId=200011235|
|Apple专区|alipays://platformapi/startapp?appId=2021003125614874&page=pages/index/index|
|快递取件码|alipayqr://platformapi/startapp?saId=10000007&qrcode=`https://market.m.taobao.com/app/cn-yz/multi-activity/authCode.html?needStartTake=true&packageId=null&entrance=logisticDetail`|
|花呗|alipays://platformapi/startapp?appId=20000199|
|神奇海洋|alipays://platformapi/startapp?appId=2021003115672468|
|蚂蚁森林|alipays://platformapi/startapp?appId=60000002|
|滴滴出行|alipays://platformapi/startapp?appId=2019062865745088|
|车来了|alipays://platformapi/startapp?appId=2019110768932964|
|高德实时公交|alipays://platformapi/startapp?appId=2018051660134749|
|铁路12306|alipays://platformapi/startapp?appId=2018122062695048|
|会员领积分|alipays://platformapi/startapp?appId=20000160&url=%2Fwww%2FmyPoints.html|
|市民中心|alipays://platformapi/startapp?appId=20000178|
|菜鸟裹裹|alipays://platformapi/startapp?appId=2021001141626787|
|运动|alipays://platformapi/startapp?appId=20000869|
|饿了么|alipays://platformapi/startapp?appId=2021001110676437|
|口碑|alipays://platformapi/startapp?appId=2021001108603619|
|酒店出行|alipays://platformapi/startapp?appId=2021001111632299|
|电影演出|alipays://platformapi/startapp?appId=2021001110648550|
|红包|alipays://platformapi/startapp?appId=88886666|
|话费充值|alipays://platformapi/startapp?appId=10000003|
|电子医保凭证|alipays://platformapi/startapp?appId=20002069&fromSource=quickWay&source=shortcut#Intent;component=com.eg.android.AlipayGphone/com.alipay.mobile.quinox.LauncherActivity;B.directly=true;B.fromDesktop=true;end|
|PockytShop 礼品卡充值(慎用)|alipays://platformapi/startapp?appId=2021003191605547|
|支付宝AQ健康管家|alipays://platformapi/startapp?appId=2060090000317856|
|大润发会员码|alipays://platformapi/startapp?appId=2019030663402947&page=pages/member-card/index|
|大润发购物卡|alipays://platformapi/startapp?appId=2019030663402947&page=pkg-my/pages/shopping-card-code/index|
|好想来会员码|alipays://platformapi/startapp?appId=2021005130648524&page=%2Fpages%2Fentry%2Findex%3FshareType%3D1%26pageName%3DMEMBER_CODE|

### 3. 淘宝 `com.taobao.taobao`
|名称|URL Schemes|
|-------------|-------------|
|淘宝一键价保|tbopen://m.taobao.com/tbopen/index.html?h5Url=https%3A%2F%2Fpages.tmall.com%2Fwow%2Fz%2Fmarketing-tools%2Fmkt%2Fprice-center%3FdisableNav%3DYES%26sourceChannel%3D2%26spm%3Da311a.7996332.0.0&action=ali.open.nav&module=h5&bootImage=0&afcPromotionOpen=false|
|淘宝 快递身份码|taobao://market.m.taobao.com/app/cn-yz/multi-activity/authCode.html?needStartTake=true&packageId=null&entrance=logisticDetail|
|淘宝人生|taobao://pages.tmall.com/wow/z/tblife/solution2/game-tblife?wh_biz=tm&disableNav=YES&uniqueTag=hdtblife&from=mytaobaoqd#/home|
|淘宝店铺的跳转|taobao://shop.m.taobao.com/shop/shop_index.htm?shopId=店铺数字id|


### 4. 网易云音乐 `com.netease.cloudmusic`
|名称|URL Schemes|
|-------------|-------------|
|网易云榜单|orpheus://ranking|
|网易云歌曲跳转|orpheus://song/一串数字 （分享出来的song?id=后面的数字）|
|网易云签到|orpheus://rnpage?component=rn-cloudshell-center|
|网易云音乐 每日推荐|orpheus://songrcmd|
|网易云音乐 听歌识曲|orpheus://identify（iOS版本为orpheuswidget://recognize）|
|网易云音乐 我喜欢的音乐|orpheus://playlist/417760994|
|网易云音乐 私人雷达|orpheus://playlist/3136952023|
|网易云音乐 私人FM|orpheus://radio|

### 5. QQ音乐 `com.tencent.qqmusic`
|名称|URL Schemes|
|-------------|-------------|
|每日30首|qqmusic://qq.com/ui/gedan?p={"id":"4487164108"}|
|我的收藏|qqmusic://qq.com/ui/myTab?p=%7B%22tab%22%3A%22fav%22%7D|
|听歌识曲|qqmusic://qq.com/ui/recognize|

### 6. 京东 `com.jingdong.app.mall`

常用命令openapp.jdmobile://virtual?params={"category":"jump","des":"m","url":"网址"}

|名称|URL Schemes|
|-------------|-------------|
|京东一键价保|openapp.jdmobile://virtual?params={"category":"jump","des":"m","url":"`https://h5.m.jd.com/babelDiy/Zeus/2RePMzTqg6UoffvMwtwVeMcnPGeg/index.html?defaultViewTab=0&appId=cuser&type=25#/`"}
|京东一键价保2(瞎折腾试出来的，慎用)|openapp.jdmobile://virtual?params={"category":"jump","des":"m","url":"`https://h5.m.jd.com/pb/016454810`"}
|京东一键价保3(瞎折腾试出来的，慎用)|openapp.jdmobile://virtual?params={"category":"jump","des":"m","url":"`https://h5.m.jd.com/pb/016454811`"}
|领签到的京豆|openapp.jdmobile://virtual?params={"category":"jump","des":"m","url":"`https://bean.m.jd.com`"}
|白条账单|openapp.jdmobile://virtual?params={"category":"jump","des":"m","url":"`https://m.jr.jd.com/rn/BTMonthBill/index.html?page=page_bill_page&monthlyBillType=0&channelName=&channelcode=Z01`"}
|订单列表|openapp.jdmobile://virtual?params={"category":"jump","des":"orderlist"}|
|所有账单|android-app://com.jingdong.app.mall/https/bill.jd.com/bill/mList.html?source=61&entrance=5#Intent;component=com.jingdong.app.mall/.open.BrowserActivity;end|

### 7. 高德地图 `com.autonavi.minimap`
|名称|URL Schemes|
|-------------|-------------|
|实时公交|amapuri://realtimeBus/home?from=shortcut&netAcc={"path":"amapservice://amap_bundle_realbus/RequestScheduleService","requestKeys":"busStation"}|

### 8. 抖音 `com.ss.android.ugc.aweme`
|名称|URL Schemes|
|-------------|-------------|
|清理缓存|snssdk1128://polaris/lynxview/?surl=`https://lf-dy-sourcecdn-tos.bytegecko.com/obj/byte-gurd-source/1325/gecko/resource/aweme_ug_popups/pages/uninstallShortcutPage/template.js?hide_loading=1&hide_nav_bar=true&use_bdx=1&hide_loading=1&hide_nav_bar=1&use_bdx=1&should_full_screen=1&use_xbridge3=1&use_bullet_container=1&bdx_launch_mode=remove_same_page&bdx_tag=uninstall_page_ug&buriedInfo={"business":"touch","business_tag":"uninstall","title":"卸载","user_touch":"user_touch"}&enable_pad_adapter=true&launch_mode=os_3dtouch&enter_to=shortcut_retain&item_type=shortcut_retain`|
|商城订单|sslocal://webcast_lynxview?type=fullscreen&url=https%3A%2F%2Flf-webcast-sourcecdn-tos.bytegecko.com%2Fobj%2Fbyte-gurd-source%2Fwebcast%2Ffalcon%2Fdouyin%2Fecommerce_orders_douyin%2Fapp_new%2Ftemplate.js%3Fenter_from%3Dug_ecom_widget.order_logistics_info_tab%26page_name%3Dorder_list_page%26tab_id%3D0&page=ecom_mall&hide_nav_bar=1&hide_status_bar=0&status_bar_color=black&show_close=0&trans_status_bar=1&hide_loading=1&enable_share=0&web_bg_color=ffffff&show_back=0&inject_config_channel=ecom_mall_awemelite&inject_scene=default&enable_latch=1|

### 9. 拼多多 `com.xunmeng.pinduoduo`
|名称|URL Schemes|
|-------------|-------------|
|多多买菜包裹列表|pinduoduo://com.xunmeng.pinduoduo/psnl_goods_help.html?_t_module_name=assistant_pickup|
|多多买菜驿站快递列表|pinduoduo://com.xunmeng.pinduoduo/mdkd/package|
|拼多多内浏览某个网址|pinduoduo://com.xunmeng.pinduoduo/`https://某个网址(比如多多驿站m.pinduoduo.net/mdkd/package)`|
|百亿补贴|pinduoduo://com.xunmeng.pinduoduo/brand_activity_subsidy.html?_pdd_fs=1&_pdd_tc=ffffff&_pdd_sbs=1&refer_share_channel=copy_link&refer_share_form=text|
|拼多多下单的包裹列表|pinduoduo://com.xunmeng.pinduoduo/psnl_goods_help.html?_t_module_name=assistant_search&pr_page_from=scheme&tab_id=400&tab_type=1&list_index=10|
