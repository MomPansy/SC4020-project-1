# Qualitative Cluster Evaluation Report

## DBSCAN Product Clustering Analysis

---

## Executive Summary

This report presents a comprehensive qualitative evaluation of DBSCAN clustering applied to product title data from PriceRunner. The analysis examines cluster quality through multiple dimensions including cluster size distribution, semantic coherence, purity metrics, and manual inspection. The goal is to assess whether DBSCAN successfully groups similar product listings together in a meaningful way that aligns with both ground-truth labels and human judgment.

---

## 1. Purpose of Analysis

### 1.1 Overview

The purpose of this qualitative analysis is to evaluate how "pure" and semantically coherent each DBSCAN cluster is—that is, what proportion of items within a predicted cluster originate from the same ground-truth (GT) cluster and whether the grouped products make logical sense from a domain perspective. This measure complements earlier external metrics (e.g., ARI and NMI) by providing a fine-grained view of cluster consistency at the individual-cluster level.

### 1.2 Concrete Example

For example, a perfectly pure and semantically coherent cluster would contain only listings belonging to a single product variant such as:

- `"apple iphone 8 plus 64gb silver"`
- `"apple iphone 8 plus 64 gb spacegrau"`
- `"apple mq8n2b/a iphone 8 plus 64gb 5.5 12mp sim free smartphone in gold"`

All three listings should be correctly grouped under one product cluster, as they represent the same product model (iPhone 8 Plus 64GB) with only minor variations in color and merchant description style.

### 1.3 Rationale

While quantitative metrics like Adjusted Rand Index (ARI) and Normalized Mutual Information (NMI) provide statistical measures of clustering quality, they may not fully capture:

1. **Semantic coherence**: Do products in a cluster actually make sense together?
2. **Practical utility**: Would these groupings be useful for downstream tasks (duplicate detection, product recommendation, catalog management)?
3. **Edge case handling**: How does DBSCAN treat outliers and rare products?
4. **Cluster interpretability**: Can domain experts understand and trust the clustering results?

This qualitative analysis addresses these gaps through direct inspection, visualization, and domain-relevant evaluation criteria.

---

## 2. Methodology

### 2.1 Analysis Framework

The qualitative evaluation consists of seven complementary analyses:

#### 2.1.1 Cluster Size Distribution Analysis

**Objective**: Understand the distribution of cluster sizes to identify whether DBSCAN creates a few large clusters or many small clusters.

**Method**:

- Calculate size distribution for all non-noise clusters
- Visualize top 30 largest clusters via bar chart
- Generate histogram of cluster sizes with logarithmic scale
- Compute descriptive statistics (mean, median, percentiles)

**Interpretation Guide**:

- **Balanced distribution**: Suggests DBSCAN captures both common and niche products
- **Few dominant clusters**: May indicate over-generalization or parameter issues
- **Many micro-clusters**: May suggest overly strict similarity thresholds

#### 2.1.2 Qualitative Cluster Inspection (Large Clusters)

**Objective**: Examine the top 5 largest clusters to assess semantic coherence among high-volume product groups.

**Method**:

- Select 5 largest DBSCAN clusters by member count
- For each cluster:
  - Display ground-truth cluster distribution (top 5 GT clusters)
  - Sample 15 random product titles
  - Show GT cluster label for each product
- Manual assessment of product similarity

**Evaluation Criteria**:

- Do all products share a common category/brand/model?
- Are there obvious mismatches or irrelevant products?
- Does the dominant GT cluster make sense?

#### 2.1.3 Qualitative Cluster Inspection (Medium & Small Clusters)

**Objective**: Assess whether smaller clusters exhibit higher purity due to stricter density requirements.

**Method**:

- Define size ranges:
  - Medium: 10-30 products
  - Small: 4-9 products
  - Tiny: 3 products
- Sample 3 random clusters from medium range, 3 from small, 2 from tiny
- Display all products (or up to 15 for larger samples)
- Evaluate coherence within size categories

**Hypothesis**: Smaller clusters may have higher semantic coherence because DBSCAN requires tighter density thresholds for core point formation.

#### 2.1.4 Noise Point Analysis

**Objective**: Determine whether products labeled as "noise" (-1) are genuinely unique outliers or misclassified products.

**Method**:

- Extract all noise points (DBSCAN_Cluster = -1)
- Analyze GT cluster distribution among noise points
- Sample 20 random noise products for inspection
- Assess whether noise points have:
  - Highly specific/verbose titles
  - Rare product categories
  - Sparse GT cluster representation

