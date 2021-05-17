# SLURM

## Introduction

Job scheduler: tackle with situation that there is more work than resources.

SLURM: simple linux utility for resource management

Design Goal:

* Small and simple
* Scalable
* Open source
* Administrator friendly
* Secure
* Fault-tolerant
* Portable (Autoconf engine adapts to environment)

![image-20210429161538880](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\SLURM.assets\image-20210429161538880.png)

Job submit Plugin

![image-20210429161732321](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\SLURM.assets\image-20210429161732321.png)

Resource selection plugin:

![image-20210429161828662](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\SLURM.assets\image-20210429161828662.png)

SLURM Entities:

1. Jobs: Resource allocation requests
2. Job steps: Set of (parallel) tasks
3. Partitions: Job queues with limits and access controls
4. Nodes
   * Sockets
     - Cores Hyperthreads
   * Memory
   * Generic Resources

  

<img src="C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\SLURM.assets\image-20210429162458464.png" alt="image-20210429162458464" style="zoom:50%;" />

Job state info:

* ID
* Name
* Time limit (min or max)
* Size specification (nodes, CPUs, sockets, cores or threads)
* Specific node
* Node features
* Dependency
* Account name
* Quality of service
* State

![image-20210429163133710](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\SLURM.assets\image-20210429163133710.png)

Daemons:

* **slurmctld**: Central controller
* **slurmd**: One compute node
* **slurmdbd**: One per enterprise

 ## Usage

### 工作模式

1. 交互模式

   Command: `srun`

   提交命令后等待作业执行完成后返回命令行窗口

2. 批处理模式

   Command: `sbatch`

   批处理作业是指用户编写作业脚本，指定资源需求约束，提交后台执行作业。提交批处理作业的命令为 `sbatch`，用户提交命令即返回命令行窗口，但此时作业在进入调度状态，在资源满足要求时，分配完计算结点之后，系统将在所分配的第一个计算结点（而不是登录结点）上加载执行用户的作业脚本。批处理作业的脚本为一个文本文件，脚本第一行以 “#!” 字符开头，并制定脚本文件的解释程序，如 `sh`，`bash`。由于计算节点为精简环境，只提供 `sh` 和 `bash` 的默认支持。

   计算开始后，工作目录中会生成以 `slurm` 开头的`.out` 文件为输出文件。

如下例子：

```bash
#!/bin/bash

#SBATCH -J sleep

#SBATCH -p debug

#SBATCH --time=1

#SBATCH -N 2

#SBATCH -n 2

#SBATCH -o %j.sleep

#SBATCH -e %j.sleep

echo ${SLURM_JOB_NODELIST}

echo run.sh start on $(date)

sleep 100

echo run.sh end on $(date)
```

3. 分配模式

   Command: `salloc`

   结点资源抢占命令。该命令支持用户在提交作业前，抢占所需计算资源（此时开始计算所用机时）。

   执行分为四步：

   1．提交资源分配请求，作业排队等待资源分配，作业开始运行，开始计费；

   2．执行用户指定的命令；

   3．命令执行结束；

   4．退出作业释放资源，作业停止运行，停止计费。

### 队列信息

1. 显示队列信息

   Command: `sinfo`

   ![image-20210429171407748](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\SLURM.assets\image-20210429171407748.png)

| 显示字段  | 解释             |
| --------- | ---------------- |
| PARTITION | 队列名称         |
| AVAIL     | 队列状态         |
| TIMELIMIT | 时间限制         |
| NODES     | 队列分配的节点数 |
| STATE     | 节点状态         |
| NODELIST  | 队列节点列表     |

| Slurm                   | 功能             |
| :---------------------- | :--------------- |
| `sinfo -N`              | 查看节点级信息   |
| `sinfo -N –states=idle` | 查看可用节点信息 |
| `sinfo –partition=cpu`  | 查看队列信息     |
| `sinfo –help`           | 查看所有选项     |

队列状态值可以包括以下几种：

**UP**

新提交的作业可能在队列上排队，并且作业可以在队列中运行。

