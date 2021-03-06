## ConFix 2018 Experiment Results
`result` directory contains Human and ConFix patches as well as analysis results.

Directories and files in `result` have the following structure.
> - project
>   - bugname
>     - buggy (Buggy code)
>     - confix (ConFix patch)
>        - edit (The applied change to generate ConFix patch)
>     - human (Human-written patch)
>     - confix.diff (Differences between buggy code & ConFix patch.) 
>     - human.diff (Differences between buggy code & human-written patch.)
>     - patch_info (Simple info. on ConFix patch)

Each `edit` file contains detailed information of the applied change like the following.

```
Class:org.jfree.chart.block.BorderArrangement //modified class
update
w
to
h
at line 450 //string representation of the change.
Applied Change
update
var0
at ArrayAccess::null[array]
to
var0 //applied abstract change in change pool
from
hadoop-0815a6f:hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/web/resources/NamenodeWebHdfsMethods.java: //where the change was collected
19 //frequency in change pool
P:ArrayAccess{array},L:,R:{34} //change's context
```

Here is a draft with details of Confix - [confix draft](https://github.com/thwak/confix2018result/wiki/confix.pdf)
