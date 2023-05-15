# Commands for project in Population Genomics
The following is command that has been ran in a terminal
## Fst calculations
For Fst calculations the tool vcftools was used to make comparisons between all five populations in pairs
```
vcftools --gzvcf chr3_460_540_phased.vcf.gz --weir-fst-pop YRI_inds.txt --weir-fst-pop LWK_inds.txt --out YRI-LWK 

vcftools --gzvcf chr3_460_540_phased.vcf.gz --weir-fst-pop YRI_inds.txt --weir-fst-pop GWD_inds.txt --out YRI-GWD

vcftools --gzvcf chr3_460_540_phased.vcf.gz --weir-fst-pop YRI_inds.txt --weir-fst-pop MSL_inds.txt --out YRI-MSL

vcftools --gzvcf chr3_460_540_phased.vcf.gz --weir-fst-pop YRI_inds.txt --weir-fst-pop ESN_inds.txt --out YRI-ESN 

vcftools --gzvcf chr3_460_540_phased.vcf.gz --weir-fst-pop LWK_inds.txt --weir-fst-pop GWD_inds.txt --out LWK-GWD

vcftools --gzvcf chr3_460_540_phased.vcf.gz --weir-fst-pop LWK_inds.txt --weir-fst-pop MSL_inds.txt --out LWK-MSL

vcftools --gzvcf chr3_460_540_phased.vcf.gz --weir-fst-pop LWK_inds.txt --weir-fst-pop ESN_inds.txt --out LWK-ESN

vcftools --gzvcf chr3_460_540_phased.vcf.gz --weir-fst-pop GWD_inds.txt --weir-fst-pop MSL_inds.txt --out GWD-MSL

vcftools --gzvcf chr3_460_540_phased.vcf.gz --weir-fst-pop GWD_inds.txt --weir-fst-pop ESN_inds.txt --out GWD-ESN

vcftools --gzvcf chr3_460_540_phased.vcf.gz --weir-fst-pop MSL_inds.txt --weir-fst-pop ESN_inds.txt --out MSL-ESN
```
The files was read into R for further processing.
See Project_fst.ipynb
## Relate
### All individuals
Relate is used on all the individuals and then used to viasulize trees to get an impression of the relationship between populations
Filter .vcf.gz to only contain the individuals in the .txt file
```
awk '{print $1}' all_inds.txt > all_ids.txt
bcftools view -S all_ids.txt -o filtered_chr3_460_540_phased.vcf chr3_460_540_phased.vcf.gz
```
Convert .vcf.gz tp .haps
```
~/populationgenomics/software/relate/bin/RelateFileFormats --mode ConvertFromVcf --haps chr3.haps --sample chr3.sample -i chr3_460_540_phased
```
Repetitive an unreliably sequenced regions is masked and each variant is assigned to ancestral or derived
```
~/populationgenomics/software/relate/scripts/PrepareInputFiles/PrepareInputFiles.sh --haps chr3.haps --sample chr3.sample --ancestor human_ancestor_3.fa --mask 20140520.chr3.strict_mask.fasta -o prep.chr3
```
Run relate
```
~/populationgenomics/software/relate/bin/Relate --mode All -m 1.25e-8 -N 30000 --haps prep.chr3.haps.gz --sample prep.chr3.sample.gz --map genetic_map_chr3_combined_b37.txt -o chr3_relate
```
Estimate historic population size
```
~/populationgenomics/software/relate/scripts/EstimatePopulationSize/EstimatePopulationSize.sh -i chr3_relate -m 1.25e-8 --poplabels all_inds.txt -o popsize --threshold 0
```
### Populations 
#### YRI
Convert .vcf.gz tp .haps
```
~/populationgenomics/software/relate/bin/RelateFileFormats --mode ConvertFromVcf --haps chr3_YRI.haps --sample chr3_YRI.sample -i chr3_YRI_460_540_phased
```
Repetitive an unreliably sequenced regions is masked and each variant is assigned to ancestral or derived
```
~/populationgenomics/software/relate/scripts/PrepareInputFiles/PrepareInputFiles.sh --haps chr3_YRI.haps --sample chr3_YRI.sample --ancestor human_ancestor_3.fa --mask 20140520.chr3.strict_mask.fasta -o prep.chr3_YRI
```
Run relate
```
~/populationgenomics/software/relate/bin/Relate --mode All -m 1.25e-8 -N 30000 --haps prep.chr3_YRI.haps.gz --sample prep.chr3_YRI.sample.gz --map genetic_map_chr3_combined_b37.txt -o chr3_YRI_relate
```
Estimate historic population size
```
~/populationgenomics/software/relate/scripts/EstimatePopulationSize/EstimatePopulationSize.sh -i chr3_YRI_relate -m 1.25e-8 --poplabels YRI_inds.txt -o popsize_YRI --threshold 0
```
Infer positive selction
```
~/populationgenomics/software/relate/scripts/DetectSelection/DetectSelection.sh -i popsize_YRI -m 1.25e-8 --poplabels YRI_inds.txt -o selection_relate_YRI
```
#### LWK
#### GWD
#### MSL
#### ESN
