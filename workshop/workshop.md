# Workshop

## Tutorial 11682
> https://gatk.broadinstitute.org/hc/en-us/articles/360035531092?id=11682

```bash
gatk PreprocessIntervals \
    -L targets_C.interval_list \
    -R /gatk/ref/Homo_sapiens_assembly38.fasta \
    --bin-length 0 \
    --interval-merging-rule OVERLAPPING_ONLY \
    -O sandbox/targets_C.preprocessed.interval_list
```

```bash
gatk CollectFragmentCounts \
    -I tumor.bam \
    -L targets_C.preprocessed.interval_list \
    --interval-merging-rule OVERLAPPING_ONLY \
    -O sandbox/tumor.counts.hdf5
```

```bash
gatk --java-options "-Xmx6500m" CreateReadCountPanelOfNormals \
    -I HG00133.alt_bwamem_GRCh38DH.20150826.GBR.exome.counts.hdf5 \
    -I HG00733.alt_bwamem_GRCh38DH.20150826.PUR.exome.counts.hdf5 \
    -I NA19654.alt_bwamem_GRCh38DH.20150826.MXL.exome.counts.hdf5 \
    --minimum-interval-median-percentile 5.0 \
    -O sandbox/cnvponC.pon.hdf5
```

```bash
gatk --java-options "-Xmx12g" DenoiseReadCounts \
    -I hcc1143_T_clean.counts.hdf5 \
    --count-panel-of-normals cnvponC.pon.hdf5 \
    --standardized-copy-ratios sandbox/hcc1143_T_clean.standardizedCR.tsv \
    --denoised-copy-ratios sandbox/hcc1143_T_clean.denoisedCR.tsv
```

```bash
gatk PlotDenoisedCopyRatios \
    --standardized-copy-ratios hcc1143_T_clean.standardizedCR.tsv \
    --denoised-copy-ratios hcc1143_T_clean.denoisedCR.tsv \
    --sequence-dictionary Homo_sapiens_assembly38.dict \
    --minimum-contig-length 46709983 \
    --output sandbox/plots \
    --output-prefix hcc1143_T_clean
```


## Tutorial 11683
> https://gatk.broadinstitute.org/hc/en-us/articles/360035890011


```bash
gatk --java-options "-Xmx3g" CollectAllelicCounts \
    -L chr17_theta_snps.interval_list \
    -I normal.bam \
    -R /gatk/ref/Homo_sapiens_assembly38.fasta \
    -O sandbox/hcc1143_N_clean.allelicCounts.tsv
```


```bash
gatk --java-options "-Xmx3g" CollectAllelicCounts \
    -L chr17_theta_snps.interval_list \
    -I tumor.bam \
    -R /gatk/ref/Homo_sapiens_assembly38.fasta \
    -O sandbox/hcc1143_T_clean.allelicCounts.tsv
```

```bash
gatk --java-options "-Xmx4g" ModelSegments \
    --denoised-copy-ratios hcc1143_T_clean.denoisedCR.tsv \
    --allelic-counts hcc1143_T_clean.allelicCounts.tsv \
    --normal-allelic-counts hcc1143_N_clean.allelicCounts.tsv \
    --output sandbox \
    --output-prefix hcc1143_T_clean
```

```bash
gatk CallCopyRatioSegments \
    --input hcc1143_T_clean.cr.seg \
    --output sandbox/hcc1143_T_clean.called.seg
```

```bash
gatk PlotModeledSegments \
    --denoised-copy-ratios hcc1143_T_clean.denoisedCR.tsv \
    --allelic-counts hcc1143_T_clean.hets.tsv \
    --segments hcc1143_T_clean.modelFinal.seg \
    --sequence-dictionary Homo_sapiens_assembly38.dict \
    --minimum-contig-length 46709983 \
    --output sandbox/plots \
    --output-prefix hcc1143_T_clean
```
