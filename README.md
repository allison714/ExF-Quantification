# ExF Quantification

Measures the linear expansion factor (ExF) of pan-ExM samples from their synapses.

The program finds synapses in the pan stain, measures the distance between the presynaptic dense projections (DPs) and the postsynaptic density (PSD), and divides it by that distance before expansion, 82 nm. The method and the 82 nm value come from M'Saad, Cairns et al., *Nature Biotechnology* 2025. What is taken from whom is set out in [METHODS_AND_CREDITS.md](METHODS_AND_CREDITS.md).

Started 6 October 2026. First sample set: GluA2 (260831_WT4).
* It's important to note this was created AFTER the publication as a way to automate future dataset measurements.

## How to run it

1. Connect the lab server drive (X:).
2. Double-click `Measure_ExF.bat` in this folder.
3. Type part of the sample name (for example `GluA2`) and press Enter.
4. Answer whether each antibody it read from the file names is a synaptic one. If it is, synapses carrying that antibody are listed first. If not, the antibody is ignored.

A 3x3 overview takes under a minute. A 44-plane stack takes a few minutes.

To use every plane of a stack rather than 12 spread through it, run from a command prompt:

```
Measure_ExF.bat GluA2 --max-planes 50
```

## What you get

Everything goes to `OneDrive - Yale University\Images\2026\Zarr Synapse ExF\`.

| File | What it is |
|---|---|
| `Synapse Log.xlsx`, sheet Synapses | The 60 best synapses of each sample: distance, ExF, a link to its picture, a link that opens Neuroglancer on it |
| `Synapse Log.xlsx`, sheet Summary | ExF per sample |
| `Synapse Log.xlsx`, sheet Method | Every step and threshold used for that run |
| `<sample>\..._synapse-NNN_zNNNN.png` | One synapse: pan, antibody, overlay with scale bar, profile with the fit |
| `<sample>\..._synapses_contact-sheet.png` | All 60 at a glance, numbered as in the log |
| `<sample>\..._synapses.csv` | Every synapse found, logged or not |

## Accepting and rejecting synapses

1. Open the contact sheet beside `Synapse Log.xlsx`. The numbers match.
2. In the Accept column, choose Y or N. Notes is yours too.
3. Save, close the workbook, and run `Measure_ExF.bat` again.

The Summary sheet then shows "ExF, accepted", from your Y rows only. Your answers are kept on every run.

Reject a synapse when it is tilted (no clear gap between the two densities), when the two peaks are two membranes rather than a PSD and DPs, or when the antibody spot sits beside the synapse instead of on it.

## Which number to use

| Column | Use it? |
|---|---|
| ExF, accepted | Yes, once you have reviewed |
| ExF, AB+ only | Yes, until then |
| ExF, all found | No. It still contains pairs of membranes, which read high (25–30×) |

## Results so far

Antibody-positive synapses, two Gaussians fitted to the profile, distance ÷ 82 nm. Not yet reviewed by a person.

| Sample | Planes | Synapses found | Antibody-positive | ExF, mean ± s.d. (median) |
|---|---|---|---|---|
| 260831_WT4_GluA2ms_40x_3x3_FusionStitcher | 1 | 250 | 44 | 17.8 ± 3.3 (17.0) |
| 260831_WT4_GluA2ms_40x_s2s_2 | 41 | 1133 | 146 | 18.2 ± 2.8 (17.7) |

**These numbers are about 10% higher than the paper's method would give.** The paper reads the distance peak to peak. Read that way, the same synapses give 16.6 ± 2.7 (n = 88). See "Open question" in the methods file before quoting an expansion factor.

## Which samples are left out

- `SZ` or `control` in the name.
- A repeat named number_number, such as `Sample1_2`.

These stay: a number that ends an antibody's name (`Homer1_2`), and a grid size (`3x3_1`; 3x3 marks the 2D overview rather than the volume).

The antibody names the program recognises are in the `ANTIBODIES` table at the top of `synapse_exf.py`. Add new ones there.

## Where the code is

The programs live in `Desktop\ZarrBatchConverter`, because they share its settings, pan-stain detection and channel colours. `Measure_ExF.bat` in this folder only starts them.

| File | What it does |
|---|---|
| `synapse_exf.py` | Finds the synapses, measures them, writes the log and pictures |
| `synapse_fit_options.py` | Compares nine ways of reading the distance off a profile |
| `ab_efficiency.py` | Separate tool: antibody labelling efficiency against the pan stain |

Requirements: Python 3.10 or newer with numpy 2, zarr 3, dask, scikit-image, scipy, matplotlib and openpyxl. The .bat installs what is missing.
