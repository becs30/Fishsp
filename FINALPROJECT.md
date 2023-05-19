#  Fish diversity comparison in US, New Hampshire Rivers using metabarcoding

## Author: Becaye Gueye

### Backgroud 
In this study, we are interested in assessing diversity of fish species across three US, New Hampshire rivers. We used metabarcoding to assess diversity. It is a DNA sequencing technique that allows to sequence environmental samples and subsequently determine organisms present on the given site. For the purpose of this study, DNA sample collected was subject to amplification using the set of fish-universal primers, MiFish. They amplify hypervariable regions of the mitochondrial 12S ribosomal RNA and allow to differentiate between species (Yamamoto et al., 2016). Environmental samples were collected in Lamprey River, Oyster River and Squamscott River and sequenced in UNH’s genome center. After sequencing, we proceeded in comparing diversity across the rivers. Multiple samples, for replicates,  were taken on different dates and analyzed. The methodolgy used for the analysis is detailed in the next section.

### Methodology:

Our files were in fastq and were obtained from Dr Jeffrey Miller. We used VS code with the RON cluster. We activated two conda environments: Genomics and Qiime. We first started with trimming the poly G tail of the reads. Secondly, we imported the trimmed fastq with Qiime and used the ‘cutadapt’ program to get rid of the primers. We then denoised the data and performed the taxonomic assignment which permitted comparison of our data against a reference database (Xiong et al., 2022). We then used Qiime 2 pipeline to generate the data we will analyze in the next portion. One of the difficulties we have run into involve the Qiime 2 pipeline, it was not able to generate graphs for all the data especially the qza files. As explained by Xiong et al. due to certain limitations, softwares of this type are difficult to use for fish metabarcoding (2022). We declared in the background section that we sampled in 3 sites, for some reason that is still unknown to us, all the data we generated revealed sites data in 2 sites only: Oyster River and Squamscott River.

### Findings

#### Figure: 

<img width="1376" alt="Screen Shot 2023-05-19 at 4 49 15 AM" src="https://github.com/becs30/Fishsp/assets/130727397/52269abd-784b-4f9f-9b44-e7b1ef00a57a">
The figure represents a barplot detailing diversity in each taken sample

#### Analysis
The Squamscott River has more diverse fish population compared to the Oyster River if all samples are to be considered. But looking at the data closely, one can see there are a lot of unassigned species of fish in the Oyster River, and that hinders the quality of the analysis. We also note that the Cyprinodontiformes fish are the most abundant in Squamscott, with also a lot of unassigned fish. The amount of unassigned species of fish might be due to mutations in their mitochondrial 16S ribosomal RNA or they could be species never sequenced before. But relying solely on species we know their 16s Squamscott river has more fish diversity. 

### References
Xiong, Fan, et al. “Methodology for Fish Biodiversity Monitoring with Environmental DNA Metabarcoding: The Primers, Databases and Bioinformatic Pipelines.” Water Biology and Security., vol. 1, no. 1, 2022, https://doi.org/10.1016/j.watbs.2022.100007.

Yamamoto, Satoshi, et al. “Environmental DNA Metabarcoding Reveals Local Fish Communities in a Species-Rich Coastal Sea.” Scientific Reports., vol. 7, no. 1, 2017, https://doi.org/10.1038/srep40368.

### Project Reproducibility
Here are the codes used to generate this graph. 

#Setting up commands 
cp /tmp/gen711_project_data/fastp.sh ~/fastp.sh

chmod +x <path to github directory>/fastp.sh

#####trimming
./fastp.sh 150 /tmp/gen711_project_data/fish/fastqs  trimmed_fastq
 
##### Activating qiime
conda activate qiime2-2022.8
  
###### Importing file to qiime

qiime tools import \
 --type "SampleData[PairedEndSequencesWithQuality]"  \
  --input-format CasavaOneEightSingleLanePerSampleDirFmt \
   --input-path /home/users/bg1152/trimmed_fastq \
   --output-path /home/users/bg1152/output/fish.qza

###### Primers and adapters removal
qiime cutadapt trim-paired \
    --i-demultiplexed-sequences /home/users/bg1152/output/fish.qza \
    --p-cores 4 \
    --p-front-f GTCGGTAAAACTCGTGCCAGC \
    --p-front-r CATAGTGGGGTATCTAATCCCAGTTTG \
    --p-discard-untrimmed \
    --p-match-adapter-wildcards \
    --verbose \
    --o-trimmed-sequences /home/users/bg1152/output/fish_cutadapt.qza


qiime demux summarize \
   --i-data /home/users/bg1152/output/fish_cutadapt.qza \
   --o-visualization /home/users/bg1152/output/fish_demux.qzv


###### DENOISING

qiime dada2 denoise-paired \
    --i-demultiplexed-seqs /home/users/bg1152/output/fish_cutadapt.qza \
    --p-trunc-len-f  120 \
    --p-trunc-len-r 115 \
    --p-trim-left-f 0 \
    --p-trim-left-r 0 \
    --p-n-threads 4 \
    --o-denoising-stats output/denoising-stats-1.qza \
    --o-table output/feature_table-1.qza \
    --o-representative-sequences output/rep-seqs-1.qza

qiime metadata tabulate \
    --m-input-file /home/users/bg1152/output/denoising-stats-1.qza \
    --o-visualization /home/users/bg1152/output/denoising-stats.qzv

 qiime feature-table tabulate-seqs \
        --i-data /home/users/bg1152/output/rep-seqs-1.qza \
        --o-visualization /home/users/bg1152/output/rep-seqs.qzv

 ######Taxonomy assignment 
qiime feature-classifier classify-consensus-vsearch \
  --i-query /home/users/bg1152/output/rep-seqs-1.qza \
  --i-reference-reads /tmp/gen711_project_data/reference_databases/12S-seqs-derep-uniq.qza \
  --i-reference-taxonomy /tmp/gen711_project_data/reference_databases/12S-tax-derep-uniq.qza \
  --p-maxaccepts 10 \
  --p-query-cov 0.80 \
  --p-perc-identity 0.9 \
  --p-threads 36 \
  --o-classification /home/users/bg1152/output/taxonomy.qza

        ######## Barplot 
qiime taxa barplot \
     --i-table /home/users/bg1152/output/feature_table-1.qza \
     --i-taxonomy /home/users/bg1152/output/taxonomy.qza \
     --o-visualization /home/users/bg1152/output/my-barplot.qzv

###### Metadata and background info
qiime feature-table filter-samples \
  --i-table feature_table.qza \
  --m-metadata-file metadata.tsv \
  --o-filtered-table feature_table_filtered.qza

qiime taxa barplot \
     --i-table feature_table_filtered.qza \
     --m-metadata-file sample-metadata.tsv \
     --i-taxonomy taxonomy.qza \
     --o-visualization filtered-barplot.qzv
 
 
