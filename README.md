<h1 align="center">MicroPython WS2812 Led Clock</h1>

<p align="center"><img src="https://img.shields.io/badge/Licence-MIT-green.svg?style=for-the-badge" /></p>

### 项目介绍

基于`安信可ESP-C3-12F`模组，搭配`WS2812`矩阵灯珠，用于显示当前时间

### 硬件介绍

硬件电路使用 [立创EDA](https://lceda.cn/) 设计，完全适合新手小白使用，PCB 板是在 [深圳嘉立创](https://www.jlc.com/) 下单打样的，本着薅羊毛的原则，板子尺寸限制在了`10cm * 10cm`以内，原理图文件可以在 [立创开源硬件平台](https://oshwhub.com/Walkline/kou-sou-dian-zhen-shi-zhong) 查看，这里不再赘述

> 主控模组选用了`安信可 ESP-C3-12F`，并非常用的`ESP32-WROOM-32D`

### 软件介绍

#### 工作流程

软件整体工作流程大致如下：

* 第一次启动或复位配网信息时，进入`配网模式`
* 如果输入信息有误，尝试联网`1分钟`后会自动重启并重复以上步骤
* 联网成功后保存`配网信息`（写入`sta_config.py`文件）并自动重启
* 重启后联网校时，并显示当前时间，之后
* 每间隔`5秒`刷新一次时间
* 每间隔`3秒`检测一次环境亮度，如果环境亮度发生变化则自动调整屏幕亮度
* 每间隔`1小时`进行一次联网校时

#### 按键功能

板子上集成了`4个`功能按键和`1个`复位按键，目前按键功能如下：

* `SW1`：长按`3秒`清除`配网信息`（`sta_config.py`文件）并重启
* `SW2`：暂无
* `SW3`：循环切换屏幕显示亮度，但还会根据环境亮度自动调节，仅做调试用
* `SW4`：屏幕显示开关
* `EN`：手动重启设备

### 关于配网

`MicroPython`不提供`SmartConfig`相关功能，也就是`Touch`和`AirKiss`配网功能，尝试了把`IDF`示例代码编译到固件中调用，目前已经可以使用以上两种方式进行配网了

> `Touch`方式需要使用乐鑫提供的 [EspTouch for Android](https://github.com/EspressifApp/EsptouchForAndroid/releases)

项目中提供的固件已经集成了半血版`SmartConfig`，用微信或 app 配网时不会提示配网成功，只用于获取`ssid`和`password`，使用方法和代码如下：

```python
from utime import sleep
import network
import smartconfig

station = network.WLAN(network.STA_IF)
station.active(True)

smartconfig.start()

# 手机连接 2.4G 无线网络（重要）
# 关注 安信可科技 微信公众号，点击 应用开发→微信配网，或
# 关注 乐鑫信息科技 微信公众号，点击 商铺→Airkiss 设备
# 输入 Wi-Fi密码 后点击 连接，等待即可

while not smartconfig.success():
    sleep(0.5)

print(smartconfig.info())

# 强制退出 微信配网 或 Airkiss 设备

>>> ('ssid', 'password')
```

> 如果长时间获取不到信息，则需要手动重启设备并重试

### Led 显示

由于点阵数量严重不足，经过思考决定使用如下形式显示当前时间

![13点35分](./images/led_display.png)

* 最大的区域显示当前时间的`小时`数字
* 最右侧区域显示当前时间分钟数的`十位数`
* 最下边区域显示当前时间分钟数的`个位数`

因此，上图显示的时间为`13点35分`，是不是又能看时间又能活动大脑，一举两得了？

### 计划增加的功能

* 目前配网时没有任何提示信息，准备增加一个提示画面（或动画）

### 存在的问题

* 为了省事没有给每一颗 LED 搭配电容，当全部 LED 以白色最大亮度（255）点亮时，会因为供电不足导致无法继续工作，所以解决方案是降低最大亮度的上限值，目前仅使用`10%`亮度，不使用遮光板的前提下亮度已经足够

* `SmartConfig`偶尔出现卡死的情况，不使用串口调试无法发现，不过无线连接本来就是个概率事件，也能说得过去。。。。吧

### 相关项目

* [MicroPython WS2812 Research](https://gitee.com/walkline/micropython-ws2812-research)

### 合作交流

* 联系邮箱：<walkline@163.com>
* QQ 交流群：
	* 走线物联：[163271910](https://jq.qq.com/?_wv=1027&k=xtPoHgwL)
	* 扇贝物联：[31324057](https://jq.qq.com/?_wv=1027&k=yp4FrpWh)

<p align="center"><img src="https://gitee.com/walkline/WeatherStation/raw/docs/images/qrcode_walkline.png" width="300px" alt="走线物联"><img src="https://gitee.com/walkline/WeatherStation/raw/docs/images/qrcode_bigiot.png" width="300px" alt="扇贝物联"></p>
