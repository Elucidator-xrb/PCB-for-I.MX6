## 注意事项

改前记得pull，改后记得及时commit + push，一个master分支到底了。AD09本身似乎不支持VCS，要是冲突了感觉稍有些难整。

改用了AD2022，不过协同使用似乎还是弄不起来；尝试了Windows局域网共享文件也不work，就先串行设计吧



## 电源流向梳理

#### 外界电源

外界电源接口：**J5**，**J6**

和一个奇怪的**+12V** （哪儿来的？）

产出电源12VIN

#### 12VIN

12VIN -> 5V_core

12VIN -> 5V_EX

#### 5V_core

5V_core -> GEN_3V3 （通过U15 - LX引脚）

！ 5V_core <-> 3P3V （U15 - EN引脚应该是 使能 的意思）

=> Connector: U10 - S153~S158

去耦电容电路： C137 C138 C139

#### 5V_EX

=> HDMI: U13-5V_SUPPLY

=> LVDS: 仅有的J11的供电

=> USB HUB: U9 - VIN  U14 - VIN

=> POWER LIGHT: 其一红灯

#### GEN_3V3

=> UART：U1 - VCC   J16, J17接口的电源

=> HDMI:  U13 - LV_SUPPLY ...	J13

=> USB HUB

=> RTC: U12 - VBAT, VDD

=> KEY: U2 - VCC, nRST

=> USER LIGHT 

=> BUTTON

GEN_3V3 -> RGMII_3V3 （转换电路，附带去耦电容，Ethernet处）

GEN_3V3 -> VCC_1V2 （Ethernet处）

#### VCC_1V2

VCC_1V2 -> RGMII_1V2（转换电路，附带去耦电容）

VCC_1V2 去耦电容电路（Ethernet处）

#### 3P3V - （哪儿来？）

=> POWER LIGHT: 两外两个绿灯

=> BOOT 拨码开关

=> Connector: U10 - S149~S152

#### 2P5V - （哪儿来？）

2P5V 去耦电容电路（Ethernet处）

=> Ethernet: U8 - nINT, nRESET, MDIO, DVDDH（共3个管脚）

=> Connector: U10 - P150, P149

#### RGMII类

RGMII_3V3

RGMII_1V2

用于Ethernet

#### USB类 - （通过USB口外界来嘛）

USB_OTG_VBUS

USB_H1_VBUS



## 工作日志

#### 11/23

rain

#### 11/24 

xrb

根据引脚宽度，线宽一般设为12mil，过孔12/24mil；

目前操作中感觉，把当前想布局布线的模块中相关元件（各种电阻电容）的连线显示，其他连线隐藏，会方便清爽些（快捷键'N'）

GND、电源的焊盘可以先不连，最后一律打过孔到GND/PWR层（这也是4层PCB比2层PCB简单的地方）

自动布线尚未尝试，感觉也挺棘手，电阻电容都还没布局

进行了HDMI模块相关小元件布局和布线，未完成，且有些疑问

#### 11/25

wz

布局+布线左下方usb相关电容电阻

#### 11/26

rain

把右侧的button和connector的电阻进行了布局和接口间的连线。

疑问：所谓的电源去耦电容的排布，即什么是放在使用的旁边

#### 11/27

xrb

调整了按键KEY15 - R15和Connector的连线方式，因为其挡到了Ethernet - U8 的布线

修改了几乎所有KEY上方的布线，不知道改的行不行，使其不至于横跨半块板而影响U8与Connector附近过孔的设置

**发觉原理图好些标识倒过来了**，很奇怪；同时发现**Ethernet中R120电阻标成了R125（？！）**，好在R125是我们没选的CAN模块上的电阻，所以无所谓了不改了，**以后R120就是原理图的R125**

排列归类了一下Ethernet有关电源的部分，但不是很清楚流向以及布局

LVDS就接了一根，还不能最终确定布线

#### 11/28

wz

调整rtc及rtc电池位置，调整数码管及其控制芯片 （U2）位置，连接uart部分，连接右侧usb-otg，调整部分过孔尺寸，优化connector-ethernet 及usb-otg布线。

主要剩余lvds及power。

#### 11/29

xrb

连接JTAG部分；管理层设置显示了全部layer，导致一些多余层显示出来（麻烦不想删了），若想关掉可以隐藏Mechanical21、18、23；把Connector的位置给锁了，终于不会误移动，元件也不会被覆盖了

尝试打过孔到GND，发现这个也挺棘手，不会；为此增加过孔的几种类型（1:2 1:3 2:4 3:4），但不知是不是这么用的；然后发现快捷键产生的default过孔变成bind3:4了，干脆全删了；

连接key数码管部分; 连接了USB漏下的几个电阻；SW1是系统启动的拨码开关，不应该放在Connector能被核心板覆盖的地方，所以移动了它的位置并连了线

梳理了下电源产生和流向的问题，还在等老师回复。还是剩 LVDS-差分线、 电源一大块