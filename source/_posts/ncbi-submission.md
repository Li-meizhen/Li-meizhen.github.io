---
title: 向NCBI提交基因组注释文件
date: 2020-05-19 16:36:29
tags:
---

今天一天只做了一件无聊的事情，还没成功。后面也免不了反复，所以先简单记录下来。

### 背景

提交基因组文件之前需要先进行[BioProject和BioSample注册](https://submit.ncbi.nlm.nih.gov/subs/bioproject/)。得到对应的两个序列号之后开始[提交基因组文件](https://submit.ncbi.nlm.nih.gov/subs/genome/)。

单个基因组选择Single genome，一次提交多个基因组选择Batch/Multiple genomes。此处以提交单个基因组为例。填完基本信息之后，最关键的环节就是基因组文件的上传。**Single genome只允许上传一个fna或SQN文件**。如果只提交基因组序列，可直接上传fasta或fasta.gz文件。

如果同时要上传注释文件，情况就有一点复杂。常见的.gff注释文件是不能直接上传的，**annotated genome需要以.sqn文件格式上传**。我们可以采用NCBI提供工具[table2ans](ftp://ftp.ncbi.nih.gov/toolbox/ncbi_tools/converters/by_program/table2asn_GFF/)将.gtf文件转为.sqn格式。选择linux版本下载，解压之后赋予执行权限即可使用，运行要求GLIBC_2.17。

### 数据准备

我在这一步走了一些弯路。首先需要准备的当然是基因组序列文件genome.fasta和对应的注释文件annotation.gff3。后续步骤中我们需要给定locus_tag，即每个基因都有的tag，与基因id以"_"分开，对应的，annotation.gff3中的基因最好采用相似的命名方式。

接下来要准备一个template.stb文件记录作者和文章信息，可以点击[这里](https://submit.ncbi.nlm.nih.gov/genbank/template/submission/)填写信息自动生成。还有一个genome.asm文件记录测序及组装方法，NCBI也提供了[自动生成页面](https://submit.ncbi.nlm.nih.gov/structcomment/genomes/)。

### 运行table2ans

运行table2ans的参数在上述数据准备的过程中都提到过。

```bash
$ table2asn -M n -J -c w -euk -gaps-min 10 -l paired-ends -locus-tag-prefix XXXX -t template.sbt -w genome.asm -i genome.fasta -f annotation.gff3 -o output.sqn -Z 
```

结果文件中首先要看 ***output.stats***，查看是否有*Errors*或者*Rejects*。另外就是关注 ***output.dr***，其中的*FATAL*信息也是要注意的。关于这两个文件中错误类型的解读可以参考[基因组验证未通过的原因解读](https://www.ncbi.nlm.nih.gov/genbank/genome_validation/)以及[dr文件解读](https://www.ncbi.nlm.nih.gov/genbank/asndisc/#Evaluating%20the%20output)。如果是真核基因组，那么BACTERIA相关的FATAL是可以忽略的，其他的问题要逐一解决或者写说明文件阐明这不是注释错误，否则即使成功提交，也会很快退回。

至于怎么解决这些出现的具体问题，那就又是另一个悲伤的故事了。



