# Mapping and coverage

* working directory on bionc
```
/mnt/scratch/ben_evans/ancient_frogz/modern_frogz
```

* convert bam to paired fastq
```
samtools bam2fq R7931.bam > R7931.fastq
```
* split into for and rev files
```
cat R7931.fastq | grep '^@.*/1$' -A 3 --no-group-separator > R7931_r1.fastq
```
```
cat R7931.fastq | grep '^@.*/2$' -A 3 --no-group-separator > R7931_r2.fastq
```
* replair pairs
```
/mnt/expressions/ben_evans/bin/bbmap_old/bbmap/repair.sh -Xmx30g in1=R7931_r1.fastq in2=R7931_r2.fastq out1=R7931_r1.corfixed.fastq out2=R7931_r2.corfixed.fastq outsingle=R7931singletons.fastq
```

* map the reads to the mt genome
```
/mnt/expressions/ben_evans/bin/bwa/bwa mem ../xenTr_MT.fasta R7931_r1.corfixed.fastq R7931_r2.corfixed.fastq | /mnt/expressions/ben_evans/bin/samtools/samtools view -Shu - | /mnt/expressions/ben_evans/bin/samtools/samtools sort - -o AMNH17274_to_XT_mtDNAsorted.bam
```

* check coverage
```
/mnt/expressions/ben_evans/bin/samtools/samtools depth AMNH17274_to_XT_mtDNAsorted.bam -a |  awk '{sum+=$3} END { print "Average = ",sum/NR}'
```


# Stampy

I coudn't get the indexing steps to work on bionc so I had to do them on info like this:
```
scl enable python27 bash
```
```
/usr/local/stampy/stampy.py -G xenXB_MT xenXB_MT.fasta
```
```
/usr/local/stampy/stampy.py -g xenXB_MT -H xenXB_MT
```
Then I transferred the files to bionc and ran stampy there like this:
```
/mnt/expressions/ben_evans/bin/stampy-1.0.32/stampy.py -g xenXL_MT -h xenXL_MT --substitutionrate=0.05 -o ./modern_frogz/R7935_stampy_to_XLmtDNA.bam -M ./modern_frogz/R7935_r1.corfixed.fastq ./modern_frogz/R7935_r2.corfixed.fastq
```
and sorted the bam:
```
/mnt/expressions/ben_evans/bin/samtools/samtools sort ./modern_frogz/R7935_stampy_to_XBmtDNA.bam -o ./modern_frogz/R7935_stampy_to_XBmtDNA_sorted.bam
```
and checked coverage:
```
/mnt/expressions/ben_evans/bin/samtools/samtools depth ./modern_frogz/R7935_stampy_to_XLmtDNA_sorted.bam -a |  awk '{sum+=$3} END { print "Average = ",sum/NR}'
```
and count how many sites have no coverage at all
```
/mnt/expressions/ben_evans/bin/samtools/samtools depth R7947_stampy_to_XLmtDNA_sorted.bam -a |  grep -c -P '\t0'
```
Separate out mapped reads only:
```
samtools view -b -F4 R7953_stampy_to_XLmtDNA.bam > R7953_stampy_to_XLmtDNA_onlymapped.bam
```
and view them:
```
samtools view R7953_stampy_to_XLmtDNA_onlymapped.bam | more
```

# Genotyping

