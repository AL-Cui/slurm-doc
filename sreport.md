# sreport

## Name

sreport - 从slurm账户数据生成报告

## SYNOPSIS

sreport [OPTIONS...] [COMMAND...]

## DESCRIPTION

sreport用于生成保存到Slurm数据库slurmdbd的Slurm作业的作业使用情况和集群利用率报告。

## OPTIONS

* -a, --all_clusters

查找所有集群而不是仅执行命令的集群。

* --federation

如果是联合集群中的一员，则为集群作业生成报告。

* -h, --help

打印描述sreport用法的帮助信息。

* --local

生成本地集群的报告，即使是联合集群的一部分。覆盖 --federation

* -M, --cluster

为集群生成报告。默认为本地集群，除非本地集群当前是联合集群的一部分，只有在这种情况下为当前联合集群中的所有集群生成报告。如果联合集群中包含的集群随时间变化，请使用此选项来标识要包含在报告中的集群。即--local。

* -n, --noheader

列出结果时不要显示标题。

* -p, --parsable

输出时用'|'分割，并且末尾有'|'符号。

* -P, --parsable2

输出时用'|'分割，并且末尾没有'|'符号。

* -Q, --quiet

不打印警告或信息性消息，仅打印错误消息。

* -t <\format>

指定输出时间格式。时间格式选项不区分大小写，可能会缩写。默认格式为分钟。下面的time命令部分列出了支持的时间格式选项。

* -T, --tres <tres_names>

报告可跟踪资源（TRES）值。默认情况下会报告CPU资源使用情况（预留资源报告除外。除非覆盖此选项，否则默认显示预留资源保留的ALL TRES类型）。对于除作业报告之外的所有报告，可以使用逗号分隔多个TRES名称，也能支持单个TRES名称，或者“ALL”针对所有TRES。“Reported”的TRES统计值是根据每个节点的最大TRES统计值乘以时间帧计算得出的。例如，如果一个节点是多个分区的一部分，并且每个节点具有不同的TRESBillingWeights定义，则该节点的TRES统计值将是最高的分区。

* -v, --verbose

打印详细的事件记录。

* -V , --version

打印版本信息并退出。

## COMMANDS

<keyword>可以从执行行中省略,sreport以交互模式执行。 sreport将处理命令，直到显式终止。

* exit

终止sreport的执行。与quit命令相同。

* help

显示sreport选项和命令的说明。

* nonparsable

在设置了parsable或parsable2之后将输出恢复为正常。

* parsable

输出以"|"分割，并用"|"结尾。

* parsable2

输出以"|"分割，不使用"|"结尾。

* quiet

不打印任何警告或信息性消息，仅打印致命错误消息。

* quit

终止sreport的执行。与exit命令相同。

* time <time_format>

指定输出时间格式。时间格式选项不区分大小写，可能会缩写。默认格式为分钟。支持的时间格式选项包括：

    SecPer
       秒数/总数百分比
    Minper
        分钟数/总数百分比
    HourPer
        小时数/总数百分比
    Seconds
        秒
    Minutes
        分钟
    Hours
        小时
    Percent
        总数的百分比

* verbose

启用详细的事件记录。

* version

显示sreport版本号。

* !!

重复执行最后一个命令。

## REPORT TYPES

有效的报告类型是：

    cluster <REPORT> <OPTIONS>
    job <REPORT> <OPTIONS>
    reservation <REPORT> <OPTIONS>
    user <REPORT> <OPTIONS>

每种类型的<\REPORT>选项包括：

    cluster - AccountUtilizationByUser, UserUtilizationByAccount, UserUtilizationByWckey, Utilization, WCKeyUtilizationByUser

    job - SizesByAccount, SizesByAccountAndWcKey, SizesByWckey

    reservation - Utilization

    user - TopUsage

## REPORT DESCRIPTION

* cluster AccountUtilizationByUser

此报告将显示分层树上的帐户利用率。默认情况下，从指定的帐户或root帐户开始，此报告将列出潜在使用在每个级别上的累加总和。使用“tree”选项跨越树以获得更好的可见性。**注意**：如果有预留资源允许在任何空闲时间整个帐户使用分配给帐户四元组的预留资源，而不是帐户中的用户四元组，因此，父帐户可能大于其子帐户的总和。

