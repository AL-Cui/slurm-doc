# Squeue

## NAME

squeue - 查看有关位于Slurm调度队列中的作业的信息

## SYSNOPSIS

squeue[OPTIONS...]

## DESCRIPTION

squeue用于查看Slurm管理的作业和作业步信息。

## OPTIONS

* -A <account_list>, --account=<account_list>

指定要查看的作业的帐户。接受逗号分隔的帐户名称列表。列出作业步时，该选项不起作用。

* -a,--all

显示所有分区中的作业和作业步的相关信息。这会导致显示出配置为隐藏的分区和用户组无法使用的分区中的相关信息。

* -r,--array

每行显示一个作业数组元素。如果没有这个选项，输出显示将会使用作业数组去优化（使用正则表达式打印的数组索引值将等待的作业数组元素合并到一行输出中）。

* --array-unique

每行显示一个唯一的处于等待状态的作业数组元素。如果没有此选项，等待状态的的作业数组元素将被分组到主数组作业中以优化显示。这也可以使用环境变量SQUEUE_ARRAY_UNIQUE进行设置。

* --federation

显示多集群上的的作业

* -h,--noheader

不打印输出头部内容

* --help

打印描述squeue指令的所有可选项的帮助消息

* --hide

不显示所有分区中的有关作业和作业步的信息。默认情况下，不会显示配置为隐藏或不可用于用户组的分区信息（即，这是默认行为）。

* -i <seconds\>, --iterate=<seconds\>

以指定的时间间隔（以秒为单位）重复收集并报告请求的信息。默认情况下，打印带有标题的时间戳。

* -j <job_id_list>, --jobs=<job_id_list>

请求以逗号分隔的作业ID列表的相关信息并输出显示。默认为所有作业。 --jobs = <job_id_list>选项可以与--steps选项一起使用，以打印有关特定作业步信息。**注意**：如果提供了作业ID列表，即使它们位于隐藏分区上，作业也会显示。由于此选项的参数是可选的，因此为了正确解析，单字母选项必须紧跟在值后面，并且不要在它们之间包含空格。例如“-j1008”而不是“-j 1008”。作业ID格式是“job_id [_array_id]”。当指定单个作业ID时，对于具有大量作业的系统，可以显着提高该命令的性能。默认情况下，该字段大小将被限制为64个字节。使用环境变量SLURM_BITSTR_LEN指定更大的字段大小。

* --local

只显示本地群集的作业。忽略此多集群中的其他群集（如果有的话）。覆盖--federation选项。

* -l,--long

根据指定的限制报告所选作业或作业步的更多可用信息。

* -L, --licenses=<license_list>

查找正在请求或使用一个或多个命名许可证的作业。许可证列表由逗号分隔的许可证名称组成。

* -M, --clusters= <string\>

发出命令到集群上。多个群集名称可以用逗号分隔。 'all'的值将查询在所有群集上运行。该选项隐式设置--local选项。

* -n, --name=<name_list>

请求具有指定名称之一的作业或作业步。该列表由逗号分隔的作业名称组成。

* --noconvert

不从原始类型转换单位（例如2048M不会转换为2G）。

* -o <output_format>, --format=<output_format>

指定要显示的信息，其大小和位置（右对齐或左对齐）。另请参阅下面描述的-O <output_format>，--Format = <output_format>选项（支持格式化灵活性较低，但支持访问所有字段）。具有各种选项的默认格式是

    default
        "%.18i %.9P %.8j %.8u %.2t %.10M %.6D %R"
    -l, --long
        "%.18i %.9P %.8j %.8u %.8T %.10M %.9l %.6D %R"
    -s, --steps
        "%.15i %.8j %.9P %.8u %.9M %N"

每个字段的格式是“％[[.] size] type”。

    size
    是最小的字段大小。如果没有指定大小，则将使用打印信息所需的任何内容。
    .
    表示输出应该右对齐，并且必须指定大小。默认情况下，输出是左对齐的。

