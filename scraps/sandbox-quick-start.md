# Sandbox Quick Start Guide

A practical checklist for implementing the Sandbox content strategy.

---

## Immediate Action Items (This Week)

### 1. Review Strategy Documents
- [ ] Read `sandbox-content-strategy.md` for full context
- [ ] Review `sandbox-draft-outlines.md` for detailed tutorial structures
- [ ] Choose 1-2 tutorials to start with (recommend: Core Microbiomes + Publication ggplots)

### 2. Prepare Data for Tutorials
**For Core Microbiomes tutorial:**
- [ ] Option A: Use BRCore built-in example data
- [ ] Option B: Simulate realistic microbiome data
- [ ] Option C: Request permission to use published data from collaborators
- [ ] Test that data loads and processes correctly

**For Ordination tutorial:**
- [ ] Use your endophyte data (anonymized if unpublished) OR
- [ ] Find suitable public dataset (e.g., from MicrobiomeDB, Qiita, or published papers with data on Figshare/Dryad)

**For Publication ggplots tutorial:**
- [ ] No specific data needed—can use built-in R datasets or generate synthetic data

### 3. Set Up Quarto Template
- [ ] Copy template from `sandbox-draft-outlines.md` to `blog/_post-template.qmd`
- [ ] Test template by creating a dummy post
- [ ] Customize theme/styling if needed (check `assets/styling/`)

### 4. Create Content Calendar
Simple spreadsheet or markdown file with:
- Tutorial title
- Target publish date
- Status (idea → outline → draft → review → published)
- Estimated time investment

---

## Tutorial Development Workflow

### Phase 1: Outline & Data (1-2 hours)
1. Copy relevant tutorial outline from `sandbox-draft-outlines.md`
2. Create blog post directory: `blog/YYYY-MM-DD-short-name/`
3. Set up `index.qmd` from template
4. Prepare data files in `blog/YYYY-MM-DD-short-name/data/` (if needed)

### Phase 2: Code Development (4-6 hours)
1. Write and test all code chunks
2. Generate all figures and save as needed
3. Create featured image (plot output, diagram, or graphic)
4. Add alt text to all images

### Phase 3: Writing (2-4 hours)
1. Fill in introductory narrative
2. Add explanatory text between code chunks
3. Write callout boxes, notes, and warnings
4. Craft conclusion and further reading section

### Phase 4: Review & Polish (1-2 hours)
1. Run entire document to ensure reproducibility
2. Check for typos, broken links, unclear explanations
3. Verify code output matches expectations
4. Test on mobile/responsive view (if possible)
5. Get peer feedback (optional but recommended)

### Phase 5: Publish (30 min)
1. Build site locally to preview
2. Commit and push to GitHub
3. Announce on social media (optional)
4. Monitor analytics and feedback

**Total Time per Tutorial**: 8-15 hours depending on complexity

---

## Repository Exploration Checklist

Before creating tutorials from your research repos, confirm:

### BRCore
- [ ] Check if there's example data in the package
- [ ] Review function documentation for tutorial examples
- [ ] Test key functions: `calculate_core()`, plotting functions
- [ ] Identify any vignettes already in package (avoid duplication)

### Endophyte Repos (endophyte_leaf_traits, endophytes_mimulus_genotype, endophytes_mimulus)
- [ ] Determine if data is publishable/shareable yet
- [ ] If not, create simulated data that mimics structure
- [ ] Review analysis scripts for tutorial-worthy workflows
- [ ] Check for existing figures that could be featured images

### Other Repos (CVR_BAR, lightSABR, mxg_genotype_exudates)
- [ ] Assess readiness for public tutorials
- [ ] Determine if data is sensitive or embargoed
- [ ] Identify 1-2 analyses that would make good tutorials

---

## Data Sharing Decision Tree

```
Is the data from published research?
├─ Yes → Can share openly (cite paper)
└─ No → Is the research in review or manuscript prep?
    ├─ Yes → Create simulated data OR wait until published
    └─ No → Is data owned by you/your lab?
        ├─ Yes → Check with advisor/PI, consider data use agreement
        └─ No → Use public data or simulate
```

