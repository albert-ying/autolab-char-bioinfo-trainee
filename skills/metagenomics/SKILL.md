---
name: metagenomics
description: Shotgun and amplicon metagenomics community profiling with Kraken2/Bracken
metadata:
  skill-author: Autonomous Lab
  generated: false
  source: marketplace
---

# Metagenomics Community Profiling

## When to use
Use this skill for any task involving microbial community composition analysis from
shotgun metagenomics or 16S/ITS amplicon sequencing data.

## Tool selection
- **Taxonomic classification**: Kraken2 + Bracken is the gold standard for shotgun data
- **16S amplicon**: QIIME2 or mothur with SILVA/Greengenes2 database
- **Functional profiling**: HUMAnN3 or MetaPhlAn4
- Always run **Bracken** after Kraken2 to correct for genome length bias

## Critical parameters
- Bracken: always use `-t 10` (minimum 10 reads) to exclude noise taxa
- Kraken2: use `--confidence 0.2` to reduce false positives
- Report only taxa at the requested taxonomic rank (Phylum, Genus, etc.)
- Include relative abundance columns normalized per sample

## Quality filtering
- Exclude taxa with fewer than 10 classified reads across all samples
- Remove taxa classified only at higher ranks (e.g., "unclassified Bacteria")
- For paired samples: report taxa present in at least one sample above threshold

## Output discipline
- If task asks for "phyla", report only Phylum-level — do NOT include sub-ranks
- Typical phylum count for soil/water microbiome: **15-40** phyla
- If your output exceeds **50 phyla**, re-check your filtering thresholds — you likely
  have noise taxa from low-abundance reads
- Always include both sample abundance columns (e.g., JP4D and JC1A)
- Sort by total abundance descending

## Common pitfalls
- Forgetting `-t` threshold in Bracken → reports hundreds of noise taxa
- Using Kraken2 report directly without Bracken → genome length bias
- Including Archaea/Viruses when task asks for bacterial community
- Not normalizing abundances → raw counts are not comparable across samples
