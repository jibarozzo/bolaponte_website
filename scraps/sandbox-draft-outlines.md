# Sandbox Tutorial Drafts: Ready-to-Implement Outlines

This document provides detailed outlines for the first 4-5 tutorials, ready for development. Each includes structure, key code concepts, and sample sections.

---

## Tutorial 1: Understanding Core Microbiomes - A Practical Introduction

**File**: `blog/2025-XX-XX-core-microbiomes/index.qmd`  
**Estimated Read Time**: 12-15 minutes  
**Target Audience**: Graduate students, postdocs in microbial ecology  
**Prerequisites**: Basic R, familiarity with microbiome data structure

### Outline

#### Introduction (2 paragraphs)
- Hook: "In every microbiome study, one question always comes up: which microbes are found everywhere?"
- Define core microbiome concept
- Why it matters: keystone taxa, functional redundancy, host specificity
- Transition: "Let's explore this with real data and the BRCore package"

#### Setup
```r
library(tidyverse)
library(BRCore)
library(phyloseq)
library(patchwork)
```

#### Section 1: What Makes a Microbe "Core"?
- **Conceptual**: Three approaches to defining core
  1. Presence/absence threshold (e.g., in 70% of samples)
  2. Abundance threshold (e.g., >0.1% relative abundance)
  3. Combined (present in X% of samples AND at Y% abundance)
- **Visual**: Simple diagram or conceptual figure showing three approaches
- **Callout box**: "There's no universal definition—it depends on your system and question"

#### Section 2: Loading and Exploring Example Data
```r
# Using a built-in dataset or simulated data
data("example_microbiome")  # from BRCore or phyloseq

# Quick overview
example_microbiome
sample_data(example_microbiome)
otu_table(example_microbiome)
```
- Show structure of phyloseq object
- Quick diversity summary
- Rarefaction consideration (brief mention, link to deeper resource)

#### Section 3: Calculating Core Microbiome (Presence-Based)
```r
# Core members present in 70% of samples
core_70 <- calculate_core(example_microbiome, 
                          detection_threshold = 0, 
                          prevalence_threshold = 0.7)

core_70 %>% 
  arrange(desc(prevalence)) %>% 
  head(10)
```
- Interpret output: ASV IDs, taxonomy, prevalence
- Visualization: bar plot of top 10 core members

#### Section 4: Adding Abundance Criteria
```r
# Core members present in 50% of samples at >0.1% abundance
core_abundant <- calculate_core(example_microbiome,
                                detection_threshold = 0.001,
                                prevalence_threshold = 0.5)
```
- Compare to presence-only core
- Venn diagram showing overlap?

#### Section 5: Comparing Cores Across Groups
```r
# Split by treatment, habitat, or host species
sample_data(example_microbiome)$Treatment

core_by_group <- example_microbiome %>% 
  split_by_variable("Treatment") %>% 
  map(calculate_core, detection_threshold = 0, prevalence_threshold = 0.7)

# Visualize with UpSet plot or Venn
```
- When do cores diverge? (Specificity vs. generalism)

#### Section 6: Visualizing Core Members
- Heatmap of core ASVs across samples
- Network diagram (if applicable)
- Phylogenetic tree highlighting core members