请注意，其中许多类型规格仅适用于作业，而其他类型仅适用于作业步骤。有效的类型规格包括：

    %all
    用垂直条分隔每个字段，打印此数据类型的所有可用字段.
    %a
    与作业相关的帐户。 （仅适用于作业）
    %A
    作业步创建的任务数量。这会报告srun --ntasks选项。 （仅适用于作业步骤）（估计官网写错了）
    %A
    作业ID。作业数组的每个元素都有唯一的值。 （仅适用于作业）
    %b
    作业或作业步所需的通用资源（gres）。 （适用于作业和作业步）
    %B
    正在执行的（batch）主机。对于分配的会话，这是会话正在执行的主机（即从中执行srun或salloc命令的节点）。对于batch作业，这是执行batch脚本的节点。在典型的Linux集群的情况下，这将是分配的计算节点零。在BlueGene或Cray系统的情况下，这将是slurmd守护进程执行作业脚本的前端主机。
    %c
    作业请求的每个节点的CPU（处理器）的最小数量。这会报告srun --mincpus选项的值，其默认值为零。 （仅适用于作业）
    %C
    如果作业已请求或分配给它的CPU（处理器）的数量。作业完成时，这个数字将反映当前分配的CPU数量。 （仅适用于作业）
    %d
    作业请求的临时磁盘空间的最小大小（以MB为单位）。 （仅适用于作业）
    %D
    分配给作业的节点数量或排队状态作业所需的最小节点数量。如果作业指定了节点范围数（例如最小和最大节点计数），或者作业指定了处理器数量而不是节点数量，并且该群集包含具有不同处理器数量的节点，那么分配给排队状态作业的实际节点数可能会超过此数字。当作业完成时，这个数字将反映当前分配的节点数量。 （仅适用于作业）
    %e
    作业结束或预计结束的时间（根据其时间限制）。 （仅适用于作业）
    %E
    作业依赖所需的剩余条件。在这些相关作业完成之前，该作业不会开始执行。由于作业依赖得不到满足的情况下作业不运行，那么就会报告完整的原始作业依赖关系规范。 NULL值意味着这个作业没有依赖关系。 （仅适用于作业）
    %f
    这项作业所需的功能。 （仅适用于作业）
    %F
    作业数组的作业ID。对于非数组作业，这是作业ID。 （仅适用于作业）
    %g
    作业的Group名称。 （仅适用于作业）
    %G
    作业的Group ID。 （仅适用于作业）
    %h
    分配给作业的计算资源是否可能被其他作业订阅。要订阅的资源可以是节点，套接字，内核或超线程，具体取决于配置。如果作业是通过超额订阅选项提交的，或者分区配置为OverSubscribe = Force，则值为“YES”，如果作业需要独占节点则为“否”，如果分配的计算节点专用于单个用户则为“用户”，如果分配的计算节点专用于单个安全类，则为“MCS”（有关更多信息，请参阅MCSPlugin和MCSParameters配置参数），否则为“OK”（通常分配专用CPUs）。（仅适用于作业）
    %H
    作业请求的每个节点的套接字数量。这将报告srun --sockets-per-node选项的值。当 --sockets-per-node未被设置时，将显示“*”。 （仅适用于作业）
    %i
    作业或作业步ID。在作业数组的情况下，作业ID格式将采用“<base_job_id> _ <index>”的格式。默认情况下，作业数组索引字段大小将被限制为64个字节。使用环境变量SLURM_BITSTR_LEN指定更大的字段大小。（对作业和作业步有效）在异步作业分配的情况下，作业ID格式将采用“＃+＃”格式，其中第一个数字是“异步作业领导者”，第二个数字是作业每个组件的零原点偏移量。
    I%
    作业请求的每个套接字的内核数量。这会报告srun --cores-per-socket选项的值。当 -  --cores-per-socket没有设置时，显示“*”。 （仅适用于作业）
    %j
    作业或作业步名称。 （适用于作业和作业步）
    %J
    作业请求的每个核心的线程数。这报告了srun --threads-per-core选项的值。当 --threads-per-core没有设置时，显示“*”。 （仅适用于作业）
    %k
    作业相关的注解。 （仅适用于作业）
    %K
    作业数组的索引。默认情况下，该字段大小将被限制为64个字节。使用环境变量SLURM_BITSTR_LEN指定更大的字段大小。 （仅适用于作业）
    %l
    作业或作业步的时间限制，以天 - 小时：分钟：秒为格式。如果尚未建立，值可能是“NOT_SET”，或者没有限制，可能是“UNLIMITED”。 （适用于作业和作业步）
    %L
    作业执行剩下的的时间，以天 - 小时：分钟：秒为格式。该值是通过从其时间限制中减去作业所用的时间来计算的。如果尚未建立，值可能是“NOT_SET”，或者没有限制，可能是“UNLIMITED”。 （仅适用于作业）
    %m
    作业请求的最小内存大小（以MB为单位）。 （仅适用于作业）
    %M
    作业或作业步使用的时间，以天 - 小时：分钟：秒为格式。日期和时间仅根据需要打印。对于作业步，此字段显示执行开始后的经过时间，因此对于已暂停的作业步将不准确。集群中节点之间的时钟偏斜会导致时间不准确。如果时间明显错误，则显示为“INVALID”。 （适用于作业和作业步）
    %o
    要执行的命令
    %O
    作业所请求的连续节点。 （仅适用于作业）
    %p
    作业的优先级（转换为0.0到1.0之间的浮点数）。另请参阅％Q。 （仅适用于作业）
    %P
    作业或作业步所在的分区。 （适用于作业和作业步）
    %q
    与作业相关的服务质量。 （仅适用于作业）
    %Q
    作业的优先级（通常是一个非常大的无符号整数）。另请参阅％p。 （仅适用于作业）
    %r
    作业处于当前状态的原因。有关更多信息，请参阅下面的JOB REASON CODES部分。 （仅适用于作业）
    %R
    对于待处理作业：作业等待执行的原因将打印在括号内。对于失败的终止作业：作业为什么失败的解释被打印在括号内。对于所有其他作业状态：分配节点列表。有关更多信息，请参阅下面的JOB REASON CODES部分。 （仅适用于作业）
    %s
    作业在节点选择上要求的的特定数据。可能的数据包括：资源分配的几何要求（X，Y，Z尺寸），连接类型（TORUS，MESH或NAV == torus else mesh），允许几何旋转（是或否），节点使用（VIRTUAL或COPROCESSOR）等（仅适用于作业）
    %S
    作业或作业步的实际或预期开始时间。 （适用于作业和作业步）
    %t
    作业状态的缩略形式。请参阅下面的JOB STATE CODES部分以了解可能的状态列表。 （仅适用于作业）
    %T
    作业状态的扩展形式。请参阅下面的JOB STATE CODES部分以了解可能的状态列表。 （仅适用于作业）
    %u
    作业或作业步的用户名称。 （适用于作业和作业步）
    %U
    作业或作业步的用户ID。 （适用于作业和作业步）
    %v
    作业的预留资源。 （仅适用于作业）
    %V
    作业的提交时间
    %w
    作业量描述密钥（wckey）。 （仅适用于作业）
    %W
    为该作业保留的许可证。 （仅适用于作业）
    %x
    作业明确排除的节点名称列表。 （仅适用于作业）
    %X
    在每个节点上为系统使用保留的核心数（内核专用）。 （仅适用于作业）
    %y
    Nice值（调整作业的调度优先级）。 （仅适用于作业）
    %Y
    对于排队状态的作业，在作业开始时预期使用的节点列表。
    %z
    作业每个节点所需的套接字，内核和线程数（S：C：T）。没有设置（S：C：T）时，显示“*”。 （仅适用于作业）
    %Z
    作业的工作目录。