first add readgroups
```
java -jar /mnt/expressions/ben_evans/bin/picard/picard.jar AddOrReplaceReadGroups I=BMNH1947_2_24_78_stampy_to_XLmtDNA.bam O=BMNH1947_2_24_78_stampy_to_XLmtDNA_rg.bam RGID=BMNH1947_2_24_78 RGLB=BMNH1947_2_24_78 RGPL=illumina RGPU=BMNH1947_2_24_78 RGSM=BMNH1947_2_24_78
```
```
java -jar /mnt/expressions/ben_evans/bin/picard/picard.jar AddOrReplaceReadGroups I=BMNH1947_2_24_79_stampy_to_XLmtDNA.bam O=BMNH1947_2_24_79_stampy_to_XLmtDNA_rg.bam RGID=BMNH1947_2_24_79 RGLB=BMNH1947_2_24_79 RGPL=illumina RGPU=BMNH1947_2_24_79 RGSM=BMNH1947_2_24_79
```
```
java -jar /mnt/expressions/ben_evans/bin/picard/picard.jar AddOrReplaceReadGroups I=16294_stampy_to_XLmtDNA.bam O=16294_stampy_to_XLmtDNA_rg.bam RGID=16294 RGLB=16294 RGPL=illumina RGPU=16294 RGSM=16294
```
then sort
```
samtools sort BMNH1947_2_24_78_stampy_to_XLmtDNA_rg.bam -o BMNH1947_2_24_78_stampy_to_XLmtDNA_rg_sorted.bam
```
```
samtools sort BMNH1947_2_24_79_stampy_to_XLmtDNA_rg.bam -o BMNH1947_2_24_79_stampy_to_XLmtDNA_rg_sorted.bam
```
```
samtools sort 16294_stampy_to_XLmtDNA_rg.bam -o 16294_stampy_to_XLmtDNA_rg_sorted.bam
```

then index bam files
```
samtools index BMNH1947_2_24_78_stampy_to_XLmtDNA_rg_sorted.bam
```
```
samtools index BMNH1947_2_24_79_stampy_to_XLmtDNA_rg_sorted.bam
```
```
samtools index 16294_stampy_to_XLmtDNA_rg_sorted.bam
```

then genotype
```
java -Xmx8G -jar /mnt/expressions/ben_evans/bin/GenomeAnalysisTK-nightly-2017-10-07-g1994025/GenomeAnalysisTK.jar -T HaplotypeCaller -R ../xenXL_MT.fasta -I BMNH1947_2_24_78_stampy_to_XLmtDNA_rg_sorted.bam --emitRefConfidence GVCF -o BMNH1947_2_24_78_stampy_to_XLmtDNA_rg_sorted.bam.g.vcf.gz -out_mode EMIT_ALL_CONFIDENT_SITES
```
```
java -Xmx8G -jar /mnt/expressions/ben_evans/bin/GenomeAnalysisTK-nightly-2017-10-07-g1994025/GenomeAnalysisTK.jar -T HaplotypeCaller -R ../xenXL_MT.fasta -I BMNH1947_2_24_79_stampy_to_XLmtDNA_rg_sorted.bam --emitRefConfidence GVCF -o BMNH1947_2_24_79_stampy_to_XLmtDNA_rg_sorted.bam.g.vcf.gz -out_mode EMIT_ALL_CONFIDENT_SITES
```
```
java -Xmx8G -jar /mnt/expressions/ben_evans/bin/GenomeAnalysisTK-nightly-2017-10-07-g1994025/GenomeAnalysisTK.jar -T HaplotypeCaller -R ../xenXL_MT.fasta -I 16294_stampy_to_XLmtDNA_rg_sorted.bam --emitRefConfidence GVCF -o 16294_stampy_to_XLmtDNA_rg_sorted.bam.g.vcf.gz -out_mode EMIT_ALL_CONFIDENT_SITES
```

and concatenate
```
java -Xmx8G -cp /mnt/expressions/ben_evans/bin/GenomeAnalysisTK-nightly-2017-10-07-g1994025/GenomeAnalysisTK.jar org.broadinstitute.gatk.tools.CatVariants -R ../xenXL_MT.fasta -V BMNH1947_2_24_78_stampy_to_XLmtDNA_rg_sorted.bam.g.vcf.gz -V BMNH1947_2_24_79_stampy_to_XLmtDNA_rg_sorted.bam.g.vcf.gz -V 16294_stampy_to_XLmtDNA_rg_sorted.bam.g.vcf.gz -out ancientfrogz.g.vcf.gz -assumeSorted
```
