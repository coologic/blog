---
title: "Apollo自动驾驶50讲笔记"
tags:
  - Apollo
categories:
  - 自动驾驶
date: 2019-05-04 23:46:08
---

# 安全

ISO26262标准

ASIL D级别硬件要求：故障率10 fit 10亿小时10个故障

安全分为两个
1. safety :自己出问题，设计问题（软硬件）、随机问（硬件），由26262规定行业标准，并非法律，规定比人的死亡率低十倍
2. security 别人的问题，被入侵 

判断标准exposure/controllable/separely
s=人员伤亡概率。e=事情发生频率。c=问题驾驶员是否有能力接管和控制 →最后定为安全等级

# Apollo
四层框架
- cloud service platform
- - hd map
- - simulation
- - data platform
- - security
- - ota
- - dueros
- open software platform
- - map engine
- - localization
- - perception
- - planning
- - control
- - end-to-end
- - hmi
- - runtime framework（ROS）
- - rtos 实时核linux
- reference hardware platform计算平台传感器
- - computing unit
- - gps、imu
- - camera
- - lidar 905，1550纳米可见光范围外激光，905接近可见光范围，为了安全功率都不高，可见范围低
- - radar
- - hmi device
- - black box
- reference vechicle platform 线控车
- - drive-by-wire vechicle
- 量产解决方案
- - 接驳小巴
- - 自主泊车
- - 无人作业小车

# 阿波罗硬件开发平台
V2X 车-人 -车  -基础设施  之间的通讯
生物电传感器在方向盘 判断驾驶员身体状况

传感器应用

超声波雷达|摄像头|毫米波雷达|激光雷达|组合导航
-----|-----|-----|-----|-----
自动巡航ACC|o|o|o|o| 
紧急制动AEB| |o|o|o| 
行人检测PD||o|o|o| 
交通标志识别TSR||o| | | 
车道偏离警告LDW| |o| | | 
泊车辅助PA|o|o|o|o| 
自动驾驶AP L3-5|o|o|o|o|o

L1L2怕误检，主体是人。L3+怕漏检，主体是车

L4目标城市+高速路。

计算刹车距离：
柏油路摩擦系数0.6

现在自动驾驶系统反应时间500毫秒以内

硬件反应时间，液压、气刹车0.3-0.8秒，计算刹车距离，在120km/s下需要130米左右，要求在150米探测距离可保证安全

激光雷达，角分辨率：atan(物体高度/距离)/2/pi*180  可以在0.4度分辨率的情况下，路锥距离32米高45cm检测到100米外的人，0.1度可检测到400米外的人，但不意味着可识别出人

未来可能趋势：
摄像头前端+激光雷达 直接输出RGB信息+空间信息
计算单元：当前是集中式一个，未来可能将传感器数据线融合到sensor box中先进行融合 然后再给后端计算平台，不同传感器之间还有时间戳同步在box中完成
开发asic芯片，将slam算法直接做到芯片中（soc），嵌入到传感器，实现边缘计算。mobileye等公司做的摄像头

自动驾驶三大部分：感知决策控制
线控三大部分：油门加速度、刹车减速度、转向。
，MK C1集成了液压和制动模块，用MK100 对C1作为备份元件。
EPS电子助力转向
线控油门，刹车踏板位置传感器，EMS发动机控制系统

2.0线控对原车ADAS系统借用，破解原车CAN总线协议。3.0线控从车底层开发，全自主化，可考虑冗余和备份需求。

# 无人车车辆自定位技术入门与应用

坐标系得到车辆位置和姿态，可以局部坐标系也可以全局世界地图，坐标系六自由度xyz，横滚，俯仰，航向

定位系统指标要求：精度误差均值<10cm、鲁棒性最大误差小于30cm、场景要求全天候

感知、决策能力不强，所以需要有定位和高精地图辅助

定位方法：基于电子信号定位（wifi gnss。。。）、环境特征匹配（IMU惯性测量单元 odometry）、航迹推算（LiDar Radar Camera与高精地图匹配）

## 激光定位
实时激光点云与预先建立的2D概率模型匹配