* <output_format>, --Format=<output_format>

指定要显示的信息。另请参阅上面介绍的-o <output_format>，--format = <output_format>选项（它支持更大的格式化灵活性，但不支持访问所有字段，因为我们用完了字母）。要求显示以逗号分隔的作业信息列表。

每个字段的格式是“type [：[.] size]”

    size
    是最小的字段大小。如果没有指定大小，则将使用打印信息所需的任何内容。
    .
    表示输出应该右对齐，并且必须指定大小。默认情况下，输出是左对齐的。

请注意，其中许多类型规格仅适用于作业，而其他类型仅适用于作业步骤。有效的类型规格包括：

    account
    打印与作业关联的帐户。 （仅适用于作业）
    admin_comment
    与作业相关的管理员注解。 （仅适用于作业）
    allocnodes
    打印分配给作业的节点。 （仅适用于作业）
    allocsid
    打印用于提交作业的会话ID。 （仅适用于作业）
    arrayjobid
    打印作业组的作业ID。 （适用于作业和作业步）
    arraytaskid
    打印作业组的任务ID。 （适用于作业和作业步）
    associd
    打印关联作业的ID。 （仅适用于作业）
    batchflag
    打印是否设置了batch标志。 （仅适用于作业）
    batchhost
    执行（batch）主机。对于分配的会话，这是会话正在执行的主机（即从中执行srun或salloc命令的节点）。对于一个batch作业，这是执行batch脚本的节点。在典型的Linux集群的情况下，这将是分配的计算节点零。在BlueGene或Cray / ALPS系统的情况下，这将是slurmd守护程序执行作业脚本的前端主机。 （仅适用于作业）
    boardspernode
    打印分配给作业的每个节点的boards数量。 （仅适用于作业）
    burstbuffer
    突发缓冲区规范（仅适用于作业）
    burstbufferstate
    突发缓冲区状态（仅适用于作业）
    chptdir
    打印作业检查点将写入的目录。 （仅适用于作业步骤）
    chptinter
    打印检查点的时间间隔。 （仅适用于作业步骤）
    cluster
    正在运行作业或作业步的群集的名称。
    clusterfeature
    作业所需的群集功能。 （仅适用于作业）
    command
    要执行的命令。 （仅适用于作业）
    comment
    与作业相关的评论。 （仅适用于作业）
    contiguous
    作业请求的连续节点。 （仅适用于作业）
    cores
    作业请求的每个套接字的内核数量。这会报告srun --cores-per-socket选项的值。当  --cores-per-socket没有设置时，显示“*”。 （仅适用于作业）
    corespec
    在每个节点上为系统使用保留的核心数（专用内核）。 （仅适用于作业）
    cpufreq
    打印分配的CPU的频率。 （仅适用于作业步骤）
    cpuspertask
    打印分配给作业的每个任务的CPU数量。 （仅适用于作业）
    deadline
    打印作业的截止时间（仅适用于作业）
    dependency
    作业依赖所需的剩余条件。在这些相关作业完成之前，这项作业不会开始执行。由于作业依赖得不到满足的情况下作业不运行，那么就会报告完整的原始作业依赖关系规范。 NULL值意味着这个作业没有依赖关系。 （仅适用于作业）
    delayboot
    延迟启动时间。 （仅适用于作业）
    derivedec
    作业的退出代码，这是任何作业步的最高退出代码。 （仅适用于作业）
    eligibletime
    作业有资格运行的时间。 （仅适用于作业）
    endtime
    作业终止的时间，实际或预期。 （仅适用于作业）
    exit_code
    该作业的退出代码。 （仅适用于作业）
    feature
    这项作业所需的功能。 （仅适用于作业）
    gres
    作业或作业步所需的通用资源（gres）。 （适用于作业和作业步）
    groupid
    作业的Group ID。 （仅适用于作业）
    groupname
    作业的Group名称。 （仅适用于作业）
    jobarrayid
    作业组的作业ID。对于非数组作业，这是作业ID。 （仅适用于作业）
    jobid
    作业ID。这将对作业数组的每个元素和异构作业的每个组件具有唯一的值。 （仅适用于作业）
    lastschedeval
    打印上次评估作业的时间。 （仅适用于作业）
    licenses
    为该作业保留的许可证。 （仅适用于作业）
    maxcpus
    打印分配给作业的最大CPU数量。 （仅适用于作业）
    maxnodes
    打印分配给作业的最大节点数量。 （仅适用于作业）
    mcslabel
    打印作业的MCS_label。 （仅适用于作业）
    minmemory
    作业请求的最小内存大小（以MB为单位）。 （仅适用作业）
    mintime
    作业的最短时间限制（仅适用于作业）
    mintmpdisk
    作业请求的临时磁盘空间的最小值（以MB为单位）。 （仅适用于作业）
    mincpus
    作业请求的每个节点的CPU（处理器）的最小数量。这会报告srun --mincpus选项的值，其默认值为零。 （仅适用于作业）
    name
    作业或作业步名称。 （适用于作业和作业步）
    network
    作业正在运行的网络。 （适用于作业和作业步）
    nice
    Nice值（调整作业的调度优先级）。 （仅适用于作业）
    nodes
    分配给作业或作业步的节点列表。在COMPLETING作业的情况下，节点列表将只包含那些尚未返回服务的节点。 （仅限有效的作业步）
    nodelist
    分配给作业或作业步的节点列表。在COMPLETING作业的情况下，节点列表将只包含那些尚未返回服务的节点。 （仅适用于作业）
    ntperboard
    分配给作业的每个board的任务数量。 （仅适用于作业）
    ntpercore
    分配给作业的每个内核的任务数量。 （仅适用于作业）
    ntpernode
    分配给作业的每个节点的任务数量。 （仅适用于作业）
    ntpersocket
    分配给作业的每个套接字的任务数量。 （仅适用于作业）
    numcpus
    如果作业运行，那么打印已请求或已分配给它的CPU（处理器）的数量。在作业完成时，这个数字将反映当前分配的CPU数量。 （适用于作业和作业步）
    numnodes
    分配给作业的节点数量或等待状态的作业所需的最小节点数量。如果作业指定了节点范围数（例如最小和最大节点计数），或者作业指定了处理器数量而不是节点数量，并且该群集包含具有不同处理器数量的节点，那么分配给排队状态作业的实际节点数可能会超过此数字。当作业完成时，这个数字将反映当前分配的节点数量。 （仅适用于作业）
    numtask
    作业或作业步请求的任务数量。这会报告--ntasks选项的值。 （适用于作业和作业步）
    origin
    联合作业源自的集群名称。 （仅对联合作业有效）
    originraw
    联合作业源自的群集ID。 （仅对联合作业有效）
    oversubscribe
    分配给作业的计算资源是否可能被其他作业订阅。要订阅的资源可以是节点，套接字，内核或超线程，具体取决于配置。如果作业是通过超额订阅选项提交的，或者分区配置为OverSubscribe = Force，则值为“YES”，如果作业需要独占节点则为“否”，如果分配的计算节点专用于单个用户则为“用户”，如果分配的计算节点专用于单个安全类，则为“MCS”（有关更多信息，请参阅MCSPlugin和MCSParameters配置参数），否则为“OK”（通常分配专用CPUs）。（仅适用于作业）
    packjobid
    异构作业领导者的作业ID。
    packjoboffset
    异构作业集合中的零原点偏移量。
    packjobidset
    异构作业中的所有作业ID。
    partition
    作业或作业步所在的分区。 （适用于作业和作业步）
    priority
    作业的优先级（转换为0.0到1.0之间的浮点数）。另见prioritylong。 （仅适用于作业）
    prioritylong
    作业的优先级（通常是一个非常大的无符号整数）。另见priority。 （仅适用于作业）
    profile
    作业简介。 （仅适用于作业）
    preemptime
    这项作业抢占的时间。 （仅适用于作业）
    qos
    与作业相关的服务质量。 （仅适用于作业）
    reason
    作业处于当前状态的原因。有关更多信息，请参阅下面的JOB REASON CODES部分。 （仅适用于作业）
    reasonlist
    对于排队状态的作业：作业等待执行的原因将打印在括号内。对于失败的终止作业：作业为什么失败的解释被打印在括号内。对于所有其他作业状态：分配节点列表。有关更多信息，请参阅下面的JOB REASON CODES部分。 （仅适用于作业）
    reboot
    指示在启动作业之前是否应重新启动分配的节点。 （仅适用于作业）
    reqnodes
    作业明确请求的节点名称（或BlueGene系统上的基本分区）列表。 （仅适用于作业）
    reqswitch
    作业所需的最大switch数量。 （仅适用于作业）
    requeue
    打印作业是否会在失败时重新排序。 （仅适用于作业）
    reservation
    作业的预留资源。 （仅适用于作业）
    resizetime
    作业运行改变的时间量。 （仅适用于作业）
    restartcnt
    作业的检查点重新启动次数。 （仅适用于作业）
    resvport
    作业的保留端口。 （仅适用于作业步骤）
    schednodes
    对于待处理作业，在作业开始时预期使用的节点列表。 （仅适用于作业）
    sct
    作业每个节点所需的套接字，内核和线程数（S：C：T）。没有设置（S：C：T）时，显示“*”。 （仅适用于作业）
    selectjobinfo
    作业在节点选择上要求的的特定数据。可能的数据包括：资源分配的几何要求（X，Y，Z尺寸），连接类型（TORUS，MESH或NAV == torus else mesh），允许几何旋转（是或否），节点使用（VIRTUAL或COPROCESSOR）等（仅适用于作业）
    siblingsactive
    有联合同级作业的集群名称。 （仅对联合作业有效）
    siblingsactiveraw
    有联合同级作业的集群ID。 （仅对联合作业有效）
    siblingsviable
    可以运行联合同级作业的集群名称。 （仅对联合作业有效）
    siblingsviableraw
    可以运行联合同级作业的集群ID。 （仅对联合作业有效）
    sockets
    作业请求的每个节点的套接字数量。这将报告srun --sockets-per-node选项的值。当 --sockets-per-node未被设置时，将显示“*”。 （仅适用于作业）
    sperboard
    分配给作业的每个board的套接字数量。 （仅适用于作业）
    starttime
    作业或作业步的实际或预期开始时间。 （适用于作业和作业步）
    state
    作业状态的扩展形式。请参阅下面的JOB STATE CODES部分以了解可能的状态列表。 （仅适用于作业）
    statecompact
    作业状态的缩略形式。请参阅下面的JOB STATE CODES部分以了解可能的状态列表。 （仅适用于作业）
    stderr
    输出标准错误的目录。 （仅适用于作业）
    stdin
    标准输入的目录（仅适用于作业）
    stdout
    标准输出的目录。 （仅适用于作业）
    stepid
    作业或作业步ID。在作业数组的情况下，作业ID格式将采用“<base_job_id> _ <index>”的格式。 （仅
    有效的作业步）
    stepname
    作业步名称。 （仅适用于作业步）
    stepstate
    作业步的状态。 （仅适用于作业步）
    submittime
    作业提交的时间。 （仅适用于作业）
    threads
    作业请求的每个内核的线程数。这报告了srun --threads-per-core选项的值。当--threads-per-core没有设置时，显示“*”。 （仅适用于作业）
    timeleft
    作业执行剩下的的时间，以天 - 小时：分钟：秒为格式。该值是通过从其时间限制中减去作业所用的时间来计算的。如果尚未建立，值可能是“NOT_SET”，或者没有限制，可能是“UNLIMITED”。 （仅适用于作业）
    timelimit
    作业或作业步的时间限制。 （适用于作业和作业步）
    timeused
    作业或作业步使用的时间，以天 - 小时：单位：分钟：秒为格式。日期和时间仅根据需要打印。对于作业步，此字段显示执行开始后的经过时间，因此对于已暂停的作业步将不准确。集群中节点之间的时钟偏斜会导致时间不准确。如果时间明显错误，则显示为“INVALID”。 （适用于作业和作业步）
    tres
    打印分配给作业的可跟踪资源。
    userid
    作业或作业步的用户ID。 （适用于作业和作业步）
    username
    作业或作业步的用户名称。 （适用于作业和作业步）
    wait4switch
    等待所需数量交换机的时间量。 （仅适用于作业）
    wckey
    作业量描述密钥（wckey）。 （仅适用于作业）
    workdir
    作业的工作目录。 （仅适用于作业）

