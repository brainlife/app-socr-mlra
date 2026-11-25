<h1 align="center">Volume-based multiple linear regression</h1>

<p align="center">
  Voxel-wise regression maps (beta, t-statistic, p-value and partial correlation) across many T1-weighted images, using subject metadata such as age and sex as regressors.
</p>

<p align="center">
  <a href="https://github.com/brainlife/abcd-spec"><img src="https://img.shields.io/badge/ABCD_Spec-v1.1-green.svg" alt="ABCD Spec v1.1"></a>
  <a href="https://doi.org/10.25663/bl.app.84"><img src="https://img.shields.io/badge/Brainlife-bl.app.84-blue.svg" alt="Run on brainlife.io"></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/License-MIT-lightgrey.svg" alt="MIT license"></a>
</p>

## At a glance

<!-- brainlife-app:begin (generated from the App manifest; edit the manifest, not this block) -->

> **Fits a voxel-wise multiple linear regression across many T1-weighted volumes with SOCR VolumeMultipleRegression, using subject metadata fields (e.g. age, sex) as regressors.**

Builds a design matrix from the brainlife metadata of each input (one column per regressor named in the regressors parameter), converts the T1 images to ANALYZE with FSL and runs the SOCR Java VolumeMultipleRegression with an intercept. Intercept beta, t-statistic, p-value and partial-correlation maps are converted back to NIfTI. The images must be in a common space; the code passes fixed dimensions of 220x220x220, and every regressor must exist as a metadata field on every input.