## 视觉定位
基于特征点定位由于环境光线变化不太适合，通过标志牌、红绿灯、车道线等高精地图信息进行定位，将视觉图像通过deeplearning标出车道线等实时信息做匹配

## IMU
加速度计包含了重力加速度，需要剔除。无人车一般用一二十万的设备，在尝试用更低价位的设备，逐渐接近小飞机。消费级IMO→光线IMU。特点短时精度高，十几万设备五六百毫秒内可保证精度，误差会随时间累积，输出频率一般在200Hz以上，独立的设备不需要其他信息输入即可工作。

## 多传感器融合定位
KF：卡尔曼滤波器，用于状态误差作为核心模块，接收惯性导航的递推作为时间同步，接收视频、点云等作低频的状态更新。

# 无人车基础知识
坐标系：左右手坐标系，常用右手
几何变化：旋转矩阵3*3矩阵，平移3*1矩阵，九个元素计算时候用，优化时候不用要不计算量大（ps应该是路径轨迹规划时候不用）。
四元数与欧拉角

坐标系：
1. ECI地心惯性坐标系（i系），Z指向北极，XY指向赤道分别指向两个恒星，坐标系不随地球自转变化
2. ECEF地心地固坐标系（e系），x指向子午面交线，y与x夹角90度，随地球自转转动，自转过程会有雨I系重合时刻
3. 当地水平坐标系，一般用ENU坐标系（导航坐标系n系），东、北、天三个方向，一般作为导航坐标系使用
4. UTM坐标系，UTM投影：通用横轴墨卡托投影。根据经纬度划分，将地球划分为60个区域，纬度也划分，在高纬度和低纬度用的公式不同因为是球体
5. 车体坐标系（b系），可以和当地坐标系匹配，求得位置，方向为右x前y上z RFU坐标系
6. IMU坐标系
7. 相机坐标系
8. 激光雷达坐标系，会转换成IMU坐标系

# 百度无人车定位技术

探路者、无人驾驶微循环车阿波龙、无人驾驶物流车新石器

GPS精度一般5-10米
载波定位技术辅助GPS，可以用RTK或者PPP技术，无人车用RTK，通过临近基站对接收机做校准，5秒内可提供厘米级定位精度
PPP是通过基站计算误差后传递给卫星，卫星消除误差后给接收机

GPS授时纳秒级精度

激光点云定位地图格式：定位地图、反射值地图快、高度值地图快，划分为12.5厘米一个像素

基于Lucas-Kanade算法的航向角优化

反射值与高度值的自适应融合：反射值匹配直方图*自适应权重*高度值匹配直方图*自适应权重\=融合直方图

视觉定位特点：
1. 摄像头技术成熟、结构化地图尺寸小，可降低系统成本
2. 车道线、路灯等道路元素稳定性高，不宜变动，地图生命周期长
3. 配置灵活，根据识别算法性能，可以使用不同的特征组合，易于拓展

# 高精地图与自动驾驶关系
增加车道、路由标牌、左右转、路灯等feature添加到地图中

无人驾驶大脑
- rtos
- 高精地图
- 感知
- 规划
- 定位
- 预测
- 仿真
- 安全

## 规划、预测、决策模块
1. A-B点的长距离规划
2. 运行过程的短距离规划，车道
3. 预测道路其他参与者的可能的轨迹
4. 根据规划和预测结果决定跟车、超车、等、换道……

高精地图为规划、预测、决策提供先验知识、经验。左转车道的车有较大几率要左转

## 安全与hdmap关系
可能由对传感器、操作系统、控制系统、通讯系统的攻击

强磁场会影响IMU准确度。轮子损坏影响轮速计精度。反射会影响激光雷达，直接激光照射可影响激光雷达。

hdmap提供一个离线的标准，对传感器结果做对比匹配，不一致时进行处理

## 仿真系统
通过仿真对强化学习、策略尝试做虚拟测试，降低成本

基于高精地图构建仿真系统

# 高精地图采集生产
GPS 3个卫星可定位，但需要4个才能准确，在空旷下好用，如高速路。城市内偏差可能在50米左右