**DOWN**

新提交的作业可能在队列上排队，但排队的作业可能不会被分配节点并在队列中运行。已经在队列上运行的作业继续运行。

**DRAIN**

没有任何新作业可能在队列上排队（作业提交请求将被拒绝与错误消息），但已分配的队列上的作业可能被分配节点并运行。（节点故障）

**INACTIVE**

没有新作业可能在队列上排队，并且已排队的作业可能不会被分配节点并运行。

**IDLE**

队列空闲

**MIX**

队列被占用，仍有可用资源

2. 队列详细信息查询

   Command: `scontrol show partition <partition name>`

![image-20210429172147000](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\SLURM.assets\image-20210429172147000.png)

| **队列参数列表**            | **参数解释**                                                 |
| --------------------------- | ------------------------------------------------------------ |
| `AllowGroups=ALL`           | 此队列允许的用户组                                           |
| `AllowAccounts=ALL`         | 此队列允许的账号                                             |
| `AllowQos=ALL`              | 此队列允许的的Qos                                            |
| `AllocNodes=ALL`            |                                                              |
| `Default=NO`                | 是否是默认队列，设置多个默认队列以最后一行的队列为默认队列   |
| `QoS=N/A`                   |                                                              |
| `DefaultTime=NONE`          | 用于未指定值的作业的运行时间限制。如果未设置，则使用 MaxTime。 格式与 MaxTime 相同。 |
| `DisableRootJobs=NO`        | 如果设置为 “YES”，则用户 root 将被阻止在该分区上运行任何作业。 |
| `ExclusiveUser=NO`          | 如果设置为 “YES”，则节点将被专门分配给用户。 同一用户可以运行多个作业，但一次只能有一个用户处于活动状态。 使用 --exclusive = user 选项也可以在每个作业的基础上提供此功能。 |
| `GraceTime=0`               |                                                              |
| `Hidden=NO`                 | 指定默认情况下是否隐藏分区及其作业。 默认情况下，隐藏的分区不会被 Slurm API 或命令报告。 可能的值为 “是” 和 “否”。默认值为 “否”。 |
| `MaxNodes=UNLIMITED`        | 可以分配给任何单个作业的最大节点数。默认值为 “UNLIMITED”     |
| `MaxTime=UNLIMITED`         | 分配给此队列的作业的最长运行时间                             |
| `MinNodes=1`                |                                                              |
| `LLN=NO`                    | 调度作业资源到最小负载的节点上的（基于空闲 CPU 的数量）。这通常只适用于具有串行作业的环境。请注意，节点权重优先于每个节点上有多少空闲资源。 |
| `MaxCPUsPerNode=UNLIMITED`  | 任何节点上可用于此分区的所有作业的最大 CPU 数。这对调度 GPU 特别有用。 |
| `Nodes=gpunode[1,2]`        | 此队列可访问的节点列表                                       |
| `PriorityJobFactor=1`       |                                                              |
| `PriorityTier=1`            | 队列优先级                                                   |
| `RootOnly=NO`               | 指定只有用户 ID 为零（即用户 root）可以在此分区中分配资源。用户 root 可以为任何其他用户分配资源，但请求必须由用户 root 启动。默认值为 “NO“。 |
| `ReqResv=NO`                | 指定此分区的用户在提交作业时需要指定一个预约。此选项可用于限制可能具有较高优先级的分区或仅在预留中允许的附加资源的使用。 可能的值为 “YES” 和 “NO”。 默认值为 “NO”。 |
| `OverSubscribe=NO`          | 控制分区在每个资源上一次执行多个作业的能力，OverSubscribe 的可能值为 “EXCLUSIVE”，“FORCE”，“YES” 和 “NO”。 请注意，值为 “YES” 或 “FORCE” 可能会对具有数千个正在运行的作业的系统产生负面影响。 默认值为 “否”。 |
| `OverTimeLimit=NONE`        |                                                              |
| `PreemptMode=OFF`           |                                                              |
| `State=UP`                  | 队列状态                                                     |
| `TotalCPUs=80`              | 此队列总 cpu 数量，配置文件中节点的 cpu 总数                 |
| `TotalNodes=2`              | 此队列的节点数量                                             |
| `SelectTypeParameters=NONE` | 分区特定的资源分配类型。此选项将替换全局 SelectTypeParameters 值。支持的值是 CR_Core，CR_Core_Memory，CR_Socket 和 CR_Socket_Memory。使用需要设置系统范围的 SelectTypeParameters 值。 |
| `DefMemPerNode=UNLIMITED`   |                                                              |
| `MaxMemPerNode=UNLIMITED`   |                                                              |

