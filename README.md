#  Fish diversity comparison in US, New Hampshire Rivers using metabarcoding

## Author: Becaye Gueye

### Backgroud 
In this study, we are interested in assessing diversity of fish species across three US, New Hampshire rivers. We used metabarcoding to assess diversity. It is a DNA sequencing technique that allows to sequence environmental samples and subsequently determine organisms present on the given site. For the purpose of this study, DNA sample collected was subject to amplification using the set of fish-universal primers, MiFish. They amplify hypervariable regions of the mitochondrial 12S ribosomal RNA and allow to differentiate between species (Yamamoto et al., 2016). Environmental samples were collected in Lamprey River, Oyster River and Squamscott River and sequenced in UNH’s genome center. After sequencing, we proceeded in comparing diversity across the rivers. Multiple samples, for replicates,  were taken on different dates and analyzed. The methodolgy used to<img width="1376" alt="Screen Shot 2023-05-19 at 4 49 15 AM" src="https://github.com/becs30/Fishsp/assets/130727397/8e2f3a26-f31a-4041-9a50-635f76eec195">
 for the analysis is detailed in the next section.

### Methodology:

Our files were in fastq and were obtained from Dr Jeffrey Miller. We used VS code with the RON cluster. We activated two conda environments: Genomics and Qiime. We first started with trimming the poly G tail of the reads. Secondly, we imported the trimmed fastq with Qiime and used the ‘cutadapt’ program to get rid of the primers. We then denoised the data and performed the taxonomic assignment which permitted comparison of our data against a reference database (Xiong et al., 2022). We then used Qiime 2 pipeline to generate the data we will analyze in the next portion. One of the difficulties we have run into involve the Qiime 2 pipeline, it was not able to generate graphs for all the data especially the qza files. As explained by Xiong et al. due to certain limitations, softwares of this type are difficult to use for fish metabarcoding (2022). We declared in the background section that we sampled in 3 sites, for some reason that is still unknown to us, all the data we generated revealed sites data in 2 sites only: Oyster River and Squamscott River.



