# WorkNotes
My Daily Notes | 记录学习日常

## CRISPR部分

**在草图序列中找出CRISPR后，判断是否完整（有无截断）**

1.预测CRISPR,顺带截取前后两端100bp
```
for input in `ls *.fasta`
do
CRISPRDetect.pl -f "$input" -o out/"$input".crispr -check_direction 1 -array_quality_score_cutoff 0 -left_flank_length 100 -right_flank_length 100
done
```
  结果示例
```
# Array family : I-E [Matched known repeat from this family],   

  
 
  
    Position      Repeat     %id  Spacer  Repeat_Sequence                 Spacer_Sequence                         Insertion/Deletion
  ==========      ======  ======  ======  =============================   ================================        ==================
     1104349          29   100.0      32  .............................   TTGTGCGAACCTCAGTTATTGTGCCCTGACGA        
     1104410          29   100.0      32  .............................   AATTTATTCGGTTTATTCCGTCGGCGCGCGAC        
     1104471          29   100.0      32  .............................   GGGATTTGCACTCCAGTTGATTGACCCGCAGC        
     1104532          29   100.0      32  .............................   CCGCCGTCGAGTGTAATACCCTGATCCATCGC        
     1104593          29   100.0      32  .............................   CAGGATTGAAATACAGCCTCGGCCTGGGACAG        
     1104654          29   100.0      32  .............................   AGGCGAGGGAGATCAGAGATCAGGCTATTACA        
     1104715          29   100.0      32  .............................   AGATCGGCGGCCAGTGGATAACCGTTATCCCC        
     1104776          29   100.0      32  .............................   GTGTGGTAATTGGTGGTCTGGCTGGTGGTTTA        
     1104837          29   100.0      32  .............................   AGAATCTGGACACTGCATATACCGCAATCCGT        
     1104898          29   100.0      32  .............................   CCAGCCGTTCAGTATTGCCGGTGTCAGCAAAA        
     1104959          29   100.0      32  .............................   GAAAAGCTACTTTTGTGTTCAACTGATGCATT        
     1105020          29   100.0      31  .............................   CCGCGCAAATCCAGCGAGCCGCCGACGCTCA         
     1105080          29   100.0      32  .............................   TTGCAAACCGTGGCAAACGCAATTAACAAAAA        
     1105141          29   100.0      32  .............................   ATTGTTATAATTATTTATTGAAATATCATTCC        
     1105202          29   100.0      32  .............................   AATCTATTGTGAATTTGAAATGGTCCAGCACT        
     1105263          29   100.0      32  .............................   GGTAAAAACACGGTCTGAACCGACATTCATGT        
     1105324          29    93.1       0  ...........AT................   |                                       
  ==========      ======  ======  ======  =============================   ================================        ==================
          17          29    99.6      32  GTGTTCCCCGCGCCAGCGGGGATAAACCG                                                  
  
  # Left flank :   ATAAGTTATCCGTTCTTTAAAAATAAGGAAATGTTTTAATTTAGTTGGTAGATTGTTGATGCGGAATAAATTTGTTTAAAAACAGTTATGTATGCTTAGT
  # Right flank :  GGCGCACTGGATGCGATGATGGATATCACTTAGAATTCCCCGCCCCTGCGGTAGAACTCCCAGCTCCCATTTTCAAACCCATCAAGACGCCTTCGCCAGC
```
2.使用awk截取每个菌株CRISPR结果中的两端100bp
```
for input in `ls *.fasta`
do
awk '/^# .*?t flank/ { print FILENAME,$0}' $input >> start_end
done
```
  结果示例
