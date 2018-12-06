# Summary of work flow for mtDNA

For all individuals I first attempted a de novo assembly after extracting paired and merged reads and replacing the quality scores of the merged portion of merged reads with an 'I' which indicates the highest quality.  For almost all modern individuals I was able to obtain a complete mtDNA assembly that was around 17kb.  For these individuals I used this assembly in the alignment and phylogenetic reconstruction.  There were 5 individuals that made an concatenated mtDNA assembly (trop, mellotrop, victo, laevis, wittei), one with an incomplete assembly (poweri) and one individual (cfboum) that did not assemble.  For the ones that made a concatenated assembly, I re-assembled using trinity with kmer = 21 or 32 (the max).  For vict and mello the 21mer provided a non-concatenated assembly that was then used in the analysis and the same for laevis and wittei with a kmer = 32.  For poweri, the assembly was fragmented into 4 contigs what were independently aligned with the others using mafft with -adjustdirection to deal with revcomp assemblies.  The poweri sequence was then concatenated manually into a complete genome.  For cfboum, the assembly only made a small contig.  Instead the boumbaensis sequence was used as a reference and a consensus was called using unique reads after deduping first using Matthias scripts: /analyzeBAM.pl and concatenate_bam.pl as detailed below.

# splitting unmapped bam file by perfect index match:
```
/mnt/scratch/ben_evans/ancient_frogz/180322/split_mapped_reads$ /home/mmeyer/perlscripts/solexa/filework/splitBAM.pl -byfile ../180322_indexcombs_desc.txt /mnt/ngs_data/180322_M02279_0234_000000000-BLB6T_LL_F2174_BN_G5570/Bustard/BWA/proc1/s_1_sequence_ancient_xenXL_MT.bam >splitting_stats.txt
```
indexes are here: /mnt/scratch/ben_evans/ancient_frogz/180403_modern_frogz_mt_caps/split_unmapped_reads/splitting_stats.txt

# three reference genomes:
```
XT: /mnt/scratch/ben_evans/ancient_frogz/xenTr_MT.fasta
```
```
XL: /mnt/scratch/ben_evans/ancient_frogz/xenXL_MT.fasta
```
```
XB: /mnt/scratch/ben_evans/ancient_frogz/xenXB_MT.fasta
```
# map 
```
bwa bam2bam -n 0.01 -o 2 -l 16500 -g /mnt/scratch/ben_evans/ancient_frogz/xenTr_MT.fasta -f R7931_mapped_to_XT.bam R7931.bam
```
# sort
```
samtools sort R7931_mapped_to_XT.bam -o R7931_mapped_to_XT_sorted.bam
```
# rmdups using circular option
```
/home/public/user/Johann/biohazard-tools/bam-rmdup -c --circular=fischbergi_R7938_mtDNA:17189 G9092_mapped_to_fischbergi_sorted.bam -o R7931_mapped_to_XT_sorted_circular.bam
```
# Remove unmapped, non-merged, filter-flagged sequences, create summary statistic but DO NOT remove duplicates
```
/home/mmeyer/perlscripts/solexa/analysis/analyzeBAM.pl -nodedup -qual 25 -paired R7931_mapped_to_XT_sorted_circular.bam 
```
# consensus caller
```
/home/mmeyer/perlscripts/solexa/analysis/consensus_from_bam.pl -ref /mnt/scratch/ben_evans/ancient_frogz/xenTr_MT.fasta R7931_mapped_to_XT_sorted_circular.L35MQ25.bam
```


# Doing a de novo assembly instead
# convert bam to fastq
```
samtools bam2fq R7935.bam > R7935.fastq
```
# make two paired fastq files
```
cat R7935.fastq | grep '^@.*/1$' -A 3 --no-group-separator > R7935_r1.fastq
cat R7935.fastq | grep '^@.*/2$' -A 3 --no-group-separator > R7935_r2.fastq
```
# get the singletons also
```
cat R7935.fastq | grep '^@.*[0-9][0-9]$' -A 3 --no-group-separator > R7935_singletons.fastq

```
# fix quality score (']' indicates bases that are merged in both directions)
```
sed -i -e 's/]/I/g' R7935_singletons.fastq
```
# add '/1' at end of header (this is required by trinity)
```
awk '/^@M02279/ {$0=$0"/1"} 1' G10631_singletons.fastq > newfile.fq
```
this does not work:
```
sed -i '/>/ s_$_/1_' R7935_singletons.fastq
```

# repair paired fastq
```
/mnt/expressions/ben_evans/bin/bbmap_old/bbmap/repair.sh -Xmx30g in1=R7935_r1.fastq in2=R7935_r2.fastq out1=R7935_r1.corfixed.fastq out2=R7935_r2.corfixed.fastq outsingle=R7935singletons.fastq
```
# concatenate the singleton reads with the r2
```
cat R7935_r2.corfixed.fastq R7935_singletons.fastq > R7935_r2.corfixed_and_singetons.fastq
```
# assemble 
```
/mnt/expressions/ben_evans/bin/trinityrnaseq/Trinity --seqType fq --left R7935_r1.corfixed.fastq --right R7935_r2.corfixed_and_singetons.fastq --no_normalize_reads --max_memory 10G --KMER_SIZE 29
```
# check which scaffolds match with blast:
```
blastn -query Trinity.fasta -db /mnt/scratch/ben_evans/ancient_frogz/XL_blastable_genome -out Trinity_blast.out -outfmt 6
```
# get names of scaffolds
```
cut -f 1 Trinity_blast.out > ids.file
```

# pull out the mtDNA seq

Take the partial header name out of Trinity_blast.out and make a file called 'ids.file' with this in it. Then type this:
```
perl -ne 'if(/^>(\S+)/){$c=$i{$1}}$c?print:chomp;$i{$_}=1 if @ARGV' ids.file Trinity.fasta > mtDNA.fasta
```
# align
```
mafft --adjustdirection input > output
```

# extract region of concatenated assembly
index the fasta file
```
samtools faidx victorianus_R7945_mtDNA.fasta
```
extract a portion:
```
samtools faidx victorianus_R7945_mtDNA.fasta victorianus_R7945_mtDNA:0-17729 > victorianus1_R7945_mtDNA.fasta
```
