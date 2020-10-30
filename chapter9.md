# 第九章，IOS编程

苹果是早期蓝牙4.0的一个支持方，作为结果，有大量的API和工具来支持BLE设备和使用iOS的应用的开发。文中的iOS的设备通常是一架iPhone（iPhone 4S或者以上），但是iOS也支持BLE运行在较新的iPad（iPad3及以上）和第五代iPod Touch设备上。

BLE也支持较新一代的Mac，包括iMac（2012之后出厂的），MacBook Pro（2012版及以上），MacBook Air（2011版及以上），Mac Pro（2013版及以上）。但本章将聚焦于iOS，特别是iOS 7及更新版本。

关乎iOS编程的BLE设备的类型和应用主要有三大块：

*带有iOS应用的外围设备*

​		在这个类别内，BLE外围设备和传感器通过相应的iOS应用匹配——比如，一个自行车功率计和节奏计使用iPhone来显示和记录数据。

*iBeacon设备*

​		iBeacon是一个只发送广播的设备，当在一个GPS信号和手机信号难渗透的地方，使用了BLE广播（参考第二章[广播与扫描](./chapter2.md#广播与扫描)）来增强室内导航，可以给iOS设备（以及安卓设备）提供定位服务。

*带有苹果通知中心服务的外围设备*

​		内置于iOS的苹果通知中心（Apple Notification Center）显示了提醒和通知，比如在iOS设备的屏幕上显示来电显示和来自新闻服务的更新。使用苹果通知中心服务（ANCS），iOS设备可以使用BLE在一个副屏上显示通知，比如带BLE功能的手表。

为传感器研发应用首要就是使用核心蓝牙框架（Core Bluetooth framework），相比于为iBeacon研发应用时首要是包含了核心定位框架（Core Location framework， ANCS不需要一个应用）。核心蓝牙框架（CoreBluetooth）是iOS应用程序接口（API， application programming interface）的一部分，用于处理BLE函数和设备相关。其CBCentralManager和CBPeripheral类允许你使用中心设备和外围设备工作，在[第三章](./chapter3.md)已具体描述。CBCentralManager提供扫描、发现和连接到远端外围设备的源代码，而CBPeripheral提供给使用远端外围设备的部分服务和特征的源代码。

本章通过研究关注BLE功能的代码示例来对这些框架加以熟悉，特别是实现BLE的应用所需要的关键的类和方法。这些实例描述了一个完整的可以编译的BLE应用的基本内容。虽然实例代码并不完整，不够完善，这也是在一个iOS设备上有着完整的功能，并可以了解到这是怎么运行的，对于编写自己的iOS的BLE应用是一个很好的开始。

![figure-bird](.\pic\figure-bird.png) *所有示例的完整的Xcode项目请见[本书GitHub项目](http://bit.ly/1qoj8Ed)。所有的示例都需要iOS 7或者更新版本，以及Xcode 5或者更新版本。*

一般的说iOS应用开发和Xcode开发环境是一个广泛而复杂的内容，大多超脱了本章或者本书的范围。如果你要寻找更多信息，我们推荐Matt Neuburg编写的*iOS 7编程* 和*iOS 7编程基础* (O’Reilly)。当然，最有权威的源代码是苹果iOS研发库，首发于[核心蓝牙编译指南](http://bit.ly/1eh8soX)。本章的示例都将紧密跟随这本指南。

## 简单的电量外围设备

第一个示例程序是一个iOS中心设备，去搜寻并连接一个简单的远端外围设备。远端外围设备的角色为一个Bluegiga BLE112硬件模组（更多信息参见第五章[Bluegiga’s BLE112/BLE113 Modules](./chapter5.md#"Bluegiga's BLE112/BLE113 Modules)）。iOS设备将作为一个GATT客户端，外围设备作为一个GATT服务端（参见第三章[角色](./chapter3.md#角色)）。

![figure-bird](.\pic\figure-bird.png) *这里使用的Bluegiga BLE112模组评估板可以作为BLE112模组家族(part #: DKBLE112)的一个开发套件的一部分从线上多家供应商获得。开发套件包含了许多预编译传感器和用于快速评估的输入资料。或者，[Jeff Rowberg的BLE112 BLE开发板](http://bit.ly/OQ3seE)更加便宜。这是一个开源硬件解决方案，因此所有的硬件设计详细资料都可以从同一个源获得。*

BLE112模组实现了BLE外围设备的功能，在这个例子中，使用了板子上的A/D转换器读取了由一个很小的电位器设置的电压值（在图9-1中的左上角被圈出），并通过BLE battery_level服务来保存该值作为BLE“电池电量”特征值（从0到100%数值划分）。iOS应用之后必须读取保存在BLE外围设备的“电池电量”并应用于应用程序。（更多关于服务和特征的信息，参见[第四章](./chapter4.md)）。

![figure9-1](.\pic\figure9-1.png)

*图9-1. Bluegiga BLE112开发套件*

![figure-bird](.\pic\figure-bird.png) *我们使用“电池电量”这个术语，而不是“电位器”，因为“电池电量”外围设备是预定义服务之一，并与在BLE中的特征相联系。尽管没有使用一个预定义配置文件的需求，对于这部分的讨论这么做是比较方便的。*

对于这个简单的应用，在核心蓝牙框架内提到的关键的类包含了CBCentralManager（iOS设备上，在BLE中的中心管理角色的一个抽象概念）。代码也包含了支持的类CBService和CBCharacteristic，因为你需要知道通过发现来确定哪一个服务和相关的特征在远端外围设备是可以使用的（参见第三章[服务和特征发现](./chapter3.md#服务和特征发现)）。

图9-2展示了写入BLE远端外围设备和CBPeripheral对象的服务和特征之间的关系。

![figure9-2](.\pic\figure9-2.png)

*图9-2. CBPeripheral, CBServices, 和 CBCharacteristic objects的关系*

为了更方便，在示例中的代码包含了一些来自BLE规格书的预定义的服务和特征（比如，电池服务），使用了16bit的UUID而不是厂家定义的129bit的UUID（参见第三章[UUID](./chapter3.md#UUID)）。

![figure-bird](.\pic\figure-bird.png) *对于更多SIG指定的服务和特征信息，蓝牙开发门户（Bluetooth Developer Portal）提供了一个完整的[预定义服务](http://bit.ly/1giCbMM)和[被采纳特征](http://bit.ly/1ggImgY)的列表。

### 扫描远端外围设备

开始前，你需要分配一个CBCentralManager实例，并为电量服务开启BLE扫描（参见第二章[广播和扫描](./chapter2.md#广播和扫描)）操作：

``` c++
// using the predefined BLE UUIDs for the battery level service
#define BATTERY_LEVEL_SERVICE_UUID 0x180f
#define BATTERY_DEVICE_INFO_SERVICE_UUID 0x180a
// build an array of services UUIDs of interest
NSArray *services = @[[CBUUID UUIDWithString:BATTERY_LEVEL_SERVICE_UUID],
[CBUUID UUIDWithString:BATTERY_DEVICE_INFO_SERVICE_UUID]];
// instantiate and start Central Manager object
CBCentralManager *centralManager = [[CBCentralManager alloc]
initWithDelegate:self queue:nil];
// scan for peripherals with services listed in services UUID array
[centralManager scanForPeripheralsWithServices:services options:nil];
self.centralManager = centralManager;
```

首先，使用核心蓝牙框架去创建一个你感兴趣的UUID服务的列表（NSarray），在这个例子中命名services。你将使用UUIDWithString方法去转换UUID字符串为UUID对应的[CBUUID](http://bit.ly/1qHI9bB)对象。

CBUUID也可以将预定义的16bit服务和特征UUID转换为完整128bit格式的相等的UUID（更多参见第三章[UUID](./chapter3.md#UUID)）。接下来，CBCentralManager对象进行实例化，UUID列表作为第一个参数提供给scanForPeripheralsWithServices方法。在这个例子中，应用仅扫描两个服务，设备信息服务（UUID16=0x180A）和电量服务（UUID16=0x180f）。只有所需要的包含在广播数据内（参见[表3-3](./chapter3.md#表3-3)）的UUID服务的外围设备将作为被找到而返回值。

注意到如果scanForPeripheralsWithSevices方法的第一个参数被赋值空，而不是感兴趣的CBUUID（服务）列表，这将返回范围内所有的BLE外围设备。这通常不是一个好的做法，除非你真的希望发现所有可能的带有服务的外围设备，因为这需要大量使用BLE无线模块硬件，导致减小电池使用时间。

### 连接到远端外围设备

当一个你感兴趣的外围设备在扫描过程中被发现，centralManager唤起didDiscoverPeripheral，后者是你存放connectPeripheral方法的函数。该方法返回相关联的外围对象的实例，这可以之后使用CBPeripheral方法时被检查到：

``` c++
- (void)centralManager:(CBCentralManager *)central
    didDiscoverPeripheral:(CBPeripheral *)peripheral
    advertisementData:(NSDictionary *) advertisementData
    RSSI:(NSNumber *)RSSI
{
    NSString *localName = [advertisementData objectForKey:
    CBAdvertisementDataLocalNameKey];
    if ([localName length] > 0)
    {
        NSLog(@"Found the battery level monitor: %@", localName);
        // found peripheral so stop scanning
        [self.centralManager stopScan];
        // return peripheral object
        self.batteryLevelPeripheral = peripheral;
        peripheral.delegate = self;
        // connect peripheral to centralManager
        [self.centralManager connectPeripheral:peripheral options:nil];
    }
}
```

在感兴趣的外围设备发现之后，很好的做法就是停止扫描（使用[self.centralManager stopScan]），因为这样可以节省电量。当然，如果你需要搜寻不止一个外围设备，修改代码示例的逻辑即可。

### 寻找与外围设备相关的服务

在外围设备被发现并且外围设备对象实例化后，你需要创建相关的服务对象和相联系的特征：

``` c++
- (void)centralManager:(CBCentralManager *)central
	didConnectPeripheral:(CBPeripheral *)peripheral
{
    // establish peripheral delegate
    [peripheral setDelegate:self];
    // discover available services
    [peripheral discoverServices:nil];
    // log results
    self.connected = [NSString stringWithFormat:@"Connected: %@",
    peripheral.state == CBPeripheralStateConnected ? @"YES" : @"NO"];
    NSLog(@"%@", self.connected);
}
```

在这段代码中，当连接建立后，centralManager调用didConnectPeripheral并将其实例化。外围设备的代理被设置，并在log中记录。

### 寻找与服务相关的特征

核心蓝牙通过调用didDiscoverServices给每一个发现服务创建了一个CBService对象。下面的代码为发现特征（参见第三章[服务和特征发现](./chapter3.md#服务和特征发现)），使用CBPeripheral类的discoverCharacteristics方法以数组方式保存进CBCharacteristic对象中：

``` c++
- (void)peripheral:(CBPeripheral *)peripheral
didDiscoverServices:(NSError *)error
{
    // sort through each service and discover chararacteristics
    // associated with each
    for (CBService *service in peripheral.services)
    {
        NSLog(@"Discovered service: %@", service.UUID);
        
        // discover characteristics associated with each service
        [peripheral discoverCharacteristics:nil forService:service];
    }
}
```

如果你传递nil作为一个参数到[peripheral discoverServices:nil]和[peripheral discoverCharacteristics:nil forService:service]中，请注意，因为iOS设备会在外围这边上尝试发现*所有* 的服务和*所有* 的特征。如果远程外围设备执行许多的服务和相关的特征，相比只执行一个你所需要的服务，这将导致电池损耗。在这个例子中没有什么问题，因为我们只执行我们在外围设备上感兴趣的部分。但是如果在你的应用中可能就是一个问题，请使用服务和特征列表。

一旦感兴趣的服务以及相关的特征被发现，你需要读取特征值并提供给应用。你可以直接使用readValueForCharacteristic:方法读取数值，如果远端外围特征是稳定状态下，这是一种很好的获取方式。

然而，如果特征一直改变，就比如电量，你应该使用BLE通知特性来解决，具体在第四章[服务启动更新](./chapter4.md#服务启动更新)阐述。当完成这个特性之后，仅当电量值改变，应用才被通知。这个可以避免了因数值改变而不断轮询远端外围设备，这对于iOS中心设备的电池生命是一个挑战，因为这产生了不必要的无线通信。

在远端外围设备上使用setNotifyValue:forCharacteristic:方法开启BLE通知（这方法在外围设备上写入对应的CCCD，如第三章[客户端特征配置描述符](./chapter3.md#客户端特征配置描述符)解释），如下面关于电量特征的代码（还是，因为厂商数据是固定的，这使得使用readValueForCharacteristic:方法是合理的）：

``` c++
- (void)peripheral:(CBPeripheral *)peripheral
didDiscoverCharacteristicsForService:(CBService *)service error:(NSError *)error
{
    // Retrieve Service for the Voltage Level
    if ([service.UUID isEqual:[CBUUID UUIDWithString:
    						BATTERY_LEVEL_SERVICE_UUID]])
    {
        for (CBCharacteristic *aChar in service.characteristics)
        {
            // Request level notifications
            if ([aChar.UUID isEqual:
                [CBUUID UUIDWithString:
            	BATTERY_LEVEL_MEASUREMENT_CHARACTERISTIC_UUID]])
            {
                [self.batteryPeripheral setNotifyValue:YES
                forCharacteristic:aChar];
                NSLog(@"Found battery level measurement characteristic");
            }
        }
    }
    
    if ([service.UUID isEqual:
		[CBUUID UUIDWithString:BATTERY_LEVEL_DEVICE_INFO_SERVICE_UUID]])
	{
        for (CBCharacteristic *aChar in service.characteristics)
        {
            if ([aChar.UUID isEqual:
            	[CBUUID UUIDWithString:
            	BATTERY_LEVEL_MANUFACTURER_NAME_CHARACTERISTIC_UUID]])
            {
                [self.batteryPeripheral readValueForCharacteristic:aChar];
                NSLog(@"Found a device manufacturer name characteristic");
            }
        }
    }
}
```

现在，无论何时外围设备发送一个通知，didUpdateValueForCharacteristic方法都将被唤醒，这让应用仅在电量特征改变时才读取其数值：

``` c++
- (void)peripheral:(CBPeripheral *)peripheral
didUpdateValueForCharacteristic:(CBCharacteristic *)characteristic
						error:(NSError *)error
{
    // Updated value for battery level measurement received
    if ([characteristic.UUID isEqual:[CBUUID
    UUIDWithString:BATTERY_LEVEL_MEASUREMENT_CHARACTERISTIC_UUID]])
    {
        // Get the battery level
        [self getBatteryLevelData:characteristic error:error];
    }
}
```

这时候，代码已经创建了与远端外围设备相关的服务和特征对象。下一小节使用这些对象来提供数据给剩余的应用。

### 读取和解码特征的方法

应用的任何位置使用下列代码可从特征对象获得电量的通知：

``` c++
- (void) getBatteryLevelData:(CBCharacteristic *)characteristic
					error:(NSError *)error
{
    // Get the Battery Level
    NSData *data = [characteristic value];
    const uint8_t *reportData = [data bytes];
    uint16_t level = 0;
                        
    if ((reportData[0] & 0x01) == 0)
    {
        // Retrieve the level value for the Battery
        level = reportData[1];
    }
    else
    {
    	level = CFSwapInt16LittleToHost(*(uint16_t *)(&reportData[1]));
    }
                        
    // Display the battery level value to the UI if no error occurred
    if( (characteristic.value) || !error )
    {
        self.batteryLevel = level;
        self.batteryLevel.text = [NSString stringWithFormat:@"%i %",
        batteryLevel];
    }
    return;
}
```

而下列代码为应用获取厂家信息：

``` c++
- (void) getManufacturerName:(CBCharacteristic *)characteristic
{
    NSString *manufacturerName = [[NSString alloc]
    initWithData:characteristic.value
    encoding:NSUTF8StringEncoding];
    self.manufacturer = [NSString stringWithFormat:@"Manufacturer: %@",
                         manufacturerName];
    return;
}
```

这时候，iOS API已经开启了由远端外围设备A/D转换器检测的电量，通过iOS设备上的BLE来进行传输，这使得应用可以进行图表、文字展示，或者保存在数据库中。

## iBeacon

iBeacon应用使用iOS的[核心定位框架](http://bit.ly/1giabJo)特性来给iOS和安卓设备在信号和GPS弱或根本没有的地方提供室内导航以及定位功能。

iBeacon模型提供了一系列连接设备和基于位置进行通讯的可能性，包括在零售业中的有趣的种种。比如，以权限为基本的市场中，销售商推送给客户特别优惠以及其他信息（客户在收集上有相对应的应用），就是基于他们在商场中的位置。在展馆内，iBeacon可以提供自我向导旅游，将关于附近展览品的详尽介绍（以文字、语音或者视频展现的方式）传递给参观者。

拥有iBeacon功能的设备使用下列四种类别值来发送BLE广播包：

*Proximity UUID*

​		一个128bit值的唯一识别值，作为一个特定的类型或者从一个特定的组织中来识别一个或者多个beacon。

*Major value（主要值）*

​		一个可选的16bit无符号整数，可以将拥有相同proximity UUID的beacon进行分组。

*Minor value（次要值）*

​		一个可选的16bit无符号整数，可以通过beacon的相同的proximity UUD和Major value，对beacon进行区分。

*RSSI 值*

​		该值被写入beacon内，用于根据信号质量来帮助计算与beacon的距离。

在展馆这个例子中，proximity UUID会与一个制定的博物馆进行捆绑，主要和次要数字会被用来对大量展馆内beacon分组或者区分。在这例子，主要值会在馆内指定一个房间，而次要值会在这个房间内与一个特定的展览品进行捆绑。

如果大量的beacon用这种方式安装，每一个都绑定了指定的房间和展览品，由接收设备上执行的iOS应用进行范围区分（参考第六章[范围](./chapter6.md#范围)），估算到每一个beacon的距离，以及计算接收设备的位置。iBeacon应用的其中一个可能使用点就是下发给拥有iOS设备的个人一些关于展览品相关的信息。

### 广播

通常，一个beacon只广播并且不提供其他服务或者连接功能（因为BLE设备一旦连接建立就停止广播）。广播包格式如图9-3所示，遵循标准的BLE广播包格式，并使用厂商指定数据AD类型（参见第三章[广播数据格式](./chapter3.md#广播数据格式)）。

![figure9-3](.\pic\figure9-3.png)

*图9-3. iBeacon使用的广播包格式*

通常，iBeacon是一个简单为硬币大小自供电的BLE硬件（比如BLE112模组）。这也可以使用核心蓝牙框架来编程进iOS设备中，去执行iBeacon广播服务。这对于测试非常有用，但是没什么实用性，因为iBeacon应用必须在最前显示。

如果你想将iOS设备变为一个iBeacon发送器，你可以使用来自Radius Networks的[iBeacon定位应用](http://bit.ly/1hq5BJS)。Radius Networks的其他比较有用的应用有OS X上的MacBeacon，这个可以将你Mac作为一个iBeacon，省掉了你即将用作iBeacon应用的iOS设备。

### 范围

*范围(Ranging)*是一个运行在接收设备（比如iPhone）的iBeacon应用上的一个进程，根据接收来自附近的beacon的无线信号的强度来估算接收设备和beacon的距离。信号质量由RSSI（接收信号质量标识）测算，这是一个用dBm单位的数字，可以由iOS设备上的BLE应用发现的外围设备使用。特别是，RSSI跟踪每一个有运行iBeacon应用的iOS设备范围内的beacon。当iPhone在房间内移动的适合，从特定的beacon进行计算的RSSI就会改变。通常，当iPhone和iBeacon距离增加时，RSSI会变小。

