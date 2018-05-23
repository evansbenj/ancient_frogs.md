# Mining DMRT1 exons out of trinity assembly

I first made a fasta file for each of the 4 exons.  I did this for ST, XLalpha, and XLbeta. Using any one should be sufficient to identify any match, even for the dodecaploids.

# Change to the directory
```
cd ../../R7931/trinity_out_dir
```

# Make a blast database out of the trinity assembly
```
makeblastdb -dbtype nucl -in Trinity.fasta -out Trinity.fasta_blastable
```

# blast each of the exons against this blast db
```
blastn -query /mnt/scratch/ben_evans/ancient_frogz/180322_D00829_0128_lane3_modernFrogs_nuclear_caps/DMRT1_ST_exon1.fasta -db Trinity.fasta_blastable -out DMRT1_exon1.out -outfmt 6
```
# get the id of the assembled scaffold(s)
```
cut -f 2 DMRT1_exon1.out > ids_exon1.file
```
# get the sequence of the assembled scaffolds(s)
```
perl -ne 'if(/^>(\S+)/){$c=$i{$1}}$c?print:chomp;$i{$_}=1 if @ARGV' ids_exon1.file Trinity.fasta > R7931_DMRT1_exon1.fasta
```

# Change the header of the matching assembled scaffolds(s)
```
sed -i -e 's/>/>R7931_DMRT1_exon1_/g' R7931_DMRT1_exon1.fasta
```

# And repeat for all 4 exons

# Then cat and align DMRT1 exons, RAG1 and RAG2

```
cat RAG1_allquery.fasta spl*/R*/trin*/R*RAG1_exon1.fasta > All_RAG1.fasta
```
```
cat RAG2_allquery.fasta spl*/R*/trin*/R*RAG2_exon1.fasta > All_RAG2.fasta 
```
```
cat DMRT1_exon1_allquery.fasta spl*/R*/trin*/R*DMRT1_exon1.fasta > All_DMRT1_exon1.fasta
```
```
cat DMRT1_exon2_allquery.fasta spl*/R*/trin*/R*DMRT1_exon2.fasta > All_DMRT1_exon2.fasta
```
```
cat DMRT1_exon3_allquery.fasta spl*/R*/trin*/R*DMRT1_exon3.fasta > All_DMRT1_exon3.fasta
```
```
cat DMRT1_exon4_allquery.fasta spl*/R*/trin*/R*DMRT1_exon4.fasta > All_DMRT1_exon4.fasta
```
```
cat DMRT1_exon5_allquery.fasta spl*/R*/trin*/R*DMRT1_exon5.fasta > All_DMRT1_exon5.fasta
```
```
cat DMRT1_exon6_allquery.fasta spl*/R*/trin*/R*DMRT1_exon6.fasta > All_DMRT1_exon6.fasta
```

# Linearize fasta files
```
sed -e 's/\(^>.*$\)/#\1#/' All_DMRT1_exon6_align.fasta | tr -d "\r" | tr -d "\n" | sed -e 's/$/#/' | tr "#" "\n" | sed -e '/^$/d' > All_DMRT1_exon6_align_linear.fasta
```