This report will display account utilization as it appears on the hierarchical tree. Starting with the specified account or the root account by default this report will list the underlying usage with a sum on each level. Use the 'tree' option to span the tree for better visibility. NOTE: If there were reservations allowing a whole account any idle time in the reservation given to the association for the account, not the user associations in the account, so it can be possible a parent account can be larger than the sum of it's children.

* cluster UserUtilizationByAccount

此报告将按账户显示用户的使用情况，而不会将多个账户通过用户分为一个组，但会在单独的行中显示它们。

This report will display users by account in order of utilization without grouping multiple accounts by user into one, but displaying them on separate lines.

* cluster UserUtilizationByWCKey

此报告将按wckey的顺序显示用户的使用情况，而不会将多个wckey通过用户分为一个组，但将它们显示在单独的行中。

This report will display users by wckey in order of utilization without grouping multiple wckey by user into one, but displaying them on separate lines.

* cluster Utilization

此报告将显示所选群集的总使用量以“已分配”，“已关闭”，“空闲”和“预留”时间来划分。预留时间是指作业在符合条件后作业等待资源的时间。如果该值对您不重要，则该数字应与空闲时间分组。

* cluster WCKeyUtilizationByUser

此报告将以wckey名称排序显示每个集群上每个用户的wckey的使用情况。

* job SizesByAccount

此报告将显示“grouping =”选项指定的作业域所用的时间量。只显示树中的单个级别，默认为根目录。如果您使用'account ='选项指定其他帐户，则sreport将使用这些帐户作为root帐户，您将收到指定的帐户的子帐户之和。

* job SizesByAccountAndWckey

此报告与SizesByAccount非常相似，区别在于每个帐户都与wckeys配对，因此标识符为account：wckey而不仅仅是帐户，因此根据使用的wckeys数量，很可能会列出多个帐户。

* job SizesByWckey

此报告将显示“grouping =”选项指定的作业域的每个wckey的时间量。

* reservation Utilization

此报告将显示系统上预留资源的总使用量。

* user TopUsage

显示群集上的用户排名。使用组选项将帐户组合在一起。默认设置是为每个用户帐户组合使用不同的行。

每种报告类型都有各种选择......

## OPTIONS FOR ALL REPORT TYPES

* All_Clusters

使用所有受监控的集群，默认为本地集群。

* Clusters=<\OPT>

要包含在报告中的集群列表。默认为本地群集。

* End=<\OPT>

报告的截止时间。默认是前一天的23:59:59。有效的时间格式是......

    HH:MM[:SS] [AM|PM]
    MMDD[YY] or MM/DD[/YY] or MM.DD[.YY]
    MM/DD[/YY]-HH:MM[:SS]
    YYYY-MM-DD[THH:MM[:SS]]

* Format=<\OPT>

用来在报告中显示的逗号分隔的字段列表。 使用format选项列出各种字段时，可以在之后添加％NUMBER以指定应打印的字符数。

例如format = name％30将打印30个字符的字符右对齐。 A -30将打印30个字符左对齐。

* Start=<\OPT>

报告的开始时间。默认是前一天的23:59:59。有效的时间格式是......

    HH:MM[:SS] [AM|PM]
    MMDD[YY] or MM/DD[/YY] or MM.DD[.YY]
    MM/DD[/YY]-HH:MM[:SS]
    YYYY-MM-DD[THH:MM[:SS]]

## OPTIONS SPECIFICALLY FOR CLUSTER REPORTS

* Accounts=<\OPT>

与UserUtilizationByAccount或AccountUtilizationByUser一起使用时，要包含在报告中的帐户列表。默认是全部。

* Tree

与AccountUtilizationByUser一起使用时，报告将跨越同阶层的帐户。

* Users=<\OPT>

与Utilization以外的任何报告一起使用时，要包括在报告中的用户列表。默认是全部。

* Wckeys=<\OPT>

与UserUtilizationByWckey或WCKeyUtilizationByUser一起使用时，要包含在报告中的wckeys列表。默认是全部。

## OPTIONS SPECIFICALLY FOR JOB REPORTS

