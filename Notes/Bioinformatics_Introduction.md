# 1 测序平台介绍

### 1.1 Illumina

Illumina目前占据了基因测序仪市场70%的份额。
#### HiSeq X Ten
2014年初推出，一套测序系统包括**10台**HiSeq X测序仪，适合群体规模的测序项目，且是当前全球最强通量最高的测序仪。
售价每台100万美元，10台起售。每台HiSeqX每天可以产生600GB数据，同时运行10台的情况下，每年可测序>18000个人类基因组（每人全基因组30倍的覆盖深度）。

#### HiSeq X Five
2015年推出，属于Ten的缩小版，包括5台HiSeq X。
#### HiSeq 4000
2015年推出，基于HiSeq2500系统开发的具有**双流动槽**的测序仪，但没有**快速运行模式**。售价90万美元，能在3.5天内测序12个基因组、100个转录组或者180个外显子组。

#### HiSeq 3000
2015年推出，也基于HSeq2500，通量为4000的一半。（需要时可以直接升级为4000）售价74万美元，每次最多测6个人类基因组（30倍），时间<3天；每次最多可测序90个外显子组（假定每个外显子组4GB）或50个人类全转录组（假定每个样品5000万条序列）。
#### HiSeq 2500
单次产出约600GB，一天内约100GB。
#### MiSeq
行业最准确最易用的台式测序仪之一。唯一一台单次运行中产生2×300bp双端reads和高达15GB数据的台式测序仪。适合小型基因组的组装或目标变异的准确检测。
* MiSeqDx
唯一一台经过FDA批准的体外诊断（IVD）下一代测序（NGS）系统，专为临床实验室环境设计。操作：1.从人类外周血标本中提取出基因组DNA（gDNA）→2.添加引物、生成带索引的文库，制备测序用gDNA样品，同时开始捕获和扩增→3.将文库添加到MiSeqDx流动槽中，上样测序。
#### NextSeq 500和550
如果说HiSeq X Ten是工厂规模的测序仪，那么NextSeq 500则是以MiSeq的大小提供HiSeq的性能。是目前唯一一款可以实现外显子组，转录组和全基因组测序的台式测序仪。单次运行可产生20-120GB数据。
### 1.2 Roche 454
基于焦磷酸测序法。GSFLX系统的特点是最大读长能够达到1000bp，且拥有一键式数据处理和分析软件。

### 1.3 Applied Biosystems SOLiD
SOLiD（supported oligo ligation detection）以四色荧光标记寡核苷酸的连续合成来读取核苷酸上的碱基，可对单个拷贝的DNA片段进行高通量测序。
### 1.4 PacBio RSII单分子测序
PacBio RS目前可获得2500-3000bp的读长，大幅降低了基因组拼接难度，每天可获得数据量是1080Mb。
### 1.5 Ion
PGM和Proton半导体测序Life Science公司利用DNA合成时释放一个氢离子的特性，利用半导体检测来达到测序目的。

快速、简洁、价格实惠。

# 2 基因组测序数据的组装

这里我们下载由Solexa/Illumina测序平台对Staphylococcus aureus strain MW2（金黄色葡萄球菌）的基因组进行测序的数据（http://www.genomic.ch/edena/mw2Reads.seq.gz）。

序列数据的解压缩

```{.python .input .Bash}
$ wget http://www.genomic.ch/edena/mw2Reads.seq.gz
$ gunzip mw2Reads.seq.gz
$ more mw2Reads.seq
#数据格式显示
```

### 2.1 SSAKE组装过程

```{.python .input .Bash}
$ wget http://www.bcgsc.ca/bioinfo/software/ssake_v4-0-tar.gz  
$ tar -xvf ssake_v4-0.tar.gz                        
$ mkdir new-assembly
$ mv mw2Reads.seq new-assembly
$ SSAKE -f /new-assembly/mw2Reads.seq -m 25 -o 8 -r 0.7      
#组装命令（待组装数据为单末端测序片段，序列长35bp）

#解压后出现ssake，然后commend not
found了……理论上组装完成得到4个文件：
①.contigs→组装最终结果重叠群序列的fasta文件
②.log→组装过程记录日志文件
③.short→用于储存待组装序列中长度小于m的部分，这些序列无法用于后续组装
④.singlets→用于储存进入组装过程但没有用于序列延伸的序列
```

