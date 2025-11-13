# Sandbox Section: Information Architecture & Navigation

This document outlines how the Sandbox section integrates with the overall website structure and proposes navigation improvements.

---

## Current Website Structure

```
bolaponte.com/
├── Home (index.qmd)
├── About (/about)
├── CV (/cv)
├── Research (/research)
├── Software (/software)
├── Sandbox (/blog)  ← CURRENTLY IMPLEMENTED AS "BLOG"
└── Contact (/contact)

Footer:
├── Code of Conduct (/coc)
├── Accessibility (/accessibility)
└── License (/license)
```

---

## Proposed Sandbox Section Structure

### Option A: Keep Flat Structure (Current)
```
/blog/ (Sandbox landing page)
├── index.qmd (listing of all posts)
├── 2024-10-15-map_purrr/
├── 2024-11-11-flexible_r/
├── [future tutorials]
└── _metadata.yml (shared settings)
```

**Pros**: Simple, easy to maintain, consistent with current setup  
**Cons**: May become unwieldy with 20+ posts; harder to filter by theme

### Option B: Organize by Theme (Recommended for Growth)
```
/blog/ (Sandbox landing page)
├── index.qmd (main listing, can filter by category)
├── r-programming/
│   ├── index.qmd (R-specific listing)
│   ├── 2024-10-15-map_purrr/
│   ├── 2024-11-11-flexible_r/
│   └── [future R posts]
├── microbial-ecology/
│   ├── index.qmd (Microbial ecology listing)
│   ├── core-microbiomes/
│   ├── ordination-basics/
│   └── [future ecology posts]
├── data-viz/
│   ├── index.qmd (Viz listing)
│   └── [viz posts]
├── workflows/
│   ├── index.qmd (Reproducibility listing)
│   ├── git-for-scientists/
│   └── [workflow posts]
└── _metadata.yml
```

**Pros**: Clear organization, easy to find related content, scalable  
**Cons**: More complex structure, requires refactoring existing posts

### Option C: Hybrid Approach (Recommended for Now)
```
/blog/ (flat structure for posts)
├── index.qmd (main listing with category filters)
├── 2024-10-15-map_purrr/ (category: R, tidyverse)
├── 2024-11-11-flexible_r/ (category: R, Linux)
├── core-microbiomes/ (category: Microbial Ecology, R)
└── ...

/blog/topics/ (category landing pages)
├── r-programming.qmd (filtered listing)
├── microbial-ecology.qmd (filtered listing)
├── data-viz.qmd (filtered listing)
└── workflows.qmd (filtered listing)
```

**Pros**: Keeps posts flat for simplicity, adds discoverability via topic pages  
**Cons**: Still need to maintain category consistency

**Recommendation**: Start with Option C (hybrid) for flexibility.

---

## Navigation Enhancements

### Current Navbar (from _quarto.yml)
```
- text: Sandbox
  icon: box
  href: blog/index.qmd
```

### Proposed Enhancement 1: Dropdown Menu
```yaml
- text: Sandbox
  icon: box
  menu:
    - text: "All Posts"
      href: blog/index.qmd
    - text: "---"  # separator
    - text: "By Topic"
    - text: "R Programming"
      href: blog/topics/r-programming.qmd
    - text: "Microbial Ecology"
      href: blog/topics/microbial-ecology.qmd
    - text: "Data Visualization"
      href: blog/topics/data-viz.qmd
    - text: "Reproducible Workflows"
      href: blog/topics/workflows.qmd
```

**Visual Preview**:
```
[Sandbox ▼]
  All Posts
  ──────────
  By Topic
  R Programming
  Microbial Ecology
  Data Visualization
  Reproducible Workflows
```

### Proposed Enhancement 2: Landing Page with Cards
Keep navbar simple, but enhance `blog/index.qmd` with topic cards:

```markdown
---
title: "Sandbox: Experiment Freely, Learn Endlessly"
---

## Featured Topics

::: {.grid}
::: {.g-col-6}
### R Programming
From tidyverse tricks to package development
[Explore R Tutorials →](/blog/topics/r-programming.html)
:::

::: {.g-col-6}
### Microbial Ecology
Core microbiomes, ordination, diversity analysis
[Explore Ecology Tutorials →](/blog/topics/microbial-ecology.html)
:::
:::

[Continue with listing of all posts]
```

**Recommendation**: Implement Enhancement 2 first (better UX, no navbar clutter).

---

## URL Strategy

### Current Pattern
`/blog/YYYY-MM-DD-slug/index.qmd` → renders to `/blog/YYYY-MM-DD-slug/`

**Pros**: Date in URL helps with provenance  
**Cons**: URLs are long, dates may not be meaningful to readers

### Alternative Pattern
`/blog/slug/index.qmd` → renders to `/blog/slug/`

**Pros**: Cleaner URLs, focus on content not date  
**Cons**: Harder to track post chronology from URL alone

