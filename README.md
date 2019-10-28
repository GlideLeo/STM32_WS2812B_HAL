# STM32_WS2812B_HAL
[![LICENSE](https://img.shields.io/badge/license-Anti%20996-blue.svg)](https://github.com/996icu/996.ICU/blob/master/LICENSE)

## 介绍
WS2812B是目前最流行的RGB全彩LED控制方案，只需要一根信号线即可实现控制，并且可以方便的级联，详细的介绍可以参考数据手册。主控芯片使用STM32F030F4P6,价格不到两元，非常实惠。

## WS2812B控制
驱动时序很简单，遵循下表的要求即可，容差范围比较大
![](http://qiniu.datasheep.cn/20191029015743.png)

LED的色彩控制格式如下
![](http://qiniu.datasheep.cn/20191029015905.png)

使用单片机的SPI接口可以完成WS2812B的信号的脉宽控制，WS2812B要求一个字节的持续时间应约为1.25 µs，因此一个SPI位应持续约0.156 µs，SPI时钟速度为6.4 MHz。在单片机时钟为48MHz的情况下，设置SPI预分频器值为8，使SPI时钟为6MHz，在WS2812B时序容差范围内。
|位|时间|
|-|-|
|1位|0.166 µs|
|2位|0.333 µs|
|3位|0.499 µs|
|4位|0.666 µs|
|5位|0.833 µs|
|6位|0.999 µs|
|7位|1.166 µs|
|8位|1.333 µs|

其中2位匹配 0.35 µs±0.15 µs,5位匹配 0.9 µs±0.15 µs.则可做如下定义：
``` c
const uint8_t zero = 0xc0;  //11000000
const uint8_t one =  0xf8;  //11111000
```

## CubeMX设置

* 配置SYS-Debug Serial Wire
* 配置RCC Crystal/Ceramic Resonator (非必需，可使用内部晶振)
* 配置SPI1 如图配置，注意NSSP默认是开启的
![](http://qiniu.datasheep.cn/20191029022007.png)
* 配置DMA 
![](http://qiniu.datasheep.cn/20191029022154.png)
* 配置时钟为48MHz

## 硬件
* STM32F030F4P6核心板
* WS2812B全彩LED灯带
* CMSIS-DAP 
* USB便携式逻辑分析仪

## 软件
* Keil MDK v5
* CubeMX

## 测试
测试使用了16个灯，用逻辑分析仪可以观察控制信号，时钟信号是不使用的。
![](http://qiniu.datasheep.cn/20191029022723.png)
截取一个Byte来看，可以明显看出来0和1，周期为1.34 us.
![](http://qiniu.datasheep.cn/20191029023110.png)

效果：
![](http://qiniu.datasheep.cn/LED_Test.gif)



## 参考
[Adresowalne diody WS2812B na STM32](https://msalamon.pl/adresowalne-diody-ws2812b-na-stm32-cz-1/)
