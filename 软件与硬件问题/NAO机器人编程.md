---
title: NAO机器人编程
categories: 软件与硬件问题
date: 2019-01-07 13:45:49
---
### NAO开发环境搭建

&emsp;&emsp;首先确保计算机上有`python 2.7`的`32`位开发环境(这是针对`Windows`环境，对于`MAC`和`Linux`，可以使用`32`或`64`位的`python 2.7`环境)，建议使用`Anaconda`进行安装。<!--more-->
&emsp;&emsp;从`NAO`机器人的官网下载`pynaoqi-2.1.4.13.win32.exe`，并进行安装，这是除了`NAO v6`以外其他型号机器人的`SDK`(`NAO v6`最显著的特征是胸口的`LOGO`被改为`NAO`)。完成以后，使用`python`输入以下指令进行测试：

``` python
import naoqi
```

如果没有出现错误，说明安装成功。

### Speak功能

&emsp;&emsp;在`python`文件中输入以下代码：

``` python
from naoqi import ALProxy  # Import ALProxy

# Create an ALProxy to the module you want to use
tts = ALProxy("ALTextToSpeech", "10.16.20.43", 9559)
tts.say("Hello, world!")  # Call a method
```

执行结果是机器人说出`Hello, world!`。注意`10.16.20.43`是配置好的机器人`IP`地址，`9559`是机器人的端口号。

### Walk功能

&emsp;&emsp;代码如下：

``` python
from naoqi import ALProxy

motion = ALProxy("ALMotion", "127.0.0.1", 6761)
motion.moveTo(1.0, 0.0, 0.0)
```

`moveTo`函数原型如下(这里使用的是`C++`版本的函数原型)：

``` cpp
bool ALMotionProxy::moveTo ( const float &x, const float &y, const float &theta );
```

Makes the robot `move` to the given pose in the ground `plane`, relative to `FRAME_ROBOT`. This is a `blocking` call.

- `x`：Distance along the `X` axis in meters.
- `y`：Distance along the `Y` axis in meters.
- `theta`：Rotation around the `Z` axis in radians `[-3.1415 to 3.1415]`.

Returns：`True` if the `moveTo` terminated successfully, `False` if it was interrupted.
&emsp;&emsp;`walkTo`函数也可以实现类似的功能：

``` python
x = 0.2
y = 0.2
theta = 1.5709
# (0.2[m], 0.2[m])の位置に左を90度向くように到達
proxyMo.walkTo(x, y, theta)

x = -0.2
y = 0.0
theta = 0.0
proxyMo.walkTo(x, y, theta)  # 後ろに0.2[m]下がる

x = 0.0
y = 0.0
theta = -1.5709
proxyMo.walkTo(x, y, theta)  # 右に90度回転
```

&emsp;&emsp;`setWalkTargetVelocity`用于设置`NAO`的步行速度以及旋转速度：

``` cpp
void ALMotionProxy::setWalkTargetVelocity (
    const float &x, const float &y,
    const float &theta, const float &frequency
);
```

Makes the robot walk at the given `velocity`. This is `a non-blocking` call.

- `x`: Fraction of `MaxStepX`. Use negative for backwards `[-1.0 to 1.0]`.
- `y`: Fraction of `MaxStepY`. Use negative for right `[-1.0 to 1.0]`.
- `theta`: Fraction of `MaxStepTheta`. Use negative for clockwise `[-1.0 to 1.0]`.
- `frequency`: Fraction of `MaxStepFrequency` `[0.0 to 1.0]`.

``` python
x = 1.0
y = 0.0
theta = 0.0
frequency = 0.6
proxyMo.setWalkTargetVelocity(x, y, theta, frequency)
time.sleep(2)  # 前方に歩行(2秒間)

x = 0.0
theta = 0.5
proxyMo.setWalkTargetVelocity(x, y, theta, frequency)
time.sleep(2)  # 左方向に回転(2秒間)

x = 1.0
theta = -0.5
proxyMo.setWalkTargetVelocity(x, y, theta, frequency)
time.sleep(5)  # 前進しながら右方向に回転(5秒間)

x = 1.0
theta = 0.0
frequency = 1.0
proxyMo.setWalkTargetVelocity(x, y, theta, frequency)
time.sleep(5)  # やや早く前進(5秒間)

# 速度ゼロ(ストップ)
proxyMo.setWalkTargetVelocity(0.0, 0.0, 0.0, frequency)
```