**Recommendation**: Keep current pattern for now (consistency), but hide dates in listing display if desired.

---

## Taxonomy & Metadata Strategy

### Current Categories (from existing posts)
- R
- tidyverse
- purrr
- Linux
- OpenBLAS
- Computer Science

### Proposed Category Hierarchy

**Level 1 (Broad themes)**: Use for main filtering
- R Programming
- Python Programming
- Microbial Ecology
- Bioinformatics
- Data Visualization
- Statistics
- Reproducible Workflows
- Command-Line Tools

**Level 2 (Specific topics)**: Use as tags for detailed filtering
- tidyverse, ggplot2, purrr, data.table (R subcategories)
- Core microbiome, Ordination, Diversity, Phylogenetics (Ecology subcategories)
- Git, Docker, Snakemake, Quarto (Workflow subcategories)
- etc.

### Proposed YAML Frontmatter Standard
```yaml
---
title: "Tutorial Title"
subtitle: "Optional subtitle"
author: "Bolívar Aponte Rolón"
date: MM/DD/YYYY
date-modified: MM/DD/YYYY
categories:
  - R Programming          # Level 1
  - Microbial Ecology      # Level 1 (if applicable)
tags:
  - tidyverse              # Level 2
  - core microbiome        # Level 2
difficulty: "Beginner"     # New field
read-time: "15 min"        # New field (estimate)
---
```

**New fields** (`difficulty`, `read-time`) help readers choose appropriate content.

---

## Search & Discovery Features

### Current Setup
- Category filtering (in `blog/index.qmd` listing)
- No full-text search (Quarto limitation without plugin)

### Proposed Enhancements

#### 1. Improved Category Filtering
Enable interactive category filter in listing:
```yaml
listing:
  filter-ui: true  # Add this!
  sort-ui: true
  categories: true
```

#### 2. "Related Posts" Section
At bottom of each tutorial, automatically show 3 related posts based on shared categories:
```markdown
## Related Tutorials
[Auto-generated listing of posts with matching categories]
```

#### 3. "Start Here" Page
Create `blog/start-here.qmd` with curated paths:
```markdown
# New to the Sandbox?

## Learning Paths

### For R Beginners
1. Git for Scientists
2. Functional Programming with purrr
3. Publication-Ready ggplots

### For Microbial Ecologists
1. Understanding Core Microbiomes
2. Community Composition Analysis
3. Linear Models for Counts

### For Workflow Enthusiasts
1. Git for Scientists
2. R Package Development 101
3. Containerizing with Docker
```

**Recommendation**: Implement "Start Here" page after 5-10 tutorials are published.

---

## Content Promotion Strategy

### On Homepage (`index.qmd`)
Add a "Latest from the Sandbox" section:
```markdown
## Latest from the Sandbox

::: {.latest-posts}
[Auto-listing of 3 most recent blog posts with thumbnails]
:::

[Explore all tutorials →](/blog/)
```

### On Research Page (`research/index.qmd`)
Link to related tutorials:
```markdown
#### Learn More
Explore related tutorials in the Sandbox:
- [Understanding Core Microbiomes](/blog/core-microbiomes/)
- [Ordination Analysis](/blog/ordination-basics/)
```

### On Software Page (`software/index.qmd`)
Link to package-specific tutorials:
```markdown
## BRCore Tutorials
- [Getting Started with BRCore](/blog/core-microbiomes/)
- [Advanced Core Analysis Workflows](/blog/core-microbiomes-advanced/)
```

**Recommendation**: Cross-link liberally to drive discovery and show integration of content.

---

## RSS Feed & Subscriptions

### Current Status
Quarto generates RSS feed automatically at `/blog/index.xml`

### Enhancements
1. **Promote RSS feed**: Add subscribe button to blog landing page
2. **Email newsletter**: Consider Mailchimp, Substack, or Buttondown integration
3. **Social media automation**: Use IFTTT or Zapier to auto-post new tutorials

**Recommendation**: Add RSS subscription link first (low effort, high value).

---

## Accessibility & Inclusive Design

### Navigation
- [x] Keyboard navigation supported (Quarto default)
- [x] ARIA labels for icons (check navbar)
- [ ] Skip-to-content link (add if not present)

