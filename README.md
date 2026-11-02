# Functional regression for regional landslide prediction

R code and data for:

> Ahmed, M., Recacho, V. S., Titti, G., Uchida, T., Borgatti, L., Morales, A. B., Francioni, M., & Lombardo, L. (2026). On the use of rainfall time series for regional landslide prediction by means of functional regression. *Engineering Geology*, 371, 108860.

Code written by Vitor Recacho.

## Overview

Landslide occurrence is modelled as a binary outcome. The functional models take the cumulative rainfall curve over the 34 days up to day 0 as a predictor, so the effect of rainfall can vary with time. The GAM benchmarks use cumulative rainfall as a scalar.

| Model | Rainfall term | Static predictors |
|---|---|---|
| FGAM | `af(precipitation, k = c(3, 4))` | Slope, eastness, northness, profile curvature, lithology |
| NFGAM | `af(precipitation, k = c(3, 4))` | None |
| GAM | `s(precipitation, k = 16)` | Slope, eastness, northness, profile curvature, lithology |
| NGAM | `s(precipitation, k = 16)` | None |

Functional models are fitted with `refund::pfr` and GAMs with `mgcv::gam`, using a binomial family.

## Data

`data_set.7z` contains `data_set.csv`, with 64,252 rows: one per spatial unit and event, across three events. The script drops the two rows with `Litho == 5`, leaving 64,250 rows, of which 2,182 contain at least one landslide.

| Column | Use |
|---|---|
| `day_33` ... `day_0` | Daily rainfall, accumulated by the script into a curve over time |
| `count` | Landslide count, converted to presence/absence |
| `slopemean`, `eastmean`, `northmean`, `profcurvme` | Static predictors, standardized by the script |
| `Litho` | Lithology class |
| `Event` | Event identifier, used in cross-validation |

## Requirements

R with the following packages:

| Purpose | Packages |
|---|---|
| Models | `mgcv`, `refund` |
| Validation | `pROC`, `caret` |
| Data | `tidyverse`, `sf`, `reshape2`, `writexl` |
| Figures | `ggplot2`, `patchwork`, `scales`, `latex2exp`, `RColorBrewer`, `ggthemes`, `viridis` |
| 3D figure | `plotly`, `reticulate`, `webshot2`, `htmlwidgets` |

The 3D figure is exported with `plotly::save_image`, which requires a conda environment named `r-reticulate` with `kaleido` installed.

## Running the analysis

Extract `data_set.7z`, set the working directory to the folder containing `data_set.csv`, and run `code.R`.

The random cross-validation repeats 100 splits for each of 10 training ratios and 4 models, for 4,000 fits in total, so a full run takes a long time.

## Outputs

| Section of `code.R` | Analysis | Output |
|---|---|---|
| 4 | Leave-one-event-out cross-validation | `confusion_data_new.Rdata` |
| 5 | Performance across rainfall time windows | `timevarying_new.pdf` |
| 6 | Random cross-validation and learning curves | `Kfoldcv_new.pdf` |
| 7 | True positive and true negative rates | `TPxTN_new.pdf` |
| 8 | Partial effects | `effects_new.pdf` |
| 9 | Confusion counts for the global models | `confusion_df.csv` |
| 10 | 3D surface of the functional rainfall effect | `plot_rotated_new_angle_new.pdf` |
| 11 | Precision-recall curves | `PRcurveRCV.pdf`, `PRCurveSCV.pdf` |

## License

MIT. See `LICENSE.txt`.