**命令拓展**：

```bash
sinfo -d
#显示状态为down的节点
sinfo -v
#显示版本信息
```

### 作业状态

Command: `squeue`

![image-20210429195314488](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\SLURM.assets\image-20210429195314488.png)

参数列表：

| Parameters                   | Explanation                                                  |
| ---------------------------- | ------------------------------------------------------------ |
| -j, --job=job(s)             | comma separated list of jobs IDs to view, default is all     |
| -l, --long                   | long report                                                  |
| -n, --name=job_name(s)       | comma separated list of job names to view                    |
| -o, --format=format          | format specification                                         |
| -p, --partition=partition(s) | comma separated list of partitions to view, default is all partitions |
| -u, --user=user_name(s)      | comma separated list of users to view                        |
| -A, --account=account(s)     | comma separated list of accounts to view, default is all accounts |

举例：

| Slurm                     | 功能                       |
| :------------------------ | :------------------------- |
| `squeue -j jobid`         | 查看作业信息               |
| `squeue -l`               | 查看细节信息               |
| `squeue -n HOST`          | 查看特定节点作业信息       |
| `squeue -A ACCOUNT_LIST`  | 查看ACCOUNT_LIST的作业     |
| `squeue`                  | 查看USER_LIST的作业        |
| `squeue –state=R`         | 查看特定状态的作业         |
| `squeue –start`           | 查看排队作业的估计开始时间 |
| `squeue –format=“LAYOUT”` | 使用给定的LAYOUT自定义输出 |
| `squeue –help`            | 查看所有的选项             |
| `squeue -t <status_name>` | 查询指定状态的作业         |

字段解释：

| 显示字段           | 解释                                   |
| ------------------ | -------------------------------------- |
| JOBID              | 作业 ID                                |
| PARTITION          | 队列名称                               |
| NAME               | 作业名                                 |
| USER               | 作业所属用户                           |
| ST                 | 作业状态                               |
| TIME               | 作业已运行时间                         |
| NODES              | 作业占用节点数                         |
| NODELIST（REASON） | 作业运行的结点列表（不运行作业的原因） |

作业状态包括：`R`(正在运行)，`PD`(正在排队)，`CG`(即将完成)，`CD`(已完成)。

### 实时作业信息

Command: `scontrol show job <JobID> --detail`

![image-20210429191629483](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\SLURM.assets\image-20210429191629483.png)

