# Mining DMRT1 exons out of trinity assembly

I first made a fasta file for each of the 6 exons.  I did this for ST, XLalpha, and XLbeta. Using any one should be sufficient to identify any match, even for the dodecaploids.

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

# Making alignment ladder
Align with mafft (mafft -adjustdirection input.fasta > output.fasta). Now to organize the alignment, I wrote a script to sort the data with the column with data on the 5' most side first, and then with the most gaps listed on the bottom.  To do this, first linearize fasta files like this:

```
sed -e 's/\(^>.*$\)/#\1#/' All_DMRT1_exon6_align.fasta | tr -d "\r" | tr -d "\n" | sed -e 's/$/#/' | tr "#" "\n" | sed -e '/^$/d' > All_DMRT1_exon6_align_linear.fasta
```
And then run this script (Converts_fasta_alignment_to_slide.pl):
```
#!/usr/bin/env perl
use strict;
use warnings;


# This program reads in a nexus file and re-orders taxa so that they begin with the taxon
# with data in the beginning and end with taxa with data in the end.

# This is useful when you have a large mafft alignment with some sketchy sequences
# because one can then more easily make taxon sets 

# Run like this "Converts_fasta_alignment_to_slide.pl inputprefix"

# The output prefix will be: inputprefix_reorder.fasta

# need to linearize the fasta file first like this:
# sed -e 's/\(^>.*$\)/#\1#/' input.fasta | tr -d "\r" | tr -d "\n" | sed -e 's/$/#/' | tr "#" "\n" | sed -e '/^$/d' > input_linear.fasta

my $inputfile = $ARGV[0];

# open an outputfile
unless (open(OUTFILE, ">".$inputfile."_reorder.fasta"))  {
	print "I can\'t write to $inputfile._reorder.fasta\n";
	exit;
}
print "Creating output file: $inputfile._reorder.fasta\n";


unless (open DATAINPUT, "$inputfile.fasta") {
	print "Can not find the input file.\n";
	exit;
}

my $hashkey;
my %datahash;
my @temp;
# read in data into a hash
while ( my $line = <DATAINPUT>) {
	chomp($line);
	if($line =~ '>'){
		# this is a fasta header
		@temp = split(" ",$line);
		$hashkey = $temp[0];
	}
	else{
		$datahash{$hashkey}[0]=$line;
	}
}		
close DATAINPUT;

my $gapcounter;
my @array;
my %rankhash;
foreach my $key (sort(keys %datahash)) {
	if(defined($datahash{$key}[0])){
		#print $key,"\n",$datahash{$key}[0],"\n";
		@array=split("",$datahash{$key}[0]);
		# count the gaps in the beginning
		$gapcounter=0;
		foreach (@array){
			if($_ ne "-"){
				last;
			}
			else{
				$gapcounter+=1;
			}
		}
		$rankhash{$key}=$gapcounter;
	}	
}
# sort keys by value
my @keys = sort { $rankhash{$a} <=> $rankhash{$b} } keys %rankhash;

foreach (@keys) {
		if(defined($datahash{$_}[0])){
			print OUTFILE $_,"\n",$datahash{$_}[0],"\n";
		}	
}
close OUTFILE


```
