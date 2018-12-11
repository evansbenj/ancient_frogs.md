# Gblocks
I decided to remove the other pipid mtDNAs and do the analysis only with mtDNA genomes from genus Xenopus and also the partial data from Chad fishbergi and the partial data from the CalAcad specimen.

I'm using IQ-tree to generate a ML phylogeny under the preferred model selected by IQ-tree.

on sharcnet in this directory: `/work/ben/iqtree-1.6.8-Linux/bin/Xenopus_mtDNA_genomez`
```
../iqtree -s All_mtDNA_genomez_new_and_Gb_align4.nexus_only_genus_Xenopus.align_GBLOCKS.nxs -m TEST -nt 1 -pre All_mtDNA_genomez_new_and_Gb_align4.nexus_only_genus_Xenopus.align_GBLOCKS.nxs_
```
```
../iqtree -s All_mtDNA_genomez_new_and_Gb_align4.nexus_only_genus_Xenopus.align_GBLOCKS.nxs -m GTR+F+I+G4 -bb 1000```