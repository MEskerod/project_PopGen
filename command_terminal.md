# Commands for project in Population Genomics
The following is command that has been ran in a terminal
## Preperation of files
The files contains more individuals than the files with meta data. These individuals are filtered out. 
Furthermore a header needs to be added to the meta data files for Relate 
```
awk '{print $1}' all_inds.txt > all_ids.txt
sed -i '1s/^/sample population  region  sex\n/' all_inds.txt
bcftools view -S all_ids.txt -o filtered_chr3_460_540_phased.vcf chr3_460_540_phased.vcf.gz
```
```
awk '{print $1}' YRI_inds.txt > YRI_ids.txt
sed -i '1s/^/sample population  region  sex\n/' YRI_inds.txt
bcftools view -S YRI_ids.txt -o filtered_chr3_YRI_460_540_phased.vcf chr3_YRI_460_540_phased.vcf.gz
```
```
awk '{print $1}' LWK_inds.txt > LWK_ids.txt
sed -i '1s/^/sample population  region  sex\n/' LWK_inds.txt
bcftools view -S LWK_ids.txt -o filtered_chr3_LWK_460_540_phased.vcf chr3_LWK_460_540_phased.vcf.gz
```
```
awk '{print $1}' GWD_inds.txt > GWD_ids.txt
sed -i '1s/^/sample population  region  sex\n/' GWD_inds.txt
bcftools view -S GWD_ids.txt -o filtered_chr3_GWD_460_540_phased.vcf chr3_GWD_460_540_phased.vcf.gz
```
```
awk '{print $1}' MSL_inds.txt > MSL_ids.txt
sed -i '1s/^/sample population  region  sex\n/' MSL_inds.txt
bcftools view -S MSL_ids.txt -o filtered_chr3_MSL_460_540_phased.vcf chr3_MSL_460_540_phased.vcf.gz
```
```
awk '{print $1}' ESN_inds.txt > ESN_ids.txt
sed -i '1s/^/sample population  region  sex\n/' ESN_inds.txt
bcftools view -S ESN_ids.txt -o filtered_chr3_ESN_460_540_phased.vcf chr3_ESN_460_540_phased.vcf.gz
```
## Fst calculations
For Fst calculations the tool vcftools was used to make comparisons between all five populations in pairs
```
vcftools --vcf filtered_chr3_460_540_phased.vcf.gz --weir-fst-pop YRI_inds.txt --weir-fst-pop LWK_inds.txt --out YRI-LWK 

vcftools --vcf filtered_chr3_460_540_phased.vcf.gz --weir-fst-pop YRI_inds.txt --weir-fst-pop GWD_inds.txt --out YRI-GWD

vcftools --vcf filtered_chr3_460_540_phased.vcf.gz --weir-fst-pop YRI_inds.txt --weir-fst-pop MSL_inds.txt --out YRI-MSL

vcftools --vcf filtered_chr3_460_540_phased.vcf.gz --weir-fst-pop YRI_inds.txt --weir-fst-pop ESN_inds.txt --out YRI-ESN 

vcftools --vcf filtered_chr3_460_540_phased.vcf.gz --weir-fst-pop LWK_inds.txt --weir-fst-pop GWD_inds.txt --out LWK-GWD

vcftools --vcf filtered_chr3_460_540_phased.vcf.gz --weir-fst-pop LWK_inds.txt --weir-fst-pop MSL_inds.txt --out LWK-MSL

vcftools --vcf filtered_chr3_460_540_phased.vcf.gz --weir-fst-pop LWK_inds.txt --weir-fst-pop ESN_inds.txt --out LWK-ESN

vcftools --vcf filtered_chr3_460_540_phased.vcf.gz --weir-fst-pop GWD_inds.txt --weir-fst-pop MSL_inds.txt --out GWD-MSL

vcftools --vcf filtered_chr3_460_540_phased.vcf.gz --weir-fst-pop GWD_inds.txt --weir-fst-pop ESN_inds.txt --out GWD-ESN

vcftools --vcf filtered_chr3_460_540_phased.vcf.gz --weir-fst-pop MSL_inds.txt --weir-fst-pop ESN_inds.txt --out MSL-ESN
```
The files was read into R for further processing.
See Project_fst.ipynb
## Relate
### All individuals
Relate is used on all the individuals and then used to viasulize trees to get an impression of the relationship between populations