### 多功能

&emsp;&emsp;`NAO`机器人还可以实现多个功能一起使用：

``` python
from naoqi import ALProxy

tts = ALProxy("ALTextToSpeech", "127.0.0.1", 6761)
motion = ALProxy("ALMotion", "127.0.0.1", 6761)

threadMove = motion.post.moveTo(1.0, 0.0, 0.0)
tts.say("I can wolk! Let's explore the area.")
motion.wait(threadMove, 0)
tts.say("I have reached my destination.")
```

执行结果是机器人在行走时说`I can wolk! Let's explore the area.`，当到达目的地时说`I have reached my destination.`。

### 机器人姿态

&emsp;&emsp;可以通过`ALRobotPosture`模块获取到机器人的姿态：

``` python
from naoqi import ALProxy

proxy = ALProxy("ALRobotPosture", "10.16.20.43", 9559)
print "Current posture: " + proxy.getPostureFamily()
```

&emsp;&emsp;`NAO`机器人有`Posture family`的概念：
&emsp;&emsp;As the number of possible postures is infinite, postures are grouped in `Posture families` in order to ease the interpretation of the current `posture`.
&emsp;&emsp;For example, the posture family `Sitting` contains the postures `SitRelax` and `Sit` as well as all the approaching `postures`.
&emsp;&emsp;Using `ALRobotPostureProxy::getPostureFamilyList` you can find out what are the available `posture families` on your robot. Here is the list of `posture families`:

Posture Family   | Description
-----------------|------------
`Standing`       | the `weight` is supported by the `feet`, the `torso` is `upright` and the `legs` are `straight`.
`Crouching`      | the `weight` is supported by the `feet`, the `torso` is `upright` and the `legs` are `bent`.
`Sitting`        | the `buttock` is in contact with the `ground` and the `torso` is `upright`.
`SittingOnChair` | the `buttock` is in contact with a `chair` (`10`cm high) and the `torso` is `upright`.
`LyingBelly`     | stretched and facing `down`.
`LyingBack`      | stretched and facing `up`.
`LyingLeft`      | stretched and facing to its `right` side.
`LyingRight`     | stretched and facing to its `left` side.
`Belly`          | facing `down` with the `trunk` lifted.
`Back`           | facing `back` with the `trunk` lifted.
`Left`           | leaning toward the `left` and touching the `ground` with its `hand`.
`Right`          | leaning toward the `right` and touching the `ground` with its `hand`.

### 调用摄像头

&emsp;&emsp;代码如下：

``` python
from naoqi import ALProxy
import cv2
import numpy as np

videoproxy = ALProxy("ALVideoDevice", "10.16.20.43", 9559)

AL_kTopCamera = 0
AL_kVGA = 1
AL_kBGRColorSpace = 13
Fps = 30
subscriber = videoproxy.subscribeCamera(
    "demo", AL_kTopCamera, AL_kVGA, AL_kBGRColorSpace, Fps)

while True:
    imageNAO = videoproxy.getImageRemote(subscriber)

    if imageNAO == None:
        print('cannot capture.')
    elif imageNAO[6] == None:
        print('no image data string.')
    else:
        frameArray = np.frombuffer(imageNAO[6], dtype=np.uint8).reshape(
                                   [imageNAO[1], imageNAO[0], imageNAO[2]])
        cv2.imshow("pepper-top-camera-320x240", frameArray)

    if cv2.waitKey(1) & 0xFF == ord('q'):
        break
```

&emsp;&emsp;`subscribeCamera`的函数原型如下：

``` cpp
std::string ALVideoDeviceProxy::subscribeCamera (
    const std::string &Name, const int &CameraIndex,
    const int &Resolution, const int &ColorSpace, const int &Fps
);
```

Subscribes to `ALVideoDevice`. When a `Video` Module registers to `ALVideoDevice`, a buffer of the requested image format is added to the buffers list. Returns an handle which identifies the module in `ALVideoDevice`. (This handle is based on the name parameter, e.g. the `3rd` one getting `_3` added to its name for instance to build this handle).

- `Name`: Name of the subscribing module.
- `CameraIndex`: `Index` of the camera in the video system (see `Camera Indexes`).
- `Resolution`: `Resolution` requested (see `Supported resolutions`).
- `ColorSpace`: `Colorspace` requested (see `Supported colorspaces`).
- `Fps`: `Fps`(frames per second) requested to the video source (see `Supported framerates`).