* -p <part_list>, --partition=<part_list>

指定要查看的作业或作业步的分区。接受逗号分隔的分区名称列表。

* -P, --priority

对于提交给多个分区的排队状态的作业，请为每个分区列出一次作业。另外，如果按照优先级排序作业，请考虑分区和作业优先级。这个选项可以用于生成一个排队状态的作业列表，可以考虑使用相同的附加相应的选项的命令通过slurm去调度（例如“--sort = -p，i --states = PD”）。

* -q <qos_list>, --qos=<qos_list>

指定要查看的作业或作业步的qos（s）。接受以逗号分隔的qos列表。

* -R, --reservation=reservation_name

指定要查看的作业的预留资源。

* -s, --steps

指定要查看的作业步。此标志后面跟随用逗号分隔的作业步列表，不加等号（请参阅示例）。作业步格式为 “JOB_ID [_array_id] .step_id”。默认为所有作业步。由于此选项的参数是可选的，因此为了正确解析，单字母选项后面必须紧跟值，并且不要在它们之间包含空格。例如“-s1008.0”而不是“-s 1008.0”。

* --sibling

显示联合集群上的所有同级作业。意味着 --federation。

* -S <sort_list>, --sort=<sort_list>

按何种命令格式输出。这使用与<output_format>相同的文件格式。长格式选项“cluster”也可用于按群集名称对作业或作业步进行排序（例如联合作业）。列出多个用逗号分隔的排序字段可以执行多种排序。字段规格可以分别以升序（默认）和降序排列，前面加“+”或“ - ”。例如，排序值“P，U”将按照分区名称对用户ID进行排序。排序作业的默认值是“P，t，-p”（按分区名称升序，然后在分区内按作业状态升序，然后按优先级降序）。作业步的排序默认值为“P，i”（按分区名称升序，然后在分区内按作业步ID升序）。

