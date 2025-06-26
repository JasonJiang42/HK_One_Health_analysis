# One Health genomic analysis of CTX-M-producing _E. coli_ #
This repository outlines the analysis pipeline for the paper *S. Jiang. et al. Cross-sectoral sharing of CTX-M-producing Escherichia coli: A One Health analysis to understand dissemination modes （in review)*

## Genome assembly and QC assessment ##
High-quality reads were assembled using SPAdes (https://github.com/ablab/spades)  
```
python spades.py --pe1-1 file1 --pe1-2 file2 -o assmebly --careful -k 21,33,55,77,99,127
```
Species confirmation by GTDB-Tk (https://github.com/Ecogenomics/GTDBTk)  
```
gtdbtk classify_wf --genome_dir genomes --out_dir gtdbtk/classify --cpus 10 --skip_ani_screen
```
QC assessment by checkM (https://github.com/Ecogenomics/CheckM)  
```
checkm lineage_wf -x fasta input_bins output_folder
```
## Genome annotation and population genomics ##  
Antibiotic resistance genes (ARGs) identification using AMRFinderPlus (https://github.com/ncbi/amr)
```
amrfinder -n seq.fna --organism Escherichia
```
The lineages were assigned by PopPUNK (https://poppunk.readthedocs.io/en/latest/index.html)
```
poppunk --create-db --output EC_database --r-files list.txt --threads 8
poppunk --fit-model lineages --ref-db EC --ranks 1,2,3
poppunk_visualise --ref-db EC --cytoscape --network-file EC/EC_graph.gt
```
## Phylogenetic analysis ##
core genome alignment was generated using snippy (https://github.com/tseemann/snippy), and recombination sites were removed with Gubbins (https://github.com/nickjcroucher/gubbins). A maximum-likelihood phylogenetic tree was then constructed using IQ-TREE (http://www.iqtree.org/) based on clean core genome SNP alignments.
```
snippy --outdir mut1 --ref ref.gbk --ctgs mut1.fasta
run_gubbins.py -p gubbins clean.full.aln
snp-sites -c gubbins.filtered_polymorphic_sites.fasta > clean.core.aln
iqtree -s clean.core.aln --boot-trees --wbtl -m GTR+I+G -B 1000 -nt 18
```
## Source prediction using DAPC ##
Call the core SNPs   
```
snippy-core --ref ref.gbk s1.fna s2.fna ...   
```
Discriminant Analysis of Principal Components (DAPC) analysis
```
if (!requireNamespace("vcfR", quietly = TRUE)) install.packages("vcfR")
if (!requireNamespace("adegenet", quietly = TRUE)) install.packages("adegenet")
if (!requireNamespace("ggplot2", quietly = TRUE)) install.packages("ggplot2")

library(vcfR)
library(adegenet)
library(ggplot2)

train_vcf_file <- "train_population_data.vcf" #The train list for source prediction was uploaded in the repository
supplementary_vcf_file <- "HK_individuals.vcf" 
train_vcf <- read.vcfR(train_vcf_file)
supplementary_vcf <- read.vcfR(supplementary_vcf_file)
train_genlight <- vcfR2genlight(train_vcf)
predict_genlight <- vcfR2genlight(supplementary_vcf)

dapc <- dapc(train_genlight, grp$grp)
pred.sup <- predict.dapc(dapc, newdata=predict_genlight)
predict_coords <- pred.sup$ind.scores
```
## Mobile genetic elements identification ##   
PLASMe is used to identify plasmid contigs (https://github.com/HubertTang/PLASMe)
```