&emsp;&emsp;Camera indexes: The camera indexes are:

Parameter ID Name   | ID Value | Description
--------------------|----------|------------
`AL::kTopCamera`    | `0`      | camera on the `top`.
`AL::kBottomCamera` | `1`      | camera on the `bottom`.

&emsp;&emsp;Supported resolutions: Resolution parameter can have the following values:

Parameter ID Name | ID Value | Description
------------------|----------|------------
`AL::kQQQQVGA`    | `8`      | Image of `40 * 30`px
`AL::kQQQVGA`     | `7`      | Image of `80 * 60`px
`AL::kQQVGA`      | `0`      | Image of `160 * 120`px
`AL::kQVGA`       | `1`      | Image of `320 * 240`px
`AL::kVGA`        | `2`      | Image of `640 * 480`px
`AL::k4VGA`       | `3`      | Image of `1280 * 960`px

&emsp;&emsp;Supported colorspaces: A `color space` is a model describing the way to represent color as an ordered list of numbers. For example you can represent the color of each pixel of your screen as a list of three elements `R`, `G` and `B`. Which are respectively the value of `Red`, `Green`, and `Blue` usually stored in an byte (range from `0` to `255`). Color space parameter for 2D camera can have the following values:

Parameter ID Name        | ID Value | Number of layers | Number of channels
-------------------------|----------|------------------|----------------------
`AL::kYuvColorSpace`     | `0`      | `1`              | `1`
`AL::kyUvColorSpace`     | `1`      | `1`              | `1`
`AL::kyuVColorSpace`     | `2`      | `1`              | `1`
`AL::kRgbColorSpace`     | `3`      | `1`              | `1`
`AL::krGbColorSpace`     | `4`      | `1`              | `1`
`AL::krgBColorSpace`     | `5`      | `1`              | `1`
`AL::kHsyColorSpace`     | `6`      | `1`              | `1`
`AL::khSyColorSpace`     | `7`      | `1`              | `1`
`AL::khsYColorSpace`     | `8`      | `1`              | `1`
`AL::kYUV422ColorSpace`  | `9`      | `2`              | `2`
`AL::kYUVColorSpace`     | `10`     | `3`              | `3`
`AL::kRGBColorSpace`     | `11`     | `3`              | `3`
`AL::kHSYColorSpace`     | `12`     | `3`              | `3`
`AL::kBGRColorSpace`     | `13`     | `3`              | `3`
`AL::kYYCbCrColorSpace`  | `14`     | `2`              | `2`
`AL::kH2RGBColorSpace`   | `15`     | `3`              | `3`
`AL::kHSMixedColorSpace` | `16`     | `3`              | `3`

### 伺服电机

&emsp;&emsp;为了让机器人做出动作，首先应当打开伺服电机，它是通过将关节的刚度设置为`1`来完成的。此操作使用`ALMotion`模块的`stiffnessInterpolation`方法来完成：

``` python
proxyMo = ALProxy('ALMotion', "127.0.0.1", 8738)
proxyMo.stiffnessInterpolation('Body', 1.0, 1.0)
```

执行此代码后，将打开所有伺服电机。`stiffnessInterpolation`的参数依次是对象关节名称(`Body`是所有关节)、目标刚度值(`1.0`)以及更改时间。将目标刚度值设置为`0`，则会关闭伺服电机：

``` python
proxyMo.stiffnessInterpolation('Body', 0.0, 1.0)
```

### 控制关节角度

&emsp;&emsp;关节角度由`ALMotion`模块进行控制。`NAO`准备了许多控制关节角度的方法，可以根据情况使用`angleInterpolation`、`angleInterpolationWithSpeed`和`setAngles`等。`angleInterpolation`的函数原型如下：

``` cpp
void angleInterpolation (
    const AL::ALValue &names, const AL::ALValue &angleLists,
    const AL::ALValue &timeLists, const bool &isAbsolute
);
```

- `names`: Name or names of joints, chains, `Body`, `JointActuators`, `Joints` or `Actuators`.
- `angleLists`: An `angle`, list of `angles` or list of list of `angles` in `radians`.
- `timeLists`: A `time`, list of `times` or list of list of `times` in `seconds`.
- `isAbsolute`: If `true`, the movement is described in `absolute` angles, else the angles are relative to the `current` angle.

``` python
proxyMo.angleInterpolation('HeadYaw', 1.2, 1.0, True)
```