* --start

按照开始时间的升序报告排队状态作业的预期开始时间和资源。这相当于以下选项： --format=“％.18i％.9P％.8j％.8u％.t％.19S％.6D％20Y％R”，--sort = S和--states = PENDING。通过将--start选项与其他选项值相结合（例如，使用不同的输出格式），可以根据需要显式更改这些选项中的任何一个。排队状态作业的预期开始时间仅在Slurm配置为使用回填调度插件时可用。

* -t <state_list>, --states=<state_list>

指定要查看处于某种状态的作业。接受一个以逗号分隔的状态列表或“all”。如果指定了“all”，则会输出任何状态的作业。如果未指定状态，则报告排队，正在运行和完成的作业。请参阅下面的JOB STATE CODES部分以获取有效状态列表。扩展和缩略形式都是有效的。请注意，提供的<state_list>不区分大小写（“pd”和“PD”是等效的）。

* -u <user_list>, --user=<user_list>

从逗号分隔的用户列表中查找作业或作业步。该列表可以由用户名或用户ID组成。在指定单个用户时，对于具有大量作业的系统，可以显著提高该命令的性能。

* --usage

打印列出squeue选项的简短帮助消息。

* -v, --verbose

报告执行squeues动作的细节。

* -V , --version

打印版本信息并退出。

