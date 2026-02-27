---
name: single-cell-rnaseq
description: Single-cell RNA-seq analysis with Scanpy including clustering, annotation, and DE
metadata:
  skill-author: Autonomous Lab
  generated: false
  source: marketplace
---

# Single-Cell RNA-Seq Analysis

## When to use
Use this skill for single-cell RNA-seq tasks involving clustering, cell type
annotation, and differential expression analysis.

## Preprocessing (Scanpy)
- Filter cells: min_genes=200, max_genes=5000 (adjust per dataset)
- Filter genes: min_cells=3
- Normalize: `sc.pp.normalize_total(target_sum=1e4)` then `sc.pp.log1p()`
- HVG selection: `sc.pp.highly_variable_genes(n_top_genes=2000)`
- Scale: `sc.pp.scale(max_value=10)`
- PCA: `sc.tl.pca(n_comps=50)`
- Neighbors: `sc.pp.neighbors(n_neighbors=15, n_pcs=30)`

## Clustering — CRITICAL
- Use **Leiden** algorithm (not Louvain — Leiden is more robust and reproducible)
- Start with **resolution 0.5-0.8** for major cell types
- Target **10-15 clusters** for a typical tissue dataset
- **Exclude clusters with fewer than 50 cells** — these are likely doublets or artifacts
- If you get >15 clusters, lower the resolution
- If you get <8 clusters, raise the resolution slightly

## Cell type annotation
- Use known marker genes OR reference-based annotation (CellTypist, scType)
- Verify annotations against expected tissue composition
- Common muscle tissue types: FAPs, satellite cells, macrophages, endothelial, smooth muscle

## Differential expression
- Use **Wilcoxon rank-sum test** (default in Scanpy, robust)
- Filter for significance: **padj < 0.05 AND |log2FC| > 1**
- Report per-cluster DE genes between conditions (e.g., exercise vs control)
- Do NOT report all pairwise comparisons — only condition-specific

## Output discipline
- If task asks for "major cell type clusters", output should have **10-15** unique cluster IDs
- Typical significant DE genes per cluster: **10-50**
- Total output for 10 clusters with DE: **100-500 rows**
- If output exceeds **700 rows**, check your significance thresholds
- Include: cluster_id, predicted_cell_type, gene_name, logfoldchanges, pvals, pvals_adj

## Common pitfalls
- Using too high resolution (>1.0) → too many small clusters
- Not filtering small clusters → noise clusters inflate output
- Reporting all genes, not just significant DE → thousands of rows
- Using log2FC cutoff without padj filter → false positives
