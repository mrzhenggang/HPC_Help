作业运行问题
============


### 为什么我用 `yhi` 命令看到很多 idle 的节点，但是我提交作业后，作业不能立即执行？

TH-1A 的作业调度原则是先来先服务，因为已经有用户优先于您提交作业并且已经在排队，所以 idle 的节点需要优先分配给优先提交作业的用户，即使用户提交作业需求节点数小于空余节点数，仍然因为先来先服务原则，处于等待状态。


### 我用 `yhrun` 提交完作业以后，网络或终端断掉后，我的作业也相应停止

请您使用 `yhbatch` 命令提交作业，这样您就不会出现网络或终端断掉后作业停止的现象。


### 请问您们的系统上的内存最大是多少 G? 我的并行程序需要使用 48G 或更大一些内存怎么办？

TH-1A 系统的每个常规计算节点上的内存是 24G，如果您的并行程序需要比 24G 大的内存，您可以通过使用更多的节点来分散您的内存的使用。目前已升级部分计算节点的内存至 48G，如有需求可直接联系我们。另外，HPC1 系统的节点内存是 128G，用户也可选择使用该系统。

### 请问我们的计算任务到某一时刻自动终止了，提示是有节点  `due to time limit` 而终止程序，请问贵中心的计算任务是有时间限制的吗？

我们的 TH-1A 系统的队列是有时间限制的，命令行下输入 `yhi`，输出结果的第三列 TIMELIMIT 即为不同分区下的最大作业运行限制。请您经常查看作业运行状态，以确保您的作业在正常运行。

### 任务提交后显示在计算，然后任务消失，也没有输出  `slurm-*-out`文件

其所在分区存储设备可能没有挂载，请联系系统管理员。

### 任务突然运行速度变慢

可能是该任务使用的计算节点内存报错，可换其他节点重新提交任务。

### 任务输出结果总是显示 `Disk quota exceeded`

首先查询磁 1 盘空间是否超限（查询命令参见存储问题第 2 点），如果未超限，系统有时会误报超限，在每个月的定期维护时会解决掉这个问题。

### 运行作业提示 `error while loading shared libraries: libXXX.so: cannot openshared object file: No such file or directory`

在计算时找不到动态库是因为计算节点和登陆节点的软件环境有所不同。链接器在处理动态库时将链接时路径（Link-time path）和运行时路径（Run-time path）分开，`-L` 只是指定了程序链接时库的路径，并不影响程序执行时库的路径；`-Wl,-rpath` 指定程序运行时库的路径，该库的路径信息保存在可执行文件中，运行时它会直接到该路径查找库；也可使用 `LD_LIBRARY_PATH` 环境变量来指定动态库在运行时的搜索路径。


### 作业运行时提示：`couldn’t chdir to '/vol-th/home/daix': No such file or directory: going to /tmp instead`

原因为所使用的计算队列无法访问用户所在的 `/vol-th` 目录，即所使用的计算队列没有挂载到 `/vol-th` 目录。解决方法：将问题报告给请联系系统管理员。

### 在计算节点上运行程序，找不到某些命令，比如说提示 `bc: Command not found`

复制登录节点上的  `bc` 命令到自己账户下，设置好该命令的环境变量后，重新运行就可以找到命令。

### PBS 作业系统里查看运行的节点名称的变量 `$PBS_NODELINE`，在 TH-1A 里对应哪一个变量?

可以试试天河上的作业调度获取节点的变量 `$SLURM_NPROCS` ，它与PBS 的 `$PBS_NODELINE` 是一样的功能。

### 提交作业后，提示 `yhbatch: error: Batch job submission failed: User's group not permitted to use this partition`

您提交作业 `-p` 参数后面指定的队列名称错误，请检查您可以使用的队列。

### 任务提交运行后，有时在还未达到队列的时间天数期限时，运行的程序已“停止工作”（输出文件没有更新），但是通过作业查询命令 `yhq` 查看，作业看起还在  `R` 运行

遇到这个情况，请您及时手动杀掉您的作业，从断掉的地方接着续算就可以了。


### 运行作业报错 `slurmd[cn4234]: Cannot send after transport endpoint shutdown`

请重新提交作业，并同时使用 `-x cn4234` 把这个节点过滤掉不使用就可以了。


### 运行作业报错 `yhrun: error: Task launch for 2440965.0 failed on node cn2892: Job credential expired`