**General Rule**: When in doubt, simulate or use public data. Reproducibility is the goal, not necessarily using your exact data.

---

## Simulating Data for Tutorials

### Microbiome Count Tables
```r
# Simple simulation
set.seed(123)
n_samples <- 30
n_asvs <- 150

# Create OTU table with realistic zeros
otu_table <- matrix(
  rnbinom(n_samples * n_asvs, size = 1, mu = 10),
  nrow = n_asvs,
  ncol = n_samples
)
# Add zeros to ~70% of entries
otu_table[sample(length(otu_table), 0.7 * length(otu_table))] <- 0

# Create sample metadata
metadata <- tibble(
  sample_id = paste0("Sample_", 1:n_samples),
  treatment = rep(c("Control", "Treatment_A", "Treatment_B"), each = 10),
  elevation = runif(n_samples, 1000, 3000)
)

# Create taxonomy table
taxonomy <- tibble(
  asv_id = paste0("ASV_", 1:n_asvs),
  kingdom = "Fungi",
  phylum = sample(c("Ascomycota", "Basidiomycota"), n_asvs, replace = TRUE),
  genus = sample(paste0("Genus_", 1:30), n_asvs, replace = TRUE)
)
```

### Count Data with Overdispersion
```r
# For GLM tutorials
set.seed(456)
n <- 100

data <- tibble(
  elevation = seq(1000, 3000, length.out = n),
  elevation_category = cut(elevation, breaks = 3, labels = c("Low", "Mid", "High")),
  host_species = sample(c("Species_A", "Species_B", "Species_C"), n, replace = TRUE),
  # Simulate overdispersed counts
  richness = rnbinom(n, size = 2, mu = exp(0.001 * elevation + rnorm(n, 0, 0.5)))
)
```

---

## Quality Checklist Before Publishing

### Content
- [ ] Clear learning objectives stated upfront
- [ ] Code is reproducible (all packages loaded, data available)
- [ ] Explanations are clear and jargon is defined
- [ ] Figures have descriptive captions and alt text
- [ ] Conclusion summarizes key takeaways
- [ ] Further reading links are relevant and working

### Technical
- [ ] All code chunks run without errors
- [ ] Warning/error messages are suppressed or explained
- [ ] Session info included at end
- [ ] Featured image is appropriate and sized correctly
- [ ] Categories/tags are relevant

### Accessibility
- [ ] Color palettes are colorblind-friendly
- [ ] Font sizes are readable
- [ ] Alt text provided for all images
- [ ] Code is formatted consistently

### SEO & Discoverability
- [ ] Title is descriptive and includes key terms
- [ ] Subtitle/description is informative
- [ ] Meta description is compelling (if custom meta supported)
- [ ] Categories match existing categories for consistency

---

## Building and Previewing Site

### Local Preview (Requires Quarto Installation)
```bash
# Navigate to repo root
cd /path/to/bolaponte_website

# Preview site (opens in browser)
quarto preview

# Build site without serving
quarto render
```

### Without Quarto Installed Locally
- Commit and push changes to GitHub
- Check GitHub Pages deployment (if set up)
- Review live site after CI/CD completes

---

## Community Engagement Strategy

### After Publishing a Tutorial

**Week 1**:
- [ ] Share on Twitter/X with #rstats, #bioinformatics, #microbiome hashtags
- [ ] Post in relevant Slack/Discord communities (e.g., R4DS, Bioinformatics)
- [ ] Email to collaborators who might find it useful

**Week 2-4**:
- [ ] Monitor comments/questions (if comments enabled)
- [ ] Respond to any GitHub issues or discussions
- [ ] Note which tutorials get most traffic (analytics)

**Month 2-3**:
- [ ] Review analytics: page views, time on page, referral sources
- [ ] Gather informal feedback: Did anyone mention the tutorial?
- [ ] Plan follow-up content based on interest

### Building a Community
- Consider starting a newsletter for tutorial announcements
- Create a "Suggest a Tutorial" form (Google Form or GitHub discussions)
- Invite guest posts from colleagues or students

---

## Troubleshooting Common Issues

