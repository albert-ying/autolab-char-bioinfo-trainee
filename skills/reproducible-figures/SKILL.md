---
name: reproducible-figures
description: Publication-quality scientific figures with matplotlib and seaborn. Consistent styling, colorblind-safe palettes, multi-panel layouts.
metadata:
    skill-author: Albert Ying
---

# Reproducible figures

## When to use

- Creating publication-quality plots
- Multi-panel figure layouts
- Consistent styling across a manuscript
- Exporting for journal submission

## Setup

```python
import matplotlib.pyplot as plt
import seaborn as sns
from aquarel import load_theme

theme = load_theme("scientific").set_grid(draw=False).set_font(size=15)
theme.set_ticks(direction="out").set_axis_labels(pad=10)
theme.apply()
```

## Multi-panel figure

```python
fig, axes = plt.subplots(1, 3, figsize=(15, 4))

# Panel A
sns.violinplot(data=df, x="group", y="value", ax=axes[0])
axes[0].set_title("A", loc="left", fontweight="bold", fontsize=14)

# Panel B
sns.heatmap(matrix, cmap="coolwarm", center=0, ax=axes[1])
axes[1].set_title("B", loc="left", fontweight="bold", fontsize=14)

# Panel C
axes[2].scatter(x, y, s=20, alpha=0.6)
axes[2].set_title("C", loc="left", fontweight="bold", fontsize=14)

theme.apply_transforms()
plt.tight_layout()
plt.savefig("figure.pdf", bbox_inches="tight")
plt.savefig("figure.png", dpi=600, bbox_inches="tight")
```

## Rules

- Always save both PDF (vector) and PNG (600 DPI)
- Use `bbox_inches="tight"` to prevent cropping
- Colorblind-safe: viridis, cividis, colorbrewer qualitative palettes
- Point cloud plots: outline layer (black, larger) + data layer (colored, smaller)
- Label every axis. Include units.
- Call `theme.apply_transforms()` before any axis rotation or tick adjustments
