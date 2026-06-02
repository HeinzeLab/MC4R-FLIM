# MC4R-FLIM
# Frontiers in Molecular Biology - Analysis Notebooks

This repository contains a series of Jupyter notebooks that implement a typical analysis pipeline for PIE-FCS/FLIM-like experiments, image preparation, ROI handling, segmentation, and brightness/number analysis. The notebooks are designed to be run in order (where applicable) and can also be used independently for specific tasks.

## Getting started
- Python: 3.9+ recommended
- Key packages used across notebooks: numpy, scipy, matplotlib, pandas, scikit-image, jupyter, and (for correlation/TTTR work) tttrlib ≥ 0.21.9
- Data layout conventions: Some notebooks assume specific subfolders (e.g., Calibration) and file formats (.ptu for TTTR data). Read the per-notebook notes below.

## Notebook overview
**0_Correlation_Calibration.ipynb**
Purpose: Compute PIE-FCS style auto- and cross-correlations from TTTR (.ptu) calibration data. Produces correlation curves and .cor export files for use in external tools (e.g., ChiSurf/Kristine).
Main steps:
- Load TTTR data with tttrlib and extract macro/micro time parameters.
- Define PIE prompt/delay windows based on number_of_bins per laser period.
- Compute:
  - ACF for red prompt (rp), red delay (rd), green prompt (gp)
  - PIE cross-correlation (gp–rd) and FRET cross-correlation (gp–rp)
- Save results:
  - PNG plot of correlation curves per file
  - .cor files with 3 columns: time axis (ms), amplitude, compatibility column (zeros) and header values encoded in the first two rows (duration s, average count rate kHz)
*Notes:*
- Expects input data under ./Calibration/*.ptu relative to the notebook.
- Average count-rate handling follows per-polarization logic and stores appropriate averages in the .cor header rows.

**0_Decays_Calibration.ipynb**
Purpose: Handle decay calibration (IRF, instrument timing, or reference lifetime calibration). Often used to determine micro-time resolution, IRF shape, or decay standards required by subsequent FLIM/TCSPC analyses.
Typical outputs: Reference decay curves, fitted parameters, figures.

**1_Save_intensity_images.ipynb**
Purpose: Export or generate intensity images from raw acquisitions. Often the first image-centric step to create per-channel intensity maps to be used by later steps.
Typical outputs: Intensity images (e.g., TIFF/PNG) per channel or per acquisition.

**2_MeanTauImages.ipynb**
Purpose: Compute mean lifetime (mean tau) images from time-resolved data (e.g., FLIM stacks). Useful for mapping lifetime contrast across the field of view.
Typical outputs: Mean-τ images and possibly per-pixel or per-ROI statistics.

**3_Segmentation.ipynb**
Purpose: Segment cells or structures of interest from intensity or lifetime-based images.
Typical outputs: Binary masks, labeled segmentation maps, and optional QC overlays.

**4_ExportROIDecays.ipynb**
Purpose: Export decay curves from user-defined ROIs to enable ROI-specific lifetime fitting and other analyses.
Typical outputs: Per-ROI decay arrays/files and summary tables.

**5_NumberBrightness.ipynb**
Purpose: Perform Number & Brightness (N&B) analysis on image series to estimate oligomerization or molecular brightness at the pixel/ROI level.
Typical outputs: N&B maps, brightness histograms, and summary metrics.

**6a_Export_redredlay_series.ipynb**
Purpose: Export series derived from red and red-delay channels, typically to support PIE separation and downstream quantitative analysis.
Typical outputs: Channel-specific series files and summary images.

**6b_Combine_reddelay_prob.ipynb**
Purpose: Combine red/red-delay information into probabilistic metrics or fused representations useful for classification or thresholding.
Typical outputs: Combined probability maps or tables.

**6c_Multisegmentation.ipynb**
Purpose: Apply multiple segmentation strategies and reconcile them into consensus masks; facilitates robust ROI definition across channels/conditions.
Typical outputs: Multiple masks, consensus segmentations, comparison/QC plots.

**6d_ExportROIDecays_Multi.ipynb**
Purpose: Export ROI decays for multi-condition or multi-dataset experiments, automating the per-condition ROI extraction.
Typical outputs: Batched ROI decay files and summary per-condition logs.

## Further notebooks used for fitting the dimerization/oligomerization
**Binning_SpeciesFractions.ipynb**
Purpose: Here the combined species fractions for monomer, dimer or oligomer (> 100 values) are binned into 15 logarithmically spaced bins.
The bins are defined based on the minimal and maximal total protein concentration present in the sample.
Typical output: csv files containing the concentration bins/bin center, mean, standard deviation, standard error of the mean (sem) and number of samples per bin
provided for each monomer, dimer and oligomer

**Fit_Models_noweights.ipynb**
Purpose: Fits the species fractions with the respective concentrations to the oligomerization model
Performs bootstrapping and compares Dimerization with olimgerization model
Typical output: modelled monomer, dimer and oligomer fraction at the respective concentrations; information on AIC/BIC;
bootstrapping results to determine mean/sem of the obtained dimerization and oligomerization constants

**Fit_Models_weights.ipynb**
Purpose: Fits the binned species fractions with the respective concentration bin center and uses the SEM as weights to the oligomerization model
Performs bootstrapping and compares Dimerization with olimgerization model
Typical output: modelled monomer, dimer and oligomer fraction at the respective concentrations; information on AIC/BIC;
bootstrapping results to determine mean/sem of the obtained dimerization and oligomerization constants

**Dimerization_Aniso.ipynb**
Purpose: Fits the species fractions with the respective concentrations to the dimerization model
Performs bootstrapping report confidence intervals
Typical output: modelled monomer and dimer fractions at the respective concentrations; information on AIC/BIC;
bootstrapping results to determine mean/sem of the obtained dimerization constant

## Suggested execution order
1) 0_Decays_Calibration.ipynb (establish timing/IRF)
2) 0_Correlation_Calibration.ipynb (validate correlation settings and export .cor files)
3) 1_Save_intensity_images.ipynb
4) 2_MeanTauImages.ipynb
5) 3_Segmentation.ipynb
6) 4_ExportROIDecays.ipynb and/or 8d_ExportROIDecays_Multi.ipynb
7) 5_NumberBrightness.ipynb
8) 6a/6b/6c/6d for advanced channel handling and segmentation ensembles

## Data and file conventions
- TTTR data: .ptu files under ./Calibration for calibration notebooks
- Exports: Plots (.png), text tables (.csv/.txt), and correlation files (.cor) next to input data unless otherwise noted
- ROI artifacts: Masks as image files (e.g., TIFF/PNG) and decay exports as text formats suitable for downstream fitting tools

## Reproducibility tips
- Keep raw data separate from derived exports (e.g., ./raw, ./derived)
- Log versions of key packages (tttrlib, numpy, scipy) for each analysis session

## Citation and acknowledgments
If you use these notebooks in a publication, please cite the underlying methods (PIE-FCS, FLIM, N&B, etc.) and acknowledge this repository. 

If you find this scripts useful, please cite:
https://www.frontiersin.org/journals/molecular-biosciences/articles/10.3389/fmolb.2026.1814859/

