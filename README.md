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
