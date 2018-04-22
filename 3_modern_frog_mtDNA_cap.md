# splitting unmapped bam file by perfect index match:
/mnt/scratch/ben_evans/ancient_frogz/180322/split_mapped_reads$ /home/mmeyer/perlscripts/solexa/filework/splitBAM.pl -byfile ../180322_indexcombs_desc.txt /mnt/ngs_data/180322_M02279_0234_000000000-BLB6T_LL_F2174_BN_G5570/Bustard/BWA/proc1/s_1_sequence_ancient_xenXL_MT.bam >splitting_stats.txt

indexes are here: /mnt/scratch/ben_evans/ancient_frogz/180403_modern_frogz_mt_caps/split_unmapped_reads/splitting_stats.txt

# three reference genomes:
XT: /mnt/scratch/ben_evans/ancient_frogz/xenTr_MT.fasta
XL: /mnt/scratch/ben_evans/ancient_frogz/xenXL_MT.fasta
XB: /mnt/scratch/ben_evans/ancient_frogz/xenXB_MT.fasta

# map 
bwa bam2bam -n 0.01 -o 2 -l 16500 -g /mnt/scratch/ben_evans/ancient_frogz/xenTr_MT.fasta -f R7931_mapped_to_XT.bam R7931.bam

# sort
samtools sort R7931_mapped_to_XT.bam -o R7931_mapped_to_XT_sorted.bam

# index
samtools index R7931_mapped_to_XT_sorted.bam

# Remove unmapped, non-merged, filter-flagged sequences, remove duplicates, create summary statistic
/home/mmeyer/perlscripts/solexa/analysis/analyzeBAM.pl -qual 25 -paired R7931_mapped_to_XT_sorted.bam 

# consensus caller
/home/mmeyer/perlscripts/solexa/analysis/consensus_from_bam.pl -ref /mnt/scratch/ben_evans/ancient_frogz/xenTr_MT.fasta R7931_mapped_to_XT_sorted.uniq.L35MQ25.bam


This was sufficient using Matthias' scripts:
* bwa bam2bam -n 0.01 -o 2 -l 16500 -g /mnt/scratch/ben_evans/ancient_frogz/xenXL_MT.fasta -f R7935_mapped_to_XL.bam R7935.bam
* samtools sort R7935_mapped_to_XL.bam -o R7935_mapped_to_XL_sorted.bam
* /home/mmeyer/perlscripts/solexa/analysis/analyzeBAM.pl -qual 25 -paired R7935_mapped_to_XL_sorted.bam 
* /home/mmeyer/perlscripts/solexa/analysis/consensus_from_bam.pl -ref /mnt/scratch/ben_evans/ancient_frogz/xenXL_MT.fasta R7935_mapped_to_XL_sorted.uniq.L35MQ25.bam