IMU+轮速计→Propagation→pose prediction+Gps+LiDAR→Sensor Fusion=New Pose→Map Generation=map

另一种方案基于camera，通过图像做标定制图

第三种是纯视觉图像制图

# 高精地图格式规范
通用的 NDS Navigation Data Standard、OpenDrive。NDS相对重量级，阿波罗基于od改动后的标准

#业界高精地图产品
## HERE公司

地图采集特点：表述的完整性、更新的实时性，众包采集

HERE定义地图四层结构：基础地图

定位层：标志物

采集用激光雷达用16线

点云下的图片分隔、物体识别方法

动态信息层：事故、修路等

司机驾驶习惯层：记录司机每条路一般在哪加减速，行为更像人

## MobilEye公司
主要基于视觉制图

## 谷歌

## TomTom

## apollo地图采集方案
64线激光雷达采集路面，16线激光雷达采集红绿灯标牌等，斜向上装。

长短焦距相机、基站、IMU、nvme ssd hard

RTK方案GPS定位

采集每分钟生成一个rosbag包，最后打包为压缩包

# Apollo地图生产技术
数据采集-数据处理-元素识别-人工验证

点云拼接，压成图像底图生产，进行图像处理

没有车道线的路需要人工补上，路口多个灯要将灯与停止线关联，路口虚拟线等

点云分隔提取特征，优点坐标准

最后底图成果有定位地图、高精地图，高精地图可生成路径规划地图、仿真地图

# 阿波罗高精地图
道路元素：道路边界、车道线左边右边中心线、限速、车道类型、车道拓扑、车道线类型、车道方向、转向信息、车道长度

路口元素：路口边界、虚拟车道

交通信号元素：红绿灯、道路标牌

逻辑关系表述元素：两个元素之间的关系

其他元素：人行道、停车线、禁止停车区、路面箭头、护栏、龙门架、建筑物、减速带等

路口Junction模型

坐标系用了UTM、WGS84坐标系

相比于OpenDRIVE的改动：
1. 元素形状表达式，采用绝对坐标点序列描述边界现状，而不是使用方程，可避免基于偏移量计算，避免转弯时出现毛刺等问题
2. 元素类型的扩展，增加禁行区、人行横道等
3. 扩展元素间关系描述，增加junction与junction内元素的关联关系
4. 增加车道中心线到真实道路边界距离，停止线与红绿灯关联关系

overlap用于描述空间两个元素之间关系

高精地图安全问题，国内采集需经过测绘和安全部门批准。发不出的数据要强制加密，对数据做非线性偏移且不可逆，抖动最大可达1.7米，定位不可完全依赖地图。高度、曲率、坡度不允许在地图中表示。

# basic motion planning and overview
规划的本质是搜索，找最优解

设计3个fields领域：robotic、control theory、AI

从简单问题开始研究，
## path finding problems
环境不变只考虑路线

BFS、DFS深度广度优先搜索都属于non-informative search

A*(A star) search 知道终点的部分信息简化搜索范围，需要对全局全知,求得全局最优解

reinforcement learning强化学习

搜索分为partially/fully observed

对于par ob要进行incremental search增量搜索，贪心算法等，不是全局最优解

D\*算法（动态A\*）算法

对于车辆规划，部分关心全局最优

规划结果要包括时间，要求速度、位置变化平滑，路径只考虑运动学，后附加动力学

规划结果进行平滑算法处理

还需考虑动态障碍物、交通规则

还需实现 real time calculation，百度要求0.2-0.3秒计算

最后是三维规划问题，坐标xy以及时间，因为路况随时间变化，联合动态信息（感知、车况）和静态信息（高精地图）进行规划

无人车规划现在主要方向：data driven generic system

推荐书：
- steven lavelle: motion planning algorithms
- principles of robot motion: theory, algorithms, and implementations
- a review of motion planning techniques for automated vehicles. IEEE trans.

# motion planning with autonomous driving
RRT路径规划算法、Lattice planner算法