Convert .vcf.gz tp .haps
```
~/populationgenomics/software/relate/bin/RelateFileFormats --mode ConvertFromVcf --haps chr3.haps --sample chr3.sample -i filtered_chr3_460_540_phased
```
Repetitive an unreliably sequenced regions is masked and each variant is assigned to ancestral or derived
```
~/populationgenomics/software/relate/scripts/PrepareInputFiles/PrepareInputFiles.sh --haps chr3.haps --sample chr3.sample --ancestor human_ancestor_3.fa --mask 20140520.chr3.strict_mask.fasta.gz -o prep.chr3
```
Run relate
```
~/populationgenomics/software/relate/bin/Relate --mode All -m 1.25e-8 -N 30000 --haps prep.chr3.haps.gz --sample prep.chr3.sample.gz --map genetic_map_chr3_combined_b37.txt -o chr3_relate
```
Estimate historic population size
```
~/populationgenomics/software/relate/scripts/EstimatePopulationSize/EstimatePopulationSize.sh -i chr3_relate -m 1.25e-8 --poplabels all_inds.txt -o popsize --threshold 0
```
#### Building trees
Trees are build at different positions of the region to get a sence on the relation between the populations. 
```
~/populationgenomics/software/relate/scripts/TreeView/TreeView.sh --haps chr3.haps --sample chr3.sample --anc popsize.anc --mut popsize.mut --poplabels ~/populationgenomics/students/mari256s/project/all_inds.txt --years_per_gen 28 --bp_of_interest 53900000 -o 53900000_tree
```
```
~/populationgenomics/software/relate/scripts/TreeView/TreeView.sh --haps chr3.haps --sample chr3.sample --anc popsize.anc --mut popsize.mut --poplabels ~/populationgenomics/students/mari256s/project/all_inds.txt --years_per_gen 28 --bp_of_interest 46200000 -o 46200000_tree
```
```
~/populationgenomics/software/relate/scripts/TreeView/TreeView.sh --haps chr3.haps --sample chr3.sample --anc popsize.anc --mut popsize.mut --poplabels ~/populationgenomics/students/mari256s/project/all_inds.txt --years_per_gen 28 --bp_of_interest 51200000 -o 51200000_tree
```
### Populations 
#### YRI
Convert .vcf.gz tp .haps
```
~/populationgenomics/software/relate/bin/RelateFileFormats --mode ConvertFromVcf --haps chr3_YRI.haps --sample chr3_YRI.sample -i filtered_chr3_YRI_460_540_phased
```
Repetitive an unreliably sequenced regions is masked and each variant is assigned to ancestral or derived
```
~/populationgenomics/software/relate/scripts/PrepareInputFiles/PrepareInputFiles.sh --haps chr3_YRI.haps --sample chr3_YRI.sample --ancestor human_ancestor_3.fa --mask 20140520.chr3.strict_mask.fasta.gz -o prep.chr3_YRI
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
```
~/populationgenomics/software/relate/bin/RelateFileFormats --mode ConvertFromVcf --haps chr3_LWK.haps --sample chr3_LWK.sample -i filtered_chr3_LWK_460_540_phased

~/populationgenomics/software/relate/scripts/PrepareInputFiles/PrepareInputFiles.sh --haps chr3_LWK.haps --sample chr3_LWK.sample --ancestor human_ancestor_3.fa --mask 20140520.chr3.strict_mask.fasta.gz -o prep.chr3_LWK

~/populationgenomics/software/relate/bin/Relate --mode All -m 1.25e-8 -N 30000 --haps prep.chr3_LWK.haps.gz --sample prep.chr3_LWK.sample.gz --map genetic_map_chr3_combined_b37.txt -o chr3_LWK_relate

~/populationgenomics/software/relate/scripts/EstimatePopulationSize/EstimatePopulationSize.sh -i chr3_LWK_relate -m 1.25e-8 --poplabels LWK_inds.txt -o popsize_LWK --threshold 0

~/populationgenomics/software/relate/scripts/DetectSelection/DetectSelection.sh -i popsize_LWK -m 1.25e-8 --poplabels LWK_inds.txt -o selection_relate_LWK
```
#### GWD
```
~/populationgenomics/software/relate/bin/RelateFileFormats --mode ConvertFromVcf --haps chr3_GWD.haps --sample chr3_GWD.sample -i filtered_chr3_GWD_460_540_phased

~/populationgenomics/software/relate/scripts/PrepareInputFiles/PrepareInputFiles.sh --haps chr3_GWD.haps --sample chr3_GWD.sample --ancestor human_ancestor_3.fa --mask 20140520.chr3.strict_mask.fasta.gz -o prep.chr3_GWD