### Content Discovery
- [ ] Descriptive category names (avoid jargon)
- [ ] Difficulty indicators (beginner-friendly signaling)
- [ ] Estimated read times (respect readers' time)

### Visual Design
- [ ] Consistent featured image style across posts
- [ ] High contrast for readability
- [ ] Responsive layout for mobile (Quarto handles this)

**Recommendation**: Add difficulty indicators and read times to improve UX.

---

## Analytics & Success Tracking

### Key Metrics to Track
- **Top posts**: Which tutorials get most views?
- **Engagement**: Time on page, scroll depth
- **Referrals**: Where are readers coming from?
- **Demographics**: Geographic distribution, devices

### Google Analytics Events to Set Up
- Download links (if you provide data/code downloads)
- External link clicks (to repos, papers, etc.)
- Category filter interactions

**Recommendation**: Review analytics monthly, adjust content strategy quarterly.

---

## SEO Optimization

### Current Status
- [x] Google Analytics configured (`_quarto.yml`)
- [x] Open Graph tags (basic, from Quarto)
- [ ] Custom meta descriptions per post
- [ ] Twitter Cards (enhanced social sharing)

### Proposed Enhancements

#### 1. Custom Meta Descriptions
Add to each tutorial's YAML:
```yaml
description: "Learn how to identify core microbiome members using BRCore. This beginner-friendly tutorial covers prevalence thresholds, abundance filtering, and visualization in R."
```

#### 2. Twitter Card Metadata
```yaml
twitter-card:
  image: "featured.png"
  creator: "@yourtwitterhandle"
```

#### 3. Sitemap.xml
Quarto generates this automatically—ensure it's submitted to Google Search Console.

**Recommendation**: Add custom descriptions for top 5 most popular posts first.

---

## Content Lifecycle Management

### New Posts
1. Draft → Review → Publish
2. Announce via social media and email (if applicable)
3. Cross-link from related pages (Research, Software)

### Aging Posts (6+ months old)
1. Review for outdated code or broken links
2. Update if package versions have changed significantly
3. Add "Last updated" badge if content refreshed

### Retired Posts (2+ years old, if outdated)
1. Archive or redirect to updated version
2. Add notice: "This tutorial covers R 3.x; see [updated version] for R 4.x"

**Recommendation**: Set calendar reminder every 6 months to audit content.

---

## Future Expansion Ideas

### Interactive Features
- **Shiny apps**: Embed interactive plots or calculators in tutorials
- **Code sandboxes**: Use WebR or similar for in-browser R code execution
- **Quizzes**: Test understanding at end of tutorials

### Community Contributions
- **Guest posts**: Invite collaborators or students to contribute
- **Community showcase**: Feature projects that used your tutorials
- **Tutorial requests**: GitHub issues or form for topic suggestions

### Multimedia
- **Video walkthroughs**: Record screencasts for complex tutorials
- **Podcast**: Discuss methods and workflows (audio format)
- **Infographics**: Visual summaries of key concepts

**Recommendation**: Start with text-based tutorials, add interactivity as you scale.

---

## Sitemap Visualization

```
Home
  └─ "Latest from Sandbox" widget

About
  └─ Link to personal blog posts (optional)

CV
  └─ "Teaching & Workshops" section links to tutorials

Research
  └─ Links to method-specific tutorials
      ├─ Core microbiomes tutorial
      ├─ Ordination tutorial
      └─ G×E tutorial

Software
  └─ Package-specific tutorials
      ├─ BRCore tutorials
      └─ esipDMP tutorials

Sandbox ← YOU ARE HERE
  ├─ Landing page (all posts)
  ├─ Topic pages (filtered listings)
  │   ├─ R Programming
  │   ├─ Microbial Ecology
  │   ├─ Data Visualization
  │   └─ Reproducible Workflows
  ├─ Start Here (curated learning paths)
  └─ Individual tutorial posts
      ├─ Featured image
      ├─ Metadata (date, categories, read time, difficulty)
      ├─ Content (code + narrative)
      ├─ Related posts
      └─ Comments (optional)

Contact
  └─ "Questions about a tutorial? Reach out!"
```

---

## Implementation Priority

### Phase 1 (Immediate)
1. ✅ Create content strategy document (done)
2. ✅ Create tutorial outlines (done)
3. ✅ Create quick start guide (done)
4. [ ] Choose first tutorial to develop
5. [ ] Set up post template
6. [ ] Draft and publish first tutorial

### Phase 2 (Month 2-3)
1. [ ] Add topic filtering to blog landing page
2. [ ] Create "Start Here" page
3. [ ] Add "Related Posts" to tutorials
4. [ ] Enable category filter UI in listing

### Phase 3 (Month 4-6)
1. [ ] Create topic landing pages
2. [ ] Add cross-links from Research and Software pages
3. [ ] Implement RSS subscribe button
4. [ ] Review analytics and adjust

### Phase 4 (6+ months)
1. [ ] Consider dropdown menu in navbar
2. [ ] Add difficulty indicators and read times
3. [ ] Explore interactive features
4. [ ] Plan community contribution framework

---

## Conclusion

The Sandbox section has strong potential to become a signature part of your website—showcasing your expertise, helping the community, and building your professional brand. Start simple with flat structure and good categorization, then scale up navigation and discovery features as content grows.

**Next Step**: Pick your first tutorial and start coding! 🚀

---

**Document Version**: 1.0  
**Last Updated**: 2025-11-13  
**Author**: Bolívar Aponte Rolón (with AI assistance)