```
ST003.fasta # Left flank :   ATAAGTTAGACGTTCTTTAAAAATAAGGAAATGTTTGAATTTAGTTGGTAGATTGTTGATGTGGAATAAATTTGTTTAAAAACAGATATGTATGCTTAGT
ST003.fasta # Right flank :  GGCGCACTGGATGCGATGATGGATATCACTTAGAATTCCCCGCCCTTGCGGTAGAACTCCCAGCTCCCATTTTCAAACCCATCAAGACGCCTTCGCCAGC
ST003.fasta # Left flank :   GCTCTTTAACATAATGGATGTGTTGTTTGTGTGATACTATAAAGTTGGTAGATTGTGACTGGCTTAAAAAATCATTAATTAATAATAGGTTATGTTTACA
ST003.fasta # Right flank :  CCATATAACCCGTTATCTCTTTCTCAAGTTTTTATATTAGCAGTACTTGTAATAAGCAACATATCCACGTAACACCTCATGTTCAAAATAGTTCTCCATG
ST005.fasta # Left flank :   GCTCTTTAACATAATGGATGTGTTGTTTGTGTGATACTATAAAGTTGGTAGATTGTGACTGGCTTAAAAAATCATTAATTAATAATAGGTTATGTTTACA
ST005.fasta # Right flank :  CCATATAACCCGTTATCTCTTTCTCAAGTTTTTATATTAGCAGTACTTGTAATAAGCAACATATCCACGTAACACCTCATGTTCAAAATAGTTCTCCATG
```
3.然后批处理后，直接看该文件flank是否完整，相对提高效率

待续

----------------------------------------------------------------------------------------------------------------------------------------
2020.08.14
1.将不足100bp的问题菌株挑出来，CRISPR结果搞到一个文件里 
```
for f in ST1027.fasta ST1032.fasta ST1049.fasta ST1061.fasta ST1063.fasta ST1070.fasta ST1081.fasta ST242.fasta ST270.fasta ST358.fasta ST364.fasta ST448.fasta ST477.fasta ST696.fasta ST701.fasta ST704.fasta ST705.fasta ST710.fasta ST713.fasta ST714.fasta ST715.fasta ST716.fasta ST721.fasta ST722.fasta ST723.fasta ST727.fasta ST728.fasta ST730.fasta ST731.fasta ST735.fasta ST737.fasta ST738.fasta ST739.fasta ST740.fasta ST745.fasta ST746.fasta ST747.fasta ST748.fasta ST749.fasta ST751.fasta ST752.fasta ST753.fasta ST754.fasta ST759.fasta ST761.fasta ST762.fasta ST763.fasta ST764.fasta ST767.fasta ST768.fasta ST769.fasta ST807.fasta ST810.fasta ST817.fasta ST826.fasta ST828.fasta ST829.fasta ST831.fasta ST847.fasta ST973.fasta
do
echo "Filename: $f" >>wenti; cat "$f" >>wenti
done
```
上方命令目的：因结果内容没有文件名（菌株名），盲目合并，分不清属于哪个菌株。想把问题菌株名和内容都导入一个文件。  
**注：问题菌株名可以使用awk命令，运用匹配字符个数或其他操作提出来?，然后再引入for循环,比手敲强。**   

2. Notepad 一个个核查，遇到不确定的，可以使用其他拼接策略（spades）重新拼一遍clean data，往往有机会把被截断的序列拼出来，似乎直接用reads结合质量值更好，但得琢磨几天，duck不必。

----------------------------------------------------------------------------------------------------------------------------------------
2020.08.19
更新台湾女诗人夏宇的一首小诗

**我只不过为了储存足够的爱  
足够的温柔和狡猾  
以防 万一  
醒来就遇见你**

**我只不过为了储存足够的骄傲  
足够的孤独和冷漠  
以防 万一  
醒来你已离去**


## 统计测序深度

### 一 将cleandata（bam）比对到组装后的序列(fasta)上去，生产比对后的bam文件  
(fastq格式或二代测序查文档前半部分，有比对和排序的操作)  
#pbmm2 align 参考序列 去除低质量值后的cleandata 输出文件  
pbmm2 align ST1024.fasta cleandata.bam ST1024_align.bam  
### 二 排序   
#@ 10为10个线程  
samtools sort -o ST1024_align_sorted.bam ST1024_align.bam -@ 10  
### 三 统计每个位点的测序深度  
samtools depth ST1024_align_sorted.bam -a > st1024.depth  
### 四 平均测序深度  
awk ‘{x+=$3} END {print x/NR}’ st1024.depth  
注：* samtools stats xx.bam可以对比对后的bam进行全面的统计。（像map了百分之多少，等） 
    *  BAMStas软件  
       BAMStats是一款从BAM文件中计算覆盖度和其他数据，并生成这些数据的描述性统计的工具（很多漂亮图片）  