* Accounts=<\OPT>

用于报告的帐户列表,默认为全部。 SizesByAccount（\*）报告仅显示1层级。如果指定了账户，则会显示指定的下一层账户，而不是指定的账户。在SizesByAccount（*）报告中，帐户的默认值为root。使用FlatView选项运行时，此解释不适用。

* FlatView

与SizesbyAccount（*）一起使用时，不会对同一阶层的帐户进行分组，而是打印作业在不带任何阶层的单独行上运行的每个帐户。

* GID=<\OPT>

要包含在报告中的组ID列表。默认是全部。

* Grouping=<\OPT>

逗号分隔的大小分组列表。(例如50,100,150将工作cpu计数为1-49,50-99,100-149，> 150)。grouping = individual将为找到的每个作业大小生成一个列。

* Jobs=<\OPT>

要包含在报告中的作业或作业步列表。默认是全部。

* Wckeys=<\OPT>

用于报告的wckeys列表。默认是全部。 SizesbyWckey报告将所有用户汇总在一起。如果只希望某些用户使用Users =选项指定它们。

## OPTIONS SPECIFICALLY FOR RESERVATION REPORTS

* Names=<\OPT>

用于报告的预留资源清单。默认是全部。

* Nodes=<\OPT>

仅显示使用这些节点的预留资源。默认是全部。

## OPTIONS SPECIFICALLY FOR USER REPORTS

* Accounts=<\OPT>

用于报告的帐户列表。默认是全部。

* Group

将每个用户的所有帐户组合在一起。Default is a separate entry for each user and account reference.

* TopCount=<\OPT>

用于TopUsage报告。更改显示的用户数。默认值为10。

* Users=<\OPT>

要包含在报告中的用户列表。默认是全部。

## FORMAT OPTIONS FOR EACH REPORT

* CLUSTER报告的格式选项AccountUtilizationByUser：

    UserUtilizationByAccount: Accounts, Cluster, Login, Proper, TresCount, Used

    UserUtilizationByWckey:

    WCKeyUtilizationByUser: Cluster, Login, Proper, TresCount, Used, Wckey

* FORMAT OPTIONS FOR JOB REPORTS

    SizesByAccount, SizesByAccountAndWckey: Account, Cluster
    SizesByWckey: Wckey, Cluster

* FORMAT OPTIONS FOR RESERVATION REPORTS

    Utilization: Allocated, Associations, Cluster, End, Flags, Idle, Name, Nodes, ReservationId, Start, TotalTime, TresCount, TresName, TresTime

* FORMAT OPTIONS FOR USER REPORTS

    TopUsage: Account, Cluster, Login, Proper, Used

另外：**所有命令和选项都不区分大小写。**

## ENVIRONMENT VARIABLES

一些sreport选项可以通过环境变量设置。下面列出了这些环境变量及其相应的选项。 （注意：命令行选项将始终覆盖这些设置）

* SREPORT_CLUSTER

类似于-M, --cluster

* SREPORT_FEDERATION

类似于 --federation

* SREPORT_LOCAL

类似于 --local

* SREPORT_TRES

类似于 -t, --tres

* SLURM_CONF

Slurm配置文件的位置。

## EXAMPLES

    sreport job sizesbyaccount
    sreport cluster utilization
    sreport user top
    sreport job sizesbyaccount All_Clusters users=gore1 account=environ PrintJobCount
        报告用户gore1在environ帐户中的作业数量
    sreport cluster AccountUtilizationByUser cluster=zeus user=gore1 start=2/23/08 end=2/24/09 format=Accounts,Cluster,TresCount,Login,Proper,Used
        使用指定字段报告用户gore1在指定的24小时内在集群zeus上的集群帐户使用情况
    sreport cluster AccountUtilizationByUser cluster=zeus accounts=lc start=2/23/08 end=2/24/09
        报告LC帐户在集群zeus上按用户顺序排列的集群帐户使用情况
    sreport user topusage start=2/16/09 end=2/23/09 -t percent account=lc
        报告在指定的一周内lc帐户内用户使用率排名

原文文档点击[此处](https://slurm.schedmd.com/sreport.html),Time: 21:06:16 GMT, June 13, 2018版。