* -w <hostlist\>, --nodelist=<hostlist\>

仅报告分配给指定节点或节点列表的作业。可以是slurm.conf中定义的NodeName或NodeHostname，如果它们不同。localhost的node_name映射到当前主机名。

## JOB REASON CODES

这些代码标识作业等待执行的原因。作业等待可能会有多个原因，在这种情况下，只会显示其中一个原因。

* AssociationJobLimit

该作业的四元组已达到其最大作业数量。

* AssociationResourceLimit

该作业的四元组已经达到了一定的资源限制。

* AssociationTimeLimit

该作业的四元组已经达到了时间限制。

* BadConstraints

作业的限制条件不能得到满足。

* BeginTime

这项作业的最早开始时间尚未达成。

* BlockFreeAction

IBM BlueGene块正在被释放，并且无法启动更多作业。

* BlockMaxError

IBM BlueGene块有太多处于错误状态的cnodes而不允许启动更多作业。

* Cleaning

作业正在从之前的执行中清除并重新排队。

* Dependency

这项作业正在等待一项依赖性作业来完成。

* FrontEndDown

没有前端节点可用于执行此作业。

* InactiveLimit

该作业到达系统的InactiveLimit。

* InvalidAccount

该作业的帐户无效。

* InvalidQOS

作业的QOS无效。