请重新提交作业，并同时使用 `-x cn2892` 把这个节点过滤掉不使用就可以了。

### 运行作业报错 `forrtl: severe (41): insufficient virtual memory`

运行作业的内存不够用了，请尝试多使用节点，每个节点上少使用核数来提交运行。


### 提交作业报错 `yhbatch: error: Batch job submission failed: Batch job violates accounting/QOS policy(job submit limit, user's size and/or time limits)`

您没有提交作业的权限，请联系工程师，为您开通权限。

### 提交的在运行的工作，刚刚发现状态都变显示为 `S`，另外，其他几个显示 `PD` 的工作，`NODELIST` 也显示为 `PartitionDown` 。不知是否是由于中心在做一些维护所致，这会影响这些工作的运行吗？

`S` 状态是系统管理员正在维护系统，临时挂起用户作业后显示的状态。原来显示 `PD` 状态的变成 `PartitionDown` 状态是不影响工作运行。

### 作业运行的状态为 CG,自己也杀不掉这个作业，是否影响我的使用，这个问题如何解决？

`CG` 状态是作业正在退出，用户如果杀不掉这些 `CG` 状态的作业也没关系，它并不影响您的使用，管理员会集中定期处理这个 `CG` 状态的作业。

### 输出的 slurm 文件中是如下数据：`yhrun: got SIGCONT` 。我在天河服务器用户手册上没找到这条数据的解释。请问这条数据代表什么意思?

这个是系统管理员临时维护系统，为了避免影响用户的作业，而把用户的作业挂起了出现的提示了。

### 使用天河 `software` 目录下的一个 mpi 实现编译程序，运行时 slurm 文件中提示报错

报错问题：使用天河 `software` 目录下的一个 mpi 实现编译程序，运行时 slurm 文件中提示报错。

```
GLEX_ERR(cn1368): _Progress(172), err
CQE:status=Dest_Key:opcode=RDMA_WRITE:signaled=1:rmt_nic_id=1370
yhrun: Job step aborted: Waiting up to 2 seconds for job step to finish.
Fatal error in PMPI_Bcast: Other MPI error, error stack:
MPIDI_CH3I_Progress(176): progress engine failure
In: PMI_Abort(1, Fatal error in PMPI_Bcast: Other MPI error, error stack:
MPIDI_CH3I_Progress(176): progress engine failure)
slurmd[cn1368]: *** STEP 2796179.0 KILLED AT 2015-10-12T11:27:12 WITH SIGNAL 9 ***
slurmd[cn1368]: *** STEP 2796179.0 KILLED AT 2015-10-12T11:27:12 WITH SIGNAL 9 ***
```

该错误提示一般是由 mpi 版本导致。解决方法：使用/vol6/source.sh 中的内容替换原~/.bashrc 中关于 intel 编译器、mpi 的路径。

### 程序运行报错：`Fatal Error: This program was not built to run in your system.`

报错问题：程序运行报错，`Fatal Error: This program was not built to run in your system. Please verify that both the operating system and the processor support Intel(R) AVX. yhrun: error: cn2375: task 0: Exited with exit code 1`

原因：该错误说明程序的编译时环境和运行时环境不一致，即程序编译时使用了支持  `AVX` 的选项，运行时的硬件环境不支持该 AVX 优化。一般这种情况发生是由于用户在编译程序时加入 `-xHOST/-xAVX`  选项（或是在安装软件时，系统自动读取到登陆节点上 CPU 的 `flag` 支持 `avx` ，故在编译软件时加入了 `-xHOST`），那程序就会根据登陆节点的 CPU 配置信息进行优化编译，然而程序的运行是在计算节点上，计算节点的 CPU配置信息可能不支持 AVX，就与登陆节点不同，就会报上面的提示错误。

解决方法：编译时去掉 `-xHOST/-xAVX` 选项，使用其他优化选项。

备注：-xHost will cause icc/icpc or icl to check the cpu information and find the highest level of extended instructions support to use.

天河登陆节点 ln1、ln2、ln3 上的 CPU 配置信息 `flag` 均无 `avx` ，ln8、ln9上均有 `avx` 。

如果在 ln8 或 ln9 上安装软件时，configure 后一定要检查下编译 flag 是否加入了 `-xHOST`，如果加入，请修改对应的 configure 文件，将 `-xHOST` 删除。