### "My code works in RStudio but not when Quarto renders"
- Check working directory with `here::here()`
- Ensure all packages are loaded in setup chunk
- Verify data paths are correct
- Use `execute: eval: true` in YAML

### "Figures aren't showing up in rendered site"
- Check file paths (use relative paths from .qmd location)
- Ensure images are in correct directory (same as .qmd or specified assets folder)
- Verify image file extensions are lowercase (.png not .PNG)

### "Code takes too long to run during render"
- Set `execute: eval: false` and manually save outputs
- Use `freeze: true` in YAML to cache results
- Consider pre-computing expensive results and loading saved objects

### "Categories/listings aren't updating"
- Clear Quarto cache: `quarto clean`
- Rebuild entire site: `quarto render`
- Check YAML frontmatter for typos

---

## Measuring Success

### Quantitative Metrics (from Google Analytics)
- Page views per tutorial
- Average time on page (engagement indicator)
- Bounce rate (are people finding what they need?)
- Referral sources (where are readers coming from?)

### Qualitative Metrics
- Comments and questions (engagement and clarity)
- GitHub stars/forks on related repos (e.g., BRCore)
- Social media mentions and shares
- Emails or messages from readers
- Invitations to speak or teach on these topics

### Personal Metrics
- Your own learning and skill development
- Portfolio building (showcase to employers, collaborators)
- Community connections made through content

---

## Revision Schedule

### Every 6 Months
- Review most popular tutorials
- Check for outdated package versions or broken links
- Update code if packages have changed significantly
- Add "Last updated" note to post

### Every Year
- Audit entire Sandbox section
- Retire or archive outdated tutorials
- Refresh featured images or examples as needed
- Consider creating "v2" of popular tutorials with new approaches

---

## Resources & Links

### Quarto Documentation
- [Quarto Guide](https://quarto.org/docs/guide/)
- [Quarto Websites](https://quarto.org/docs/websites/)
- [Quarto Publishing](https://quarto.org/docs/publishing/)

### R & RStudio
- [R for Data Science (2e)](https://r4ds.hadley.nz/)
- [RStudio Cheatsheets](https://posit.co/resources/cheatsheets/)

### Visualization & Design
- [R Graph Gallery](https://r-graph-gallery.com)
- [Fundamentals of Data Visualization](https://clauswilke.com/dataviz/)
- [ColorBrewer](https://colorbrewer2.org/)

### Git & Version Control
- [Happy Git with R](https://happygitwithr.com/)
- [GitHub Docs](https://docs.github.com/)

### Microbial Ecology & Bioinformatics
- [phyloseq Documentation](https://joey711.github.io/phyloseq/)
- [vegan Tutorial](https://cran.r-project.org/web/packages/vegan/vignettes/intro-vegan.pdf)
- [QIIME2 Tutorials](https://docs.qiime2.org/)

---

## Next Tutorial Ideas (Parking Lot)

As you think of new tutorial ideas, add them here:

1. **Idea**: [Brief description]
   - **Repo/Source**: [Which repo or expertise area]
   - **Estimated Difficulty**: Beginner/Intermediate/Advanced
   - **Priority**: High/Medium/Low

2. **Idea**: Reproducible project structures with `here` and `renv`
   - **Repo/Source**: General workflow knowledge
   - **Estimated Difficulty**: Beginner
   - **Priority**: Medium

3. **Idea**: Phylogenetic signal in microbiome data
   - **Repo/Source**: BRCore, lightSABR
   - **Estimated Difficulty**: Advanced
   - **Priority**: Medium

---

## Final Checklist: Ready to Launch Sandbox?

- [ ] At least 1 tutorial drafted and tested
- [ ] Data prepared and accessible (simulated or public)
- [ ] Template created for future posts
- [ ] Content calendar established (even if rough)
- [ ] Analytics set up to track success
- [ ] Social media/announcement plan ready
- [ ] Excited and confident to share your expertise!

---

**You've got this!** Start with one tutorial, learn from the process, and iterate. The Sandbox is a space to experiment—imperfect is better than unpublished.

---

**Document Version**: 1.0  
**Last Updated**: 2025-11-13  
**Author**: Bolívar Aponte Rolón
