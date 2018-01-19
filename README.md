# ancient_frogs

To extract a bam file from each index do this:
```
samtools view -r G9087 -b ancientfrogs_to_xtr_mtDNA.bam -o ancientfrogs_to_xtr_mtDNA_G9087.bam
samtools view -r G9088 -b ancientfrogs_to_xtr_mtDNA.bam -o ancientfrogs_to_xtr_mtDNA_G9088.bam
samtools view -r G9089 -b ancientfrogs_to_xtr_mtDNA.bam -o ancientfrogs_to_xtr_mtDNA_G9089.bam
samtools view -r G9090 -b ancientfrogs_to_xtr_mtDNA.bam -o ancientfrogs_to_xtr_mtDNA_G9090.bam
samtools view -r G9091 -b ancientfrogs_to_xtr_mtDNA.bam -o ancientfrogs_to_xtr_mtDNA_G9091.bam
samtools view -r G9092 -b ancientfrogs_to_xtr_mtDNA.bam -o ancientfrogs_to_xtr_mtDNA_G9092.bam
samtools view -r G9093 -b ancientfrogs_to_xtr_mtDNA.bam -o ancientfrogs_to_xtr_mtDNA_G9093.bam
samtools view -r G9094 -b ancientfrogs_to_xtr_mtDNA.bam -o ancientfrogs_to_xtr_mtDNA_G9094.bam
samtools view -r G9095 -b ancientfrogs_to_xtr_mtDNA.bam -o ancientfrogs_to_xtr_mtDNA_G9095.bam
samtools view -r G9096 -b ancientfrogs_to_xtr_mtDNA.bam -o ancientfrogs_to_xtr_mtDNA_G9096.bam
samtools view -r G9097 -b ancientfrogs_to_xtr_mtDNA.bam -o ancientfrogs_to_xtr_mtDNA_G9097.bam
samtools view -r G9098 -b ancientfrogs_to_xtr_mtDNA.bam -o ancientfrogs_to_xtr_mtDNA_G9098.bam

```

Notes from Matthias:
The processed raw data from 3 ancient samples lives in
/mnt/ngs_data/171204_D00829_0097_BH2M3JBCX2_R_PEdi_G9106_G8532/Bustard/Final_Sequences/proc1/frog_libs
and fpr modern samples:
/home/mmeyer/solexa_data/180109_D00829_0110.lane1_modern_frogs_WGS_repeat/

The mapper:
http://bioaps01:16415/

The mapped museum frogs live in
/mnt/ngs_data/171204_D00829_0097_BH2M3JBCX2_R_PEdi_G9106_G8532/Bustard/BWA/proc1/frog_libs_xenTr9.bam

Split the sequences by readgroup
/home/mmeyer/perlscripts/solexa/filework/splitBAM.pl -byfile ../../171204_D00829_0097_lane1_indexcombs_desc.txt /mnt/ngs_data/171204_D00829_0097_BH2M3JBCX2_R_PEdi_G9106_G8532/Bustard/BWA/proc1/frog_libs_xenTr9.bam >splitting_Stats.txt

Filter sequences and produce summary statistics
/home/mmeyer/perlscripts/solexa/analysis/analyzeBAM.pl ../split/*.bam

Look for DNA damage
/home/mmeyer/perlscripts/solexa/analysis/substitution_patterns.pl ../*.bam

Compute size distribution plots
/home/mmeyer/perlscripts/solexa/analysis/size_distribution.pl ../split/*.bam

Index reference genome
bwa index FASTA

Do the mapping
bwa bam2bam -n 0.01 -o 2 -l 16500 -g INDEXEDREFERENCE -f OUTFILE

Sort the file, ....
