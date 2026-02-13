---
name: ngs-pipeline
description: NGS data processing with pysam and deeptools. BAM/CRAM handling, coverage analysis, quality control, and peak calling support.
metadata:
    skill-author: Albert Ying
---

# NGS pipeline

## When to use

- Processing BAM/CRAM/VCF files
- Computing genome-wide coverage and signal tracks
- Quality control of sequencing data
- Generating bigWig files for visualization

## Read processing with pysam

```python
import pysam

# Read BAM and filter
bam = pysam.AlignmentFile("sample.bam", "rb")
for read in bam.fetch("chr1", 1000000, 2000000):
    if read.mapping_quality >= 30 and not read.is_duplicate:
        # process read
        pass

# Count reads per region
count = bam.count("chr1", 1000000, 2000000)

# Extract coverage array
coverage = bam.count_coverage("chr1", 1000000, 2000000, quality_threshold=20)
```

## Signal tracks with deeptools

```bash
# BAM to normalized bigWig
bamCoverage -b sample.bam -o sample.bw --normalizeUsing RPKM --binSize 10

# Correlation between samples
multiBamSummary bins -b s1.bam s2.bam -o results.npz
plotCorrelation -in results.npz --corMethod pearson -o correlation.pdf

# Signal around TSS
computeMatrix reference-point -S sample.bw -R genes.bed -a 3000 -b 3000 -o matrix.gz
plotHeatmap -m matrix.gz -o heatmap.pdf --colorMap viridis
```

## QC checklist

1. FastQC: per-base quality, adapter content, duplication rate
2. Alignment rate > 90% for DNA, > 70% for RNA
3. Duplication rate context-dependent (high for ChIP-seq input is expected)
4. Insert size distribution should be unimodal for paired-end
5. Strand balance ~50/50 for DNA, may vary for strand-specific RNA
