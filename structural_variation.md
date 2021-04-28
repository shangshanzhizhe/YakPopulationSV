# Structural Variants

## SV detection

### Reads alignment

#### NGM-LR alignment
```sh
ngmlr -t 4 -r reference.fasta -q reads.fastq -o sample.sam -x ont
```

#### Convert to BAM format
```sh
samtools view -bS sample.sam | samtools sort > sample.bam
samtools index sample.bam
```

### SV calling

Following the tutorial of Sniffles [population calling](https://github.com/fritzsedlazeck/Sniffles/wiki/SV-calling-for-a-population)

```sh
sniffles -m sample.bam -v sample.vcf
```

### SV genotyping

#### Merge all SVs

```
ls *.vcf > vcf_files_raw_calls.txt
SURVIVOR merge vcf_files_raw_calls.txt 1000 1 1 -1 -1 -1 merged_SURVIVOR_1kbpdist_typesave.vcf
```

#### genotyping each VCF (force calling)

```sh
sniffles -m sample.bam -v sample_gt.vcf --genotype -n -1 --Ivcf merged_SURVIVOR_1kbpdist_typesave.vcf
```

#### Merge all VCFs

```sh
ls *_gt.vcf > vcfs.list
ls *.bam > bam.list
mkdir -p tmp
sh Jasmine run.sh file_list=vcfs.list genome_file=reference.fasta bam_list=bam.list
out_dir=tmp > Yaks.all.lr.vcf
```

#### Repetitive elements annotation

```sh
RepeatMasker -q xsmall -a -species "bos grunniens" -pa 30 SV.del.ins.fasta 
```

#### Region-based SV annotation

Following the mannual of [ANNOVAR](https://annovar.openbioinformatics.org/en/latest/)

#### Fst calculation

```sh
vcftools --vcf Yaks.all.lr.vcf --weir-fst-pop domestic.yak.indv.list --weir-fst-pop wild.yak.indv.list --out Yaks.all.lr.eachSV
```