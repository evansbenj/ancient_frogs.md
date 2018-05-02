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
