---
title: 大众点评微信小程序端api探索（1）
tags:
  - 爬虫
  - 大众点评
  - 微信小程序
  - Charles
categories:
  - 爬虫教程
date: 2022-04-20 00:00:00
---


> 利用Charles抓包微信小程序的数据交互接口，分析调用接口所需的字段。
>
> 通过小程序反编译，拿到页面的处理逻辑，并尝试复现api接口的关键参数。

<!--more-->

### 能够获取点评商户列表的入口分析

目前潜在可以获取数据的四种途径：电脑Web、手机Web、官方App、微信小程序，本次主要分析微信小程序入口。

![dpSpider](./dianping-wxmp-api-spider-1/dpSpider.png "点评爬虫思维导图")

### 前置准备

安装[Charles](https://www.charlesproxy.com/ "Charles")，配置SSL代理，确保能接受到手机端的请求包，可[参考文章1](https://juejin.cn/post/6878113131990630413)、[参考文章2](http://zhaoxincheng.com/index.php/2021/03/14/ios%E7%B3%BB%E7%BB%9F%E6%8A%93%E5%8C%85%E5%85%A5%E9%97%A8%E5%AE%9E%E8%B7%B5%E4%B9%8B%E7%9F%AD%E9%93%BE/)。

### 抓包分析

打开手机Http代理、打开Charles，进入大众点评小程序的美食列表页面，会发现一个关键字为**` wxmapi`**的请求。

在小程序中，进一步筛选**`全部浦东新区-面包甜点-人气优先`**，可以捕捉到一个新的请求，不断下拉列表会产生新的请求。

<img src="./dianping-wxmp-api-spider-1/dpwxmapp.png" alt="mtsiReferrer_cmp" style="zoom:25%;" />



接口的请求形式是`GET`，需要分别解析**URL**中的关键字段及**请求头**中的信息。

#### URL分析

**`全部浦东新区-面包甜点-人气优先`**的请求URL如下：

```
https://m.dianping.com/wxmapi/search?optimus_uuid=17ff3533648c8-368bd0eeacae20-0-0-17ff3533648c8&optimus_platform=13&optimus_partner=203&optimus_risk_level=71&optimus_code=10&_=1649241517616&cookieId=pYXXCec7thu1_ppvwZAt4B6pU3oxTU6jYrd0iCmooyA&dpid=pYXXCec7thu1_ppvwZAt4B6pU3oxTU6jYrd0iCmooyA&token=&cityId=1&locateCityid=1&filterCategory=%5B10%5D&categoryId=117&sortId=2&myLat=31.255701065063477&myLng=121.67395782470703&start=10&limit=10&mtsiReferrer=%252Fpages%252Flist%252Flist%253F_%253D1649241517616%2526cookieId%253DpYXXCec7thu1_ppvwZAt4B6pU3oxTU6jYrd0iCmooyA%2526dpid%253DpYXXCec7thu1_ppvwZAt4B6pU3oxTU6jYrd0iCmooyA%2526token%253D%2526cityId%253D1%2526locateCityid%253D1%2526filterCategory%253D10%2526categoryId%253D10%2526sortId%253D0%2526range%253D-1%2526lat%253D31.255701065063477%2526lng%253D121.67395782470703%2526myLat%253D31.255701065063477%2526myLng%253D121.67395782470703%2526start%253D0%2526limit%253D10&regionId=5&parentRegionId=5&parentCategoryId=10&device_system=IOS&wxmp_version=9.4.0&_token=eJxNjMeOo2AQhN%2BFK5bgJ%2BMbySRjsDFxtAcyJppkG1b77uuZHa1GavVXVd2q39CoptAe3UHzBO0BRbAYAUiURglyByX%2FMwpgDM2yYAfFoytC%2Bw%2BcJncUYH99Bpe3%2F6BJdAcwnPm1%2ByEx4j2fP%2Br7BbpHSR0V2YRMWTQmJXL%2FMnnfp0kZdV3W%2FNTQDvp3L%2Fs2%2B1rQu6i9voverL8ZfXP%2B5nQrOmgPZdrqOsxsPjfujPBZDc%2BxXjGXRdxITuJVWypV6xak54OmnQwJ3FY8OCL%2BPKGoOr50RX%2FGy2bCVhgrmm1P%2BsYnQL0W5045eW6J0vHmOZVwkXu3og7DKaQGW5DIh1L2AR%2FBx8TVAxDm25rCy2q0%2BNJaMLLA%2BVLFMTfwA4rfqRs%2FR0OZ8o8uCAUf0FlvZD1xc2OgtOsrtb1c8En%2BcRmc5mlXfp3Rl1zo%2BynnTdwRj25xdGu6L6OaSkxlanKTLgDmSHgIG5YpMTNAe9%2BWFkcwmNfrxHS9FmDi4cZadBWQwlQh5qHqE4tQiamjvFkyk7kiGyNWXEyS1TvaokJt5samV5lNPJ%2FXTelkV3idjsHlSjTGSgdqmW5tUU8GtzSBbwFrZRwESygftMgpVL3GXm%2BaUCHrINXag9VVD2PkJlpGxRGTtKVx7soa9hrQDy8wZG%2BkbVE7YpHrkQE76rJfPNZiAD7jStd4uj9f3YSPcHh9wd4ictFAcf3QMdemP5sHWVawF4vlxdEPxC7VqrOi1TljPULnpqPjouJeV0kdYMQhTArcK52VbLHzmuecnPmX%2Bx368xd2rvkr
```

为了方便猜测各个参数的含义，同时获取**`海淀区-五道口-小吃快餐-口味最佳`**的URL，将两组URL参数横向对比如下：

<img src="./dianping-wxmp-api-spider-1/url_cmp.png" alt="url_cmp" style="zoom:50%;" />

其中`白色部分`是固定不变或可有可无的参数，`蓝色部分`是根据品类、区域、排序不同而发生变化的参数，`黄色部分`是含义不明、但必须有的参数。

**_**参数：看位数可能是以毫秒计的timestamp，记录了请求时间，尝试转换：

- 1649241517616 = 2022-04-06 18:38:37
- 1649262970040 = 2022-04-07 00:36:10

​	与实际访问时间相近

**token**参数：登录点评账号后返回的一个固定不变的字符串，在**当前这个会话中**固定不变，如果重新登录后会更新，可以近似认为不会变动。

**_token**参数：每次请求都会发生变化。猜测是由其他参数，以某种加密方式得到的字符串，用来在服务器端验证该请求确实是由小程序产生，而不是人工模拟产生的。

**mtsiReferrer**：是一个经过URL编码后的请求项，还原后的内容如下：

<img src="./dianping-wxmp-api-spider-1/mtsiReferrer_cmp.png" alt="mtsiReferrer_cmp" style="zoom:40%;" />

其中大部分参数，或是无关紧要、可有可无，或是有明确含义、容易构造，只有**_**和**token**的含义尚不清楚。

#### 请求头分析

大致分析完请求地址的构成，接下来分析**请求头的参数**：

<img src="./dianping-wxmp-api-spider-1/header.png" alt="header" style="zoom:40%;" />

大部分参数无关紧要或在多次请求中保持不变，只有**mtgsig**比较复杂，将字典拆分开，可以得到以下几个键值对：

<img src="./dianping-wxmp-api-spider-1/mtgsig.png" alt="mtgsig" style="zoom:40%;" />

**a1**:猜测为http协议版本号，不重要

**a2**:时间戳

- 1649261287957 = 2022-04-07 00:08:07
- 1649262991238 = 2022-04-07 00:36:31

**a3、a7、x0**:含义未知，但取值相同

**a4、a5、a6、d1**:含义未知，取值不同

#### 抓包分析小结

构造请求需要解析**每次发生变化**且**必须提供**的参数，如**时间戳**、**_token**、**mtgsig字典**。

只有通过找到请求头构造的逻辑，才有可能模拟小程序的数据请求。

#### api接口的返回内容分析

一次返回10个商户的列表信息，以其中单独某个商户为例，包含信息如下：

```json
{'type': 1,
 'shopInfo':{
   'adInfo': {......},
   'adShop': True,
   'authorityLabelColor': '#22A3DD',
   'bookType': '1',
   'branchName': '团结湖店',
   'categoryId': 4447,
   'categoryName': '融合烤肉',
   'cityId': 2,
   'defaultPic': ......,
   'name': '英雄故事地摊烤肉',
   'priceText': '￥98/人',
   'recommendReason': {'alignType': 0, 'bizId': 'index=1,source=ugccontent,type=ugccontent',
      'cornerRadius': 0,
      'fontSize': 12,
      'from': 'ad',
      'iconAlignType': 0,
      'iconCornerRadius': 0,
      'iconHeight': 0,
      'iconTextSpacing': 0,
      'iconType': 0,
      'iconWidth': 0,
      'keepStyle': 0,
      'lineNum': 0,
      'priority': 0,
      'text': '“这家地摊烤肉店就在东三环团结湖北路的路边，很好找”',
      'textType': 1,
      'type': 12},
     'regionName': '朝阳公园/团结湖',
     'reviewCount': '508条',
     'shopDealInfos': [{'countDownSeconds': 0,
       'dealGroupPrice': 0,
       'dealID': '731428516',
       'dealTitle': '45元 代金券1张',
       'dealType': 2,
       'iconType': 0,
       'iconUrl': {'height': 16,
        'iconType': 0,
        'priority': 0,
        'url': 'https://www.dpfile.com/sc/mobile/mapi-dp-web/search_list_coupon.png',
        'width': 16},
       'marketPrice': 0,
       'productType': 'spu',
       'subDealTitle': '',
       'weAppShowType': 0},
      {'countDownSeconds': 0,
       'dealGroupPrice': 0,
       'dealID': '730190633',
       'dealTitle': '168元 九宫格套餐',
       'dealType': 1,
       'iconType': 0,
       'iconUrl': {'height': 16,
        'iconType': 0,
        'priority': 0,
        'url': 'https://www.dpfile.com/sc/mobile/mapi-dp-web/search_list_tuan.png',
        'width': 16},
       'marketPrice': 0,
       'productType': 'deal',
       'subDealTitle': '',
       'weAppShowType': 0}],
     'shopPower': 45,
     'shopStateInformation': [],
     'shopType': 10,
     'tagList': [{'alignType': 0,
       'backgroundColor': '#F8F6F6',
       'borderColor': '',
       'cornerRadius': 0,
       'fontSize': 11,
       'from': 'ad',
       'icon': '',
       'iconAlignType': 0,
       'iconCornerRadius': 0,
       'iconHeight': 0,
       'iconTextSpacing': 0,
       'iconType': 0,
       'iconWidth': 0,
       'keepStyle': 0,
       'lineNum': 0,
       'priority': 0,
       'text': '有停车场',
       'textColor': '#111111',
       'textType': 0,
       'type': 12},
      {'alignType': 0,
       'backgroundColor': '#F8F6F6',
       'borderColor': '',
       'cornerRadius': 0,
       'fontSize': 11,
       'from': 'ad',
       'icon': '',
       'iconAlignType': 0,
       'iconCornerRadius': 0,
       'iconHeight': 0,
       'iconTextSpacing': 0,
       'iconType': 0,
       'iconWidth': 0,
       'keepStyle': 0,
       'lineNum': 0,
       'priority': 0,
       'text': '夜宵',
       'textColor': '#111111',
       'textType': 0,
       'type': 12},
      {'alignType': 0,
       'backgroundColor': '#F8F6F6',
       'borderColor': '',
       'cornerRadius': 0,
       'fontSize': 11,
       'from': 'ad',
       'icon': '',
       'iconAlignType': 0,
       'iconCornerRadius': 0,
       'iconHeight': 0,
       'iconTextSpacing': 0,
       'iconType': 0,
       'iconWidth': 0,
       'keepStyle': 0,
       'lineNum': 0,
       'priority': 0,
       'text': '有包厢',
       'textColor': '#111111',
       'textType': 0,
       'type': 12},
      {'alignType': 0,
       'backgroundColor': '#F8F6F6',
       'borderColor': '',
       'cornerRadius': 0,
       'fontSize': 11,
       'from': 'ad',
       'icon': '',
       'iconAlignType': 0,
       'iconCornerRadius': 0,
       'iconHeight': 0,
       'iconTextSpacing': 0,
       'iconType': 0,
       'iconWidth': 0,
       'keepStyle': 0,
       'lineNum': 0,
       'priority': 0,
       'text': '卫生间',
       'textColor': '#111111',
       'textType': 0,
       'type': 12},
      {'alignType': 0,
       'backgroundColor': '#F8F6F6',
       'borderColor': '',
       'cornerRadius': 0,
       'fontSize': 11,
       'from': 'ad',
       'icon': '',
       'iconAlignType': 0,
       'iconCornerRadius': 0,
       'iconHeight': 0,
       'iconTextSpacing': 0,
       'iconType': 0,
       'iconWidth': 0,
       'keepStyle': 0,
       'lineNum': 0,
       'priority': 0,
       'text': '可预订',
       'textColor': '#111111',
       'textType': 0,
       'type': 12}],
     'starScore': '4.60',
     'hasTagList': True,
     'serviceTags': [{'text': '订',
       'url': 'https://www.dpfile.com/sc/mobile/mapi-dp-web/search_list_book.png',
       'width': '32rpx',
       'height': '32rpx'}],
     'labelStyle': 'linear-gradient(to right bottom, rgba(34, 163, 221, 0.7), #22A3DD)',
     'shopIndex': 0,
     'listStart': 0,
     'shopUuid': 'H1fl4Bpnb5OTVLOO',
     'shop_uuid': 'H1fl4Bpnb5OTVLOO',
     'navData': {'url': '/pages/detail/detail?shopUuid=H1fl4Bpnb5OTVLOO&shopType=10&shopStyle=multipic'},
     'msource': 'mina_food'}}
```

比较有用的字段有branchName、categoryName、name、regionName、reviewCount、shopUuid、starScore、shopPower，还有一些团购券名称，与web和app的商户列表页所展示的信息基本相同。

### 模拟请求压力测试

在进一步探索关键参数的构造逻辑之前，先对接口的请求频次进行压力测试。

用同一组header和url，不间断请求并判断返回数据是否正确，直到403或商户列表返回错误。

```python
import requests, json, time
from requests.packages.urllib3.exceptions import InsecureRequestWarning
requests.packages.urllib3.disable_warnings(InsecureRequestWarning)
headers = {
          'Host':	'm.dianping.com',
          'Referer':	'https://servicewechat.com/wx734c1ad7b3562129/329/page-frame.html',
          'User-Agent': 'Mozilla/5.0 (iPhone; CPU iPhone OS 11_3 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Mobile/15E216 MicroMessenger/8.0.2(0x18000239) NetType/WIFI Language/zh_CN',
          'openid':	'pYXXCec7thu1_ppvwZAt4B6pU3oxTU6jYrd0iCmooyA',
          'mtgsig':	r'{"a1":"1.1","a2":1649576956647,"a3":"80w9yz927u045u8uz4yu7603332u67uu82uu64655z787978v3655246","a4":"9b97e1b1c93c1ad9b1e1979bd91a3cc95633e5898f29d940","a5":"g3sPYh2cISZaqTNdrtbOPsfP3Q3O6u8+lVKzyvTXTbIe32t6U3IjC/Y/Tm87bnu/KNqPEzBmjUk6g3nChUyp3WEp0sl2FyUtD62LVpSDP8vFbFB/f/sCukwARwtYsSYFk7LakyzeF5cFEYQ99WDNv4BIgchcC3kMez90NkUPW1L/PYWktWMPs574v1qIxfecamp5Kia89K3CXOzXnSMdaEuCsCkeZjUNSh6G0JPLeuoV0pQFp5Nx5mEnXobOE69MoluJWCiFlARaWzvWtN8zKPTNlpFQHbjn//9+JXEEKJdW4r75ijRe2YkAXvhGaW==","a6":"w1.0jdLnqnOZlRK1axXRMjSRs0cmq6XE4uJUA0RqfZsYD3kaMPtZhoxEORJIMUSL1qU7w6/1j1FddF69gYsBPQu/mI3LM/H90x/koo8ZFLb7iNXM9VSyvC45NfnCtPxmt/kwloZqDVffTQev4cxxUNJqPdsQ31q7u8ZlpneqIo5dOgAO10fdMpEjDDCNCBPVAgEZut5WT0gMmO6ve9Iby8npcX76yaHE+LV1qAWeTPvPqKAiQk9Qbi5DTrSg4kHtjXy+Kymys+Idcdv1s4DWz4+GDzgrmzXLxiydnBPvuYYc5NP3zleE6UvKhyL1WphWUr+cfUfmpQFfAcYwZ6JCw7x4V8T2cuD9vt54y2rPoBx8M4h/PmiB30HH+ak7mWgmJJHhU1QCMQZu2YkGQTgTXwNZpRla2O8TSpCTyCNpzMdJ9oqlMCadjgsS2eUuuoD3R0aM3Zx3C9gkc8/3T0jdpXdOqCo47Cd1kFH53/gYdUHTJqOc7IjyIVz+n3PrgbuWW1m6","a7":"wx734c1ad7b3562129","x0":3,"d1":"322ded3253281ee792e15be4dcbd598b"}',
          'wechatversion':	'8.0.2',
          'platformversion':	'11.3',
          'channel':	'weixin',
          'minaversion':	'9.4.1',
          'dpid':	'pYXXCec7thu1_ppvwZAt4B6pU3oxTU6jYrd0iCmooyA',
          'openidPlt':	'oPpUI0UW5pnHRZxIld9vh5vcRWqU',
          'platform':	'Android',
          'sdkversion':	'2.16.1',
          'Accept-Language':	'zh-cn',
          "Content-type": "application/json",
          'minaname':	'dianping-wxapp',
          'channelversion':	'8.0.2',
          'Accept':	'*/*',
          'Accept-Encoding':	'br, gzip, deflate',
          'token': '80af43d7470060da45b5a462fa1c492fd1ec9faa541cc32cdf8eda99894d4483d43f1a9e76183bbc9a1c3def6db848b12fabc6c345382ed6abcd7480992df2002b7db8e89045c0e9c2ee2baf030b3bd63f000557225b3d9b5349d0876b2f693b'
}

url = r"https://m.dianping.com/wxmapi/search?optimus_uuid=17ff3533648c8-368bd0eeacae20-0-0-17ff3533648c8&optimus_platform=13&optimus_partner=203&optimus_risk_level=71&optimus_code=10&_=1649576957098&cookieId=pYXXCec7thu1_ppvwZAt4B6pU3oxTU6jYrd0iCmooyA&dpid=pYXXCec7thu1_ppvwZAt4B6pU3oxTU6jYrd0iCmooyA&token=80af43d7470060da45b5a462fa1c492fd1ec9faa541cc32cdf8eda99894d4483d43f1a9e76183bbc9a1c3def6db848b12fabc6c345382ed6abcd7480992df2002b7db8e89045c0e9c2ee2baf030b3bd63f000557225b3d9b5349d0876b2f693b&cityId=2&locateCityid=1&filterCategory=%5B10%5D&categoryId=10&sortId=0&regionId=0&start=0&limit=10&mtsiReferrer=%252Fpages%252Flist%252Flist%253F_%253D1649576957098%2526cookieId%253DpYXXCec7thu1_ppvwZAt4B6pU3oxTU6jYrd0iCmooyA%2526dpid%253DpYXXCec7thu1_ppvwZAt4B6pU3oxTU6jYrd0iCmooyA%2526token%253D80af43d7470060da45b5a462fa1c492fd1ec9faa541cc32cdf8eda99894d4483d43f1a9e76183bbc9a1c3def6db848b12fabc6c345382ed6abcd7480992df2002b7db8e89045c0e9c2ee2baf030b3bd63f000557225b3d9b5349d0876b2f693b%2526cityId%253D2%2526locateCityid%253D1%2526filterCategory%253D10%2526categoryId%253D10%2526sortId%253D0%2526regionId%253D0%2526start%253D0%2526limit%253D10&device_system=IOS&wxmp_version=9.4.1&_token=eJxVjEmPm1AQhP8LV1vCLI8HI%2BVgY3bbeNhhNIfHjjGLzU6U%2Fx5PModEavVXVd2qn9hTibG33RbrO%2ByNYGgOQIYDFMfRWyz6L4MkSW6x8OkcsbcPCoItQ3CfX4Hx8h8Q7LYESbGf238kSb%2Fm60d5vWAtikqUJR3eJegZ5Xj7x6RNE0c5quvk%2Fq%2FGttjfe95UyZ%2BFvYoq61X0YvlN9M3%2Bm12R1dgblqirY3f9QK77d1yN9kRBHdhC0q568T5kxbtNS%2BQ%2BUNw8Osrnyu6rd1XU2Tq9U8ZNXWHl7jM%2B3vnwQdSDl02c4CxEbWajdrn7dSrMbRuHxHxpwnNGNA15iQx9yq%2FXI57uUBjEOGSzacWHWuaSIJ1Pq%2ByPV2qDCzWdloq21JK4GSQ6hb4s%2Bl6LzjaFQ%2BCMNzDys3BjzlJ8ZQjHwkuWH4NstpaLF0vaGMap6D9atVrKkS5yYE29OTJnspMpoQVz4abH1V%2FDQ1GzHPLu4gw1wG4aHiiPlUvFwqYnRBmmc0hYEM1rt9%2FbcCWeJuI3DdE5swGnHIHQGfRQcu1kYA0tccqmgWvhrLVcFaKHeFNCZ8f2ZFl%2B5jfdMkvxpLiSEISgKhnBOAA3Mu%2FaSbenw2NoNdXOxPGE%2BEVUBQR96eBHvgoyKQutXnNZPToX1G1p5C7XxESTwsdsUGSDGzxPZfitfqq51aTe4uaPkoZxZbrVVTk4wXE6R1bPmyBo8QnJuwAXjs68VjI6%2BfrlOPZUwsOAhMpevGThZfPsfNAybIfunbRIQhrRXL4DXdjHHvL0hPMX9Vns2FZRkTFl6DEYU%2BVwa66hhmOTquY4QuPRaYhzYqTw61BXjFehfCWr1rLQY%2Bk3hgk3%2B1wX7U75gf36Dd1RFac%3D"
cnt = 1
start_time = time.time()
while True:
    r=requests.get(url, headers=headers, verify=False)
    time.sleep(0.05)
    try:
        dat = json.loads(r.content.decode('utf-8'))
    except:
        end_time = time.time()
        break
    try:
        mch_cnt = len(dat['data']['list'])
    except:
        end_time = time.time()
        break
    if mch_cnt != 10:
        end_time = time.time()
        break
    print('压力测试第{0}次，累计返回商户数量{1}个，返回code{2}，当次商户列表数量{3}......'.format(cnt, cnt*10,r.status_code,mch_cnt))
    cnt += 1
    if cnt>10000:
        end_time = time.time()
        break

print('-------测试结束-------')
print('耗时{0}秒，共请求{1}次，累计返回商户{2}个'.format(int(end_time-start_time), cnt, cnt*10))
```

第一轮测试，共请求3580次后出现错误，耗时约2500秒，假如不考虑休息时间，完成400w商户的爬取需80小时左右。

但是经过若干轮测试后，每轮能爬取的商户数量逐渐减少。

最后一轮测试，耗时503秒，共请求833次，累计返回商户8330个，其后开始出现滑块验证，返回内容如下：

```json
{'msg': '您的网络好像不太给力，请稍后再试',
 'code': 406,
 'customData': {'verifyPageUrl': 'https://verify.meituan.com/v2/app/general_page?action=spiderindefence&requestCode=af7323bc14f94bfda66cd1c79001c6f7&platform=13&adaptor=auto&succCallbackUrl=https://optimus-mtsi.meituan.com/optimus/verifyResult',
  'riskLevel': '71',
  'imageUrl': 'https://verify.meituan.com/v2/captcha?action=spiderindefence&request_code=af7323bc14f94bfda66cd1c79001c6f7',
  'requestCode': 'af7323bc14f94bfda66cd1c79001c6f7',
  'verifyUrl': 'https://optimus-mtsi.meituan.com/optimus/verify?request_code=af7323bc14f94bfda66cd1c79001c6f7'}}
```

根据链接中的关键字`spiderindefence`，可能击中反爬逻辑。

暂时没有更好的解决方案。

### 反编译小程序

鉴于压力测试发现比较容易击中反爬逻辑，可能很难完成400w商户的爬取工作，中止进一步探索。

如果要进一步挖掘关键参数的构造特征，可以通过反编译微信小程序的源代码，进一步分析背后的js逻辑。

反编译小程序可以参考[文章1](https://blog.csdn.net/qappleh/article/details/99717651)、[文章2](https://blog.csdn.net/septwolves2015/article/details/114405694)、[文章3](http://xuedingmiao.com/blog/xcx_unpack.html)、[文章4](http://xuedingmiao.com/blog/xcx_unpack_sub_package.html)、[文章5](https://ukm028kzyr.feishu.cn/docs/doccnOSxESTO6H2MjfTG7ePftyd)，能否成功看运气，以及微信未来更新小程序编译机制也会导致现有工具失效。

有现成的反编译[工具](https://pan.baidu.com/s/1-xTHQiZC4s4GeDEXDnMKSw?pwd=vq4b#list/path=%2F)，但是需要收费，也可以按照上述文章去Github找源码进行尝试。

点评最新版本的小程序，反编译成功后的[源代码下载](WXAPP_DP_crack.zip "大众点评反编译源码下载")。

反编译成功后，用[微信小程序IDE](https://developers.weixin.qq.com/miniprogram/dev/devtools/download.html)打开，我们主要关注美食页面的js逻辑，可以按如下配置编译：

<img src="./dianping-wxmp-api-spider-1/编译路径.png" alt="header" style="zoom:40%;" />

小程序的基本开发逻辑，可参考[官方文档](https://developers.weixin.qq.com/miniprogram/dev/framework/)，但我们不需要关注细节，只需要找到**美食页下拉后**调用的函数入口。

根据官方文档描述，小程序页面下拉到底后，会自动调用页面的`onPullDownRefresh`函数，如图所示：

<img src="./dianping-wxmp-api-spider-1/jsapi.png" alt="header" style="zoom:40%;" />



后续请求参数的构造可以从这个入口开始分析。













