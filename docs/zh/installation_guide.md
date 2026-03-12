# Torchvision Adapter插件 软件安装

## 安装前准备
- 需完成CANN开发或运行环境的安装，具体操作请参考《[CANN 软件安装指南](https://www.hiascend.com/document/detail/zh/canncommercial/850/softwareinst/instg/instg_0000.html)》（商用版）或《[CANN 软件安装指南](https://www.hiascend.com/document/detail/zh/CANNCommunityEdition/850/softwareinst/instg/instg_0000.html)》（社区版）。
- 需完成Ascend Extension for PyTorch插件安装，具体请参考 https://gitcode.com/ascend/pytorch 。
- 昇腾软件栈需要安装的版本请参考[版本说明](../../README.zh.md#版本说明)。
- Python支持版本参考Ascend Extension for PyTorch的[PyTorch与Python版本配套表](https://gitcode.com/Ascend/pytorch/blob/master/README.zh.md)。
-  需完成Torchvision安装。
   以PyTorch 2.6.0版本，匹配安装torchvision 0.21.0为例。

   -  方法1：源码编译安装

      按照以下命令进行编译安装。
      ```shell
      git clone https://github.com/pytorch/vision.git
      cd vision
      git checkout v0.21.0
      # 编包
      python setup.py bdist_wheel
      # 安装
      cd dist
      pip3 install torchvision-0.21.*.whl
      ```

   -  方法2：pip安装
      ```shell
      # 指定官方源安装
      pip install torchvision==0.21.0 --index-url https://download.pytorch.org/whl/cpu
      ```
      > [!NOTE]  
      > pip安装可能出现torch和torchvision不匹配的问题，如有此问题，建议使用源码编译安装。

## 安装步骤

按照以下命令编译安装Torchvision Adapter插件。

```shell
# 下载Torchvision Adapter代码，进入插件根目录
git clone https://gitcode.com/ascend/vision.git vision_npu
cd vision_npu
git checkout v0.21.0-7.1.0
# 安装依赖库
pip3 install -r requirement.txt
# 初始化CANN环境变量
source /usr/local/Ascend/ascend-toolkit/set_env.sh # Default path, change it if needed.
# 编包
python setup.py bdist_wheel
# 安装
cd dist
pip install torchvision_npu-0.21.*.whl
```
使用最新版本，可拉取对应分支最新代码编译安装，稳定版本可以切换到对应分支的tag，参考[版本说明](../../README.zh.md#版本说明)。