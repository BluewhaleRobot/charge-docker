# 自动充电模块上传的数据包

发布频率固定为25hz，即充电模块每秒自动通过串口发出25个数据包。

数据包格式：包头+长度+数据内容+结束符0x00

包头：占3个字节，0xcd 0xeb 0xd7

长度：占1个字节，长度不包括包头和长度本身字符，这个长度还包括字符串结
束符0x00，当前值固定为10*5=0x32。

内容：由10个4字节小端模式二进制表示的数字串联在一起构成,用空格分开。

|包头|长度|power_charger|空格|power_battery|...|数据n|...|time_stamp|结束符|
|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
|0xcd,0xeb,0xd7|0x32|4个字节|1个字节|4个字节|...|4个字节|...|4个字节|0x00|

完整数据包内容构成一个c语言结构体，结构体具体构成如下所示：

```c
typedef struct {
    float power_charger;      //当前充电极片电压，单位V。
    float power_battery;       //当前电池电压，单位V。
    float current;             //当前充电电流，单位A。
    unsigned int left_sensor1;  //左侧第一个红外传感器探测到的信号值，参考下文定义。
    unsigned int left_sensor2;  //左侧第二个红外传感器探测到的信号值，参考下文定义。
    unsigned int right_sensor1; //右侧第一个红外传感器探测到的信号值，参考下文定义。
    unsigned int right_sensor2; //右侧第二个红外传感器探测到的信号值，参考下文定义。
    float distance1;           //超声波模块测距值，单位mm。
    float distance2;           //保留，扩展用。
    unsigned int time_stamp;   //时间戳,单位为2毫秒，用于统计丢包率。
}UPLOAD_STATUS;
```