上一节都是点模型，认为车是一个质点不会相撞，应考虑车的体积进行规划，考虑刚体

constraints分三类：local物体是否相交等、differential方向盘曲率转向半径、global

连续空间解困难用离散空间，离散化网格后求解discretize

离散化方法：roadmap、cell decomposition、potential field

连续空间内撒一些点，以离散点连线找最短路径

RRT算法，改进用平滑的线sample避免线段连接不平滑

quadratic programming 求解速度快，但要求求解空间是突的，无人车不符合要求（前方有树，左右两种路属于可行空间，连线中点不在空间中）

在有规则的路上RRT仍不平滑，用lattice sampling，改进成可动态规划的方法，先撒点再连线，每一层点可重复利用上一层的结果

撒点坐标系：沿着道路中线及垂直线方向撒点可解决道路拐弯问题。参考文献：motion planning for autonomous driving with a conformal spatiotemporal lattice。此时还没考虑时间，没有加入动力学每一时间加速度

考虑时间会成为三维空间的拓扑问题(前方障碍物二维平面不可平滑穿过，三维考虑时间控制速度可平滑的从左侧到右侧)

三维优化的一种思路：迭代，先进行二维再增加三维，不一定保证最优解。No Model Is Perfect, but Many Are Useful.

bezeir spline平滑方法，cubic bezeir spline，注意平滑过程有可能撞到障碍物，更高阶的平滑与位置点的匹配性可能更低。

龙格现象，用高阶平滑曲线要求误差足够小会出现：插值次数越高，插值结果越偏离原函数的现象称为龙格现象。

# motion planning with environment
先构建vehicle state and model 需要考虑前后轮差，只考虑刚体模型无法，需要解决转向，构建bicycle model，车实际运行方向沿着前轮走，前后两个轮子运动过程中转向中心垂直于前后轮方向。转向半径前后轮不同，后轮半径与前后轮距离有关系

SL曲线坐标系(Frenet Frame)：
- S:the direction along centerline
- L:the dierction orthogonal to the centerline

要将SL mapping 到 XY世界坐标，可以唯一对应到xy坐标。但是xy坐标不能唯一变换到sl坐标，比如圆形sl坐标圆心和所有圆上点垂直。

一般道路转弯半径足够大，在道路上来看xy to sl可以做到唯一，但掉头时需特殊处理

曲线sl在计算机存储离散点，连接成折线，首先先判断世界坐标系xy点是否可投影到折线上，找到符合的折线段，即所有可能解

超平面分隔定理：hyperplane separation theorem:两个凸的多边形一定可以找到一个支线将他们分隔，时间复杂度是O(n^2)判断所有线

# motion planning environment
sl坐标系优化结果是否平滑还取决于道路中心线是否平滑，需要考虑curvature曲率连续

curvature不能进行后期平滑，转弯变化过程不能单独平滑，否则会导致轨迹变化

龙格现象 interpolation

smoothing spline是一个polynomial。piecewice polynomial

分段多项式的端点约束条件要保证三街道相等

另一种方式spiral path螺旋曲线，在极坐标对曲率分析

# optimization inside motion planning

泰勒高阶也可以用，但是高阶求导过程也需要计算，权衡计算量，实践来说用二阶导进行逼近即可。

二次规划用牛顿法，有可能不会收敛到最优解，局部最优

改进：分段后再用牛顿，比较后得到最优解

启发式搜索，先撒点，找到撒点中的最低值作为牛顿迭代的起始点

决策问题是离散空间中的优化问题

KKT condition、BFGS

# understand more on the MP difficulty
解决高纬空间优化问题方法：离散化、sample、降纬分析针对每个维度分别优化（EM算法，本质也算贪心算法）

静态障碍物步骤：
撒点，动态规划连点，带约束的二次优化优化出平滑曲线

动态障碍物：
投影障碍物到当前sl路径，画出s-t曲线时间和前方道路可行区域曲线，可得到时间上的禁行区

连续帧做出来的决策也要保证连续性

