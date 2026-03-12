# 特性介绍
## 使用cv2图像处理后端

1. Opencv-python版本推荐（推荐使用opencv-python=4.6.0）。

   ```python
    pip3 install opencv-python==4.6.0.66
   ```



2. 脚本适配。

   通过以下方式使能Opencv加速，在导入torchvision相关包前导入torchvision_npu包，在构造dataset前设置图像处理后端为cv2：

   ```python
   # 使能cv2图像处理后端
   
    ...
    import torchvision
    import torchvision_npu # 导入torchvision_npu包
    import torchvision.datasets as datasets
    ...
    torchvision.set_image_backend('cv2') # 设置图像处理后端为cv2
    ...
    train_dataset = torchvision.datasets.ImageFolder(...)
    ...
   ```

   

3. cv2算子适配原则。

   - transforms方法实际调用pillow算子以及tensor算子，cv2算子调用接口与pillow算子调用接口保持一致。

   - **cv2算子只支持numpy.ndarray作为入参**，resize接口支持输入Image.Image格式，在内部会做Image->array和array->Image的转换，**其他类型会直接抛出类型异常。**

     ```python
     TypeError(
         "Using cv2 backend, the image data type should be numpy.ndarray or PIL.Image.Image. Unexpected type {}".format(type(img)))
    
     ```
   - 注意如果输入是Image.Image格式，会造成数据转换的额外开销，性能有所下降。
     ``` python
     warnings.warn(
            "Performance Notice: PIL Image (Image.Image) input detected. "
            "Automatic conversion to NumPy array may cause processing overhead."
        )
     ```

   - **cv2算子不支持pillow算子的BOX、HAMMING插值方式，会直接抛类型异常。**

     由于pillow算子共有6种插值方式分别是NEAREST、BILINEAR、BICUBIC、BOX、HAMMING、LANCZOS，但cv2算子支持5种插值方式NEAREST、LINEAR 、AREA、CUBIC、LANCZOS4，pillow算子的BOX、HAMMING插值方式存在无法映射cv2算子实现，此时使用cv2图像处理后端会直接抛出TypeError。

     ```python
     TypeError("Opencv does not support box and hamming interpolation")
     ```

   - cv2算子插值底层实现和pillow插值底层实现略有差异，存在图像处理结果差异，因此由插值方式导致的图像处理结果不一致情况为正常现象，通常两者结果以余弦相似度计算，结果近似在99%以内。


4. cv2算子支持列表以及性能加速情况。
   
   单算子实验结果在arm架构的Atlas训练系列产品上获得，单算子实验的cv2算子输入为np.ndarray，pillow算子输入为Image.Image。cv2算子支持列表见表1。

   **表 1**  cv2算子支持列表
      
     | ops               | 处理结果是否和pillow完全一致       | cv2单算子FPS | pillow单算子FPS | 加速比      |
   |-------------------------| -------------------------------------- | ------------ | --------------- | ----------- |
   | to_pil_image      | √（只接受tensor或np.ndarray） | -            | -               |             |
   | pil_to_tensor     | √                       | 753          | **2244**        | -198%       |
   | to_tensor         | √                       | **259**      | 240             | **7.9%**    |
   | normalize         | √（只接受tensor输入）          | -            | -               |             |
   | hflip             | √                       | **4629**     | 4230            | **9.43%**   |
   | resized_crop      | 插值底层实现有差异               | **1096**     | 445             | **146.29%** |
   | vflip             | √                       | **8795**     | 6587            | **33.52%**  |
   | resize            | 插值底层实现有差异               | **1086**     | 504             | **115.48%** |
   | crop              | √                       | **10928**    | 6743            | **62.06%**  |
   | center_crop       | **√**                   | **19267**    | 9606            | **100.57%** |
   | pad               | √                       | **3394**     | 1310            | **159.08%** |
   | rotate            | 插值底层实现有差异               | **1597**     | 1346            | **18.65%**  |
   | affine            | 插值底层实现差异，仿射矩阵获取也有差异     | **1604**     | 1287            | **24.64%**  |
   | invert            | √                       | **8110**     | 2852            | **184.36%** |
   | perspective       | 插值底层实现有差异               | **674**      | 288             | **134.03%** |
   | adjust_brightness | √                       | **1174**     | 510             | **130.20%** |
   | adjust_contrast   | √                       | **610**      | 326             | **87.12%**  |
   | adjust_saturation | **√**                   | **603**      | 385             | **56.62%**  |
   | adjust_hue        | 底层实现有差异                 | **278**      | 76              | **265.79%** |
   | posterize         | √                       | **2604**     | 2356            | **10.53%**  |
   | solarize          | √                       | **3109**     | 2710            | **14.72%**  |
   | adjust_sharpness  | 底层实现有差异                 | **314**      | 293             | **7.17%**   |
   | autocontrast      | √                       | **569**      | 540             | **5.37%**   |
   | equalize          | 底层实现有差异                 | **764**      | 590             | **29.49%**  |
   | gaussian_blur     | 底层实现有差异                 | **1190**     | 2               | **59400%**  |
   | rgb_to_grayscale  | 底层实现有差异                 | **3404**     | 710             | **379.44%** |

