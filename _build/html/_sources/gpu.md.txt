GPU
============

### 使用 CUDA toolkit 编译程序后，在  `gpu_test` 分区提交作业，运行时提示错误：`no CUDA-capable device is detected`

可能原因有二种情况：

1.可能是分配到的该计算节点上用于连接CPU 与 GPU 的 PCIe 总线松动，导致无法找到 device。解决方法：在提交作业是可使用 `-x` 选项屏蔽该节点；

2.可能是编译时使用的 CUDA toolkit 和计算节点上安装的 CUDA driver 版本不一致导致，gpu_test 分区的计算节点上的 CUDA driver 版本是与 CUDA 7.5 的 toolkit 对应的。解决方法：使用 CUDA 7.5 或 CUDA 5.0 的 toolkit 编译程序。另外，如果同时设置了多个版本的 CUDA 环境，也会导致该问题出现，解决方法：清除所有有关 CUDA 的环境变量设置，然后重新加载一个版本的 CUDA 环境。