# reinforcement learning and data driven approaches
推荐课：sergey levine cs-294 伯克利的课，需要学过MDP(Markov Decision Processes马可夫决策过程)
http://rail.eecs.berkeley.edu/deeprlcourse-fa17/index.html

http://people.duke.edu/~kh269/teaching/b659/schedule.htm

imitation learning

distribution shifting problem蝴蝶效应

long term goal，short term goal

reward function，强化学习不适合训练出reward function，是用于指定目标函数情况下优化学习，但想要绘制出reward function较困难，一般手调reward functional

reward functional有用IRL以学习的方法完成，但过程很慢，每一步都要嵌套一个reinforcement learning，是迭代套迭代的过程

书：deep reinforcement learning hands-on, maxim lapan

  # Apollo ROS原理与实现、概述、原理

  ros不满足自动驾驶工程的地方：
  - 大数据量传输性能瓶颈（支持一两兆，需求百兆每秒）
  - 单中心的网络存在单点风险
  - 数据格式缺乏向后兼容（msg需要提前定义好）

传感器数据量
- gps 50kb/s
- lidar 50-70mb/s
- video cameras 120-180mb/s
- ultrasonic sensors 10-100kb/s
- obometry sensors 10kb/s
- radar sensors 10-100kb/s
单路传感器多个消费者时负载更大

## 通信性能优化
ros对于消息大小过大会出现延迟，ros还有消息丢弃机制，延迟过久的会被丢弃

基于共享内存通讯减少传输中的数据拷贝，提升传输效率

从四次内存拷贝：用户态序列化-内核态-接收端-用户态反序列化

到两次拷贝：发送节点序列化，接受节点共享内存拿到指针直接反序列化，减少两次内核态操作

共享内存可直接支持一对多传输场景

饮用共享内存消息通讯延迟降低1-2倍，一对一吞吐均值达到5.5GB/s一对四达到12GB/s，GPU资源占用率降低30%

## 去中心化网络拓扑
rosmaster为中心节点，保证了模块间解耦

但节点间通讯过于依赖rosmaster这个单点，且没有异常恢复机制，某个节点崩溃其他节点并不知道

多机情况下单点问题更突显，英伟达px2实际上有两个系统同时运行，一个是冗余备份系统

使用RTPS服务发现协议实现完全的P2P网络拓扑，新的流程：

subscriber启动时向当前域所有节点发信息，并，node收到消息后和sub分别建立连接，然后分别向sub发送当前自己的拓扑信息。等于每个节点都维护者自己和其他所有节点都维护了当前域的拓扑信息。sub接收完成后维护所有拓扑信息和实际要publisher的node建立点对点连接，进而实现去除master的依赖提高系统鲁棒性

在ros底层实现的，上层无影响

共享内存通讯方式会根据拓扑关系具体选择使用tcp还是共享内存方式

## 数据兼容性拓展
ros的msg方式可以实现更大程度的解耦合，且支持跨语言的node通讯，msg实际上是中间文件可翻译成不同语言的文件，编译期解析

node在进行通讯时，publisher接受消息后首先进行md5校验确定消息和订阅信息的正确，msg升级后需要所有相关node升级

某个模块升级后rosbag记录的历史数据回放也会无法识别，只能通过批量转换工具将历史数据转换后使用

apollo使用protobuf替代msg，可以使用optional实现对字段新增的支持

相比于嵌套使用（将protobuf序列化后用msg发送，然后反序列化，msg中无法看到具体消息内容且序列化效率低）

apollo实现ros内深度集成protobuf：
- 可以直接编译proto消息，学习难度低
- 工程中可以直接使用PB格式消息
- 调试工具直接解析显示 

ROSSpin可以监听队列情况，是阻塞的
ROSSpin once会对所有已达消息进行一次回调函数调用处理

其他通讯方式rosparam/rosservice

## TF坐标系转换
每一个node 都有自己的xyz坐标系，通过ft转换来确定每个节点间的关系

## rqt接口
ros的qtui界面，可以图形化调试

## urdf
xml格式描述机器人结构、运动副等信息，通过父子节点构成完整的拓扑结构