| **作业参数列表** | **参数解释**                                                 |
| ---------------- | ------------------------------------------------------------ |
| JobId            | 作业 id                                                      |
| JobName          | 作业名                                                       |
| UserId           | 用户 id                                                      |
| GroupId          | 用户组                                                       |
| MCS_label        |                                                              |
| Priority         | 作业优先级                                                   |
| Nice             | 调整作业调度优先值 (仅适用于作业)，越小越优先                |
| Account          | 账号                                                         |
| QOS              | 服务质量，用户可以使用的资源限制                             |
| JobState         | 作业状态                                                     |
| Reason           | 作业在当前的原因                                             |
| Dependency       | 作业依赖关系。作业只有依赖的作业完成才运行，如显示 NULL, 则无依赖 (仅适用于作业) |
| Requeue          | 节点失效时是否重排队，0 为否，1 为是                         |
| Restarts         | 失败时，是否重运行，0 为否，1 为是                           |
| BatchFlag        | 是否批处理设定了标记 (仅限于作业)，0 为否，1 为是            |
| Reboot           | 节点空闲时是否重启节点，0 为否，1 为是                       |
| ExitCode         | 作业退出码                                                   |
| RunTime          | 已运行时间                                                   |
| TimeLimit        | 请求的 Walltime 时间                                         |
| TimeMin          | 最小时间                                                     |
| SubmitTime       | 作业提交时间                                                 |
| EligibleTime     | 预计作业开始时间                                             |
| StartTime        | 开始运行时间                                                 |
| EndTime          | 预计结束时间                                                 |
| Deadline         | 作业的截止时间                                               |
| PreemptTime      | 作业抢占时间                                                 |
| SuspendTime      | 挂起时间                                                     |
| SecsPreSuspend   |                                                              |
| LastSchedEval    |                                                              |
| Partition        | 队列                                                         |
| ReqNodeList      | 请求的节点列表                                               |
| ExcNodeList      | 执行的节点                                                   |
| NodeList         | 实际运行的节点列表                                           |
| BatchHost        | 执行节点 (仅适用于作业)，对于分配的回话：显示回话执行节点 (如，srun 或 salloc 命令执行节点)。对于批处理作业：显示的执行批处理节点。 |
| NumNodes         | 节点数                                                       |
| NumCPUs          | CPU 数                                                       |
| NumTasks         | 任务数                                                       |
| CPUs/Task        | CPU 核数 / 任务数                                            |
| TRES             | 使用 CPU 的个数 (对应 TRES 的子属性 cpu:TRES=cpu=1,mem=2847M,node=1,billing=1) |
| socks/Node       |                                                              |
| CoreSpec         | 各节点预留 CPU 核数，如未包含，则表示 *                      |
| MinCPUsNode      | 每节点最小的 cpu 核数                                        |
| MinMemoryNode    | 每节点最小的内存大小，0 表示无限制                           |
| MinTmpDiskNode   | 每节点最小临时存储硬盘大小，0 表示无限制                     |
| Features         | 特性                                                         |
| DelayBoot        |                                                              |
| Gres             | 通用资源                                                     |
| Reservation      | 预留资源                                                     |
| OverSubscribe    | 分配给此作业的计算资源能否被其它作业预约 (仅适用于作业)，可被预约的资源包含节点，CPU 核，线程。值可以为：YES,NO,USER,MCS,OK |
| Contiguous       | 是否需要分配连续节点，OK 是，NO 否                           |
| Licenses         | 作业预留的授权                                               |
| Network          | 作业运行的网络                                               |
| Command          | 作业执行的命令（仅适用于作业）                               |
| WorkDir          | 工作目录                                                     |
| StdError         | 错误输出路径                                                 |
| StdIn            | 标准输入路径                                                 |
| StdOut           | 标准输出路径                                                 |

### 作业状态值

| 状态值     | 解释           |
| ---------- | -------------- |
| PENDING    | 排队 PD        |
| RUNNING    | 运行 R         |
| SUSPENDED  | 挂起 S         |
| COMPLETED  | 完成 CD        |
| COMPLETING | CG             |
| FAILED     | 作业运行失败 F |
| CANCELLED  | 作业被取消 CA  |
| TIMEOUT    | 超时 TO        |

`srun`加载的任务还会产生作业步：

```bash
squeue -s
```

`scontrol`常见参数列表：

| OPTION          |                                                           | COMMAND                |                                                              |
| --------------- | --------------------------------------------------------- | ---------------------- | ------------------------------------------------------------ |
| -a or --all     | equivalent to "all" command                               | cluster                |                                                              |
| -d or --details | equivalent to "details" command                           | create                 |                                                              |
| -h or --help    |                                                           | reconfigure            |                                                              |
| -M or --cluster | equivalent to "cluster" command NOTE:SlurmDBD must be up. | `show <ENTITY> [<ID>]` | display state of identified entity, default is all records. may be "aliases", "assoc_mgr""burstBuffer",       "config","daemons","federation", "frontend","hostlist","hostlistsorted", "hostnames","job","layouts", "node", "partition","reservation","slurmd", "step", or "topology"(also for BlueGene only: "block" or "submp"). |

