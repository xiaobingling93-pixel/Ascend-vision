# Torchvision Adapter插件 快速入门

## 运行环境变量

运行以下命令初始化CANN环境变量。

```bash
source /usr/local/Ascend/ascend-toolkit/set_env.sh
```

以上命令以root用户安装后的默认路径为例，请用户根据set\_env.sh的实际路径进行替换。

> [!NOTE]  
> docker中运行使用dvpp功能，需要映射`/dev/dvpp_cmdlist`设备文件，文件权限相关说明请参考[对Docker进行安全加固](https://www.hiascend.com/document/detail/zh/mindcluster/730/clustersched/dlug/mxdlug_com_022.html)。

## NPU适配

以Torchvision的`torchvision.ops.nms`算子为例，在CUDA/CPU环境中，该算子通过如下方法进行调用：

   ``` python
    # 算子的cuda/cpu版本调用
    import torch
    import torchvision
    
    ...
    torchvision.ops.nms(boxes, scores, iou_threshold) # boxes 和 scores 为 CPU/CUDA Tensor
   ```

   经过安装Torchvision Adapter插件之后，只需增加`import torchvision_npu`则可按照原生方式调用Torchvision算子。

   ```python
    # 算子的npu版本调用
    import torch
    import torch_npu
    import torchvision
    import torchvision_npu
    
    ...
    torchvision.ops.nms(boxes, scores, iou_threshold) # boxes 和 scores 为 NPU Tensor
   ```
