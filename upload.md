# 自动充电模块上传的数据包

发布频率固定为25hz，即充电模块每秒自动通过串口发出25个数据包。

数据包格式：包头+长度+数据内容

包头：占3个字节，0xcd 0xeb 0xd7

长度：占1个字节，长度不包括包头和长度本身字符，当前值固定为11*5=0x37。

内容：由11个5字节数字串联在一起构成,每5个字节表示一个变量。其中前4个字节是小端模式二进制表示，第5个字节由前4个字节求和后取低8位得到。

|包头|长度|power_charger|校验和1|power_battery|数据校验和2|数据n|校验和n|time_stamp|校验和11|
|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
|0xcd,0xeb,0xd7|0x37|4个字节|1个字节|4个字节|...|4个字节|...|4个字节|1个字节|

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
