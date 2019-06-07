```
bwa index kobeli_R7957_mtDNA_denovo.fasta 

bwa bam2bam -n 0.01 -o 2 -l 17811 -g kobeli_R7957_mtDNA_denovo.fasta -f R7957_mapped_kobeli_denovo.bam R7957.bam

samtools sort R7957_mapped_kobeli_denovo.bam -o R7957_mapped_kobeli_denovo_sorted.bam

/home/mmeyer/perlscripts/solexa/analysis/analyzeBAM.pl -qual 25 -paired R7957_mapped_kobeli_denovo_sorted.bam

/home/mmeyer/perlscripts/solexa/analysis/consensus_from_bam.pl -ref kobeli_R7957_mtDNA_denovo.fasta -coverage 3 R7957_mapped_kobeli_denovo_sorted.uniq.L35MQ25.bam
```
