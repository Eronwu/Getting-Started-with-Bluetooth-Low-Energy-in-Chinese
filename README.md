# Getting Started with Bluetooth Low Energy 中文翻译版

> **Getting Started with Bluetooth Low Energy**
> 
> 作者：Kevin Townsend, Carles Cufí, Akiba, and Robert Davidson
> 
> 出版社：O'Reilly Media (2014)

### 初版翻译：[Eron Wu](https://github.com/Eronwu)
### 2026 年修订：AI 辅助精校

---

## 关于这个翻译项目

这是一本 2014 年出版的 BLE 入门经典。虽然蓝牙技术已经迭代到了 6.0 版本，但这本书对 BLE 基础概念（GATT、GAP、Bonding、Encryption 等）的讲解依然清晰易懂，是很好的入门读物。

本书最初由 [Eron Wu](https://github.com/Eronwu) 出于兴趣翻译完成，为中文社区提供了宝贵的学习资料。

2026 年，借助大语言模型的能力，对全书进行了**逐章对照原书原文的全面修订**。不避讳地说，这次修订大量使用了 AI 辅助——对照原书 PDF 提取原文，逐段对比、修正误译、重写生硬句子。人工主要负责抽查和技术概念把关。

效果嘛，比纯人工校对快得多，质量也提升了不少。算是 AI 时代做开源翻译的一个小实践

## 章节导航

### BLE 概述
- [第一章 介绍](./chapter1.md)
- [第二章 协议基础](./chapter2.md)
- [第三章 GAP（广播和连接）](./chapter3.md)
- [第四章 GATT（服务和特征）](./chapter4.md)

### 开发测试工具
- [第五章 硬件平台](./chapter5.md)
- [第六章 调试工具](./chapter6.md)
- [第七章 应用设计工具](./chapter7.md)

### 开发平台
- [第八章 Android 编程](./chapter8.md)
- [第九章 iOS 编程](./chapter9.md)
- [第十章 嵌入式应用开发](./chapter10.md)

## 这次修订改了啥

对照原书 PDF 逐章校对后，发现初版翻译有不少"机翻味"和误译，主要修了这些：

**严重误译举例：**
- `silicon vendors` → "硅供应商" → **"芯片供应商"**
- `Just Works` → "仅工作" → **"简单配对"**
- `Racing to Idle` → "向着空闲而去" → **"快速回到空闲状态"**
- `no intrusive licensing costs` → "没有破坏证书的消费" → **"没有强制性的授权费用"**
- `radio` → 多处误译为"广播" → **"射频"**

**术语统一：**
- `Specification` → "规格书" → **"规范"**
- `Characteristic` → 统一为 **"特征"**
- `Descriptor` → 统一为 **"描述符"**
- `Security Manager` → 统一为 **"安全管理器"**

**语句重写：**
- 大量直译导致的拗口句子重写为通顺的中文
- 修正了一些技术概念的错误描述

## 译者注

专业名词翻译尽量根据行业习惯取舍：

- "蓝牙低功耗" 全书多用 BLE 缩写
- Host → "主机"，Controller → "控制器"
- 可能带来歧义或技术交流中更常用英文的词，保留中英文对照，如时序（timing）、连接（connections）
- 公司名称保留原文，如 Nordic Semiconductor

---

**本书已翻译完成。若文章内容和翻译有不正确之处，恳请提 issue 指正，感谢！也欢迎一起讨论蓝牙技术 📡**