### 节点信息

Command: `scontrol show node`

![image-20210429192932374](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\SLURM.assets\image-20210429192932374.png)

| **节点参数列表**  | **参数解释**            |
| ----------------- | ----------------------- |
| NodeName          | 节点名称                |
| Arch              | 系统架构                |
| CoresPerSocket    |                         |
| CPUAlloc          | 分配给的 CPU 核数       |
| CPUErr            | 出错的 CPU 核数         |
| CPUTot            | 总的 CPU 核数           |
| CPULoad           | CPU 负载                |
| AvailableFeatures | 可用特性                |
| ActiveFeatures    | 激活的特性              |
| Gres              | 通用资源                |
| NodeAddr          | 节点 IP 地址            |
| NodeHostName      | 节点名                  |
| Version           | SLURM 版本              |
| OS                | 操作系统                |
| RealMemory        | 实际的物理内存，单位 GB |
| AllocMem          | 已分配的内存，单位 GB   |
| FreeMem           | 可用内存，单位 GB       |
| Sockets           |                         |
| Boards            | 主板数                  |
| State             | 节点状态                |
| ThreadsPerCore    | CPU 线程数              |
| TmpDisk           | 临时硬盘存储大小        |
| Weight            | 权重                    |
| Owner             |                         |
| MCS_label         |                         |
| Partitions        |                         |
| BootTime          | 开机实际                |
| SlurmdStartTime   | SLURM 守护进程启动时间  |
| CfgTRES           |                         |
| AllocTRES         |                         |
| CapWatts          |                         |
| CurrentWatts      |                         |
| LowestJoules      |                         |
| ConsumedJoules    |                         |
| ExtSensorsJoules  |                         |
| ExtSensorsWatts   |                         |
| ExtSensorsTemp    |                         |
| Reason            |                         |

### 节点状态值

可能的状态包括: 

```
allocated(alloc), completing(comp), down(down), drained(drain), draining(drng), fail(fail), failing(failg), future(futr), idle(idle), maint(maint), mixed(mix), perfctrs(npc), power_down(pow_dn), power_up(pow_up), reserved, and unknown(unk)。
```

请注意，后缀 “*” 标识当前未响应的节点。

`idle` ：表示节点处于空闲状态

`alloc` ：表示节点所有 CPU 都被占用，新提交的作业将排队。

`drain` ：出现这个状态时，不影响正在运行的作业，但是不接受新的作业调度，可以使用命令 `sinfo –R` 打印节点不正常的状态产生原因

`mix` ：节点具有分配 CPU 的作业，而其他的 CPU 状态是 IDLE，新提交的作业继续运行

`unk Slurm`： 控制器刚刚启动，节点的状态尚未确定。

`down` ：故障节点不可用。

### 作业运行参数

下列参数适用于`srun`，`sbatch`和`salloc`

