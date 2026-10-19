# MaRTy and the Many Grades of Shade

A graduate course assignment for GPH 598 (Sensable Heatscapes) at ASU analyzing field measurements from MaRTy, a mobile device that measures mean radiant temperature (MRT) along with air temperature, wind speed, and humidity. The analysis asks: how much does shade reduce pedestrian heat exposure, and are all types of shade equally effective?

## Approach
- Compared air temperature with MRT across sites
- Fit a multiple linear regression of the air temperature–MRT difference on time of day, surface type, shade category, relative humidity, and wind speed, with VIF and ANOVA checks
- Compared mean MRT by shade category and by time of day (12:30 PM, 3:30 PM, 8:30 PM)
- Compared two sites with similar air temperatures but very different MRTs

## Key findings
- The model explains about 68% of the variation (adjusted R² = 0.68)
- Sun exposure and time of day were the strongest predictors
- Built shade had the lowest average MRT, followed closely by tree shade and then engineered shade; sun-exposed sites were much hotter
- Planning implications: zoning changes such as higher density and smaller setbacks can produce more shade for pedestrians

## Files
- `MaRTy.qmd`: analysis and report source (renders to PDF)
- `MaRTy.pdf`: the finished report
- `header.tex`: LaTeX formatting for the PDF

## Reproducing
The data file is not included. Update the import path in `MaRTy.qmd` to point to your copy of the dataset, then render with Quarto.