| | |
|---|---|
| **Inputs** | `neuro/anat/t1w` |
| **Outputs** | `raw` |
| **Container** | `brainlife/fsl:5.0.9` |
| **Resources** | 4 CPUs · 16 GB memory |
| **Run it** | [doi.org/10.25663/bl.app.84](https://doi.org/10.25663/bl.app.84) |

#### Inputs

| Input | Datatype | Required | Description |
|---|---|---|---|
| `t1` | `neuro/anat/t1w` | yes | T1-weighted images of several subjects; one row of the design matrix per image. All images in the same space; the SOCR call uses -dim 220 220 220. Each input needs the metadata fields listed in regressors. |

#### Outputs

| Output | Datatype | Description |
|---|---|---|
| `output` | `raw` | SOCR regression outputs (ANALYZE files), including the intercept Beta, TStat, Pvalue and RPartCorr maps also converted to NIfTI, and the design matrix dm.txt. |

<details>
<summary><b>Parameters</b> (1)</summary>

| Parameter | Type | Default | Description |
|---|---|---|---|
| `regressors` | string | `age,sex` | Comma-separated list of brainlife metadata fields (e.g. age,sex) used as regressors; each value is read from _inputs[].meta.<field>. |

</details>

<!-- brainlife-app:end -->

## Running the App

### On brainlife.io

Open the App at [doi.org/10.25663/bl.app.84](https://doi.org/10.25663/bl.app.84), select several T1-weighted images (one per subject), set `regressors` to a comma-separated list of brainlife.io metadata fields (default `age,sex`) and press **Execute**. The App:

1. builds a design matrix (`dm.txt`) with one row per image and one column per regressor, reading each value from the dataset's metadata;
2. converts the images to ANALYZE format with FSL;
3. runs the [SOCR command-line VolumeMultipleRegression](http://wiki.stat.ucla.edu/socr/index.php/SOCR_EduMaterials_AnalysesCommandLineVolumeMultipleRegression) analysis with an intercept;
4. converts the intercept beta, t-statistic, p-value and partial-correlation maps back to NIfTI.

The images must be in a common space, and every regressor must exist as a metadata field on every input. The analysis is run with fixed image dimensions of 220 × 220 × 220.

### Locally

You need [Singularity/Apptainer](https://apptainer.org/) and [`jq`](https://jqlang.github.io/jq/). FSL 5.0.9 and SOCR 1.0 run inside containers.

1. Clone this repository.
2. Create `config.json` with the list of images, the regressors and, for each image, its metadata in `_inputs` (in the same order as `t1`), as brainlife.io writes it (see [`config.json.sample`](config.json.sample)):

   ```json
   {
       "t1": ["/input/sub-01/t1.nii.gz", "/input/sub-02/t1.nii.gz"],
       "regressors": "age,sex",
       "_inputs": [
           { "id": "t1", "meta": { "subject": "01", "age": 23, "sex": 0 } },
           { "id": "t1", "meta": { "subject": "02", "age": 31, "sex": 1 } }
       ]
   }
   ```

3. Run the App:

   ```bash
   ./main
   ```

## Outputs

```text
dm.txt                                       design matrix
interceptConstant_Intercept_Beta.nii.gz      intercept beta map
interceptConstant_Intercept_TStat.nii.gz     t-statistic map
interceptConstant_Intercept_Pvalue.nii.gz    p-value map
interceptConstant_Intercept_RPartCorr.nii.gz partial-correlation map
*.img, *.hdr                                 SOCR regression outputs in ANALYZE format
```

## Troubleshooting

- **`failed to run VolumeMultipleRegression`**: the SOCR regression failed. Check that all images have the same dimensions and that every regressor metadata field is set on every input.

## Citing

> [!TIP]
> If you use this App in your work, please cite the App, the software it builds on and brainlife.io (below). GitHub's **Cite this repository** button gives the App citation in APA and BibTeX.

- Volume-based Multiple Linear Regression. brainlife.io App. [doi:10.25663/bl.app.84](https://doi.org/10.25663/bl.app.84)
- Jenkinson et al. FSL. *NeuroImage* 62:782-790 (2012). [doi:10.1016/j.neuroimage.2011.09.015](https://doi.org/10.1016/j.neuroimage.2011.09.015)
- Hayashi S, Caron BA, Heinsfeld AS, et al. brainlife.io: a decentralized and open-source cloud platform to support neuroscience research. *Nature Methods* 21:809–813 (2024). [doi:10.1038/s41592-024-02237-2](https://doi.org/10.1038/s41592-024-02237-2)
- Avesani P, McPherson B, Hayashi S, et al. The open diffusion data derivatives, brain data upcycling via integrated publishing of derivatives and reproducible open cloud services. *Scientific Data* 6:69 (2019). [doi:10.1038/s41597-019-0073-y](https://doi.org/10.1038/s41597-019-0073-y)

## Authors

- Soichi Hayashi

## Funding

[![Wellcome-226486/Z/22/Z](https://img.shields.io/badge/Wellcome-226486%2FZ%2F22%2FZ-purple.svg)](https://wellcome.org/)
[![NIH-NINDS-U24NS140384](https://img.shields.io/badge/NIH_NINDS-U24NS140384-green.svg)](https://reporter.nih.gov/project-details/11261037)
[![NIH-NINDS-UM1NS132207](https://img.shields.io/badge/NIH_NINDS-UM1NS132207-green.svg)](https://reporter.nih.gov/project-details/11411328)
[![NIH-NIBIB-R01EB029272](https://img.shields.io/badge/NIH_NIBIB-R01EB029272-green.svg)](https://reporter.nih.gov/project-details/10262925)
[![NIH-NIBIB-R01EB030896](https://img.shields.io/badge/NIH_NIBIB-R01EB030896-green.svg)](https://reporter.nih.gov/project-details/10428625)
[![NIH-NIMH-R01MH126699](https://img.shields.io/badge/NIH_NIMH-R01MH126699-green.svg)](https://reporter.nih.gov/project-details/10460628)
[![NIH-NIDCD-R01DC020088](https://img.shields.io/badge/NIH_NIDCD-R01DC020088-green.svg)](https://reporter.nih.gov/project-details/11140284)
[![NSF-BCS-1734853](https://img.shields.io/badge/NSF_BCS-1734853-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1734853)
[![NSF-BCS-2203524](https://img.shields.io/badge/NSF_BCS-2203524-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=2203524)
[![NSF-IIS-1636893](https://img.shields.io/badge/NSF_IIS-1636893-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1636893)
[![NSF-OAC-2148729](https://img.shields.io/badge/NSF_OAC-2148729-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=2148729)
[![NSF-IIS-1912270](https://img.shields.io/badge/NSF_IIS-1912270-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1912270)
[![NSF-IIS-2148700](https://img.shields.io/badge/NSF_IIS-2148700-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=2148700)
[![NSF-OAC-1916518](https://img.shields.io/badge/NSF_OAC-1916518-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1916518)

<sub>brainlife.io is publicly funded, and acknowledging that support helps keep it running. This App was developed with support from Wellcome, the U.S. National Institutes of Health (including the BRAIN Initiative BRAIN CONNECTS program: The Axonal Projectome EXchange, U24NS140384, and the Center for Mesoscale Connectomics, UM1NS132207) and the U.S. National Science Foundation.</sub>

## License

[MIT](LICENSE).