**Interpretation**:

- **High proportion from singleton GT clusters**: Noise detection working correctly
- **High proportion from large GT clusters**: Potential parameter tuning issue

#### 2.1.5 Cluster Purity Analysis

**Objective**: Quantify cluster homogeneity by measuring the proportion of the dominant GT cluster within each DBSCAN cluster.

**Definition**:
$$\text{Purity}(C_i) = \frac{\max_{j} |C_i \cap GT_j|}{|C_i|}$$

Where:

- $C_i$ = DBSCAN cluster $i$
- $GT_j$ = Ground truth cluster $j$
- Purity ranges from 0 (completely mixed) to 1 (perfectly pure)

**Method**:

1. For each DBSCAN cluster:

   - Count products from each GT cluster
   - Identify dominant GT cluster (largest count)
   - Calculate purity = (dominant GT count) / (total cluster size)
   - Record number of distinct GT clusters merged

2. Generate four visualizations:

   - **Purity distribution histogram**: Shows spread of purity scores
   - **Purity vs. cluster size scatter plot**: Reveals size-quality relationship
   - **GT cluster diversity histogram**: How many GT clusters merged per DBSCAN cluster
   - **Cumulative purity distribution**: Percentage of clusters above purity thresholds

3. Report statistics:
   - Mean/median/std of purity scores
   - Percentage of clusters with purity ≥ 0.9, 0.8, 0.7, 0.5
   - Top 10 most pure clusters
   - Top 10 least pure clusters

**Interpretation Thresholds**:

- **Purity ≥ 0.9**: Excellent—cluster is nearly homogeneous
- **0.7 ≤ Purity < 0.9**: Good—cluster has clear dominant category
- **0.5 ≤ Purity < 0.7**: Moderate—cluster mixes multiple categories
- **Purity < 0.5**: Poor—cluster is highly heterogeneous

#### 2.1.6 Word Cloud Visualization

**Objective**: Visually assess cluster themes by identifying dominant terms in product titles.

**Method**:

- Generate word clouds for top 6 largest clusters
- Configure word cloud parameters:
  - Max 50 words per cloud
  - Viridis colormap
  - Background: white
  - Lowercase normalization
- Display in 3×2 grid for comparison

**Interpretation**:

- **Coherent word clouds**: Show clear brand/model/category terms (e.g., "iphone", "64gb", "plus")
- **Diffuse word clouds**: Indicate mixed or heterogeneous clusters
- **Term frequency**: Dominant words should align with product category

#### 2.1.7 Manual Coherence Scoring

**Objective**: Human-in-the-loop validation by manually rating cluster coherence on a stratified sample.

**Method**:

1. Stratified sampling:

   - 3 clusters from large size tier (≥75th percentile)
   - 4 clusters from medium size tier (25th-75th percentile)
   - 3 clusters from small size tier (<25th percentile)

2. For each sampled cluster:

   - Display up to 15 product titles
   - Show purity score and GT cluster distribution
   - Prompt for manual coherence rating:
     - **High**: Most/all products clearly related
     - **Medium**: Products somewhat related or mixed
     - **Low**: Products appear random/unrelated

3. Aggregate ratings to estimate overall clustering quality

**Scoring Guidance**:

- Consider product category, brand, model, attributes
- Tolerate minor variations (color, merchant-specific wording)
- Flag clear mismatches (different product categories)

### 2.2 Data and Parameters

- **Dataset**: PriceRunner product catalog (35,311 products)
- **Ground Truth**: 13,233 labeled product clusters
- **Feature Representation**: TF-IDF vectors (500 features, cosine distance)
- **DBSCAN Parameters**: Optimized via grid search (eps, min_samples)
- **Evaluation Framework**: Combination of automated metrics and manual inspection

---

## 3. Results

### 3.1 Cluster Size Distribution

**Key Statistics**:

- Total DBSCAN clusters formed: [To be populated from notebook execution]
- Largest cluster size: [To be populated]
- Smallest cluster size: [To be populated]
- Mean cluster size: [To be populated]
- Median cluster size: [To be populated]

**Distribution Characteristics**:
The cluster size distribution exhibits a [heavy-tailed/balanced/uniform] pattern, where:

- Top 10% of clusters contain [X]% of all clustered products
- [Y]% of clusters have fewer than 5 members
- [Z]% of clusters have 10+ members

