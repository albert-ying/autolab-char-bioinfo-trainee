---
name: viral-metagenomics
description: Viral metagenomics assembly and species identification from sequencing reads
metadata:
  skill-author: Autonomous Lab
  generated: false
  source: marketplace
---

# Viral Metagenomics Assembly and Classification

## When to use
Use this skill for identifying viral species from metagenomic sequencing data,
especially clinical or environmental samples.

## Assembly — CRITICAL
- **Use metaSPAdes** (`spades.py --meta`) for viral metagenomics — NOT megahit
- **Do NOT set `--min-contig-len`** or any minimum contig length filter
- Keep ALL assembled contigs, even short ones (≥200bp)
- Viral genomes can be small (500bp-10kb); aggressive length filters lose real viral hits
- A 496bp contig could be a real viral species — do not discard it

## Host removal
- Map reads to host reference genome first (BWA-MEM2 or Bowtie2)
- Keep only unmapped reads (`samtools view -f 4`) for assembly
- This reduces false-positive classifications from host contamination

## Classification
- Use **Kaiju** with viral database for protein-level classification (more sensitive than nucleotide)
- Cross-check ambiguous hits with BLASTn against NCBI nt if available
- Report ICTV-standardized species names when possible
- Map common synonyms to their canonical ICTV names

## Output discipline
- Group contigs by domain and species — report contig_count per species
- **Always include an "Unclassified" row** for contigs with no confident classification
- Typical viral metagenomics from clinical samples: **2-10 species** identified
- If only 1-2 species found, verify assembly quality — check if short contigs were lost
- If many species (>15), verify classification confidence — may have false positives

## Common pitfalls
- Using megahit with `--min-contig-len 500` → loses small viral contigs
- Forgetting unclassified contigs → missing "Unclassified" category in output
- Not removing host reads → false viral classifications from host sequences
- Using nucleotide-only classification → misses divergent viruses