`proxyMo`是`ALMotion`的`Proxy`对象，`HeadYaw`是头部的一个关节，该代码使机器人头部围绕`z`轴在`1.0`秒内旋转到`1.2`弧度。
&emsp;&emsp;以下代码使`HeadYaw`在`0.5`秒内到达`0.5`弧度，在`1.0`秒内到达`1.2`弧度：

``` python
proxyMo.angleInterpolation('HeadYaw', [0.5, 1.2], [0.5, 1.0], True)
```

以下代码同时移动多个关节：

``` python
names = ['HeadYaw', 'HeadPitch']
angleLists = [[1.2], [-0.3]]
timeLists = [[1.0], [1.0]]
proxyMo.angleInterpolation(names, angleLists, timeLists, True)
```

`HeadPitch`表示围绕`y`轴旋转头部连杆的关节，`angleLists`和`timeLists`的元素数必须等于指定关节的数量。稍微复杂的实例如下：

``` python
names = ['HeadYaw', 'HeadPitch']
angleLists = [[0.0, 1.2], [0.0, -0.3]]
timeLists = [[0.5, 1.0], [0.5, 1.0]]
proxyMo.angleInterpolation(names, angleLists, timeLists, True)
```

`HeadYaw`在`0.5`秒通过到达`0.0`弧度，在`1.0`秒通过`1.2`弧度；`HeadPitch`在`0.5`秒通过`0.0`弧度，在`1.0`秒通过`-0.3`弧度。下面是左臂和右臂一起移动的示例：

``` python
angleLists = [
    [0.0, 1.0], [0.0, 1.0], [0.0, -1.0],
    [0.0, -1.0], [0.0, -1.0], [0.0, 1.0]]

timeLists = [[1.0, 2.0]] * 6
proxyMo.post.angleInterpolation('LArm', angleLists, timeLists, True)

angleLists = [
    [0.0, -1.0], [0.0, -1.0], [0.0, 1.0],
    [0.0, 1.0], [0.0, 1.0], [0.0, 1.0]]

timeLists = [[1.0, 2.0]] * 6
proxyMo.angleInterpolation('RArm', angleLists, timeLists, True)
```

---

### getSensorNames

&emsp;&emsp;The function is:

``` cpp
std::vector<std::string> ALMotionProxy::getSensorNames();
```

Gets the list of `sensors` supported on your robot. The function returns the vector of `sensor` names.

``` python
from naoqi import ALProxy

motionProxy = ALProxy("ALMotion", "127.0.0.1", 3164)
sensorList = motionProxy.getSensorNames()

for sensor in sensorList:
    print(sensor)
```

### getPosition

&emsp;&emsp;The function is:

``` cpp
std::vector<float> ALMotionProxy::getPosition (
    const std::string &name,
    const int &frame,
    const bool &useSensorValues
);
```

Gets a `Position` relative to the `FRAME`. Axis definition:

- the `x` axis is positive toward the robot's front.
- the `y` from right to left.
- the `z` is vertical.

The angle convention of `Position6D` is `Rot_z(wz).Rot_y(wy).Rot_x(wx)`.

- `name`: Name of the item. Could be: any joint or chain or sensor (Use `ALMotionProxy::getSensorNames` for the list of sensors supported on your robot).
- `frame`: Task frame {`FRAME_TORSO` = 0, `FRAME_WORLD` = 1, `FRAME_ROBOT` = 2}.
- `useSensorValues`: If `true`, the `sensor` values will be used to determine the `position`.

Returns: Vector containing the `Position6D` using meters and radians (`x`, `y`, `z`, `wx`, `wy`, `wz`).

``` python
from naoqi import ALProxy
import motion

motionProxy = ALProxy("ALMotion", "127.0.0.1", 3164)

# Example showing how to get the position of the top camera
name = "CameraTop"
frame = motion.FRAME_WORLD
useSensorValues = True
result = motionProxy.getPosition(name, frame, useSensorValues)
print "Position of", name, "in World is:"
print result
```

The result is:

``` python
Position of CameraTop in World is:
[0.021435432136058807, -0.04219575226306915, 0.5314476490020752,
 0.03944920375943184, -0.17428916692733765, -0.13973376154899597]
```

### getAngles

&emsp;&emsp;The function is:

``` cpp
std::vector<float> ALMotionProxy::getAngles ( const AL::ALValue &names, const bool &useSensors );
```

Gets the `angles` of the joints.