**Interpretation**:
[This section will be populated based on actual results. Example interpretations:]

- **If heavily skewed**: DBSCAN identifies a few dominant product categories while treating most products as small clusters or noise, which may reflect the natural sparsity of the product catalog.

- **If balanced**: DBSCAN successfully captures product diversity at multiple scales, suggesting good parameter tuning.

### 3.2 Large Cluster Inspection

**Findings from Top 5 Largest Clusters**:

[This section will contain specific examples from notebook execution, formatted as:]

**Cluster 1** (Size: [X] products, Purity: [Y]):

- **Dominant GT Cluster**: [Category name] ([Z]% of cluster)
- **Sample Products**:
  1. [Product title 1]
  2. [Product title 2]
  3. [Product title 3]
     ...
- **Assessment**: [High/Medium/Low coherence] - [Reasoning]

[Repeat for clusters 2-5]

**Overall Observations**:

- [Observation about brand dominance]
- [Observation about attribute consistency]
- [Observation about misclassifications, if any]

### 3.3 Medium and Small Cluster Inspection

**Medium Clusters (10-30 products)**:

- Number of clusters in range: [X]
- Average purity: [Y]
- Coherence assessment: [Summary of inspection findings]
- Representative example: [Brief description]

**Small Clusters (4-9 products)**:

- Number of clusters in range: [X]
- Average purity: [Y]
- Coherence assessment: [Summary]
- Notable finding: [Key insight]

**Tiny Clusters (3 products)**:

- Number of clusters in range: [X]
- Typical characteristics: [Description]
- Quality assessment: [Finding]

**Key Insight**:
[Analysis of whether smaller clusters exhibit higher purity/coherence as hypothesized]

### 3.4 Noise Point Analysis

**Statistics**:

- Total noise points: [X] ([Y]% of dataset)
- Unique GT clusters represented in noise: [Z]
- Top GT cluster in noise: [Category name] ([N] products, [M]% of noise)

**Sample Noise Products**:
[Examples of 5-10 noise products with brief commentary]

**Interpretation**:
The noise ratio of [Y]% is [reasonable/high/low] for DBSCAN on this dataset. Analysis reveals that noise points predominantly consist of:

1. **[Finding 1]**: [Explanation with examples]
2. **[Finding 2]**: [Explanation with examples]
3. **[Finding 3]**: [Explanation with examples]

**Assessment**: Noise handling is [appropriate/needs adjustment] because [reasoning].

### 3.5 Cluster Purity Analysis

#### 3.5.1 Quantitative Results

**Overall Purity Statistics**:

- Mean purity: [X.XXX]
- Median purity: [X.XXX]
- Standard deviation: [X.XXX]

**Purity Distribution**:

- Clusters with purity ≥ 0.9 (Excellent): [X] ([Y]%)
- Clusters with purity ≥ 0.8 (Good): [X] ([Y]%)
- Clusters with purity ≥ 0.7 (Acceptable): [X] ([Y]%)
- Clusters with purity ≥ 0.5 (Moderate): [X] ([Y]%)
- Clusters with purity < 0.5 (Poor): [X] ([Y]%)

**GT Cluster Merging**:

- Mean GT clusters per DBSCAN cluster: [X.X]
- Median GT clusters per DBSCAN cluster: [X]
- Maximum GT clusters in single DBSCAN cluster: [X]
- Pure clusters (only 1 GT cluster): [X] ([Y]%)

#### 3.5.2 Top Performing Clusters

**Top 10 Most Pure Clusters**:
| Cluster ID | Size | Purity | Dominant Category |
|------------|------|--------|-------------------|
| [X] | [Y] | [0.XXX] | [Category] |
| ... | ... | ... | ... |

**Analysis**: The highest-purity clusters predominantly contain [observation about common characteristics].

#### 3.5.3 Lowest Performing Clusters

**Top 10 Least Pure Clusters**:
| Cluster ID | Size | Purity | GT Clusters Mixed |
|------------|------|--------|-------------------|
| [X] | [Y] | [0.XXX] | [N] |
| ... | ... | ... | ... |

**Analysis**: Low-purity clusters tend to [observation about causes—e.g., merge semantically similar but distinct products, mix general terms].

#### 3.5.4 Purity vs. Cluster Size Relationship

**Finding**: [Positive/Negative/No clear] correlation between cluster size and purity (correlation coefficient: [X.XX]).

