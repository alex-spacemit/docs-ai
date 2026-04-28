---
sidebar_position: 4
---

# 其他数学库

> **其他数学库** 主要用于为上层 AI 框架、数值计算程序和高性能算子提供基础数学能力。在 SpacemiT RISC-V 平台上，常用的预编译数学库主要包括 OpenBLAS 和 SLEEF。

---

- [其他数学库](#其他数学库)
    - [目前支持的数学库](#目前支持的数学库)
    - [资源获取](#资源获取)
    - [OpenBLAS Demo](#openblas-demo)
    - [SLEEF Demo](#sleef-demo)


### 目前支持的数学库

- **OpenBLAS**：提供 BLAS/LAPACK 接口，常用于矩阵乘、向量运算、线性代数例程，是许多数值库和推理框架的底层依赖。
- **SLEEF**：提供高性能 SIMD 数学函数实现，常用于 `sin`、`cos`、`exp`、`log` 等标量或向量数学计算优化。

在 RISC-V 平台上，这两类库通常用于：

- 作为推理框架或数值程序的底层依赖
- 为自定义算子、Kernel、Benchmark 程序提供高性能数学实现
- 支撑矩阵计算、激活函数近似、预处理与后处理中的数值计算流程

### 资源获取

> &#x2139;&#xfe0f;OpenBLAS 与 SLEEF 的 RISC-V 预编译包建议从 SpacemiT 发布目录或配套发布包中获取，目录内容会按版本持续更新。

建议优先下载包含 `include/` 与 `lib/` 的二进制发布包，并根据实际工具链设置运行库路径。

~~~ bash
# 使用预编译包时，通常只需要准备头文件目录和 lib 目录
export MATH_LIB_DIR=/path/to/riscv64-math-libs
export LD_LIBRARY_PATH=${MATH_LIB_DIR}/lib:${LD_LIBRARY_PATH}
~~~

### OpenBLAS Demo

下面的示例演示了如何调用 OpenBLAS 完成一个最小矩阵乘：

- C

~~~ c
#include <cblas.h>
#include <stdio.h>

int main() {
  const int m = 2;
  const int n = 2;
  const int k = 2;

  float a[4] = {1, 2, 3, 4};
  float b[4] = {5, 6, 7, 8};
  float c[4] = {0, 0, 0, 0};

  cblas_sgemm(CblasRowMajor, CblasNoTrans, CblasNoTrans,
              m, n, k, 1.0f, a, k, b, n, 0.0f, c, n);

  printf("C = [%f, %f, %f, %f]\n", c[0], c[1], c[2], c[3]);
  return 0;
}
~~~

- Shell

~~~ bash
export OPENBLAS_DIR=/path/to/openblas-riscv64
export LD_LIBRARY_PATH=${OPENBLAS_DIR}/lib:${LD_LIBRARY_PATH}

gcc demo_openblas.c -o demo_openblas \
  -I${OPENBLAS_DIR}/include \
  -L${OPENBLAS_DIR}/lib \
  -lopenblas

./demo_openblas
~~~

### SLEEF Demo

下面的示例演示了如何调用 SLEEF 数学函数库：

- C

~~~ c
#include <stdio.h>
#include <sleef.h>

int main() {
  double x = 0.5;
  double s = Sleef_sind1_u10(x);
  double c = Sleef_cosd1_u10(x);

  printf("sin(%f) = %f\n", x, s);
  printf("cos(%f) = %f\n", x, c);
  return 0;
}
~~~

- Shell

~~~ bash
export SLEEF_DIR=/path/to/sleef-riscv64
export LD_LIBRARY_PATH=${SLEEF_DIR}/lib:${LD_LIBRARY_PATH}

gcc demo_sleef.c -o demo_sleef \
  -I${SLEEF_DIR}/include \
  -L${SLEEF_DIR}/lib \
  -lsleef

./demo_sleef
~~~

如果上层程序同时依赖 OpenBLAS 与 SLEEF，建议统一收敛到同一套 RISC-V 工具链和 sysroot，以避免 ABI 或运行时库不一致问题。