* JobHeldAdmin

该作业由系统管理员持有。

* JobLaunchFailure

这项作业无法启动。这可能是由于文件系统问题，程序名称无效等。

* Licenses

这项作业正在等待许可证。

* NodeDown

作业所需的节点已关闭。

* NonZeroExitCode

作业以非零退出代码终止。

* PartitionDown

此作业所需的分区处于DOWN状态。

* PartitionInactive

此作业所需的分区处于非活动状态，无法启动作业。

* PartitionNodeLimit

此作业所需的节点数超出了分区的当前限制。还可以表明所需节点为DOWN或DRAINED。

* PartitionTimeLimit

作业的时间限制超出了分区的当前时间限制。

* Priority

该分区提前预留资源存在一个或多个更高优先级的作业。

* Prolog

它的PrologSlurmctld程序仍在运行。

* QOSJobLimit

该作业的QOS已达到其最大作业数。

* QOSResourceLimit

该作业的QOS已经达到了一定的资源限制。

* QOSTimeLimit

该作业的QOS已经达到了时间限制。

* ReqNodeNotAvail

该作业特别要求的某些节点目前不可用。该节点当前可能正在使用中，为其他作业保留，提前预留，关闭，已排除或未响应。DOWN，DRAINED或没有响应的节点将被标识为作业的“reason”字段的一部分，称为“UnavailableNodes”。这些节点通常需要系统管理员的介入才能使用。

* Reservation

该作业正在等待其提前预留资源。

* Resources

该作业正在等待资源可用。

* SystemFailure

Slurm系统，文件系统，网络等的故障。

* TimeLimit

该作业耗尽了时间限制。

* QOSUsageThreshold

所需的QOS阈值已被超过。

* WaitingForScheduling

