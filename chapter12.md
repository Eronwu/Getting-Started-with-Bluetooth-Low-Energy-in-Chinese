# 第十二章 增订版 B：现代开发工具更新

从 2014 年原书出版到 2026 年，BLE 开发工具和平台发生了巨大变化。原书中推荐的 Eclipse IDE、nRFGo Studio、nRF51822 等工具和芯片，大部分已经被更新的产品所取代。

本章将介绍现代 BLE 开发所需的工具、平台和最佳实践，帮助你快速上手 2026 年的 BLE 开发。如果你已经阅读了原书的[第五章 硬件平台](./chapter5.md)和[第十章 嵌入式应用开发](./chapter10.md)，本章内容可以看作是这些章节的"2026 年更新版"。

> **提示**：如果你是 BLE 开发新手，建议先阅读原书的[第一章](./chapter1.md)到[第四章](./chapter4.md)建立基础知识，再阅读原书的[第五章](./chapter5.md)了解硬件平台概念，然后来阅读本章了解现代工具链。

---

## 硬件平台演进

### nRF52 系列：nRF51 的继任者

原书[第五章 nRF51822-EK](./chapter5.md#nrf51822-eknordic-semiconductors-nordic-半导体)中详细介绍的 nRF51822 是 Nordic 的第一代 BLE SoC。nRF52 系列是其继任者，在性能、内存、功能和功耗上都有质的飞跃，而价格相差不大。

#### nRF52832

nRF52832 是 nRF52 系列的中端产品，适合大多数 BLE 应用：

| 特性 | nRF51822（原书） | nRF52832 |
|------|-----------------|----------|
| 内核 | ARM Cortex-M0, 16 MHz | ARM Cortex-M4F, 64 MHz |
| Flash | 128/256 KB | 512 KB |
| RAM | 16 KB | 64 KB |
| BLE 版本 | BLE 4.2 | BLE 5.0（2 Mbps、Coded PHY） |
| NFC | 无 | NFC-A 标签支持 |
| 加密 | AES-128 | AES-128 + ARM CryptoCell |

> **升级建议**：如果你正在开始新项目，不要选择 nRF51 系列。nRF52832 在价格相近的情况下，性能提升 4 倍，内存增加 4 倍，还支持 BLE 5.0 的新特性。

#### nRF52840

nRF52840 是 nRF52 系列的高端产品，适合复杂应用和多协议场景：

| 特性 | nRF52832 | nRF52840 |
|------|----------|----------|
| Flash | 512 KB | 1 MB |
| RAM | 64 KB | 256 KB |
| USB | 无 | USB 2.0 全速控制器 |
| IEEE 802.15.4 | 无 | 支持（Thread/Zigbee） |
| 蓝牙 Mesh | 支持 | 支持（更优） |
| GPIO | 最多 32 个 | 最多 48 个 |

**典型应用场景**：

- **USB 设备**：nRF52840 内置 USB 控制器，可以直接作为 USB HID 设备（键盘、鼠标）或 USB CDC 设备（虚拟串口）
- **多协议网关**：同时支持 BLE 和 Thread/Zigbee，可以作为智能家居网关
- **复杂传感器节点**：256 KB RAM 可以运行更复杂的数据处理算法
- **音频应用**：足够的内存支持 LC3 编解码器（LE Audio）

#### nRF5340

nRF5340 是 Nordic 最新的双核 SoC：

- **应用处理器**：ARM Cortex-M33，128 MHz，1 MB Flash，512 KB RAM
- **网络处理器**：ARM Cortex-M33，64 MHz，专用运行 BLE 协议栈
- **BLE 5.3** 支持
- 适合需要强大应用处理能力的场景

> **双核架构的优势**：网络处理器专门处理 BLE 协议栈，应用处理器专注于业务逻辑。两者通过 IPC（进程间通信）机制通信，提高了实时性和可靠性。

### ESP32 系列：低成本多协议方案

乐鑫（Espressif）的 ESP32 系列是另一个流行的选择，特别受 Maker 社区和物联网开发者欢迎。与原书[第五章 其他硬件平台和模块](./chapter5.md#其他硬件平台和模块)中描述的模块不同，ESP32 系列集成了 WiFi 和 BLE，价格极低。

#### ESP32（经典）

第一代 ESP32，2016 年发布：

| 特性 | 规格 |
|------|------|
| 内核 | 双核 Xtensa LX6, 240 MHz |
| SRAM | 520 KB |
| Flash | 外置，通常 4 MB |
| BLE | BLE 4.2 |
| WiFi | WiFi 4 (802.11 b/g/n) |
| 价格 | 约 $2-3/模块 |

#### ESP32-C3

ESP32-C3 是乐鑫的 RISC-V 架构芯片，2021 年发布：

| 特性 | 规格 |
|------|------|
| 内核 | 单核 RISC-V, 160 MHz |
| SRAM | 400 KB |
| Flash | 外置，通常 4 MB |
| BLE | BLE 5.0 |
| WiFi | WiFi 4 (802.11 b/g/n) |
| 价格 | 约 $1-2/模块 |

> **RISC-V 架构**：ESP32-C3 使用开源的 RISC-V 指令集，不依赖 ARM 授权。这意味着更低的成本和更好的供应链安全性。

#### ESP32-S3

ESP32-S3 专注于 AI/ML 边缘计算场景：

| 特性 | 规格 |
|------|------|
| 内核 | 双核 Xtensa LX7, 240 MHz |
| SRAM | 512 KB |
| 向量指令 | 支持 AI/ML 加速 |
| BLE | BLE 5.0 |
| WiFi | WiFi 4 |
| USB | USB OTG（全速） |

#### ESP32-C6

ESP32-C6 是乐鑫最新的多协议芯片，2023 年发布：

| 特性 | 规格 |
|------|------|
| 内核 | 单核 RISC-V, 160 MHz |
| BLE | BLE 5.3 |
| WiFi | WiFi 6 (802.11ax) |
| Thread/Zigbee | IEEE 802.15.4 |
| Matter | 原生支持 |

> **ESP32 vs nRF52 对比**：
>
> | 维度 | ESP32 | nRF52 |
> |------|-------|-------|
> | 价格 | 极低（$1-5） | 中等（$3-8） |
> | 功耗 | 较高 | 极低 |
> | BLE 协议栈 | 开源（NimBLE/Bluedroid） | 闭源（SoftDevice） |
> | WiFi | 内置 | 无 |
> | 开发生态 | Arduino、ESP-IDF | Zephyr、nRF Connect SDK |
> | 适合场景 | IoT 网关、Maker 项目 | 电池供电传感器、穿戴设备 |

### 现代开发板推荐

| 开发板 | 芯片 | 特点 | 适用场景 | 参考价格 |
|--------|------|------|----------|----------|
| nRF52840 DK | nRF52840 | Nordic 官方开发板，功能最全 | 原型开发、学习 | ~$50 |
| nRF52833 DK | nRF52833 | 性价比高的 Nordic 开发板 | 一般 BLE 开发 | ~$35 |
| ESP32-DevKitC | ESP32 | 便宜、社区活跃 | IoT 项目、Maker | ~$5 |
| ESP32-C3-DevKitM | ESP32-C3 | RISC-V 架构，成本极低 | 成本敏感项目 | ~$4 |
| Particle Xenon | nRF52840 | 支持 Mesh 网络 | 分布式传感器网络 | ~$15 |
| Adafruit Feather nRF52840 | nRF52840 | Arduino 兼容，电池充电 | 可穿戴设备 | ~$25 |

---

## Android BLE API 变化

原书[第八章 Android 编程](./chapter8.md)基于 Android 4.3/4.4 的 BLE API 编写。从那时起，Android 的 BLE API 经历了多次重大变化。

### Android 12+ 权限模型变更

从 Android 12（API 31）开始，BLE 扫描和连接的权限模型发生了重大变化。这是 Android BLE 开发中最重要的变化之一。

#### 旧权限模型（Android 11 及以下）

```xml
<!-- 需要位置权限才能扫描 BLE -->
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.BLUETOOTH" />
<uses-permission android:name="android.permission.BLUETOOTH_ADMIN" />
```

在 Android 11 及以下，BLE 扫描需要位置权限（ACCESS_FINE_LOCATION 或 ACCESS_COARSE_LOCATION）。这是因为 BLE 信标可以用于定位追踪，Google 将其归类为位置相关功能。

#### 新权限模型（Android 12+）

```xml
<!-- 新的 BLE 专用权限 -->
<uses-permission android:name="android.permission.BLUETOOTH_SCAN"
    android:usesPermissionFlags="neverForLocation" />
<uses-permission android:name="android.permission.BLUETOOTH_CONNECT" />
<uses-permission android:name="android.permission.BLUETOOTH_ADVERTISE" />
```

> **重要变化**：`BLUETOOTH_SCAN` 权限可以声明 `neverForLocation` 标志，表示你的应用不会使用 BLE 扫描来确定用户位置。这样就不需要位置权限了，保护了用户隐私。

#### 兼容旧版本的最佳实践

```xml
<!-- Android 12+ -->
<uses-permission android:name="android.permission.BLUETOOTH_SCAN"
    android:usesPermissionFlags="neverForLocation" />
<uses-permission android:name="android.permission.BLUETOOTH_CONNECT" />
<uses-permission android:name="android.permission.BLUETOOTH_ADVERTISE" />

<!-- Android 11 及以下 -->
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"
    android:maxSdkVersion="30" />
<uses-permission android:name="android.permission.BLUETOOTH"
    android:maxSdkVersion="30" />
<uses-permission android:name="android.permission.BLUETOOTH_ADMIN"
    android:maxSdkVersion="30" />
```

### BluetoothLeScanner 替代旧 API

原书[第八章 连接到远端设备](./chapter8.md#连接到远端设备)中使用的 `BluetoothAdapter.startLeScan()` 方法已被弃用。现代 Android BLE 开发应使用 `BluetoothLeScanner`。

#### 旧方法（已弃用）

```java
// 旧方法（已弃用，不推荐使用）
bluetoothAdapter.startLeScan(new BluetoothAdapter.LeScanCallback() {
    @Override
    public void onLeScan(BluetoothDevice device, int rssi, byte[] scanRecord) {
        // 处理设备
    }
});
```

#### 新方法（推荐）

```java
BluetoothLeScanner scanner = bluetoothAdapter.getBluetoothLeScanner();

// 配置扫描设置
ScanSettings settings = new ScanSettings.Builder()
    .setScanMode(ScanSettings.SCAN_MODE_LOW_POWER)  // 低功耗模式
    .setReportDelay(0)  // 立即报告
    .build();

// 配置过滤器（可选）
List<ScanFilter> filters = new ArrayList<>();
ScanFilter filter = new ScanFilter.Builder()
    .setServiceUuid(ParcelUuid.fromString("0000180f-0000-1000-8000-00805f9b34fb"))  // 电池服务 UUID
    .build();
filters.add(filter);

// 开始扫描
scanner.startScan(filters, settings, new ScanCallback() {
    @Override
    public void onScanResult(int callbackType, ScanResult result) {
        BluetoothDevice device = result.getDevice();
        int rssi = result.getRssi();
        // 处理设备
    }

    @Override
    public void onBatchScanResults(List<ScanResult> results) {
        // 批量扫描结果
    }

    @Override
    public void onScanFailed(int errorCode) {
        // 扫描失败处理
    }
});
```

### 后台扫描限制

Android 对后台 BLE 扫描施加了越来越严格的限制：

| Android 版本 | 后台扫描限制 |
|-------------|-------------|
| Android 8.0+ | 后台扫描频率受限，每小时只能扫描几次 |
| Android 10+ | 后台扫描需要位置权限 |
| Android 12+ | 可以使用 `BLUETOOTH_SCAN` 权限在后台扫描（不需要位置权限） |
| Android 14+ | 进一步限制后台扫描，需要声明 `BLUETOOTH_SCAN` 且不能使用 `neverForLocation` |

### 现代 Android BLE 最佳实践

1. **使用 Kotlin 协程**：简化异步 BLE 操作

```kotlin
suspend fun connectToDevice(device: BluetoothDevice) {
    return suspendCancellableCoroutine { continuation ->
        val callback = object : BluetoothGattCallback() {
            override fun onConnectionStateChange(gatt: BluetoothGatt, status: Int, newState: Int) {
                if (newState == BluetoothProfile.STATE_CONNECTED) {
                    continuation.resume(Unit)
                } else {
                    continuation.resumeWithException(Exception("Connection failed"))
                }
            }
        }
        device.connectGatt(context, false, callback)
    }
}
```

2. **处理权限变更**：同时支持新旧权限模型

```kotlin
fun checkBlePermissions(): Boolean {
    return if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.S) {
        // Android 12+
        hasPermission(Manifest.permission.BLUETOOTH_SCAN) &&
        hasPermission(Manifest.permission.BLUETOOTH_CONNECT)
    } else {
        // Android 11 及以下
        hasPermission(Manifest.permission.ACCESS_FINE_LOCATION)
    }
}
```

3. **使用 Foreground Service**：需要持续扫描时使用前台服务

```xml
<service
    android:name=".BleScanService"
    android:foregroundServiceType="location" />
```

4. **处理连接状态变化**：BLE 连接可能随时断开，需要优雅处理

```kotlin
override fun onConnectionStateChange(gatt: BluetoothGatt, status: Int, newState: Int) {
    when (newState) {
        BluetoothProfile.STATE_CONNECTED -> {
            // 连接成功，开始服务发现
            gatt.discoverServices()
        }
        BluetoothProfile.STATE_DISCONNECTED -> {
            // 连接断开，尝试重连
            if (shouldReconnect) {
                reconnect(gatt.device)
            }
        }
    }
}
```

---

## iOS Core Bluetooth 更新

原书[第九章 iOS 编程](./chapter9.md)基于 iOS 7 的 Core Bluetooth 框架编写。虽然 Core Bluetooth 的核心概念没有变化，但 iOS 在权限、后台模式和 API 方面有多次更新。

### iOS 13+ 后台模式改进

iOS 13 对 Core Bluetooth 的后台模式进行了改进：

- 后台连接更稳定
- 后台通知传递更可靠
- 减少了后台模式下的连接断开

### 权限和隐私变化

| iOS 版本 | 变化 |
|----------|------|
| iOS 13.0+ | 引入 `CBManagerState` 替代 `CBCentralManagerState` |
| iOS 13.4+ | 支持 `CBAdvertisementDataIsConnectable` 键 |
| iOS 15.0+ | 改进的后台扫描行为 |
| iOS 16.0+ | 更严格的后台模式限制 |

### 现代 iOS BLE 开发注意事项

1. **使用 Swift Concurrency**：iOS 15+ 支持 async/await，简化 BLE 代码

```swift
func connect(to peripheral: CBPeripheral) async throws {
    try await withCheckedThrowingContinuation { continuation in
        centralManager.connect(peripheral, options: nil)
        // 在 delegate 中处理连接结果
    }
}
```

2. **处理隐私权限**：确保在 Info.plist 中正确声明 `NSBluetoothAlwaysUsageDescription`

```xml
<key>NSBluetoothAlwaysUsageDescription</key>
<string>此应用需要使用蓝牙来连接设备</string>
```

3. **后台模式配置**：在 Xcode 项目设置中启用 "Uses Bluetooth LE accessories" 后台模式

4. **iBeacon 开发**：需要 "Location Always" 权限，原书[第九章 iBeacon](./chapter9.md#ibeacon)中的代码仍然适用，但需要更新权限声明

---

## 现代工具链

### VS Code + PlatformIO

原书[第十章 嵌入式工具链](./chapter10.md#嵌入式工具链)中使用的 Eclipse + GNU ARM 工具链已经过时。现代嵌入式开发更倾向于使用 VS Code + PlatformIO。

#### 为什么选择 PlatformIO？

| 特性 | Eclipse（原书） | PlatformIO（现代） |
|------|----------------|-------------------|
| 编辑器 | 基础 | 现代化（VS Code 集成） |
| 包管理 | 手动 | 内置包管理器 |
| 调试 | 复杂配置 | 一键调试 |
| 跨平台 | 需要手动配置 | 原生支持 |
| 库管理 | 手动下载 | 内置库注册表 |
| 学习曲线 | 陡峭 | 平缓 |

#### 安装 PlatformIO

1. 安装 [VS Code](https://code.visualstudio.com/)
2. 在扩展市场搜索并安装 "PlatformIO IDE"
3. 创建新项目：

```bash
# 使用命令行创建项目
pio init --board nrf52840_dk --project-option "framework=zephyr"
```

4. 编写代码、编译、上传：

```bash
# 编译
pio run

# 上传到开发板
pio run --target upload

# 打开串口监视器
pio device monitor
```

### Zephyr RTOS 的 BLE 支持

原书[第十章 mbed BLE API](./chapter10.md#mbed-ble-api)中介绍的 mbed 平台仍然存在，但 Zephyr RTOS 已经成为开源 BLE 开发的主流选择。

#### 什么是 Zephyr？

Zephyr 是 Linux 基金会托管的开源 RTOS，支持多种架构（ARM、RISC-V、Xtensa 等）和多种开发板。它对 BLE 有出色的支持：

- 完整的 BLE 主机协议栈
- 支持 BLE 5.0+ 特性（2M PHY、Coded PHY、扩展广播）
- 蓝牙 Mesh 支持
- LE Audio 支持（实验中）
- 跨硬件平台（nRF52、ESP32、STM32 等）

#### 使用 Zephyr 开发 BLE 应用

示例：创建一个简单的 BLE 心率服务

```c
#include <zephyr/kernel.h>
#include <zephyr/bluetooth/bluetooth.h>
#include <zephyr/bluetooth/uuid.h>
#include <zephyr/bluetooth/gatt.h>

static uint8_t heart_rate_measurement = 60;

static struct bt_gatt_attr attrs[] = {
    BT_GATT_PRIMARY_SERVICE(BT_UUID_HRS),
    BT_GATT_CHARACTERISTIC(BT_UUID_HRS_MEASUREMENT,
                           BT_GATT_CHRC_NOTIFY,
                           BT_GATT_PERM_READ,
                           NULL, NULL, NULL),
    BT_GATT_CCC(NULL, NULL),
};

static struct bt_gatt_service hrs = BT_GATT_SERVICE(attrs);

void main(void)
{
    bt_enable(NULL);
    bt_gatt_service_register(&hrs);
    bt_le_adv_start(BT_LE_ADV_CONN, NULL, 0, NULL, 0);

    while (1) {
        bt_gatt_notify(NULL, &attrs[1],
                       &heart_rate_measurement,
                       sizeof(heart_rate_measurement));
        k_sleep(K_SECONDS(1));
    }
}
```

> **优势**：使用 Zephyr 开发 BLE 应用的优势包括统一的 API（跨硬件平台可移植）、活跃的社区和商业支持、与 Nordic Connect SDK、ESP-IDF 等厂商 SDK 集成。

### 现代调试工具

#### nRF Connect for Desktop

Nordic 提供的免费桌面工具，替代了原书[第十章 写入 nRF51822](./chapter10.md#写入-nrf51822)中提到的 nRFGo Studio：

- **协议嗅探器（Protocol Sniffer）**：捕获和分析 BLE 空中数据包
- **GATT 客户端/服务器测试**：模拟中心设备或外围设备
- **功耗分析**：测量和分析设备功耗
- **支持 Windows、macOS、Linux**

#### nRF Connect for Mobile

移动应用，用于测试 BLE 设备：

- 扫描和连接 BLE 设备
- 读写 GATT 特征
- 创建自定义 GATT 服务器
- 支持 iOS 和 Android

#### Wireshark

开源网络协议分析器，支持 BLE 嗅探：

- 配合 Nordic 或 TI 的嗅探器硬件使用
- 可视化 BLE 协议栈交互
- 过滤和搜索功能强大
- 原书[第六章 PCA10000 USB Dongle 和 Wireshark](./chapter6.md#pca10000-usb-加密狗和-wireshark)中的内容仍然适用

---

## 现代 BLE 开发最佳实践

### 1. 使用 BLE 5.0+ 特性

如果你的目标硬件支持，充分利用 BLE 5.0+ 的新特性（见[增订版 A：BLE 5.0](./addendum-ble-evolution.md#ble-502016-年更快更远更多)）：

- 使用 2 Mbps PHY 加快数据传输
- 使用 Coded PHY 扩展通信距离
- 使用扩展广播传输更多数据

### 2. 关注功耗优化

BLE 的核心优势是低功耗，开发时应始终关注功耗（原书[第一章 关键限制](./chapter1.md#关键限制)中的功耗分析仍然适用）：

- 使用最长的连接间隔（在满足需求的前提下）
- 启用从机延迟（Slave Latency）
- 仅在需要时开启射频
- 使用硬件加速加密

### 3. 处理连接可靠性

BLE 连接可能因各种原因断开：

- 实现自动重连逻辑
- 缓存 GATT 服务发现结果（原书[第四章 属性缓存](./chapter4.md#属性缓存)中的概念仍然适用）
- 处理 MTU 交换失败
- 监控连接质量（RSSI、连接事件丢失）

### 4. 安全性

现代 BLE 应用应该重视安全（原书[第三章 安全](./chapter3.md#安全)和[第四章 安全](./chapter4.md#安全)中的概念仍然适用，但需要更新）：

- 使用 LE 安全连接（LE Secure Connections）
- 启用绑定（Bonding）存储密钥
- 使用隐私功能保护设备地址
- 实现适当的访问控制

### 5. 跨平台兼容性

如果你的应用需要同时支持 iOS 和 Android：

- 测试两个平台的 BLE 行为差异
- iOS 对后台操作有更多限制
- Android 设备间的 BLE 实现差异较大
- 考虑使用跨平台框架（如 Flutter + flutter_blue_plus）

---

## 推荐阅读和资源

- [nRF Connect SDK 文档](https://docs.nordicsemi.com/)
- [ESP-IDF BLE 编程指南](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-reference/bluetooth/index.html)
- [Zephyr BLE 文档](https://docs.zephyrproject.org/latest/connectivity/bluetooth/bluetooth.html)
- [Android BLE 开发者指南](https://developer.android.com/guide/topics/connectivity/bluetooth/ble-overview)
- [iOS Core Bluetooth 编程指南](https://developer.apple.com/documentation/corebluetooth)
- [蓝牙开发者门户](https://www.bluetooth.com/develop-with-bluetooth/)