~/populationgenomics/software/relate/bin/Relate --mode All -m 1.25e-8 -N 30000 --haps prep.chr3_GWD.haps.gz --sample prep.chr3_GWD.sample.gz --map genetic_map_chr3_combined_b37.txt -o chr3_GWD_relate

~/populationgenomics/software/relate/scripts/EstimatePopulationSize/EstimatePopulationSize.sh -i chr3_GWD_relate -m 1.25e-8 --poplabels GWD_inds.txt -o popsize_GWD --threshold 0

~/populationgenomics/software/relate/scripts/DetectSelection/DetectSelection.sh -i popsize_GWD -m 1.25e-8 --poplabels GWD_inds.txt -o selection_relate_GWD
```
#### MSL
```
~/populationgenomics/software/relate/bin/RelateFileFormats --mode ConvertFromVcf --haps chr3_MSL.haps --sample chr3_MSL.sample -i filtered_chr3_MSL_460_540_phased

~/populationgenomics/software/relate/scripts/PrepareInputFiles/PrepareInputFiles.sh --haps chr3_MSL.haps --sample chr3_MSL.sample --ancestor human_ancestor_3.fa --mask 20140520.chr3.strict_mask.fasta.gz -o prep.chr3_MSL

~/populationgenomics/software/relate/bin/Relate --mode All -m 1.25e-8 -N 30000 --haps prep.chr3_MSL.haps.gz --sample prep.chr3_MSL.sample.gz --map genetic_map_chr3_combined_b37.txt -o chr3_MSL_relate

~/populationgenomics/software/relate/scripts/EstimatePopulationSize/EstimatePopulationSize.sh -i chr3_MSL_relate -m 1.25e-8 --poplabels MSL_inds.txt -o popsize_MSL --threshold 0

~/populationgenomics/software/relate/scripts/DetectSelection/DetectSelection.sh -i popsize_MSL -m 1.25e-8 --poplabels MSL_inds.txt -o selection_relate_MSL
```
#### ESN
```
~/populationgenomics/software/relate/bin/RelateFileFormats --mode ConvertFromVcf --haps chr3_ESN.haps --sample chr3_ESN.sample -i filtered_chr3_ESN_460_540_phased

~/populationgenomics/software/relate/scripts/PrepareInputFiles/PrepareInputFiles.sh --haps chr3_ESN.haps --sample chr3_ESN.sample --ancestor human_ancestor_3.fa --mask 20140520.chr3.strict_mask.fasta.gz -o prep.chr3_ESN

~/populationgenomics/software/relate/bin/Relate --mode All -m 1.25e-8 -N 30000 --haps prep.chr3_ESN.haps.gz --sample prep.chr3_ESN.sample.gz --map genetic_map_chr3_combined_b37.txt -o chr3_ESN_relate

~/populationgenomics/software/relate/scripts/EstimatePopulationSize/EstimatePopulationSize.sh -i chr3_ESN_relate -m 1.25e-8 --poplabels ESN_inds.txt -o popsize_ESN --threshold 0

~/populationgenomics/software/relate/scripts/DetectSelection/DetectSelection.sh -i popsize_ESN -m 1.25e-8 --poplabels ESN_inds.txt -o selection_relate_ESN
```
## CLUES
ClUES can infer selection from trees build by Relate 
The programs that is neeed to run CLUES is copied from the GitHub repository
CLUES should be used in idividual SNPs

### GWD
Use relate to sample branch lengths
```
~/populationgenomics/software/relate/scripts/SampleBranchLengths/SampleBranchLengths.sh -i popsize_GWD -o chr3_GWD_relate_resample_46658737_500 -m 1.25e-8 --coal popsize_GWD.coal --format b --num_samples 300 --first-bp 46658737 --last-bp 46658737
```
Run CLUES from CLUES folder
```
python inference.py --times ~/populationgenomics/students/mari256s/project/relate/GWD/chr3_GWD_relate_resample_46658737 --coal ~/populationgenomics/students/mari256s/project/relate/GWD/popsize_GWD.coal --burnin 60 --out ~/populationgenomics/students/mari256s/project/clues/selection_clues_GWD_46658737_300_burnin_coal 
```
Plot trajectory 
```
python plot_traj_Maria.py ~/populationgenomics/students/mari256s/project/clues/selection_clues_GWD_46658737_300_burnin_coal  ~/populationgenomics/students/mari256s/project/clues/traj_plots/traj_clues_GWD_46658737_300_burnin_coal
```
