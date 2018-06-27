#map
bwa bam2bam -n 0.01 -o 2 -l 16500 -g /mnt/scratch/ben_evans/ancient_macaques/Mfasc_Mauritus_mtDNA_genome.fasta -f A11272_extractedReads-Cercopithecidae_mapped_to_MfascMAURITUS.bam A11272_extractedReads-Cercopithecidae.bam

#sort
samtools sort A11272_extractedReads-Cercopithecidae_mapped_to_MfascMAURITUS.bam -o A11272_extractedReads-Cercopithecidae_mapped_to_MfascMAURITUS_sorted.bam

#Remove unmapped, non-merged, filter-flagged sequences, remove duplicates, create summary statistic
/home/mmeyer/perlscripts/solexa/analysis/analyzeBAM.pl -qual 25 -paired A11272_extractedReads-Cercopithecidae_mapped_to_MfascMAURITUS_sorted.bam

#consensus caller
/home/mmeyer/perlscripts/solexa/analysis/consensus_from_bam.pl -ref /mnt/scratch/ben_evans/ancient_macaques/Mfasc_Mauritus_mtDNA_genome.fasta A11272_extractedReads-Cercopithecidae_mapped_to_MfascMAURITUS_sorted.uniq.L35MQ25.bam

