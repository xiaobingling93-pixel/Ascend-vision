# Torchvision Adapter

## 简介

本项目开发了Torchvision Adapter插件，用于昇腾适配Torchvision框架。
目前该适配框架增加了对Torchvision所提供的常用算子的支持，提供了基于cv2和基于昇腾NPU的图像处理加速后端以加速图像处理。

## 目录结构

关键目录如下：

```ColdFusion
├─ci                             # 持续集成脚本目录
├─torchvision_npu                # 核心适配目录
│  ├─csrc/                       # 底层核心目录
│  ├─datasets/                   # 数据集加载适配目录
│  ├─io/                         # 图像io加速目录
│  ├─transforms/                 # 图像预处理核心目录
│  ├─ops/                        # 视觉算子NPU实现目录
├─docs                           # 项目文档目录
├─third_party/                   # 外部依赖目录
└─test                           # 测试目录
```

## 版本说明

|Torchvision Adapter分支 |Torchvision Adapter Tag  | PyTorch版本   | PyTorch Extension版本    |Torchvision版本 | 驱动版本 | CANN版本|
| -----------------------|----------------------- | ------------- | ------------------------ | ------------- | -----| ---------|
| master  |    -        |     2.7.1     |   2.7.1.post2        | 0.22.1  |  Ascend HDK 25.3.0 | CANN 8.5.0 |
| v0.21.0-dev  |    v0.21.0-7.1.0        |     2.6.0     |   2.6.0        | 0.21.0  |  Ascend HDK 25.2.0 | CANN 8.2.RC1 |
| v0.20.1-dev      |    v0.20.1-7.1.0        |     2.5.1    |   2.5.1.post1         | 0.20.1  |  Ascend HDK 25.2.0 | CANN 8.2.RC1 |
| v0.16.0-dev |    v0.16.0-7.1.0        |     2.1.0     |   2.1.0.post13 | 0.16.0  | Ascend HDK 25.2.0 | CANN 8.2.RC1 |
| v0.20.1-dev      |    v0.20.1-7.0.0        |     2.5.1     |   2.5.1        | 0.20.1  | Ascend HDK 25.0.RC1 | CANN 8.1.RC1|
| v0.16.0-dev |    v0.16.0-7.0.0        |     2.1.0     |   2.1.0.post12 | 0.16.0  | Ascend HDK 25.0.RC1 | CANN 8.1.RC1|
| v0.16.0-dev |    v0.16.0-6.0.0        |     2.1.0     |   2.1.0.post10 | 0.16.0  | Ascend HDK 24.1.RC3 | CANN 8.0.0 |
| v0.16.0-dev |    v0.16.0-6.0.rc3        |     2.1.0     |   2.1.0.post8 | 0.16.0  | Ascend HDK 24.1.RC3 | CANN 8.0.RC3|
| v0.16.0-dev |     v0.16.0-6.0.rc2        |     2.1.0     |   2.1.0.post6 | 0.16.0  | Ascend HDK 24.1.RC2 | CANN 8.0.RC2|
| v0.12.0-dev |     v0.12.0-6.0.rc2        |     1.11.0     |   1.11.0.post14 | 0.12.0  | Ascend HDK 24.1.RC2 | CANN 8.0.RC2|

## 环境部署

Torchvision Adapter插件的安装操作，具体请参见《[Torchvision Adapter插件 软件安装](docs/zh/installation_guide.md)》。

## 快速入门

Torchvision Adapter插件的快速入门，具体请参见《[Torchvision Adapter插件 快速入门](docs/zh/quick_start.md)》。

## 特性介绍

Torchvision Adapter包括如下主要特性，各特性用法请参见《[Torchvision Adapter特性指南](docs/zh/feature.md)》：

- 使用cv2图像处理后端
- 使用DVPP图像处理后端
- 使用DVPP视频处理后端
- 数据预处理使用DVPP的限制
- NPU算子支持原生算子列表
- 使用CPU进行图像处理
- 使用鲲鹏CPU进行视频处理

## 版本维护策略

**维护模式** - 本项目已进入基本维护阶段，不再进行新功能开发，仅进行必要的缺陷修复和安全更新。不再接受新功能提案（RFC）、特性请求或代码贡献。

## 联系我们

如果有任何疑问或建议，请提交[GitCode Issues](https://gitcode.com/Ascend/vision/issues)，我们会尽快回复，感谢您的支持。

## 安全声明

Torchvision Adapter的系统安全加固、运行用户建议和文件权限控制等内容，请参见[Torchvision Adapter 安全声明](docs/zh/SECURITYNOTE.md)。

## 免责声明

致Torchvision Adapter插件使用者

- 本插件仅供调试和开发使用，使用者需自行承担使用风险，并理解以下内容：

    - 数据处理及删除：用户在使用本插件过程中产生的数据属于用户责任范畴。建议用户在使用完毕后及时删除相关数据，以防信息泄露。
    - 数据保密与传播：使用者了解并同意不得将通过本插件产生的数据随意外发或传播。对于由此产生的信息泄露、数据泄露或其他不良后果，本插件及其开发者概不负责。
    - 用户输入安全性：用户需自行保证输入的命令行的安全性，并承担因输入不当而导致的任何安全风险或损失。对于输入命令行不当所导致的问题，本插件及其开发者概不负责。
- 免责声明范围：本免责声明适用于所有使用本插件的个人或实体。使用本插件即表示您同意并接受本声明的内容，并愿意承担因使用该功能而产生的风险和责任，如有异议请停止使用本插件。
- 在使用本工具之前，请谨慎阅读并理解以上免责声明的内容。对于使用本插件所产生的任何问题或疑问，请及时联系开发者。

## License

Torchvision Adapter插件的使用许可证，具体请参见[LICENSE](LICENSE)文件。

## 致谢

感谢来自社区的每一个PR!