| `**参数**`                                        | `**参数作用**`                                               | `**备注**`                                                   |
| ------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `-J 或者 --job-name`                              | `指定作业名称`                                               | `默认为加载的程序 / 批处理脚本文件名 / 执行的命令`           |
| `-p 或者 --partition`                             | `指定队列资源`                                               | `作业不能跨分区`                                             |
| `-N 或者 –nodes=<number>`                         | 指定节点数量                                                 |                                                              |
| `-n 或者 --ntasks = <number> 总 task 数 (总核数)` | 指定处理器数量srun -p debug -N 2 --cpus-per-task=10 -n 2 sleep 10&NumNodes=2 NumCPUs=20 NumTasks=2 CPUs/Task=10TRES=cpu=20,node=2Nodes=gpunode[1-2] CPU_IDs=0-9 MinCPUsNode=10 | 默认值为每个节点一个任务，但请注意，--cpus-per-task 选项将更改此默认值。 |
| -`o 或者 --output=<filename pattern>`             | 指定 stdout 的输出文件。提供 --output 选项以允许将 stdout 重定向到不同的位置。 如果指定的文件已经存在，它将被覆盖。 |                                                              |
| `-e 或者 --error=<filename pattern>`              | 指定 stderr 的输出文件。提供 --error 选项以允许将 stdout 和 stderr 重定向到不同的位置。 如果指定的文件已经存在，它将被覆盖。 |                                                              |
| `-t或者 --time`                                   | 时间分辨率是一分钟，第二个值被四舍五入到下一分钟。=0 为要求不限时。 可接受的时间格式包括 “分钟”，“分钟：秒”，“小时：分钟：秒”，“天 - 小时”，“天 - 小时：分钟” 和 “天 - 小时：分：秒”。超出时间限制的作业将被终止，作业状态变为 TIMEOUT。 | 应尽可能准确估计，调度时用此估计时间进行 backfill 判断       |
| `-A 或者 --account=<account>`                     | [null 指定账号]                                              |                                                              |
| `-d 或者 --dependency 指定作业的依赖关系`         | after:job_id [:jobid...] 在指定作业开始之后afterok:job_id [:jobid...] 在指定作业成功开始之后afternotok:job_id [:jobid...] 在指定作业不成功结束之后afterany:job_id [:jobid...] 在指定作业结束之后… | 满足依赖关系的作业才能运行；不可能满足依赖关系的作业将永远不会运行，一直排队。 |
| `--priority=<值>`                                 | [null 作业优先级]                                            | 请求具体工作优先级。 可能会受到配置的限制。 值应该是数值或 “TOP”（用于最高可能值）。 |

### 作业控制

1. 取消作业

   ```bash
   scancel <jobid>
   ```

2. 挂起作业

   ```bash
   scontrol suspend <jobid>
   ```

​       暂时释放处理器资源

​       节点被释放，可以分配给其他资源

​     挂起正在运行的工作。job_list 参数是逗号分隔的作业 ID 列表。使用 resume 命令恢复执行。用户进程必须在收到 SIGSTOP 信号后停止，并在收到 SIGCONT 后恢复，以使此操作生效。并非所有架构和配置都支持停职。如果重新排出暂停的工作，它将被置于保持状态。

3. 恢复作业

   ```bash
   scontrol resume <jobid>
   ```

4. 作业重新排队

   ```bash
   scontrol requeue <jobid>
   ```

   可将运行或挂起批处理的作业重新排队

   释放资源，置为 PENDING 状态

   不会重新分配作业 ID

   提交时间置为重排队时间

5. 保留作业

   ```bash
   scontrol hold [job list]
   ```

   防止挂起的作业被启动（将其优先级设置为 0）。使用 release 命令允许作业被调度。job_list 参数是**作业 ID 的逗号分隔列表或作业名称的 “jobname =”**，它将尝试保存具有该名称的所有作业。请注意，当系统管理员使用 hold 命令保留作业时，只有系统管理员可能会释放作业才能执行（另请参阅 uhold 命令）。当作业被其所有者保留时，也可能由工作所有者 release。此外，当你尝试保留一个 running 的作业时，将不会暂停或取消它。但是，它会将作业优先级设置为 0，并更新作业 reason 字段，这将在以后重新排列时保留该作业。

   ```bash
   scontrol unhold [job list]
   ```

   防止挂起的作业被启动（将其优先级设置为 0）。job_list 参数是作业 ID 或作业名称的空格分隔列表。使用 release 命令允许作业被调度。此命令是为系统管理员设计的，用于保存作业，以便作业所有者可以释放它，而不需要系统管理员的干预（另请参阅 hold 命令）。

6. 修改作业

   ```bash
   scontrol update <Parameter=Value>
   ```

   例如修改队列和名字：

   ```bash
   scontrol update jobid=xx partition=<New paritition> name=<New name>
   ```

### slurm.conf文件配置

User无需配置此文件，详细见Reference List。



Reference List

[SLURM 使用基础教程 - dahu1 - 博客园 (cnblogs.com)](https://www.cnblogs.com/dahu-daqing/p/12693334.html)