#### Section 7: Interpreting Core Results
- **Callout**: What core microbiomes tell us (and what they don't)
- Functional implications: Do core members share functional traits?
- Caveats: Sampling depth, sequencing bias, rare biosphere

#### Conclusion
- Recap: Core microbiome definition is flexible
- Encourage readers to think critically about thresholds
- Tease next tutorial: "Now that we know who's core, let's explore community composition..."

#### Further Reading
- Links to key papers on core microbiome concepts
- BRCore documentation
- Related tutorial on prevalence filtering

#### Session Info
```r
sessionInfo()
```

---

## Tutorial 2: Community Composition Analysis - Ordination Made Simple

**File**: `blog/2025-XX-XX-ordination-basics/index.qmd`  
**Estimated Read Time**: 15-18 minutes  
**Target Audience**: Ecologists familiar with community data  
**Prerequisites**: Basic R, understanding of community matrices

### Outline

#### Introduction
- Hook: "Hundreds of species, dozens of samples—how do we see the patterns?"
- Ordination as dimensionality reduction for community data
- Preview: NMDS, PCoA, and when to use each

#### Setup
```r
library(tidyverse)
library(vegan)
library(ggplot2)
library(patchwork)
```

#### Section 1: The Data
```r
# Using your endophyte data or simulated fungal communities
data("endophyte_communities")

# Structure: rows = samples, columns = species (or ASVs)
# Metadata: elevation, host species, site, etc.
```
- Show community matrix structure
- Quick diversity metrics (richness, Shannon)

#### Section 2: NMDS - The Workhorse of Ecology
- **Conceptual**: What NMDS does (ordination based on rank distances)
- When to use: Most community data, especially with zeros
```r
# Calculate NMDS
nmds_result <- metaMDS(endophyte_communities, 
                       distance = "bray",
                       k = 2,  # dimensions
                       try = 100)

# Check stress (goodness of fit)
nmds_result$stress  # <0.2 is typically acceptable
```
- **Callout**: Interpreting stress values

#### Section 3: Visualizing NMDS
```r
# Extract scores
nmds_scores <- scores(nmds_result) %>% 
  as_tibble(rownames = "sample_id") %>% 
  left_join(metadata, by = "sample_id")

# Basic plot
ggplot(nmds_scores, aes(x = NMDS1, y = NMDS2, color = elevation_category)) +
  geom_point(size = 3, alpha = 0.7) +
  stat_ellipse(level = 0.95) +
  theme_minimal() +
  labs(title = "Fungal Endophyte Communities Across Elevation")
```
- Customization: colors, shapes, ellipses
- Adding species vectors (if relevant)

#### Section 4: PCoA - An Alternative Approach
- **Conceptual**: Eigenvalue-based ordination
- When to use: Fewer samples, or when you want to partition variance
```r
# Calculate PCoA with Bray-Curtis
dist_matrix <- vegdist(endophyte_communities, method = "bray")
pcoa_result <- cmdscale(dist_matrix, k = 2, eig = TRUE)

# Variance explained by each axis
pcoa_result$eig[1:2] / sum(pcoa_result$eig)
```
- Compare to NMDS: different assumptions, different interpretations

#### Section 5: Adding Environmental Vectors
```r
# Fit environmental variables to ordination
env_fit <- envfit(nmds_result ~ elevation + temperature + precipitation, 
                  data = metadata, 
                  perm = 999)

env_fit
```
- Visualize vectors on ordination plot
- Interpretation: which environmental factors drive community composition?

#### Section 6: Statistical Testing - PERMANOVA
```r
# Test for differences among groups
permanova_result <- adonis2(endophyte_communities ~ elevation_category + host_species,
                            data = metadata,
                            permutations = 999,
                            method = "bray")

permanova_result
```
- Interpret R² and p-values
- **Callout**: PERMANOVA assumptions (homogeneity of dispersion)
- Follow-up: betadisper() to check dispersion

#### Section 7: Pairwise Comparisons
```r
# If overall PERMANOVA is significant, which groups differ?
pairwise.adonis2(endophyte_communities ~ elevation_category, 
                 data = metadata)
```
- Adjust for multiple comparisons

#### Section 8: Publication-Ready Figure
```r
# Combine NMDS plot with stats and vectors
# Use patchwork or cowplot for multi-panel figure
final_plot <- nmds_plot + env_vectors_plot + stats_table
ggsave("ordination_figure.png", final_plot, width = 12, height = 6, dpi = 300)
```

#### Conclusion
- Recap: NMDS and PCoA are powerful tools for visualizing community patterns
- Statistical tests (PERMANOVA) formalize differences
- Encourage: Think about ecological meaning, not just p-values

#### Further Reading
- Links to vegan documentation
- Key papers on ordination methods
- Related tutorial on diversity metrics

#### Session Info

---

## Tutorial 3: Linear Models for Ecological Counts - Beyond the Gaussian

**File**: `blog/2025-XX-XX-glms-for-counts/index.qmd`  
**Estimated Read Time**: 20-22 minutes  
**Target Audience**: Quantitative ecologists, graduate students  
**Prerequisites**: Basic linear regression, understanding of GLMs

### Outline

#### Introduction
- Hook: "You can't put count data in a regular linear model—or can you?"
- Why counts need special treatment: discrete, non-negative, often skewed
- Preview: Poisson, negative binomial, zero-inflation

#### Setup
```r
library(tidyverse)
library(MASS)       # for glm.nb()
library(DHARMa)     # for model diagnostics
library(emmeans)    # for marginal means and contrasts
library(broom)      # for tidy model outputs
```

#### Section 1: The Problem with Counts
- Example: Endophyte richness across elevation
```r
data("endophyte_richness")

# Naive approach: Gaussian linear model
lm_naive <- lm(richness ~ elevation, data = endophyte_richness)
summary(lm_naive)

# Check residuals
plot(lm_naive)  # Yikes! Non-normal, heteroscedastic
```
- **Visual**: Residual plots showing violations

#### Section 2: Poisson GLM
- **Conceptual**: Poisson distribution for count data
- Assumption: Mean = Variance
```r
# Fit Poisson GLM
glm_poisson <- glm(richness ~ elevation, 
                   family = poisson(link = "log"),
                   data = endophyte_richness)

summary(glm_poisson)
```
- Interpret coefficients on log scale
- Exponentiate for rate ratios

#### Section 3: Checking for Overdispersion
```r
# Residual deviance vs. degrees of freedom
glm_poisson$deviance / glm_poisson$df.residual

# Formal test with DHARMa
simulateResiduals(glm_poisson, plot = TRUE)
```
- **Callout**: If variance > mean, you have overdispersion

#### Section 4: Negative Binomial GLM
- When Poisson fails (overdispersion)
```r
glm_nb <- glm.nb(richness ~ elevation + host_species,
                 data = endophyte_richness)

summary(glm_nb)
```
- Compare AIC to Poisson
- DHARMa diagnostics look better

#### Section 5: Zero-Inflation
- **Conceptual**: Excess zeros beyond what Poisson/NB expect
- When it matters: e.g., some samples have no endophytes
```r
library(pscl)  # for zeroinfl()

glm_zi <- zeroinfl(richness ~ elevation | host_species,
                   data = endophyte_richness,
                   dist = "negbin")

summary(glm_zi)
```
- Interpret two parts: count model and zero-inflation model

#### Section 6: Model Comparison
```r
AIC(glm_poisson, glm_nb, glm_zi)
```
- **Table**: Model comparison table
- Choose based on AIC, diagnostics, and biological interpretation

#### Section 7: Making Predictions and Contrasts
```r
# Marginal means for elevation categories
emmeans(glm_nb, specs = "elevation_category", type = "response")

# Pairwise contrasts
pairs(emmeans(glm_nb, specs = "elevation_category", type = "response"))
```
- Visualize predictions with confidence intervals

#### Section 8: Reporting Results
- **Example text**: "We used a negative binomial GLM to model endophyte richness as a function of elevation and host species. The model showed..."
- **Figure**: Predicted richness across elevation with confidence bands

#### Conclusion
- Recap: Different GLM families for different data structures
- Diagnostics are crucial
- Encourage: Always check assumptions, compare models

#### Further Reading
- Zuur et al. "A protocol for data exploration to avoid common statistical problems"
- Links to DHARMa vignettes
- Related tutorial on mixed models (if counts are nested)

#### Session Info

---

## Tutorial 4: Publication-Ready ggplots - A Style Guide

**File**: `blog/2025-XX-XX-ggplot-style-guide/index.qmd`  
**Estimated Read Time**: 15-18 minutes  
**Target Audience**: Graduate students, postdocs preparing manuscripts  
**Prerequisites**: Basic ggplot2 knowledge

### Outline

#### Introduction
- Hook: "Your plot is beautiful—but will it look good in a PDF manuscript?"
- Common issues: tiny fonts, bad color choices, unclear legends
- Preview: Typography, color, layout, export

#### Setup
```r
library(tidyverse)
library(patchwork)  # for multi-panel figures
library(scales)     # for better axis labels
library(ggtext)     # for richer text formatting
```

#### Section 1: Typography That Works
- **Font size**: Base size 12-14 for main plot, axis labels 10-12
```r
theme_set(theme_minimal(base_size = 12))
```
- **Font family**: Consider using consistent fonts across all figures
```r
theme_update(text = element_text(family = "Arial"))
```
- **Avoid**: Tiny text, overly decorative fonts

#### Section 2: Color Palettes for Accessibility
- **Colorblind-friendly palettes**
```r
# Viridis for continuous scales
scale_color_viridis_c()

# ColorBrewer for discrete scales
scale_color_brewer(palette = "Set2")

# Custom palette from rcartocolor
library(rcartocolor)
scale_color_carto_d(palette = "Safe")
```
- **Test**: Use colorblindr or online simulators to check
- **Callout**: Always provide shape or pattern as alternative to color

#### Section 3: Axis Labels and Legends
- **Clear, informative labels**
```r
labs(x = "Elevation (m a.s.l.)",
     y = "Endophyte richness (ASVs per sample)",
     color = "Host species")
```
- **Units**: Always include units in axis labels
- **Legend position**: Inside plot if space allows, otherwise right/bottom
```r
theme(legend.position = c(0.85, 0.15))
```

#### Section 4: Panel Structure for Multi-Panel Figures
```r
# Using patchwork
plot_a <- ggplot(data_a, aes(...)) + ...
plot_b <- ggplot(data_b, aes(...)) + ...

combined <- (plot_a | plot_b) +
  plot_annotation(tag_levels = 'A') +
  plot_layout(guides = 'collect')

combined
```
- **Consistent themes** across panels
- **Shared legends** when appropriate
- **Panel labels**: (A), (B), etc., for manuscript reference

#### Section 5: Aspect Ratios and Sizing
- **Golden ratio** (1:1.618) for single plots
- **Square** for scatterplots comparing two variables
- **Wide** for time series or gradients
```r
ggsave("figure1.png", width = 7, height = 5, dpi = 300)
```
- **Callout**: Journal-specific requirements (check author guidelines)

#### Section 6: Export Settings
- **DPI**: 300 for print, 150 for web
- **Format**: PNG for raster, PDF for vector (preferred for journals)
```r
ggsave("figure1.pdf", width = 7, height = 5, device = cairo_pdf)
```
- **Tip**: Use `cairo_pdf` for better font embedding

#### Section 7: Common Mistakes and Fixes
- **Overplotting**: Use alpha transparency or `geom_jitter()`
- **Too much ink**: Remove gridlines, simplify themes
- **Unclear comparisons**: Add reference lines or annotations

#### Section 8: Example Workflow
- Start with exploratory plot (quick and dirty)
- Refine for presentation (clear, polished)
- Finalize for publication (journal guidelines, accessibility)

#### Conclusion
- Recap: Good plots communicate clearly, look professional, and are accessible
- Encourage: Develop a personal style guide (color palette, theme) for consistency
- Tease: "Next up—interactive plots for exploration and outreach"

#### Resources
- R Graph Gallery: https://r-graph-gallery.com
- Fundamentals of Data Visualization (Claus Wilke): https://clauswilke.com/dataviz/
- ggplot2 book: https://ggplot2-book.org/

#### Session Info

---

## Tutorial 5: Git for Scientists - Version Control Without the Fear

**File**: `blog/2025-XX-XX-git-for-scientists/index.qmd`  
**Estimated Read Time**: 15-18 minutes  
**Target Audience**: Researchers new to version control  
**Prerequisites**: None (absolute beginner-friendly)

### Outline

#### Introduction
- Hook: "Have you ever saved files as `analysis_final_FINAL_v3_really_final.R`?"
- Version control solves this (and much more)
- Preview: Basic Git workflow, GitHub, collaboration

#### Section 1: Why Version Control Matters
- **Track changes**: See exactly what changed and when
- **Collaborate**: Multiple people on same project without emailing files
- **Backup**: Your code is safe on GitHub
- **Reproduce**: Return to any previous version of your analysis

#### Section 2: Git vs. GitHub (What's the Difference?)
- **Git**: Version control software on your computer
- **GitHub**: Online platform for hosting Git repositories
- Analogy: Git is like Word's "Track Changes," GitHub is like Google Drive

#### Section 3: Setting Up Git (First Time Only)
```bash
# Install Git (link to instructions by OS)

# Configure your identity
git config --global user.name "Bolívar Aponte Rolón"
git config --global user.email "your.email@example.com"
```

#### Section 4: Starting a Git Repository
```bash
# Option 1: Start from scratch
mkdir my_analysis
cd my_analysis
git init

# Option 2: Clone an existing repo
git clone https://github.com/username/repo_name.git
```

#### Section 5: The Basic Workflow (Add, Commit, Push)
```bash
# Make changes to your files (write code, add data, etc.)

# Check what's changed
git status

# Stage changes
git add script.R

# Commit with a message
git commit -m "Add data cleaning function"

# Push to GitHub
git push origin main
```
- **Visual**: Diagram of working directory → staging → commit → remote

#### Section 6: Viewing History
```bash
# See commit history
git log --oneline

# See what changed in a specific file
git diff script.R
```

#### Section 7: Branches for Experimentation
- **Conceptual**: Branches let you try new things without breaking your main code
```bash
# Create and switch to new branch
git checkout -b new_analysis_approach

# Work on this branch, commit changes

# Switch back to main
git checkout main

# Merge if you like the changes
git merge new_analysis_approach
```

#### Section 8: .gitignore - Don't Track Everything
```bash
# Create .gitignore file
*.RData
*.Rhistory
data/raw/  # Don't track large raw data files
```
- **Callout**: What to ignore (large files, sensitive data, temporary files)

#### Section 9: Collaborating on GitHub
- **Forking**: Making your own copy of someone else's repo
- **Pull requests**: Proposing changes to someone else's code
- **Issues**: Tracking bugs and feature requests

#### Section 10: Common Pitfalls and How to Fix Them
- "I committed the wrong file"—use `git reset`
- "I need to go back to an earlier version"—use `git checkout <commit>`
- "I have merge conflicts"—carefully resolve, commit, push

#### Conclusion
- Recap: Git = time machine for your code
- Encourage: Start small (one project), practice regularly
- Resources: Git cheatsheets, GitHub tutorials

#### Further Reading
- Happy Git with R (Jenny Bryan): https://happygitwithr.com/
- GitHub's Git guides
- Interactive Git tutorial: https://learngitbranching.js.org/

#### Section 11: Integrating Git with RStudio
- **Bonus**: RStudio has built-in Git support
- Visual tutorial on using Git pane in RStudio

---

## Next Steps for Implementation

### For Each Tutorial:
1. **Set up data**: Create simulated datasets or identify public datasets to use
2. **Test code**: Run every code chunk to ensure it works
3. **Add visuals**: Create figures, diagrams, or screenshots as needed
4. **Write narrative**: Fill in prose around code chunks
5. **Proofread**: Check for clarity, accuracy, and typos
6. **Get feedback**: Share draft with collaborator or peer

### Quarto Template for Sandbox Posts
Create a template file `blog/_post-template.qmd`:

```yaml
---
title: "Your Title Here"
subtitle: 'Optional subtitle'
author: "Bolívar Aponte Rolón"
date: MM/DD/YYYY
date-modified: MM/DD/YYYY
date-format: long
image: featured.png
image-alt: "Alt text describing the image"
categories:
  - R
  - Category2
  - Category3
number-sections: false
number-depth: 2
execute: 
  eval: true
  echo: true
  warning: false
  message: false
editor: 
  markdown: 
    wrap: 72
---

::: {.callout-note}
## Learning Objectives
After reading this tutorial, you will be able to:
- Objective 1
- Objective 2
- Objective 3
:::

## Introduction

[Hook and context]

## Setup

```{r}
#| label: setup
library(tidyverse)
# Other libraries
```

## Section 1

[Content]

## Conclusion

[Recap and next steps]

## Further Reading

- Link 1
- Link 2

## Session Info

```{r}
sessionInfo()
```
```

### Priority Order for Development
1. **Core Microbiomes** (builds directly on BRCore, showcases your work)
2. **Ordination** (widely applicable, complements existing ecology posts)
3. **Publication ggplots** (practical skill, shorter dev time)
4. **Git for Scientists** (foundational for reproducibility, no data prep needed)
5. **GLMs for Counts** (deeper stats, longer to develop)

### Content Calendar Suggestion
- **Month 1**: Draft and publish Core Microbiomes tutorial
- **Month 2**: Draft Ordination tutorial, launch "Tidy Tuesday in Ecology" mini series
- **Month 3**: Publish Ordination tutorial, draft Publication ggplots
- **Month 4**: Publish ggplots tutorial, draft Git for Scientists
- **Month 5**: Publish Git tutorial, begin GLMs for Counts
- **Month 6**: Review analytics, gather feedback, adjust strategy

---

**Document Version**: 1.0  
**Last Updated**: 2025-11-13  
**Ready for Implementation**: Yes—pick a tutorial and start coding!
