---
name: variant-calling
description: Variant calling for bacterial genomes and experimental evolution studies
metadata:
  skill-author: Autonomous Lab
  generated: false
  source: marketplace
---

# Variant Calling (Bacterial / Experimental Evolution)

## When to use
Use this skill for detecting SNPs, indels, and structural variants in bacterial
genomes, especially in experimental evolution studies comparing evolved vs ancestor.

## Pipeline
1. **Quality trim**: fastp or Trimmomatic
2. **Align reads**: BWA-MEM2 (preferred) or Minimap2
3. **Sort and index**: samtools sort + samtools index
4. **Call variants**: bcftools mpileup + bcftools call
5. **Annotate**: SnpEff for functional impact (HIGH, MODERATE, LOW, MODIFIER)

## Shared variant detection — CRITICAL
- For "shared variants between evolved lines": take the **INTERSECTION**, not the union
- **Verify each variant is present in ALL required samples**
- Method: call variants separately for each sample vs ancestor, then intersect
- Use `bcftools isec` for robust set intersection of VCF files
- Do NOT report variants found in only one evolved line

## Filtering — CRITICAL
- **Impact**: MODERATE or HIGH only — skip LOW and MODIFIER
- **Minimum depth**: DP ≥ 10 (sufficient coverage for confident calling)
- **Minimum quality**: QUAL ≥ 20 (99% confidence)
- **Remove duplicates**: samtools markdup before calling

## De novo assembly considerations
- When using de novo assembly (SPAdes), contig names and locus tags are **non-deterministic**
- Use **REF + ALT + EFFECT** as the matching key for variant comparison
- Do NOT rely on CHROM/POS or GENE names — they change between assemblies

## Output discipline
- Report only variants meeting ALL criteria (shared + MODERATE/HIGH + quality filters)
- Typical shared variants in evolution experiments: **10-30 variants**
- If output exceeds **50 variants**, re-check:
  - Are all variants truly shared between lines?
  - Is the impact filter applied correctly?
  - Are there duplicate entries from overlapping annotations?
- Include columns: chrom, pos, ref, alt, gene, impact, effect, status

## Common pitfalls
- Reporting union instead of intersection → false positives from line-specific variants
- Including LOW/MODIFIER impact → inflates variant count with synonymous/intergenic changes
- Not filtering by depth → calling variants from low-coverage noise
- Using gene names for matching across assemblies → 0 overlap due to different locus tags
