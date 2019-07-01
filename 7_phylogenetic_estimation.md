# Gblocks not needed - I just removed the DLOOP and 3 other sections identified by eye
I decided to include other pipid mtDNAs and do the analysis only with mtDNA genomes, and also another analysis with the partial data from Chad fishbergi and the partial data from the CalAcad specimen and also some sanger seqs from several other samples. I plan to do a BEAST and iqtree analysis for both datasets.

* BEAST
For the beast analysis I am using the date estimates from Feng et al., 2017 to calibrate the tree (all with a normal distribution) as follows:
Xenopodinae  mean 45.3  sd 5.7
Pipidae mean 117.6 sd 6.6
Pipoidea mean 159.4 sd 6.0

For beast I am on Info in this directory:
```
/2/scratch/ben/2019_ancient_macaque_mtDNA/beast/bin/Pipoidea_nobits_noDLOOP_noDELETE3_4
```
I am using a relaxed lognormal clock with a Yule tree prior, these calibration points, and the model of evolution selected using the BIC below (GTR+F+I+G4)

* IQTREE
I'm using IQ-tree to generate a ML phylogeny under the preferred model selected by IQ-tree.
on sharcnet in this directory: `/work/ben/iqtree-1.6.8-Linux/bin/Xenopus_mtDNA_genomez`
```
../../iqtree -s All_mtDNA_genomez_pipoids_noDLOOP_noDELETE3_withBITS.nxs -m TEST -nt 1 -pre All_mtDNA_genomez_pipoids_noDLOOP_noDELETE3_withBITS.nxs_

```
BIC choose this model:  GTR+F+I+G4 
```
../../iqtree -s All_mtDNA_genomez_pipoids_noDLOOP_noDELETE3_noBITS.nxs -m GTR+F+I+G4 -bb 1000

```