## 使用DVPP图像处理后端

1. 脚本适配。

   通过以下方式使能DVPP加速，在导入torchvision相关包后导入torchvision_npu包。
```python
   # 使能DVPP图像处理后端
   ...
   import torch
   import torch_npu
   import torchvision
   import torchvision_npu # 导入torchvision_npu包

   torchvision.set_image_backend('npu') # 设置图像处理后端为npu，即使能DVPP加速
   ...
   npu_output = torchvision.datasets.folder.default_loader(...)
   ```

2. 执行单元测试脚本。

   ```
   cd test/test_npu/
   python test_default_loader.py
   ```
   输出结果OK即为验证成功。

3. DVPP支持列表

   为如下图像/视频处理方法提供了DVPP处理能力，在设置图像处理后端为NPU时，使能DVPP加速。支持接口列表如下表2所示。

   **表 2**  DVPP支持功能列表

   | datasets/transforms/io      | functional       | 处理结果是否和社区接口一致 |    限制                 |
   |----------------------|------------------|--------------------|------------------------------  |
   | default_loader       |    | 输出为NPU tensor，一般与to_tensor搭配使用                        | JPEG图像分辨率: 6x4~32768x32768   |
   | ToTensor             | to_tensor        | 支持四维tensor输入，一般与default_loader搭配使用                 | 分辨率: 6x4~4096x8192     |
   | ColorJitter          | adjust_hue       | 底层实现有差异，误差±1左右 | 分辨率: 6x4~4096x8192     |
   | encode_jpeg          |              |   | 分辨率: 32x32~8192x8192<br>输出宽高需要2对齐 |
   | decode_jpeg          |              | 输出结果为NPU tensor  | device参数指定为npu|

## 使用DVPP视频处理后端

1. 脚本适配。

   通过以下方式使能DVPP加速，在导入torchvision相关包后导入torchvision_npu包，并设置视频处理后端为npu：

   ```python
   # 使能DVPP视频处理后端
   ...
   import torchvision
   import torchvision_npu # 导入torchvision_npu包
   ...
   torchvision.set_video_backend('npu') # 设置视频处理后端为npu，即使能DVPP加速
   ...
   vframes, aframes, info = torchvision.io.read_video(...)
   ...
   ```

2. 执行单元测试脚本。

   ```
   cd test/test_npu/
   python test_read_video.py
   ```
   输出结果OK即为验证成功。

3. DVPP支持列表

   为如下视频处理方法提供了DVPP处理能力，在设置视频处理后端为NPU时，使能DVPP加速。支持接口列表如下表3所示。

   **表 3**  DVPP支持功能列表

   | functional | 处理结果是否和pyav完全一致 | 限制                    |
   | ---------- | -------------------------- | ----------------------- |
   | read_video | 底层实现有差异，误差±3左右 | 仅支持h264/h265编码格式 |

## 数据预处理使用DVPP的限制
在DVPP使用场景中，如果DVPP搭配PyTorch的Dataloader进行数据预处理，存在如下场景使用限制。

限制：使用dataloader多进程加载数据时（单进程不影响），全局作用域中不能包含涉及NPU初始化的代码，以下面代码为例。
   ```python
   # 此用例当前TORCH_NPU套件不支持
   ...
   import torch, torch_npu
   import torchvision
   import torchvision_npu # 导入torchvision_npu包
   ...
   # 全局作用域中包含初始化操作
   torch.npu.set_device(0)

   if __name__ == '__main__':
      ...
      torchvision.set_video_backend('npu') # 设置视频处理后端为npu，即使能DVPP加速
      ...
      dataloader(...,num_workers=4,....)  # 使用多进程数据预处理
      ...
   ```
规避方法：当使能DVPP加载数据且dataloader中使用多进程情况下，应避免在全局作用域进行涉及NPU初始化的操作，可将相应代码放在主函数中，以下面代码为例。
   ```python
   # 规避方法
   ...
   import torch, torch_npu
   import torchvision
   import torchvision_npu # 导入torchvision_npu包
   ...

   if __name__ == '__main__':
      torch.npu.set_device(0) 
      ...
      torchvision.set_video_backend('npu') # 设置视频处理后端为npu，即使能DVPP加速
      ...
      dataloader(...,num_workers=4,....)  # 使用多进程数据预处理
      ...
   ```

