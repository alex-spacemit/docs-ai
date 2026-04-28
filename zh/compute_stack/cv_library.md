---
sidebar_position: 3
---

# 计算机视觉库

> **计算机视觉库** 主要面向图像读取、预处理、后处理以及传统视觉算法开发场景。在 SpacemiT RISC-V 平台上，推荐优先使用 OpenCV 的 RISC-V 预编译版本，减少交叉编译和依赖配置成本。

---

- [计算机视觉库](#计算机视觉库)
    - [目前支持的CV库](#目前支持的cv库)
    - [资源获取](#资源获取)
    - [OpenCV Demo](#opencv-demo)


### 目前支持的CV库

OpenCV 是最常用的开源计算机视觉基础库之一，常见用途包括：

- 图像与视频的读取、保存、格式转换
- Resize、Crop、Pad、颜色空间变换等预处理流程
- 检测、分割、分类任务中的后处理与可视化
- 与 ONNXRuntime、xslim 等推理框架协同完成端到端应用开发

在 SpacemiT 平台的软件栈中，OpenCV 往往作为 AI 应用的前后处理组件使用，而不是独立的推理引擎。

### 资源获取

> &#x2139;&#xfe0f;OpenCV 的 RISC-V 预编译包建议从 SpacemiT 归档目录获取，目录内容会随版本更新。

可通过浏览器或命令行访问对应归档目录，选择当前发布的 riscv64 版本压缩包。

~~~ bash
# 示例：进入归档目录后选择对应的 riscv64 OpenCV 预编译包
# 建议在发布页或归档目录中选择最新版本
~~~

预编译包通常至少包含以下内容：

- `include/` 头文件
- `lib/` 动态库或静态库
- `bin/` 或示例程序

使用时只需在编译阶段指定头文件和库路径，并在运行阶段补充 `LD_LIBRARY_PATH`。

### OpenCV Demo

下面给出一个最小图像读取示例，用于验证 OpenCV 预编译包是否可正常工作。

- C++

~~~ cpp
#include <iostream>
#include <opencv2/opencv.hpp>

int main() {
  cv::Mat image = cv::imread("test.jpg");
  if (image.empty()) {
    std::cerr << "failed to load image" << std::endl;
    return 1;
  }

  cv::Mat resized;
  cv::resize(image, resized, cv::Size(224, 224));
  std::cout << "image size: " << resized.cols << "x" << resized.rows << std::endl;
  return 0;
}
~~~

- Shell

~~~ bash
export OPENCV_DIR=/path/to/opencv-riscv64
export LD_LIBRARY_PATH=${OPENCV_DIR}/lib:${LD_LIBRARY_PATH}

g++ demo_opencv.cpp -o demo_opencv \
  -I${OPENCV_DIR}/include/opencv4 \
  -L${OPENCV_DIR}/lib \
  -lopencv_core -lopencv_imgcodecs -lopencv_imgproc

./demo_opencv
~~~

如果应用还需要视频解码、摄像头访问或更多图像格式支持，可在此基础上继续链接 `opencv_videoio` 等模块。