**Interpretation**:

- **If positive correlation**: Larger clusters tend to be more homogeneous, suggesting DBSCAN successfully identifies dominant product categories.
- **If negative correlation**: Smaller clusters are purer, indicating DBSCAN's density threshold effectively isolates niche products.
- **If no correlation**: Purity is independent of size, suggesting other factors (feature quality, product diversity) dominate.

### 3.6 Word Cloud Insights

**Cluster 1** (Size: [X]):

- **Dominant terms**: [term1], [term2], [term3]
- **Interpretation**: [Analysis of whether terms form coherent product category]

**Cluster 2** (Size: [X]):

- **Dominant terms**: [term1], [term2], [term3]
- **Interpretation**: [Analysis]

[Continue for clusters 3-6]

**Cross-Cluster Comparison**:

- [Observation about term uniqueness across clusters]
- [Observation about brand/model specificity]
- [Observation about attribute representation]

**Overall Assessment**: Word clouds [do/do not] reveal semantically coherent themes, suggesting that TF-IDF features [successfully/partially/inadequately] capture product similarity.

### 3.7 Manual Coherence Scoring Results

**Stratified Sample Evaluation** (10 clusters):

| Size Tier | Cluster ID | Size | Purity | Manual Rating   | Notes        |
| --------- | ---------- | ---- | ------ | --------------- | ------------ |
| Large     | [X]        | [Y]  | [0.XX] | High/Medium/Low | [Brief note] |
| Large     | [X]        | [Y]  | [0.XX] | High/Medium/Low | [Brief note] |
| ...       | ...        | ...  | ...    | ...             | ...          |

**Aggregated Results**:

- **High coherence**: [X]/10 clusters ([Y]%)
- **Medium coherence**: [X]/10 clusters ([Y]%)
- **Low coherence**: [X]/10 clusters ([Y]%)

**Inter-rater Reliability**: [If multiple evaluators, report agreement statistics]

**Qualitative Observations**:

1. [Key observation from manual inspection]
2. [Key observation]
3. [Key observation]

**Alignment with Quantitative Metrics**:
Manual ratings [strongly/moderately/weakly] correlate with purity scores (Spearman ρ = [X.XX]), suggesting that purity is [a good/an imperfect/a poor] proxy for human-perceived cluster quality.

---

## 4. Interpretation and Discussion

### 4.1 Overall Clustering Quality Assessment

Based on the comprehensive qualitative evaluation, DBSCAN clustering of product titles demonstrates:

**Strengths**:

1. **[Finding 1]**: [Explanation with evidence from multiple analyses]
2. **[Finding 2]**: [Explanation]
3. **[Finding 3]**: [Explanation]

**Weaknesses**:

1. **[Finding 1]**: [Explanation with evidence]
2. **[Finding 2]**: [Explanation]
3. **[Finding 3]**: [Explanation]

### 4.2 Comparison with Quantitative Metrics

The qualitative analysis [confirms/contradicts/nuances] findings from quantitative metrics:

- **ARI Score ([X.XX])**: [How qualitative findings relate to ARI]
- **NMI Score ([X.XX])**: [How qualitative findings relate to NMI]
- **Silhouette Score ([X.XX])**: [How qualitative findings relate to internal metrics]

**Key Insight**: [Discussion of any discrepancies between human judgment and automated metrics, and what this reveals about the clustering task]

### 4.3 Practical Implications

#### 4.3.1 For Product Duplicate Detection

- **Suitability**: [High/Moderate/Low]
- **Reasoning**: [Analysis based on purity and coherence findings]
- **Recommendation**: [Specific guidance]

#### 4.3.2 For Product Recommendation Systems

- **Suitability**: [High/Moderate/Low]
- **Reasoning**: [Analysis]
- **Recommendation**: [Guidance]

#### 4.3.3 For Catalog Management

- **Suitability**: [High/Moderate/Low]
- **Reasoning**: [Analysis]
- **Recommendation**: [Guidance]

### 4.4 Methodological Considerations

**Limitations of This Analysis**:

1. **Subjective evaluation**: Manual coherence scoring reflects evaluator's domain knowledge and may vary
2. **Sample size**: Qualitative inspection covers [X]% of clusters; findings may not generalize to all clusters
3. **Feature representation**: TF-IDF may not capture semantic nuances (synonyms, abbreviations)
4. **Ground truth assumptions**: GT clusters from PriceRunner may themselves have inconsistencies

