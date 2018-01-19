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
