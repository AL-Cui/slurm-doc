# 通用资源（GRES）调度
通用资源（GRES）的调度是通过灵活的插件机制得到支持的。目前支持图形处理器（GPU）和英特尔集成多核（MIC）处理器。
##配置
Slurm在默认配置中不支持通用资源。必须要明确指出在slurm.conf配置文件中要管理哪些资源。配置参数如下：
* GresType 用逗号分割开的要管理的通用资源列表（例如GresTypes = gpu，mic）。名字可能是一个可选的插件，提供对资源的额外控制。
* Gres 通用资源配置详细信息使用这样的格式： <name> [：<type>] [：no_consume]：<number> [K | M | G]。第一个字段是资源名称，它与GresType配置参数名称相匹配。该可选类型字段可被用于标识通用资源的模型。通用资源也可以用可选字段“：no_consume”指定为非消耗品（即多个作业可以使用相同的通用资源）。最后一个字段必须指定该通用资源的count。可以使用“K”，“M”或“G”后缀分别将count乘以1024,1048576或1073741824。默认情况下，节点没有通用资源，count的最大值为4,294,967,295。
**请注意**，每个节点的Gres规格以与其他资源管理相同的方式工作。根据FastSchedule参数的值，当一个节点被发现资源较少的时候，将处于DOWN状态。
**另外**，BlueGene系统不支持Gres规范。
slurm.conf文件的例子如下：
```

        #Configure support for our four GPUs
        GresTypes=gpu,bandwidth
        NodeName=tux[0-7] Gres=gpu:tesla:2,gpu:kepler:2,bandwidth:lustre:no_consume:4G
```
每个具有通用资源的计算节点还必须包含一个gres.conf文件，该文件描述节点上可用的资源，它们的count，关联的设备文件以及应与这些资源一起使用的cores。可用的配置参数如下：
* Name 通用资源的名称（必须与slurm.conf中的GresTypes值匹配）。任何想要的名字都可以使用。每个通用资源都有一个可选插件，可以提供专用资源的选项。目前包含可选插件的通用资源包括：
1.**gpu** 图形处理器
2.**nic** 网卡
3.**mic** 英特尔多核处理器
* NodeName 通过指定每行所适用的节点，可以使用可选的NodeName规范来允许一个gres.conf文件用于集群中的所有计算节点。NodeName规范可以使用Slurm主机列表规范，如下例所示。
* Count 此节点上可用的此类资源数量。默认值设置为指定的文件值的数量（如果有），否则默认值为1。可以使用“K”，“M”或“G”作为后缀分别乘以1024,1048576或1073741824（例如“Count = 10G”）。**请注意**，Count是一个32位字段，最大值是4,294,967,295。
* Cores 指定可以使用此资源的特定内核的第一个线程CPU索引号。例如，可能强烈希望使用特定的内核与特定的设备（例如在NUMA体系结构上）。可以使用逗号分隔列表指定多个内核，或者可以使用“ - ”分隔符（例如“0,1,2,3”或“0 \ -3”）指定范围。如果指定，则只有已识别的内核可以与每个通用资源一起分配;尝试使用其他内核将不会受到认可。如果没有指定，那么任何内核都可以与资源一起使用，这也增加了Slurm调度算法的速度。如果任何内核可以有效地与资源一起使用，那么不要在Slurm调度逻辑中指定用于提高速度的Cores选项。**注意**：如果您的内核包含多个线程，则仅列出每个内核的第一个线程。逻辑是这样的，它使用内核而不是每个GRES的线程调度。
* File 与资源关联的设备文件的完全合格的路径名。该名称可以包含一个数字范围后缀，由Slurm解读（例如File = / dev / nvidia [0-3]）。如果要支持通用资源分配的执行（即防止用户使用已经分配给不同用户的资源），则通常需要该字段。文件分配的执行依赖于Linux控制组（cgroups）和Slurm的task / cgroup插件，它们将分配的文件放入作业的cgroup并防止使用其他文件。请参阅Slurm的[Cgroups指南](https://slurm.schedmd.com/cgroups.html)以获取更多信息。 如果指定了File，则Count必须设置为指定或未设置的文件名的数量（默认值是指定的文件数）。**注意**：如果您为某个节点上的资源指定File参数，则必须在所有节点上指定该选项，并且Slurm将跟踪每个节点上每个特定资源的分配。
* Type 可以选择性地指定该设备的类型。例如，这可能用于识别GPU的特定模型，然后用户可以在其作业请求中指定。如果指定了Type，则Count的大小受限制（当前为1024）。
gres.conf文件的样例如下：
```

        # Configure support for our four GPUs, plus bandwidth
        Name=gpu Type=tesla  File=/dev/nvidia0 COREs=0,1
        Name=gpu Type=tesla  File=/dev/nvidia1 COREs=0,1
        Name=gpu Type=kepler File=/dev/nvidia2 COREs=2,3
        Name=gpu Type=kepler File=/dev/nvidia3 COREs=2,3
        Name=bandwidth Type=lustre Count=4G
```
## 运行作业
除非在作业提交时使用salloc，sbatch和srun命令支持的--gres选项特别要求，否则不会为作业分配任何通用资源。该选项需要一个参数，指定需要哪些通用资源以及多少资源。资源规范是这种形式name[：type：count]。name与GresTypes和Gres配置参数指定的名称相同。type标识了该通用资源的特定类型（例如GPU的特定型号）。count指定了需要多少资源并且默认值为1。例如： sbatch --gres = gpu：kepler：2 ....

作业将根据需要分配特定的通用资源以满足请求。如果作业被暂停，那些资源不能被其他作业使用。

如上所述，可以使用带有srun命令的--gres选项将分配给作业的通用资源分配给作业步。默认情况下，分配给作业的所有通用资源将分配给作业步。如果需要，作业步可以明确指定与作业不同的通用资源计数。这种设计选择是基于每个作业执行许多作业步的情况。如果作业步被默认授予对所有通用资源的访问权限，则某些作业步将需要明确指定零个通用资源的数量，我们认为这些数量更容易混淆。可以为作业步分配特定的通用资源，这些资源将不能用于其他作业步。一个简单的例子如下所示。
```

        #!/bin/bash
#
# gres_test.bash
# Submit as follows:
# sbatch --gres=gpu:4 -n4 -N1-1 gres_test.bash
#
srun --gres=gpu:2 -n2 --exclusive show_device.sh &
srun --gres=gpu:1 -n1 --exclusive show_device.sh &
srun --gres=gpu:1 -n1 --exclusive show_device.sh &
wait
```

