# nuclear capture data

# Summary of workflow for nuclear capture data

For modern frogs (and ancient ones) I did a denovo assembly in order to separately assemble duplicated reads.  THis worked well for the modern frogs but not (I think) for the ancient ones.  For the ancient ones I am going to map reads directly to the fishbergi reference.

For RAG2. the fishbergi reference is here:
```
/mnt/scratch/ben_evans/ancient_frogz/modernFrogs_nuclear_caps_180322_D00829_0128_lane3/R7935_fisch_RAG2.fasta
```
Need to make ones for RAG1alphabeta and DMRT1alphabeta...


# make specific folder for each mtDNA genome
```
mkdir /mnt/scratch/ben_evans/ancient_frogz/180322_D00829_0128_lane3_modernFrogs_nuclear_caps/split_unmapped_reads/R7937
```
# go to specific folder for each mtDNA genome
```
cd /mnt/scratch/ben_evans/ancient_frogz/180322_D00829_0128_lane3_modernFrogs_nuclear_caps/split_unmapped_reads/R7937
```
# convert capture bam to fastq
```
samtools bam2fq ../R7937.bam > R7937.fastq
```
# make two paired fastq files
```
cat R7937.fastq | grep '^@.*/1$' -A 3 --no-group-separator > R7937_r1.fastq
cat R7937.fastq | grep '^@.*/2$' -A 3 --no-group-separator > R7937_nuclear_cap_r2.fastq
```
# get the singletons also
```
cat R7937.fastq | grep '^@.*[0-9][0-9]$' -A 3 --no-group-separator > R7937_singletons.fastq
```
# fix quality score ( ']' indicates bases that are merged in both directions)
```
sed -i -e 's/]/I/g' R7937_singletons.fastq
```
# add '/1' at end of header (this is required by trinity)
```
sed -i '/@/ s_$_/1_' R7937_singletons.fastq
```
*****
# concatenate the forward paired reads and merged reads
```
cat R7937_r1.fastq R7937_singletons.fastq > R7937_nuclear_cap_r1.fastq
```
# start screen
```
screen -S trinnuc_R7937
```
# assemble 
```
/mnt/expressions/ben_evans/bin/trinityrnaseq/Trinity --seqType fq --left R7937_nuclear_cap_r1.fastq --right R7937_nuclear_cap_r2.fastq --no_normalize_reads --max_memory 10G --KMER_SIZE 29
```
******
make blastdb
```
makeblastdb -in Trinity.fasta -dbtype nucl -out Trinity.fasta_blastable
```
```
blastn -query Trinity.fasta -db /mnt/scratch/ben_evans/ancient_frogz/XL_blastable_genome -out Trinity_blast.out -outfmt 6
```
```
perl -ne 'if(/^>(\S+)/){$c=$i{$1}}$c?print:chomp;$i{$_}=1 if @ARGV' ids.file Trinity.fasta > mtDNA.fasta
```
```
awk -v seq="42732422 10004 289064 42567259-,...,42597465-" -v RS='>' '$1 == seq {print RS $0}' /net/infofile4-inside/volume1/scratch/ben/2016_Tympa_and_Octomys_WGS/AO245_WGS/abyss_genome_assembly/AO245_newtrim_scaffolds.fa
```

This nuclear capture is now on graham here:
```
/home/ben/projects/rrg-ben/ben/ancient_frogz/modernFrogs_nuclear_caps_180322_D00829_0128_lane3
```

# 2020 Capture Update
We also did a capture of DMRT1, scanw, ccdc, sox3 and AR.  8916 probes, 2 bp tiling, company was Genscript.

Data are here (on graham):
```
/home/ben/projects/def-ben/ben/2020_Frog_Capture/200605_D00829_0342_lane1/Final_Sequences
```

Did assembly in Germany

make blast db
```
makeblastdb -in Trinity.fasta -dbtype nucl -out Trinity.fasta_blastable
```
Then, for each library, go to the folder:
```
cd ../../R7932/trinity_out_dir/
```
blast the capture seq
```
blastn -query /mnt/scratch/ben_evans/2020_Frog_Capture/Final_Sequences/XT_SOX3.fasta -db Trinity.fasta_blastable -out XT_SOX3.out -outfmt 6
```
get the ids of the matching assemblies
```
cut -f 2 XT_SOX3.out > ids_SOX3.file
```
or with a 300 bp cutoff for a match:
```
awk '$4 > 300' XL_scanw_ex1.out | cut -f 2 > ids_exon1.file
```

get the seqs
```
perl -ne 'if(/^>(\S+)/){$c=$i{$1}}$c?print:chomp;$i{$_}=1 if @ARGV' ids_SOX3.file Trinity.fasta > R7931_SOX3.fasta
```
replace the names
```
sed -i -e 's/>/>R7931_SOX3_/g' R7931_SOX3.fasta
```
concatenate the seqs
```
cat XL_ccdc69w_ex1_and_ex2.fasta R*/trin*/*ccdc_exon1_exon2.fasta > All_ccdc_exon1_and_exon2.fasta
```
and align wit mafft --adjustdirectionaccurately

After making the aligned fasta file with mafft, it may be useful to delete everything after a square bracket from the trinity output:
```
sed -e 's/\[[^][]*//g' All_SOX3_gt300bp_align.fasta_mm > All_SOX3_gt300bp_align.fasta_mmm
```
