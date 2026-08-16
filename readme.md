# Bird sightings in the Netherlands (eBird, 2015–2023)

An exploratory analysis of citizen-science bird observations in the
Netherlands: what gets seen, when, where, and whether the way people observe
changes what they report. Built as the mid-course project of the Ironhack Data
Analytics bootcamp (2023).

![Sightings vs population by province](images/regression_sightings_population_labeled.png)

## Data

- **eBird Basic Dataset** (Cornell Lab of Ornithology, release Aug 2023),
  Netherlands subset, Jan 2015 – Sep 2023. ~1.8 M observation records,
  50 columns, ~600 MB tab-separated. Stored here via Git LFS.
- **CBS StatLine** population by province, 2023.

The analysis in the notebook runs on a random 10,000-row sample of the eBird
file so it stays fast on a laptop; the pipeline is the same on the full file.
After dropping empty or single-valued columns (country, county, IBA/BCR/USFWS
codes, atlas block, project code, group identifier, etc.) the working set is
31 columns.

## Questions & findings

**1. What gets seen most?**
271 species in the sample. The top of the list is exactly what you'd expect
in a Dutch garden or polder: Eurasian Coot, Mallard, Blackbird, Wood-Pigeon,
Carrion Crow, Great Tit, Greylag Goose, Black-headed Gull, Magpie, Blue Tit.
A long tail — over a third of species appear five times or fewer.

![Top 30 species](images/bird_sightings.png)

**2. When do people look?**
Sightings roughly double at the weekend: ~2,000 per day on Saturday and
Sunday vs ~1,050–1,200 Monday–Thursday, with Friday in between. This is a
pattern in *observer effort*, not bird behaviour — a useful reminder that
citizen-science data measures people as much as wildlife.

![Sightings by day of week](images/bird_sightings_by_day.png)

**3. Where?**
Noord-Holland (1,955) and Zuid-Holland (1,621) dominate; Drenthe (192) and
Limburg (360) trail. Regressing sightings on province population with OLS
gives **R² = 0.73** (p = 0.0004, n = 12): population explains most of the
variance, so a province "having more birds" is largely a province having more
people with binoculars. Zeeland is the interesting outlier — far more sightings
than its population predicts, consistent with it being a birding destination.

**4. Does the observation protocol change what is reported?**
eBird records how the observer was observing: Traveling (75%), Stationary
(15%), Incidental (6%), Historical (3%). A χ² test of independence between
species and protocol type on the raw counts is hugely significant
(χ² = 1325, p ≈ 10⁻²⁸) — but that table is full of cells with expected counts
below 5, which inflates χ². Restricting to species with ≥5 observations per
protocol, the test no longer rejects independence (p ≈ 1). The honest
conclusion is that the *common* species are seen under every protocol in
similar proportions, and the apparent dependence comes from rare species
appearing once under one method.

![Sightings by protocol type](images/bird_sightings_by_type_histo.png)

## What I'd do next

Run on the full 1.8 M rows; add seasonality (month) properly; normalise
sightings by observer effort (duration, distance) rather than raw counts;
map sightings with province shapefiles.

## Repo layout

```
notebooks/Data_Bird.ipynb   full analysis (pandas, seaborn, scipy, statsmodels)
data/raw/                   eBird extract (LFS) + CBS population CSV
data/cleaned/               pruned working dataset
images/                     charts used above and in the slides
slides/                     presentation deck
```

## Run it

```bash
pip install -r requirements.txt
git lfs pull                       # fetch the eBird file
jupyter notebook notebooks/Data_Bird.ipynb
```

Data © Cornell Lab of Ornithology; see `data/raw/terms_of_use.txt`.
