编译问题
=========

### 在 TH-1A 系统上，使用 mpf90 编译并行程序，提示说  `command not found`

TH-1A 系统上使用的 mpif90 由于有不同版本的 mpi，所以没有设置mpif90 的环境变量，用户可以查看手册“编译环境”章节设置环境变量即可。

### 我需要使用高版本的 python，可以我输入 python 后，系统显示的是 Python 2.6

我们在 TH-1A 系统的共享目录 `/vol-th/software/` 下面部署了python2.7版本，您可以通过设置环境变量来使用高版本的 python。


### 在使用 python 的 numpy 时提示无法找到  `ImportError: No module named Numpy`

python 的常用库安装在 `/usr/lib64/python2.6 ` 下，所以请确保使用 `/usr/bin`下的 python，验证方式如下：

```
>which python /usr/bin/python
>python
Python 2.6.6 (r266:84292, Feb 25 2014, 09:00:08)
[GCC 4.4.7 20120313 (Red Hat 4.4.7-3)] on linux2
>>>import numpy
```

### 常见的 `undefined reference to` 问题解决办法

- 目标文件缺失

	**原因**：当进行可执行程序链接时，链接命令中找不到某个函数所在源代码的目标文件 `***.o`，出现 `undefined reference to ***` 错误。

	**解决办法**：找到需要的目标文件 `***.o` ，然后将其添加到链接命令中，解决问题。

- 库文件缺失

	**原因**：当进行可执行程序链接时，链接命令中找不到某个库函数所在的库文件，出现 `undefined reference to ***` 错误。

	**解决办法**：找到需要的库文件，然后将其绝对路径使用-L 和-l 添加到链接命令中，解决问题。

- 多个库文件连接顺序

	**原因**：当进行可执行程序链接时，所需目标文件和库文件均齐全，但由于链接顺序问题，出现库文件依赖报错，从而同样会出现 `undefined reference to ***` 错误。
	
	**解决办法**：在链接命令中给出所依赖的库时，需要注意库之间的依赖顺序，依赖其他库的库一定要放到被依赖库的前面，避免  `undefined reference` 的错误，完成编译链接。

- C++代码链接 C 库文件

	**原因**：若库文件或目标文件由 c 代码生成的，则在c++代码中链接目标函数时，也会碰到 `undefined reference` 的问题。

	**解决办法**：由于 c++代码调用 c 语言库的函数，链接目标文件时候找不到，出现 `undefined reference to ***` 错误。只需在调用 C 语言库函数的源码文件中，把与 c 语言库相关的头文件包含添加一个 extern "C"的声明即可。


### 在链接 MKL 库时，报错：`undefined reference to dgemm_ ***`

原因为 MKL 库没有被有效链接。下面为一个范例脚本：

```
>cat demo.sh 

#!/bin/bash
export MKLLIB=/vol-th/intel_composer_xe_2013.0.079_lib
mpif90 -c ./Main/exe.f90
mpif90 -o exe exe.o \
-L${MKLLIB} -lmkl_intel_lp64 -lmkl_sequential -lmkl_core -lpthread \
-Wl,-rpath,${MKLLIB},-L${MKLLIB} -lmkl_intel_lp64 -lmkl_sequential \
-lmkl_core -lpthread
```

MKL库参数详细设置方法：[英特尔® 数学核心函数库](https://software.intel.com/zh-cn/articles/intel-mkl-link-line-advisor)


### 用户在天河上安装商业软件无法正常使用

天河使用的是自主互联的通讯系统，一般是把开源或商业的有源代码的软件，重新编译后，才能正常使用的。


### 使用 Intel 编译器编译时报错：ld: cannot find -lm

编译时出现该报错，一般是由于在编译选项中含有 `-fast`（包含 `-static` )或 `-static`  导致的，该选项需要使用 glibc 的静态库。由于部分操作系统没有安装 glibc 的静态库，或者 glibc 库不在标准目录下，因而没有 libm 的静态库。

解决方法：
```
locate libm.a 
ifort -static hello.f90 - L/usr/lib/x86_64-redhat-linux5E/lib64 -lm
```

可参考链接：[Error: ld: cannot find -lm](https://software.intel.com/en-us/articles/error-ld-cannot-find-lm/)


### 使用 gfortran 编译程序，同时调用 liblapack.a 和 libblas 时在链接过程中报错

错误描述：在天河系统上，使用 gfortran 编译程序，同时调用 `/vol-th/software/lapack-3.4.2`下的` liblapack.a` 和 `libblas.a` 时（ `-L/vol-th/software/lapack-3.4.2 -llapack -lblas`），在链接过程中报错：

```
/vol-th/software/lapack-3.4.2/liblapack.a(ilaenv.o): In function 'ilaenv_':
ilaenv.f:(.text+0x106): undefined reference to `for_cpystr'
/vol-th/software/lapack-3.4.2/liblapack.a(xerbla.o): In function `xerbla_':
xerbla.f:(.text+0x13): undefined reference to `for_len_trim'
```

原因：`/vol-th/software/lapack-3.4.2` 下的 `liblapack.a` 和  `libblas.a` 是使用 `Intel2013` 编译器编译生成的，在使用这两个库的某些函数时，需要调用 Intel 编译器的一些基础库 `libifcore.a`，因此需要显示地加入该库，链接书写内容应该为

```
-L/vol6/software/lapack-3.4.2 -llapack -lblas \
-L/vol6/intel_composer_xe_2013.0.079_lib -lifcore
```