- `names`: Names the joints, chains, `Body`, `JointActuators`, `Joints` or `Actuators`.
- `useSensors`: If `true`, `sensor` angles will be returned.

Returns: Joint angles in `radians`.

``` python
from naoqi import ALProxy

motionProxy = ALProxy("ALMotion", "127.0.0.1", 3164)

# Example that finds the difference between the command and sensed angles.
names = "Body"
useSensors = False
commandAngles = motionProxy.getAngles(names, useSensors)
print "Command angles:"
print str(commandAngles)
print ""

useSensors = True
sensorAngles = motionProxy.getAngles(names, useSensors)
print "Sensor angles:"
print str(sensorAngles)
print ""

errors = []

for i in range(0, len(commandAngles)):
    errors.append(commandAngles[i] - sensorAngles[i])

print "Errors"
print errors
```

The result is:

``` python
Command angles:
[0.0, -0.17000000178813934, 1.4594838619232178, 0.2101430743932724, -1.1964812278747559,
 -0.4150501489639282, -0.17000000178813934, 0.10000000149011612, 0.12999999523162842,
 -0.09000000357627869, 0.09000000357627869, -0.12999999523162842, -0.17000000178813934,
 -0.10000000149011612, 0.12999999523162842, -0.09000000357627869, 0.09000000357627869,
 0.12999999523162842, 1.4594838619232178, -0.2101430743932724, 1.1964812278747559, 0.4150501489639282]

Sensor angles:
[0.0, -0.17000000178813934, 1.4594838619232178, 0.2101430743932724, -1.1964812278747559,
 -0.4150501489639282, -0.17000000178813934, 0.10000000149011612, 0.12999999523162842,
 -0.09000000357627869, 0.09000000357627869, -0.12999999523162842, -0.17000000178813934,
 -0.10000000149011612, 0.12999999523162842, -0.09000000357627869, 0.09000000357627869,
 0.12999999523162842, 1.4594838619232178, -0.2101430743932724, 1.1964812278747559, 0.4150501489639282]

Errors
[0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0]
```

### getSummary

&emsp;&emsp;The function is:

``` cpp
std::string ALMotionProxy::getSummary();
```

Returns a string `representation` of the Model's `state`.

``` python
from naoqi import ALProxy

motionProxy = ALProxy("ALMotion", "127.0.0.1", 3749)
print motionProxy.getSummary()  # Example showing how to get a summary of Nao's state
```

The result is:

``` python
---------------------- Model ---------------------------
        BodyName   Stiffness     Command      Sensor
         HeadYaw    1.000000    0.000000    0.000000
       HeadPitch    1.000000   -0.170000   -0.170000
  LShoulderPitch    1.000000    1.459484    1.459484
   LShoulderRoll    1.000000    0.210143    0.210143
       LElbowYaw    1.000000   -1.196481   -1.196481
      LElbowRoll    1.000000   -0.415050   -0.415050
    LHipYawPitch    1.000000   -0.170000   -0.170000
        LHipRoll    1.000000    0.100000    0.100000
       LHipPitch    1.000000    0.130000    0.130000
      LKneePitch    1.000000   -0.090000   -0.090000
     LAnklePitch    1.000000    0.090000    0.090000
      LAnkleRoll    1.000000   -0.130000   -0.130000
    RHipYawPitch    1.000000   -0.170000   -0.170000
        RHipRoll    1.000000   -0.100000   -0.100000
       RHipPitch    1.000000    0.130000    0.130000
      RKneePitch    1.000000   -0.090000   -0.090000
     RAnklePitch    1.000000    0.090000    0.090000
      RAnkleRoll    1.000000    0.130000    0.130000
  RShoulderPitch    1.000000    1.459484    1.459484
   RShoulderRoll    1.000000   -0.210143   -0.210143
       RElbowYaw    1.000000    1.196481    1.196481
      RElbowRoll    1.000000    0.415050    0.415050
---------------------- Tasks --------------------------
            Name          ID    BrokerID    Priority
----------------- Motion Cycle Time --------------------
            20 ms
```

### wakeUp

&emsp;&emsp;The function is:

``` cpp
void ALMotionProxy::wakeUp();
```

The robot wakes up: sets `Motor` on and, if needed, goes to `initial` position. For example, `H25` or `H21` sets the `Stiffness` on and keeps is `current` position.

``` python
from naoqi import ALProxy

motionProxy = ALProxy("ALMotion", "127.0.0.1", 3749)
motionProxy.wakeUp()
```

