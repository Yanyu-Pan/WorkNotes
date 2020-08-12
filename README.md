# WorkNotes
My Daily Notes | 记录学习日常

## CRISPR部分

**在草图序列中找出CRISPR后，判断是否完整（有无截断）**

预测CRISPR,顺带截取前后两端100bp
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
使用awk截取每个菌株CRISPR结果中的两端100bp
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
然后批处理后，直接看该文件flank是否完整，相对提高效率

待续

