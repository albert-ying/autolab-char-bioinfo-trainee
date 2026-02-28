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
- **Taxonomic classification**: Kraken2 is the primary classifier for shotgun data
- **Abundance re-estimation**: Bracken corrects genome length bias BUT drops rare taxa.
  For low-classification-rate datasets (<10% classified), use Kraken2 report directly
  with `--report` flag and parse phylum-level counts from the report file.
- **16S amplicon**: QIIME2 or mothur with SILVA/Greengenes2 database

## Critical parameters
- Kraken2: use `--confidence 0.2` to reduce false positives
- If using Bracken: set `-t 0` to preserve all taxa including rare ones
- Report only taxa at the requested taxonomic rank (Phylum, Genus, etc.)
- Include relative abundance columns normalized per sample
- Exclude Eukaryota from bacterial community analyses unless explicitly requested

## Handling low-classification-rate datasets
- Check classification rate first: if <10% of reads classified, expect many rare taxa
- Use Kraken2 report directly (NOT Bracken) to preserve rare phyla
- Parse the `.kreport` file: column 4 = rank code (P = phylum), column 6 = taxon name
- Sum reads at phylum level across all sub-taxa
- Normalize to relative abundance per sample

## Quality filtering
- For paired samples: report taxa present in at least one sample
- Remove taxa classified only at higher ranks (e.g., "unclassified Bacteria")
- Do NOT apply aggressive read-count thresholds — let the classification confidence
  handle noise rather than post-hoc filtering

## Output discipline
- If task asks for "phyla", report only Phylum-level — do NOT include sub-ranks
- Typical phylum count for soil/water microbiome: **15-40** phyla
- If your output exceeds **50 phyla**, re-check your filtering thresholds
- Always include both sample abundance columns (e.g., JP4D and JC1A)
- Sort by total abundance descending

## Common pitfalls
- Using Bracken on low-classification-rate data → drops rare but real taxa
- Including Archaea/Viruses when task asks for bacterial community
- Not normalizing abundances → raw counts are not comparable across samples