### goToPosture

&emsp;&emsp;The function is:

``` cpp
bool ALRobotPosture::goToPosture ( const std::string postureName, const float speed );
```

Makes the robot go to the predefined `posture` asked in parameter. It is possible to modify the speed of the move. The move is `intelligent`: it will start from `beginning` posture of the robot, and choose all the steps to reach the asked `posture`. This is a `blocking` call. Use a `post` if you want to make it `non-blocking`.

- `postureName`: Name of the predefined `posture` to be reached. See `Predefined postures`.
- `speed`: Relative `speed` between `0.0` and `1.0`.

Returns: Returns if the predefined `posture` was reached.

``` python
from naoqi import ALProxy

postureProxy = ALProxy("ALRobotPosture", "127.0.0.1", 3749)
postureProxy.goToPosture("StandInit", 1.0)
postureProxy.goToPosture("SitRelax", 1.0)
postureProxy.goToPosture("StandZero", 1.0)
postureProxy.goToPosture("LyingBelly", 1.0)
postureProxy.goToPosture("LyingBack", 1.0)
postureProxy.goToPosture("Stand", 1.0)
postureProxy.goToPosture("Crouch", 1.0)
postureProxy.goToPosture("Sit", 1.0)
```

### rest

&emsp;&emsp;The function is:

``` cpp
void ALMotionProxy::rest();
```

The robot `rests`: goes to a `relax` and `safe` position and sets `Motor` off. For example, `H25` or `H21` goes to the `Crouch` posture and sets the `Stiffness` off.

``` python
from naoqi import ALProxy

motionProxy = ALProxy("ALMotion", "127.0.0.1", 3749)
postureProxy = ALProxy("ALRobotPosture", "127.0.0.1", 3749)
postureProxy.goToPosture("StandInit", 0.5)  # Send NAO to Pose Init
motionProxy.rest()
```

### moveInit

&emsp;&emsp;The function is:

``` cpp
void ALMotionProxy::moveInit();
```

`Initializes` the `move` process. Checks the robot pose and takes a right posture. This is `blocking` called.

``` python
from naoqi import ALProxy

motionProxy = ALProxy("ALMotion", "127.0.0.1", 3749)
postureProxy = ALProxy("ALRobotPosture", "127.0.0.1", 3749)
postureProxy.goToPosture("Stand", 0.5)  # Send NAO to Pose Init
motionProxy.moveInit()  # Example showing how Initialize move process
```

### setStiffnesses

&emsp;&emsp;The function is:

``` cpp
void ALMotionProxy::setStiffnesses ( const AL::ALValue &names, const AL::ALValue &stiffnesses );
```

Sets the `stiffness` of one or more joints. This is a `non-blocking` call.

- `names`: Names of joints, chains, `Body`, `JointActuators`, `Joints` or `Actuators`.
- `stiffnesses`: One or more `stiffnesses` between `zero` and `one`.

``` python
from naoqi import ALProxy

motionProxy = ALProxy("ALMotion", "127.0.0.1", 3749)

# Example showing how to set the stiffness to 1.0.
# Beware, doing this could be dangerous, it is safer to use the
# stiffnessInterpolation method which takes a duration parameter
names = 'Body'
stiffnesses = 1.0  # If only one parameter is received, this is applied to all joints
motionProxy.setStiffnesses(names, stiffnesses)
```

### setAngles

&emsp;&emsp;The function is:

``` cpp
void ALMotionProxy::setAngles (
    const AL::ALValue &names,
    const AL::ALValue &angles,
    const float &fractionMaxSpeed
);
```

Sets `angles`. This is a `non-blocking` call.

- `names`: The name or names of joints, chains, `Body`, `JointActuators`, `Joints` or `Actuators`.
- `angles`: One or more `angles` in `radians`.
- `fractionMaxSpeed`: The `fraction` of maximum `speed` to use.

``` python
from naoqi import ALProxy
import time

motionProxy = ALProxy("ALMotion", "127.0.0.1", 3749)

motionProxy.setStiffnesses("Head", 1.0)

# Example showing how to set angles, using a fraction of max speed
names = ["HeadYaw", "HeadPitch"]
angles = [0.2, -0.2]
fractionMaxSpeed = 0.2
motionProxy.setAngles(names, angles, fractionMaxSpeed)

time.sleep(3.0)
motionProxy.setStiffnesses("Head", 0.0)
```