## SDF
仿真模拟过程用Gazebo工具使用SDF语言描述机器人，urdf也可以加载到gazebo

## ros time
虚拟时钟功能

# 感知
选传感器

传感器|原理|优点|缺点
----|----|----|----
Lidar|主动式，ToF|测距准|稀疏感知范围近
Camera|被动式|稠密感知范围远|测距不准受环境影响
Radar|主动式，多普勒频移测速|测距测速准|噪点多，非金属障碍物召回低，无法做识别
Ultrosonic|主动式|近距离测距|位置感知粗糙，只有近距离
高精地图|提前感知静态元素先验|可以无差错精细感知，减轻线上感知负担|增加了高精地图和高精度定位依赖
Image-Lidar|接收器同时接受可见光和激光|原始就是4D数据|感知范围近

参数分别为内参外参。内参传感器焦距lidar线间角度等，外参具体放置的位姿等

标定间：墙壁贴很多二维码，不一样 apriltag，通过识别判定位置。使用高精度扫描仪建立3d标定间，生成查找表，开车进入后识别确定位置。最后可视化的check，两个图像融合或点云融合看是否清晰不模糊

传感器标定
1. lidar内参标定，lidar和ridar对比
2. lidar-to-gps外参标定，空旷地反绕8字开
3. lidar-to-lidar外参标定
4. lidar-to-camera外参标定
5. camera to camera外参标定
6. 自然场景中的lidar to camera外参标定，需要在场景中有一些边缘交通标志盘等特征，以边缘做对其调参
7. 自然场景中的bitocal cameras外参标定，有重叠的camera对比重叠区域
8. camera to radar外参标定，得到相对于camera的高度，假设radar水平安装，对cad模型测量得到，对高度不敏感主要看相机和radar的角度关系，即相机相对于水平面的倾斜角，可借助lidar得到地面水平面标定camera倾角

# 感知算法
lidar感知算法（检测算法）：启发式方法Ncut，深度学习方法CNNSeg

视觉感知：深度学习检测和分隔、跟踪，2d-to-3d，多相机融合、红绿灯（感知与地图交互）

radar感知、超声波检测

## NCut
思路
* 基于空间平滑性假设，空间上接近的点来自于同一个障碍物
* 基于点云构建graph G=(v,e,w)
* 吧点云检测问题建模城graph分隔问题，一个cluster是一个障碍物
* 预处理（ROI过滤，去地面）和后处理（异常过滤）

基于Graph的聚类是经典问题，也是谱聚类问题

优点解释性好，缺点分隔规则过于简单，难以应付实际情况，缺乏语义信息

点云感知-持续改进深度学习方法MV3D(CVPR2017)

感知的2D框，当前帧需要考虑历史帧，不能频繁抖动会影响距离检测

## CNN检测
AlexNet  VGGNet  ResNet参考kaiming he的系列工作

cv检测到AD检测
* 第一视角和结构化道路约束
* 不仅仅输出2dBox，局部end to end，3d信息，时序稳定性
* 多任务学习，网络结构适配：障碍物，车道线，道路边界，定位元素
* 属性识别：车尾灯，车门开闭

需要一个model识别上述所有内容，训练数据也不一定每张内容均包括所有label，需要对数据做分类、筛选等

边缘分隔比bounding box难，要判断出可行驶区域

## 感知算法：后处理及算
2d to 3d几何计算
* 相机pose影响
* 接地点，2d框，绝对尺寸多条途径回3d
* 稳定性至关重要

时序信息计算：跟踪
* 对相机频率和延时有要求
* 充分利用监测模型的输出信息：特征、类别
* 可以考虑轻量级的Metric Learning

多相机环视融合：相机布局决定融合策略

几何计算+时序计算+环视融合

## 感知算法：红绿灯
sensor选型和setup，获取最大可视范围：
HDR大于100dB，1080p；
双camera，长短焦切换6mm+25mm

高精地图交互，增加在线识别鲁棒性：
地图提供灯组3D坐标和交通含义；
只看RoI区域避免形状识别

深度学习，对应appearance变化：
检测：灯组、灯头在哪？分类：什么颜色？