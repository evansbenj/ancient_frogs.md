*map
bwa bam2bam -n 0.01 -o 2 -l 16500 -g /mnt/scratch/ben_evans/ancient_frogz/ancient_frogz_nuclear_caps_180405/fishbergi_RAG1_alpha_and_beta.fasta -f A11272_extractedReads-Cercopithecidae_mapped_to_MfascMAURITUS.bam A11272_extractedReads-Cercopithecidae.bam

*sort
samtools sort A11272_extractedReads-Cercopithecidae_mapped_to_MfascMAURITUS.bam -o A11272_extractedReads-Cercopithecidae_mapped_to_MfascMAURITUS_sorted.bam


* for G9090, i accidentally deleted the bam file so I needed to work with fastq files I had generated for Trinity.  Here is what I did:
```
bwa aln /mnt/scratch/ben_evans/ancient_frogz/ancient_frogz_nuclear_caps_180405/fishbergi_RAG1_alpha_and_beta.fasta G9090_nuclear_cap_r1.fastq > G9090_mapped_to_fischbergiRAGalphabeta.sa1.sai 
bwa aln /mnt/scratch/ben_evans/ancient_frogz/ancient_frogz_nuclear_caps_180405/fishbergi_RAG1_alpha_and_beta.fasta G9090_nuclear_cap_r2.fastq > G9090_mapped_to_fischbergiRAGalphabeta.sa2.sai 
bwa sampe /mnt/scratch/ben_evans/ancient_frogz/ancient_frogz_nuclear_caps_180405/fishbergi_RAG1_alpha_and_beta.fasta G9090_mapped_to_fischbergiRAGalphabeta.sa1.sai G9090_mapped_to_fischbergiRAGalphabeta.sa2.sai G9090_nuclear_cap_r1.fastq G9090_nuclear_cap_r2.fastq > G9090_mapped_to_fischbergiRAGalphabeta.sam
samtools view -u G9090_mapped_to_fischbergiRAGalphabeta.sam | samtools sort -o G9090_mapped_to_fischbergiRAGalphabeta_sorted.bam
```


*Remove unmapped, non-merged, filter-flagged sequences, remove duplicates, create summary statistic
/home/mmeyer/perlscripts/solexa/analysis/analyzeBAM.pl -qual 25 -paired G9090_mapped_to_fischbergiRAGalphabeta_sorted.bam

*consensus caller
/home/mmeyer/perlscripts/solexa/analysis/consensus_from_bam.pl -ref /mnt/scratch/ben_evans/ancient_frogz/ancient_frogz_nuclear_caps_180405/fishbergi_RAG1_alpha_and_beta.fasta G9090_mapped_to_fischbergiRAGalphabeta_sorted.uniq.L35MQ25.bam

