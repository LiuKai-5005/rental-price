# 使用Python脚本，每日爬取租房价格

在美国租房，特别是旧金山湾区，房租占据每个月的开销的大头。租房的房源通常来自公寓或者私人房东。很多人会选择专门的出租公寓，这种公寓只租不卖，经营也比较专业化。大部分出租公寓会有官网可以查询价格/空房，并且可以在线签约，规模小一些的出租公寓可能就没有官网，租房信息只挂在第三方租房网站上。下面记录了一下我已经实现的过程。

## 需求分析：
- 问题在于公寓的在线价格每天都在变化，户型的选择也很多，心仪的户型太贵或者抢不到。
- 一个房间在available date之后，如果还没有租出去，价格也会随之调整（通常随时间递减）。如果能随时掌握公寓的房租价格的动向，能捡到一个很好的deal，住起来岂不是美滋滋。
- 因此，可以写了一个基于`Flask`框架的`python`脚本查询- 所有可供签约的房间价格和其他信息，并使用task scheduler每天query房租信息，然后保存到本地或者推送至手机、邮箱、微信等。

## 问题分析：
- 这是主动`pull`信息，存在所有`pull model`的问题，即无法第一时间感知到价格的变化，所以只能人为设置一个`pull frequency`不断地去试探是否有新动态。
- 推送到手机或者邮箱还没有实现（TODO）

## 简介
  `Essex`和`Avalon`都是湾区比较知名的专业连锁出租公寓，以我感兴趣的`Essex`公寓举例。

- 这家连锁公寓的官网是:
  https://www.essexapartmenthomes.com/apartments/san-jose/century-towers/floor-plans-and-pricing

- 官网价格区截图:
![price](https://user-images.githubusercontent.com/54691613/147894380-ad5f1766-0cec-4615-a37b-86ecaa8233cb.png)

# 分析
为了每天能自动接收这座公寓的价格信息，这个脚本大概需要做如下几件事:
  - 脚本自动抓取所有待租公寓房间的价格、大小、入住日期、及其他重要信息
  - 提取如上信息拼接消息文本，并发送给自己 TODO

# 实现
一开始我准备用爬虫爬网页文本，并分析文本提取价格信息。但是转念一想我应该先查看网站是不是自己有API提供这些信息给前端，按`F12`打开Inspect，发现确实有这个API通过`GET`请求获取这些信息。为了确保这个API不需要鉴权一类验证，我复制API网址在隐身窗口打开，同样可以拿到数据。

因此实现方法应该是让脚本发一个GET请求触发推送出租信息。因此，我们可以通过requst url 拿到`json`格式的payload之后，就可以获取该`GET` request所需要的全部信息。
https://www.essexapartmenthomes.com/EPT_Feature/PropertyManagement/Service/GetPropertyAvailabiltyByRange/543205/2022-03-03/2022-03-17
![api](https://user-images.githubusercontent.com/54691613/147894396-d1bf9e13-4356-41c1-8d2e-80b7ba967ca6.png)

经过仔细研究后，发现这个获取租房信息的GET请求本身还支持一些参数，如最低价格，最高价格，预期入住日期等，接下来就是代码实现了

# 同理
Avalon公寓的`GET`的URL是：https://api.avalonbay.com/json/reply/ApartmentSearch?communityCode=CA049&min=2000&max=4000&desiredMoveInDate=2022-03-01T07:00:00.000Z
可以指定价格区间以及入住日期，使用`Postman`拿到`Json`之后就可以拿到公寓价格等信息了。
![avalon](https://user-images.githubusercontent.com/54691613/148008618-c3a0d534-8073-4c30-b1f5-a6b936b550ab.png)

## 代码
见repo

## 结果
这里仅展示了`C3`户型的价格走势图
![house_price_2022-01-02_2022-03-04](https://user-images.githubusercontent.com/54691613/147894436-3faccae7-2438-4f16-bf55-6a929f9a27fb.png)


