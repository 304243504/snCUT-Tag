
module load MACS2/2.1.1

macs2 callpeak \
-t RMAS02.bam \
-n sample_name \
-f BAM \
-g 3.6e8 \
-p .1 \
--call-summits \
--outdir /PATH_TO_OUTDIR/ 

############### Step 1. Define candidate elemets
for i in *narrowPeak
do
sample=${i%_*}
bedtools sort -faidx /public/home/spluan/ABC/MH63RS2_chromsize.txt -i ${sample}_peaks.narrowPeak > ${sample}_peaks.narrowPeak.sorted
done

## Call candidate regions
conda activate final-abc-env

python /public/home/spluan/Applications/ABC/ABC_master/src/makeCandidateRegions.py \
--narrowPeak ./${sample}_peaks.narrowPeak.sorted \
--bam /public/home/spluan/scCUT_N_Tag/all_file_important/bam/cluster/cluster_bam/${sample}/${sample}_sort.bam \
--outDir /public/home/spluan/scCUT_N_Tag/all_file_important/bam/cluster/ABC/${sample}/ \
--chrom_sizes /public/home/spluan/ABC/MH63RS2_chromsize.txt \
--peakExtendFromSummit 250 \
--nStrongestPeaks 150000


############### Step 2. Quantifying Enhancer Activity:
for i in *candidateRegions.bed
do
sample=${i%_*}

python /public/home/spluan/Applications/ABC/ABC_master/src/run.neighborhoods.py \
--candidate_enhancer_regions ${sample}_peaks.narrowPeak.sorted.candidateRegions.bed \
--genes ../../../MH63RS2_gene.bed \
--H3K27ac /public/home/spluan/ABC/H3K27ac/RMCS95/20210613_bam/RMCS95.bam,/public/home/spluan/ABC/H3K27ac/RMCS96/20210613_bam/RMCS96.bam \
--ATAC ../../${sample}_sort.bam \
--chrom_sizes ../../../MH63RS2_chromsize \
--cellType ${sample} \
--outdir ../../02_Quantifying_Enhancer_Activity/
done

############### Step 3. Computing the ABC Score:
/public/home/spluan/scCUT_N_Tag/all_file_important/06_ABC/01_new_ABC/gethicdir.sh

python /public/home/spluan/Applications/ABC/ABC_master/src/predict.py \
--enhancers EnhancerList.txt \
--genes GeneList.txt \
--HiCdir /public/home/spluan/HiC/japonica/article/actual/new_timeout/add_Fconvert/bedpe_intra/bedpe_prepare_for_ABC/SRR6765292/here/ \
--hic_type bedpe \
--hic_resolution 5000 \
--scale_hic_using_powerlaw \
--threshold .02 \
--cellType sample_cell \
--outdir ../03_Computing_ABC_Score/ \
--chrom_sizes /public/home/spluan/scCUT_N_Tag/all_file_important/bam/cluster/cluster_bam/MH63RS2_chromsize \
--make_all_putative



