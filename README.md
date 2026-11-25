<!-- brainlife-app:begin (generated from the App manifest; edit the manifest, not this block) -->

**Fits a voxel-wise multiple linear regression across many T1-weighted volumes with SOCR VolumeMultipleRegression, using subject metadata fields (e.g. age, sex) as regressors.**

Builds a design matrix from the brainlife metadata of each input (one column per regressor named in the regressors parameter), converts the T1 images to ANALYZE with FSL and runs the SOCR Java VolumeMultipleRegression with an intercept. Intercept beta, t-statistic, p-value and partial-correlation maps are converted back to NIfTI. The images must be in a common space; the code passes fixed dimensions of 220x220x220, and every regressor must exist as a metadata field on every input.

Run it on brainlife.io: [https://doi.org/10.25663/bl.app.84](https://doi.org/10.25663/bl.app.84)

| Input | Datatype | Required | Description |
|---|---|---|---|
| `t1` | `neuro/anat/t1w` | yes | T1-weighted images of several subjects; one row of the design matrix per image. All images in the same space; the SOCR call uses -dim 220 220 220. Each input needs the metadata fields listed in regressors. |

| Output | Datatype | Description |
|---|---|---|
| `output` | `raw` | SOCR regression outputs (ANALYZE files), including the intercept Beta, TStat, Pvalue and RPartCorr maps also converted to NIfTI, and the design matrix dm.txt. |

| Parameter | Type | Default | Description |
|---|---|---|---|
| `regressors` | string | `age,sex` | Comma-separated list of brainlife metadata fields (e.g. age,sex) used as regressors; each value is read from _inputs[].meta.<field>. |

Runtime: container `brainlife/fsl:5.0.9`; 4 CPUs; 16 GB memory.

<!-- brainlife-app:end -->

## Volume-based Multiple Linear Regression Analysis

> http://wiki.stat.ucla.edu/socr/index.php/SOCR_EduMaterials_AnalysesCommandLineVolumeMultipleRegression
