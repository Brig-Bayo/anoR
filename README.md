# anoR

# anoR: Comprehensive ANOVA Analysis with Automated Test Selection

[![CRAN status](https://www.r-pkg.org/badges/version/anoR)](https://CRAN.R-project.org/package=anoR)
[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)

## Overview

**anoR** is a comprehensive R package for ANOVA (Analysis of Variance) that automatically determines whether to use parametric or non-parametric tests based on data characteristics. The package provides a complete workflow from data preprocessing through statistical analysis to professional report generation.

### Key Features

- **Automated Test Selection**: Intelligently chooses between parametric (ANOVA) and non-parametric (Kruskal-Wallis, Friedman) tests based on assumption testing
- **Comprehensive Preprocessing**: Handles missing data, outlier detection, and data transformations
- **Assumption Testing**: Multiple normality tests (Shapiro-Wilk, Anderson-Darling, Lilliefors) and homogeneity tests (Bartlett, Levene)
- **Effect Size Calculations**: Provides eta-squared, rank-biserial correlation, and Kendall's W
- **Post-hoc Analysis**: Tukey HSD for parametric tests, Dunn's test for non-parametric
- **Rich Visualizations**: Boxplots, Q-Q plots, interaction plots, and residual diagnostics
- **Professional Reports**: Automated HTML and PDF report generation with statistical tables and plots
- **S3 Methods**: Integrated `print()`, `summary()`, and `plot()` methods for easy result exploration

## Installation

### From GitHub (Development Version)

```r
# Install devtools if you haven't already
install.packages("devtools")

# Install anoR from GitHub
devtools::install_github("https://github.com/Brig-Bayo/anoR")
```

### Dependencies

The package requires the following R packages:
- `stats`, `graphics`, `grDevices`, `utils` (base R)
- `ggplot2`, `dplyr`, `tidyr` (data manipulation and visualization)
- `car`, `nortest`, `dunn.test` (statistical tests)
- `knitr`, `rmarkdown`, `htmltools`, `DT` (report generation)
- `plotly`, `gridExtra`, `broom` (enhanced visualizations and data handling)

## Quick Start

### Basic One-Way ANOVA

```r
library(anoR)

# Load example data
data(iris)

# Perform comprehensive ANOVA analysis
result <- anoR_analysis(
  data = iris,
  response = "Sepal.Length",
  factors = "Species",
  output_dir = "iris_analysis"
)

# View results
print(result)
summary(result)
plot(result)
```

### Two-Way ANOVA with Custom Settings

```r
# Load ToothGrowth data
data(ToothGrowth)

# Convert dose to factor
ToothGrowth$dose <- as.factor(ToothGrowth$dose)

# Comprehensive two-way analysis
result <- anoR_analysis(
  data = ToothGrowth,
  response = "len",
  factors = c("supp", "dose"),
  missing_method = "complete",
  outlier_method = "iqr",
  remove_outliers = TRUE,
  transformation = NULL,
  generate_reports = TRUE,
  report_formats = c("html", "pdf"),
  dataset_name = "Tooth Growth Study",
  output_dir = "tooth_analysis"
)
```

## Detailed Usage

### Function Parameters

The main `anoR_analysis()` function provides extensive customization options:

#### Data Specification
- `data`: Data frame containing your dataset
- `response`: Name of the continuous response variable
- `factors`: Vector of factor variable names (1-2 factors supported)
- `block`: Blocking variable for repeated measures designs

#### Preprocessing Options
- `missing_method`: How to handle missing data ("complete", "mean", "median")
- `outlier_method`: Outlier detection method ("iqr", "zscore")
- `remove_outliers`: Whether to remove detected outliers
- `transformation`: Data transformation ("log", "sqrt", "reciprocal", "square")

#### Analysis Control
- `alpha`: Significance level for assumption tests (default: 0.05)
- `force_parametric`: Force parametric tests even if assumptions violated
- `force_nonparametric`: Force non-parametric tests even if assumptions met
- `post_hoc`: Whether to perform post-hoc tests

#### Output Options
- `create_plots`: Generate visualization plots
- `output_dir`: Directory for saving plots and reports
- `generate_reports`: Create HTML/PDF reports
- `report_formats`: Vector specifying report formats
- `dataset_name`: Name for reports and documentation
- `verbose`: Print progress messages

### Supported Designs

#### One-Way Designs
- **Parametric**: One-way ANOVA with Tukey HSD post-hoc
- **Non-parametric**: 
  - Kruskal-Wallis test (3+ groups) with Dunn's post-hoc
  - Mann-Whitney U test (2 groups)

#### Two-Way Designs
- **Parametric**: Two-way ANOVA with interaction terms
- **Non-parametric**: Kruskal-Wallis on interaction groups

#### Repeated Measures
- **Non-parametric**: Friedman test with pairwise Wilcoxon post-hoc

### Assumption Testing

The package automatically tests ANOVA assumptions:

1. **Normality Testing**:
   - Shapiro-Wilk test (sample size ≤ 5000)
   - Anderson-Darling test
   - Lilliefors (Kolmogorov-Smirnov) test

2. **Homogeneity of Variance**:
   - Bartlett's test
   - Levene's test

Based on these tests, the package automatically recommends parametric or non-parametric approaches.

### Visualizations

The package creates comprehensive visualizations:

- **Boxplots**: Group comparisons with optional faceting
- **Q-Q Plots**: Normality assessment overall and by groups
- **Interaction Plots**: Mean profiles for two-way designs
- **Residual Plots**: Model diagnostics for parametric tests
- **Summary Plots**: Combined visualization panels

### Report Generation

Professional reports include:

- Executive summary with key findings
- Data overview and preprocessing details
- Assumption testing results with interpretations
- Statistical analysis results with effect sizes
- Post-hoc test results when applicable
- All visualizations with captions
- Conclusions and recommendations
- Technical appendix with methodology

## Examples

### Example 1: Plant Growth Analysis

```r
# Load PlantGrowth data
data(PlantGrowth)

# Analyze plant weight by treatment group
plant_analysis <- anoR_analysis(
  data = PlantGrowth,
  response = "weight",
  factors = "group",
  dataset_name = "Plant Growth Experiment",
  output_dir = "plant_analysis"
)

# Check if assumptions were met
plant_analysis$assumptions$recommendation$parametric_appropriate

# View post-hoc results if significant
if (!is.null(plant_analysis$parametric_results$post_hoc)) {
  print(plant_analysis$parametric_results$post_hoc$results)
}
```

### Example 2: Custom Preprocessing

```r
# Example with outlier removal and transformation
custom_analysis <- anoR_analysis(
  data = your_data,
  response = "outcome",
  factors = c("treatment", "gender"),
  missing_method = "mean",
  outlier_method = "zscore",
  remove_outliers = TRUE,
  transformation = "log",
  alpha = 0.01,  # More stringent assumption testing
  post_hoc = TRUE,
  output_dir = "custom_analysis"
)
```

### Example 3: Forced Non-Parametric Analysis

```r
# Force non-parametric approach regardless of assumptions
nonparam_analysis <- anoR_analysis(
  data = your_data,
  response = "score",
  factors = "condition",
  force_nonparametric = TRUE,
  dataset_name = "Non-parametric Analysis",
  output_dir = "nonparam_results"
)
```

## Output Structure

The `anoR_analysis()` function returns a comprehensive list object with class "anoR":

```r
str(result)
# List of 12
#  $ data                : data.frame - Preprocessed data
#  $ response           : chr - Response variable name
#  $ factors            : chr - Factor variable names
#  $ preprocessing      : list - Preprocessing summary
#  $ assumptions        : list - Assumption test results
#  $ test_approach      : chr - "parametric" or "nonparametric"
#  $ parametric_results : list - ANOVA results (if applicable)
#  $ nonparametric_results: list - Non-parametric results (if applicable)
#  $ plots              : list - Generated plots
#  $ reports            : list - Report file paths
#  $ output_dir         : chr - Output directory
#  $ summary            : chr - Text summary
#  - attr(*, "class")= chr "anoR"
```

## Advanced Features

### Custom Plot Access

```r
# Access individual plots
result$plots$boxplot          # Boxplot
result$plots$qq_plot          # Q-Q plots
result$plots$interaction      # Interaction plot (two-way)
result$plots$residual_plots   # Residual diagnostics

# Create custom summary plot
plot(result, type = "summary")
plot(result, type = "boxplot")
plot(result, type = "qq")
```

### Extracting Specific Results

```r
# Get assumption test p-values
normality_p <- result$assumptions$normality$overall$shapiro$p_value
homogeneity_p <- result$assumptions$homogeneity$factor1$levene$p_value

# Get effect sizes
if (result$test_approach == "parametric") {
  eta_squared <- result$parametric_results$eta_squared
} else {
  effect_size <- result$nonparametric_results$eta_squared_approx
}

# Access post-hoc comparisons
if (!is.null(result$parametric_results$post_hoc)) {
  posthoc_table <- result$parametric_results$post_hoc$results
}
```

## Best Practices

### Data Preparation
1. Ensure factor variables are properly coded as factors
2. Check for data entry errors and extreme outliers
3. Consider the appropriateness of transformations for your domain
4. Verify independence of observations

### Interpretation Guidelines
1. Always check assumption test results before interpreting main effects
2. Consider effect sizes alongside statistical significance
3. Use post-hoc tests only when main effects are significant
4. Report both statistical and practical significance

### Reporting Results
1. Include assumption testing results in your methodology
2. Report effect sizes with confidence intervals when available
3. Describe any data preprocessing steps taken
4. Use the generated visualizations to support your conclusions

## Troubleshooting

### Common Issues

**Error: "Currently only one-way and two-way ANOVA are supported"**
- Solution: Limit analysis to 1-2 factor variables

**Warning: "Some groups have fewer than 2 observations"**
- Solution: Check factor level combinations and consider collapsing categories

**Error: "Cannot apply reciprocal transformation with zero values"**
- Solution: Add a small constant before transformation or choose different transformation

**Warning: "PDF generation requires LaTeX installation"**
- Solution: Install LaTeX (e.g., TinyTeX) or generate HTML reports only

### Performance Considerations

- Large datasets (>10,000 observations) may take longer for assumption testing
- PDF report generation requires LaTeX installation
- Consider using `verbose = FALSE` for batch processing

## Contributing

We welcome contributions to anoR! Please see our [Contributing Guidelines](CONTRIBUTING.md) for details on:

- Reporting bugs
- Suggesting enhancements
- Submitting pull requests
- Code style guidelines

## Citation

If you use anoR in your research, please cite:

```
Boamah, B. (2025). anoR: Comprehensive ANOVA Analysis with Automated Test Selection. 
R package version 1.0.0. https://github.com/Brig-Bayo/anoR
```

## License

This package is licensed under the GPL-3 License. See [LICENSE](LICENSE) for details.

## Author

**Bright Boamah**
- Email: brigbayo@icloud.com
- GitHub: (https://github.com/Brig-Bayo/anoR)

## Acknowledgments

- The R Core Team for the excellent statistical computing environment
- Contributors to the packages that anoR depends on
- The statistical community for developing robust ANOVA methodologies

---

For more information, visit the [anoR GitHub repository](https://github.com/Brig-Bayo/anoR) or check the package documentation with `help(package = "anoR")`.