**Validation Considerations**:

- [Discussion of how findings could be validated]
- [Suggestions for additional analyses]

---

## 5. Conclusions and Recommendations

### 5.1 Summary of Findings

**Primary Question**: Can DBSCAN successfully cluster similar product titles together?

**Answer**: [Clear statement based on comprehensive evidence]

DBSCAN clustering achieves [excellent/good/moderate/poor] performance on product title clustering, characterized by:

- Average cluster purity of [X.XX], with [Y]% of clusters exceeding 0.7 purity threshold
- [Z]% noise ratio, predominantly capturing [genuine outliers/misclassified products]
- [Strong/Moderate/Weak] semantic coherence in manual inspection
- [Consistent/Inconsistent] word cloud themes across clusters

### 5.2 Recommendations for Improvement

If clustering quality is insufficient for the target application, consider:

#### 5.2.1 Parameter Tuning

- **Current parameters**: eps=[X], min_samples=[Y]
- **Suggested adjustments**: [Specific recommendations based on findings]
- **Expected impact**: [Predicted outcome]

#### 5.2.2 Feature Engineering

- **Current approach**: TF-IDF with 500 features
- **Alternative 1**: Sentence embeddings (BERT, Sentence-BERT) for semantic similarity
- **Alternative 2**: Hybrid features incorporating product attributes (brand, category, price)
- **Expected impact**: [Analysis]

#### 5.2.3 Algorithm Selection

- **HDBSCAN**: Adapts to varying density; may handle multi-scale product catalog better
- **Hierarchical clustering**: Provides cluster hierarchy for flexible granularity
- **Expected impact**: [Analysis]

#### 5.2.4 Post-processing

- **Cluster merging**: Combine high-purity small clusters with similar themes
- **Noise reassignment**: Apply nearest-neighbor classification for high-confidence noise points
- **Expected impact**: [Analysis]

### 5.3 Final Verdict

For the specific use case of [duplicate detection/recommendation/catalog management], DBSCAN clustering is:

**[RECOMMENDED / RECOMMENDED WITH MODIFICATIONS / NOT RECOMMENDED]**

**Justification**: [Concise summary tying together quantitative metrics, qualitative findings, and practical requirements]

---

## 6. Appendices

### Appendix A: Evaluation Criteria Summary

**Good Signs (DBSCAN is Working)**:

- ✅ High purity scores (≥0.7) for most clusters
- ✅ Cluster samples show clear product similarity
- ✅ Noise points are genuinely unique/outlier products
- ✅ Word clouds show coherent themes per cluster
- ✅ Manual inspection reveals meaningful groupings

**Warning Signs (May Need Adjustments)**:

- ⚠️ Low average purity (<0.5)
- ⚠️ Clusters mix unrelated products
- ⚠️ Too many noise points (>40%)
- ⚠️ Manual inspection shows random groupings
- ⚠️ Word clouds are diffuse without clear themes

### Appendix B: What to Try Next

If results are unsatisfactory:

1. **Adjust parameters** - Try different eps/min_samples from grid search
2. **Better embeddings** - Use BERT/Sentence-BERT instead of TF-IDF
3. **Try HDBSCAN** - Adapts to varying density automatically
4. **Feature engineering** - Add brand, category, price information
5. **Domain-specific preprocessing** - Normalize product attributes, expand abbreviations
6. **Ensemble methods** - Combine multiple clustering approaches
7. **Accept the results** - If clusters make business sense, low ARI is acceptable!

### Appendix C: Reproducibility

**Environment**:

- Python version: [X.X.X]
- scikit-learn version: [X.X.X]
- Key libraries: pandas, numpy, matplotlib, seaborn, wordcloud

**Random Seeds**:

- Cluster sampling: `random_state=42`
- Ensures reproducible inspection results

**Data**:

- Source: `pricerunner_aggregate.csv`
- Preprocessing: [Description of any cleaning/normalization]

---

## Document Metadata

- **Report Generated**: [Date]
- **Analysis Notebook**: `final-dbscan-evaluation.ipynb`
- **Dataset Version**: PriceRunner Product Catalog
- **Evaluator(s)**: [Name(s)]
- **Review Status**: [Draft/Final]

---

**Note**: Sections marked with `[To be populated]` or `[X]` placeholders will be filled with actual results upon notebook execution. This template provides the analytical framework and interpretation guidelines for comprehensive qualitative evaluation.
