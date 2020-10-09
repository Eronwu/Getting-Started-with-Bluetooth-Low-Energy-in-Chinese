# 第八章，安卓编程

使用一个像BLE的无线标准的其中一个好处是其对各种广泛的智能手机和平板的支持。无线标准打开了一个新的与嵌入式硬件项目相沟通的维度，如今已经可以用廉价的硬件和大量接口进行设计。

在接口之外，你可以使用手机作为进入更大的互联网的入口，或者结合其他应用或者API创建一个与你自己创建的嵌入式硬件混合的程序。这使得一个新的类型的便宜设备可以使用，还提供大量的功能。

本章介绍了软硬件，以及在安卓操作系统上加入BLE功能的流程需求的一个基本概括。

## 开始

本章的安卓项目开发范例基于德州仪器（TI）制造的低成本的SensorTag（见第七章[SensorTag](./chapter7.md#SensorTsag)）设备。SensorTag提供了许多传感器，是一个可以提供大量信息进行处理和可视化的很好的复杂传感器设备的例子。

因为安卓的GUI端会有一点复杂，超过本书介绍的范围，本章将聚焦于你将从SensorTag的哪里提取以及通过BLE获取数据。在一点上，许多其他可用的资源可以来证明展示数据。

### 获取硬件

对于硬件，你需要一个安卓设备运行安卓4.3或更高的版本。安卓开始支持BLE的版本为4.3，但我们建议设备至少运行的是安卓4.4版本，这包含了一个已经升级的并且更稳定的BLE协议栈的版本。你也需要确认硬件是否支持BLE。这里的样例项目使用的是运行安卓4.4的谷歌Nexus7。

![figure-bird](.\pic\figure-bird.png) *为了确认设备是否支持BLE，参见蓝牙的[智能设备列表](http://bit.ly/1iDPs0M)。*

你也需要选一个TI的SensorTag，在这个项目中作为从设备服务。更多关于SensorTag设备请参见第七章[SensorTag](./chapter7.md#SensorTag)。

## 获取软件

对于这个项目，你需要三款软件：

*Eclipse安卓开发工具（ADT）*

​	可在[安卓开发网站](http://bit.ly/PYcBmE)使用。

*蓝牙应用加速器*

​	可在[蓝牙SIG网站](http://bit.ly/1kQPQuv)使用。

*TI SensorTag安卓应用资源代码*

​	可在[TI网站](http://bit.ly/1kR0aTd)使用。

安装过程中最重要、最耗时的部分是安装安卓开发工具，这需要懂一点如何使用Eclipse IDE。你最好去[安卓开发网站](http://bit.ly/PYcBmE)得到更多关于设置开发环境的指示。你将需要下载最新的SDK以及进行所有的安卓开发工具的更新。

## 配置硬件

在可以使用作为开发设备前，安卓设备需要一些配置。首先，你需要激活开发者模式，如果该模式并没有被开启。进入设置菜单，滑倒底部，并选择关于。在关于的界面上（见图8-1），快速点击“编译版本”7次开启开发者模式。

![figure8-1](.\pic\figure8-1.png)

*图8-1. 从关于界面开启开发者模式*

你应该可以在设置菜单中看到一个新的“开发者选项”条目。现在，你将需要开启“USB调试”和“保持唤醒”选项，如图8-2所示。

![figure8-2](.\pic\figure8-2.png)

*图8-2. 在”开发者选项“中开启“USB调试”和保持唤醒“选项*

最后，前往设置菜单，选择 保存→选项→“USB电脑连接”，并使能相机选项（如图8-3）去允许进行文件传输。

![figure8-3](.\pic\figure8-3.png)

*图8-3. 配置安卓作为一个相机进行传输文件*

这听起来似乎反人类，但是设备需要进入相机模式才可以作为开发工具进行工作。

### 开始一个新的项目

为了开启一个新的项目，你需要导入蓝牙应用加速器（Bluetooth Application Accelerator）的安卓文件到这个项目。打开Eclipse ADT，选择文件/导入，导航到含有应用加速器的文件目录，选择安卓/BLE范例的文件夹。点击OK导入项目到工作空间。

在这一点上，你可能希望粗略看下应用加速器文件是怎么布局的。如果这个对于你来说看起来比较陌生，不用担心。相比花大量时间在应用加速器里面，你只需要将BLE库主要的类文件移到自己项目中即可。

现在是时候创建自有的安卓项目了。在Eclipse内，选择文件→新建→安卓应用项目。对于应用名字，使用BleSensorTag。最小SDK需求的版本，指定安卓4.3版本。这个是安卓支持BLE的最低版本。对于目标SDK和编译，使用设备所支持的最新安卓版本。接收默认，点击剩余窗口的下一步。最后，项目向导（project wizard）会创建一个新的称为BleSensorTag的安卓项目。

如图8-4所示（该图在左边展示了项目目录结构，左边为主代码窗口），一个安卓项目含了许多文件夹和文件，但是你只需要在里面的一部分进行工作。主要的源程序文件时位于 /src 目录。

![figure8-4](.\pic\figure8-4.png)

*图8-4. Eclipse中主要的Java窗口*

每一个项目都包含了一个清单（manifest），为AndroidManifest.xml，展现了该应用对于安卓系统的具体要素信息。安卓在系统内运行任何程序之前需要一些信息。除此之外，你也将用到 /res 文件夹，这包含了XML的文件，掌控了一些将会用到的布局和菜单。

![bird](.\pic\figure-bird.png) *安卓程序是一个大型的内容，远超过本章的范围，本章只关注于整合BLE到安卓应用的部分。对于更多复杂的GUI部分，我们推荐你咨询其他安卓编程文章。*

在你开始任何实际的编写代码之前，你需要给与安卓清单（manifest）BLUETOOTH和BLUETOOTH_ADMIN权限。双击AndroidManifest.xml文件，选择权限标签（Permissions tab，见图8-5）.选择“增加...”→“用户权限”，将android.permission.BLUETOOTH 输入进名称框里。在另一个“用户权限”里面重复一遍动作，这回写上android.permission.BLUETOOTH_ADMIN。当应用安装好后，这两个条目会要求用户访问该名字服务的权限。

![figure8-5](.\pic\figure8-5.png)

*图8-5. 安卓清单文件权限窗口*

现在，你需要从蓝牙应用加速器内移入一些类的文件到项目中。这将会使类文件和方法为自己所用，让BLE编程变得更为简便。选择和复制以下文件：

- BleWrapper.java
- BleWrapperUiCallbacks.java
- BleNamesResolver.java
- BleDefinedUUIDs.java

将文件复制到BleSensorTag项目的 /src 目录（或者拖放到该目录）。一旦你增加了这些文件，/src 目录就如图8-6。

![figure8-6](.\pic\figure8-6.png)

*图8-6. 安卓 /src 目录*

BleWrapper是由蓝牙SIG组织发布的应用加速器库的主要部分。这是安卓BLE库的一个简化封装，使访问和使用库都更简便，因为其掌管了大量复杂的处理流程。

这就完成了项目的前期工作。现在你已经创建了这个项目，配置了清单文件，安装了类的库文件，你已经准备好安卓代码的大餐了。

## 初始化BLE库

是时候开始实作了。第一步（强调BLE的编码中心，而不是过多在GUI的一侧），范例将会主要打印出BLE收到的信息。之后，你将增加一些GUI特性来将BLE的数据展示出来。

在你开始之前，你需要创建一个已导入的BleWrapper库的实例。这文件是来自蓝牙SIG组织的蓝牙应用加速器：

```java
public class MainActivity extends Activity {
    // add this line to instantiate the BLE wrapper
    private BleWrapper mBleWrapper = null;
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
	}
```

文件中增加的这行为创建BleWrapper类的实例，这包含了你将用于访问底层安卓BLE库的方法。在指导手册中，你还需增加一些回调函数（callback），这是来自安卓BLE库内的一些处理事件。

在之前的代码段中，可以注意到自动生成的一个函数叫做onCreate()。当应用初始启动时该函数被调用，你将在开始阶段使用该函数进行一些初始化，有且只执行一次。这函数包含的是，如果设备进入睡眠或者context丢失之后再重新返回的情况下，不需要再重新初始化的内容。

安卓的文献中都由概括出“activity生命周期”的概念，描述了应用在其自身“生命”过程中不同的阶段的方法。如当应用开启时，三个独立的生命周期方法将被调用：onCreate(), onStart(), 和onResume()。当仅需要初始化一次就初始化应用onCreate()的类，当设备进入睡眠或者context丢失的情况下需要重新初始化则初始化onResume()的类。

文献中提供了在这个activity生命周期内更多的信息，这是需要理解的很重要的概念，因为你将初始例行程序的一部分做在onCreate()里面，一部分做在onResume()里面。当关机时你也将需要处理一些收尾部分，这将在onPause()或者onStop()完成。

在onCreate()方法里面，你将看到部分由项目向导（project wizard）生成的样板（boilerplate）代码。在这之后，你将增加更多的代码去初始化你的mBleWrapper对象以及完成其他初始任务：

```java
@Override
protected void onCreate(Bundle savedInstanceState)
{
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    
    mBleWrapper = new BleWrapper(this, new BleWrapperUiCallbacks.Null()
    {
    });
    
    if (mBleWrapper.checkBleHardwareAvailable() == false)
    {
        Toast.makeText(this, "No BLE-compatible hardware detected",
        Toast.LENGTH_SHORT).show();
        finish();
    }
}
```

这部分代码通过将其实例化并调用自身构造函数（constructor），分配了一个新BleWrapper对象到mBleWrapper成员变量中（上一段为空）。当进行该封装器(wrapper)的实例化时，你还需要增加一个BLE回调函数作为该构造函数的一个参数。这里就是增加处理一些来自BLE协议栈的通知事件代码的地方。

在软件编程中，*回调函数（callback）*作为一个可执行代码的引用，作为一个参数被传入函数中，当函数完成执行完毕之后将回调这段代码。对于现在，我们对这个构造函数不增加任何回调，之后再回来看这部分。

在完成构造函数之后，代码执行一个检查，确认BLE硬件是否在这个系统上可用。如果为否，一个称为“Toast”的弹出信息框会提示用户缺失BLE硬件，并关闭app。

这里就完成了应用启动时需要在onCreate()方法内执行的代码。现在看onResume()方法，该方法在应用启动以及应用在任何时候从睡眠中恢复时执行代码：

```java
@Override
protected void onResume() {
    super.onResume();
    
	// check for Bluetooth enabled on each resume
    if (mBleWrapper.isBtEnabled() == false)
    {
        // Bluetooth is not enabled. Request to user to turn it on
        Intent enableBtIntent = new Intent(BluetoothAdapter.
        ACTION_REQUEST_ENABLE);
        startActivity(enableBtIntent);
        finish();
    }
    
    // init ble wrapper
    mBleWrapper.initialize();
    }
}
```

![figure-bird](.\pic\figure-bird.png) *Eclipse提供一个快速建立这些函数的捷径。只需要敲方法名字的一开头部分的几个字母，之后按Ctrl+Spacebar。从弹出的自动完成列表中选择函数，让Eclipse为你建造函数。*

这里的样板代码为super.onResume()。你需要在这后边加入自有代码。在这个案例中，你需要检查蓝牙在每一次从一个不同的context或者睡眠中恢复后，是否是可以用的。当其他程序使用在用时，蓝牙可能已经被关闭了。不去捕获状态改变仅仅假设蓝牙一直时开启的，这将会最终导致一些故障发生，或者软件的异常。为处理这种状况，如果程序发现蓝牙是关闭的，就将使用Android intent发送给用户一个请求，去打开蓝牙并退出应用。下一次应用重新打开的时候，如果蓝牙是开启的，程序就将继续进行。

一旦代码通过前面的检查，就将开始初始化BleWrapper，这将打开蓝牙界面并从安卓蓝牙适配器（Android Bluetooth adapter）内获取一个实例。一旦你得到该实例，你就可以访问BLE无线模块和协议的函数。

最后，你需要处理安卓生命周期的最后一个方法，onPause()。在任何context丢失、设备进入睡眠状态、或者应用关闭的时候，该方法都被调用：

```java
@Override
protected void onPause() {
    super.onPause();
    
    mBleWrapper.diconnect();
    mBleWrapper.close();
}
```

在我们的范例中，安卓设备将作为一个GAP中心设备和一个GATT客户端，并且SensorTag设备将会作为一个GAP从设备和一个GATT服务端（参见第三章[角色](./chapter3.md#角色)和第四章[角色](./chapter4.md#角色)）。

还是一样，样板代码保持在顶部。在这之后，程序调用两个封装器的方法。第一个方法让你去与远端设备“断开连接”。这个实际上是与远端从设备断开连接，并执行uiDeviceDisconnected()的回调方法。如果你需要在与从设备断开连接之后处理一些事情，你需要复写uiDeviceDisconnected()回调函数。最后，关闭BleWrapper，即完全关闭本地GATT客户端和中心设备。

## 连接到远端设备