### 2.2 Edena组装过程

```{.python .input .Bash}
$ wget http://www.genomic.ch/edena/edena2.1.1_linux64.tar.gz
$ tar -xvf edena2.1.1_linux64.tar.gz
$ cd edena2.1.1_linux64
#产生中间过程文件out_20.ovl用于后续组装
$ edena -r ../new-assembly/mw2Reads.seq -M 20 -p out_20 -t 1      
$ edena -e
./out_20.ovl -p strict_20 -c 100 -s 1
#Edena组装分2部，一是重叠模式建立重叠图，二是组装模式对图进行修剪并输出最终组装结果；
①.info→组装过程记录日志文件
②.fasta→用于储存符合设定条件并被输出的重叠群序列
③.cov文件→用于储存组装结果重叠群序列的每个碱基位置的覆盖度信息
```

**edena也是commend not found!**
目测需要把edena添加到什么默认PATH里去，于是：

```{.python .input .Bash}
$ echo
'PATH=$PATH:/home/sayori/bioinformatics/Edena/edena2.1.1_linux64' >> ~/.bashrc
$
source ~/.bashrc
之后可以顺利使用edena命令完成组装得到数据。
```

### 2.3 SOAPdenovo组装过程

```{.python .input}
​```Base
$ wget http://soap.genomics.org.cn/soappdenovo.html
$ tar -xvf ./ SOAPdenovo-v1.04.tgz
$ cd SOAPdenovo_Release1.04
$ ./SOAPdenovo
$ mkdir ./new-assembly
#移入数据，配置configFile
$ maximal read length
max_rd_len=35
asm_flags=1
f=./mw2Read.seq
$ ./SOAPdenovo all -s ./configFile -K 25 -o graph_prefix -p 8
#结果中的.contig文件即为组装重叠群的储存文件
```

# 3 ChIP-seq数据分析

## 3.1 碱基识别

ChIP-Seq的原始数据是从深度测序平台得到的图片文件，第一步分析便是通过图片文件确定对应的碱基位置（base calling）。这一般由测序平台自带的软件完成，也有其他算法，如Erlich(2008) 和Rougemont(2008)等，据称是减少测序错误而增加了有意义的原始数据，但是也增加了CPU的消耗。

## 3.2 定位到基因组

考虑到：

1.ChIP-Seq中可能存在测序错误；

2.样本中可能存在单核苷酸多态性（SNP），插入或缺失等；

要求ChIP-Seq产生的短序列片段比对软件需要允许**较少**错配。

## 3.3 富集区域的鉴定

### 算法

PeakFinder、MACS、SISSRs、QuEST、PeakSeq、FindPeaks、CISGenome、W-ChIPeaks。

### 基本原理

扫描整个基因组，在给定的基因组区间范围内统计短序列片段数，然后根据对照组或者给定的最低富集标准，最终得出富集峰（由测序标签(tags)形成的富集区，通过延伸或还原的方法得到富集峰）。

### 问题

* 如何通过标签序列的富集区域确定真实的蛋白质结合位点？
* 如何区分真实的结合位点和随机的背景噪声？
  →对照组实验的数据/计算模型模拟背景数据的分布；

### Peak Calling算法比较

##### 基于窗口扫描方法（MACS，SISSRs，CisGenome，spp）

选定固定大小的窗口区域，计算区域中的标签序列数，超过设定富集程度阈值的即为后补富集区，两者间距离小于设定值则合并为一个富集区。

##### 标签富集法（PeakFinder，PeakSeq，FindPeaks，GLITR）

计算序列片段之间的重叠区大小，确定最大的重叠区为富集区域。

##### 核密度估计法（QuEST，F-Seq，CSDeconv）

不设定窗口，直接对富集密度进行评估，超过设定富集程度阈值的即为候补富集区，两者间距离小于设定值则合并为一个富集区。