这项作业还没被设置理由。等待调度程序确定适当的原因。

## JOB STATE CODES

作业在执行过程中通常会经过几个状态。典型的状态是PENDING，RUNNING，SUSPENDED，COM​​PLETING和COMPLETED。每个状态的解释如下。

* BF BOOT_FAIL

由于启动失败导致作业终止，通常是由于硬件故障（例如，无法引导节点或块并且无法重新执行作业）。

* CA CANCELLED

作业被用户或系统管理员明确取消。这项作业可能已经启动，也可能没有启动。

* CD COMPLETED

作业已经以退出代码为零终止所有节点上的所有进程。

* CF CONFIGURING

作业已分配资源，但正在等待它们准备就绪（例如引导）。

* CG COMPLETING

作业正在完成。某些节点上的某些进程可能仍处于活动状态。

* DL DEADLINE

作业在截止日期终止。

* F FAILED

作业以非零退出代码或其他失败情况终止。

* NF NODE_FAIL

作业因一个或多个分配的节点失败而终止。

* OOM OUT_OF_MEMORY

作业出现内存不足错误。

* PD PENDING

作业正在等待资源分配。

* PR PREEMPTED

作业由于抢占而终止。

* R RUNNING

作业目前有一个分配。

* RD RESV_DEL_HOLD

作业被持有。

* RF REQUEUE_FED

作业由联合集群重新排队。

* RH REQUEUE_HOLD

被持有的作业正在重新排队。

* RQ REQUEUED

正在完成的作业被重新排队。

* RS RESIZING

作业即将改变大小。

* RV REVOKED

由于其他集群开始该作业，同级作业已从群集中删除。

* SE SPECIAL_EXIT

这项作业以特殊状态重新进行。如果作业已经以特定的退出值终止，则用户可以设置该状态，通常在EpilogSlurmctld中。

* ST STOPPED

作业有一个分配，但执行已经用SIGSTOP信号停止。这作业仍保留了CPUS。

* S SUSPENDED

作业有一个分配，但是执行已经被暂停，并且CPU已经被释放用于其他作业。

* TO TIMEOUT

作业达到时限后终止。

## ENVIRONMENT VARIABLES

一些squeue选项可以通过环境变量设置。下面列出了这些环境变量及其相应的选项。 （注意：命令行选项将始终覆盖这些设置。）

* SLURM_BITSTR_LEN

指定用于保存作业数组的任务ID表达式的字符串长度。默认值是64个字节。值为0将打印所需长度的完整表达式。较大的值可能会对应用程序性能产生不利影响。

* SLURM_CLUSTERS

与--clusters相同

* SLURM_CONF

Slurm配置文件的位置。

* SLURM_TIME_FORMAT

指定用于报告时间戳的格式。标准值（默认值）以“year-month-dateThour：minute：second”的形式生成输出。如果是当天，相对值仅返回“hour:minute:second”。对于今年的其他日期，它会打印“Tomorr”（明天），“Ystday”（昨天），即将到来的星期的当天名称（例如“Mon”，“Tue”，等等），否则是日期（例如“4月25日”）。对于其他年份，它返回没有时间的日期月份和年份（例如“2012年6月6日”）。所有时间戳都使用24小时格式。

* SQUEUE_ACCOUNT

-A <account_list>, --account=<account_list>

* SQUEUE_ALL

-a, --all

* SQUEUE_ARRAY

-r, --array

* SQUEUE_NAMES

--name=<name_list>

* SQUEUE_FEDERATION

--federation

* SQUEUE_FORMAT

-o <output_format>, --format=<output_format>

* SQUEUE_FORMAT2

-O <output_format>, --Format=<output_format>

* SQUEUE_LICENSES

-p-l <license_list>, --license=<license_list>

* SQUEUE_LOCAL

--local

* SQUEUE_PARTITION

-p <part_list>, --partition=<part_list>

* SQUEUE_PRIORITY

-P, --priority

* SQUEUE_QOS

-p <qos_list>, --qos=<qos_list>

* SQUEUE_SIBLING

\fB--sibling

* SQUEUE_SORT

-S <sort_list>, --sort=<sort_list>

* SQUEUE_STATES

-t <state_list>, --states=<state_list>

* SQUEUE_USERS

-u <user_list>, --users=<user_list>

英文版点击[此处](https://slurm.schedmd.com/squeue.html),Time: 21:06:17 GMT, June 13, 2018版。