## NPU算子支持原生算子列表

   对于torchvision中的原生算子支持情况如表3所示。

   **表 4**  NPU支持的原生算子列表

   | 算子            | 是否支持 |
   |---------------|------|
   | nms           | √    |
   | deform_conv2d | √    |
   | ps_roi_align  | -    |
   | ps_roi_pool   | -    |
   | roi_align     | √    |
   | roi_pool      | √    |
   
   注：我们在v0.xx.x-7.0.0版本后，修复了nms算子在scores参数存在负数情况下的精度问题，如遇此场景，请按[版本配套表](#版本配套表)安装各个组件。

## 使用CPU进行图像处理
使用c++多线程实现算子在使用CPU进行图像处理时的性能优化，以达到加速的目的。
1. 脚本适配

   通过以下方式使能CPU多线程加速图像处理，在导入torchvision相关包前导入torchvision_npu包，在使用前设置图像处理后端为`moal`，以Normalize为例，当输入为cpu侧数据类型为`float`时，图像处理后端为`moal`后会使用该优化方案对该算子进行优化
   ```python
   # 使用优化后的Normalize
   ...
   import torchvision
   import torchvision_npu # 导入torchvision_npu包
   ...
   torchvision.set_image_backend('moal') # 设置图像处理后端为moal
   normalize = torchvision.transforms.Normalize(mean, std, inplace)
   normalize_res = normalize(normalize_input)
   ...
   ```
   
2. 执行单元测试脚本
   
   ```
   cd test/test_cpu/
   python -m unittest discover
   ```
   输出结果OK即为验证成功。

3. CPU优化算子支持列表以及性能加速情况

   单算子实验结果在arm架构的Atlas训练系列产品上获得，单算子实验的结果在OpenSora1.2中使用1080p的视频数据集进行训练时的单算子时间求平均值获得。CPU优化算子支持列表见表5。

   **表 5**  CPU优化算子支持列表
      
   | ops               | 处理结果是否和原生算子完全一致       | 原始单算子时间(ms) | 优化单算子时间(ms) | 加速比         |
   |-------------------|-----------------------|-------------|-------------|-------------|
   | to_tensor         | √（只接受uint 8类型的tensor） | 341.24      | 173.15      | 97.08%      |
   | normalize         | √（只接受float类型的tensor）  | 14.67       | 5.19        | 182.66%     |


## 使用鲲鹏CPU进行视频处理
基于`kunepng`向量化指令，使能多线程，实现算子在使用`鲲鹏CPU`进行视频处理时的性能优化，以达到加速的目的。

1. 脚本适配
   
   设定`torchvision.set_video_backend()`和环境变量`TORCHVISION_OMP_NUM_THREADS`以激活`torchvision.io.read_video`加速分支。
   ```python
   ...
   import os
   import torchvision
   import torchvision_npu # 导入torchvision_npu包
   ...
   torchvision.set_image_backend('pyav')  # 设置视频处理后端为pyav(该值为默认值，若没有改动可缺省)
   os.environ['TORCHVISION_OMP_NUM_THREADS'] = 8  # 通过环境变量设定使用多少线程加速该算子。未设定正整数或平台不支持，则走torchvision官方实现
   video_frame, audio_frame, info = torchvision.io.read_video(video_path)  # 调用原生接口
   ...
   ```
   
   设定环境变量`TORCHVISION_OMP_NUM_THREADS`以激活`torchvision.transforms.v2.functional.uniform_temporal_subsample_video`加速分支。
   ```python
   ...
   import os
   import torchvision
   from torchvision.transform import v2
   import torchvision_npu # 导入torchvision_npu包
   ...
   os.environ['TORCHVISION_OMP_NUM_THREADS'] = 8  # 通过环境变量设定使用多少线程加速该算子。未设定正整数或平台不支持，则走torchvision官方实现
   tensor_sampled = v2.functional.uniform_temporal_subsample_video(tensor_input, num_samples)  # 调用原生接口
   ...
   ```


2. 执行单元测试

   ```bash
   cd test/test_kunpeng/
   python -m unittest discover
   ```
   输出结果OK即为验证成功。

3. `鲲鹏CPU`优化算子支持列表以及性能加速情况

   单算子实验在`arm`架构的`Atlas`训练系列产品上获得，单算子实验的结果在`OpenSora1.2`中使用`1080p`的视频数据集进行训练时，单算子时间求平均值获得。`CPU`优化的视频处理相关算子支持列表见表`6`。

   **表 6**   鲲鹏CPU支持算子列表
      
   | kunpeng                          | 处理结果是否和原生算子完全一致 | 原始单算子时间(ms) | 优化单算子时间(ms) | 加速比     |
   |----------------------------------|------------|-------------|-------------|---------|
   | read_video                       | √（针对`yuv420p,yuvj420p`有向量化加速） | 1657.05     | 737.20      | 124.77% |
   | uniform_temporal_subsample_video | √          | 12.59       | 5.03        | 150.29% |



**Torchvision Adapter适配NPU的方案见[适配指导](docs/适配指导.md)。**