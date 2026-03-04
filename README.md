# CRISPResso2 Batch Prep Tool

A lightweight, browser-based tool for building and validating [CRISPRessoBatch](https://github.com/pinellolab/CRISPResso2) `.batch` files — no installation required beyond a web browser.

> **Note:** This tool targets **CRISPRessoBatch** (multi-sample amplicon sequencing batch mode), not CRISPRessoWGS (which operates on whole-genome BAM files) or CRISPRessoPooled (pooled amplicons from a single FASTQ). If you are running standard amplicon sequencing experiments across multiple samples, this is the right tool.

---

## The Problem

Running [CRISPRessoBatch](https://github.com/pinellolab/CRISPResso2) requires a tab-separated `.batch` file with exact column names and a non-standard file extension. In practice this means:

- Manually formatting an Excel sheet and saving it with a `.batch` extension
- Cryptic failures when a column name has a typo or trailing space
- No feedback until the run actually starts

This tool smooths over all of that.

---

## Features

- **Auto-detects Illumina R1/R2 pairs** from standard naming (`SampleA_S1_L001_R1_001.fastq.gz`)
- **Editable sample table** — paste in amplicon and guide sequences directly; invalid characters are stripped automatically
- **Pre-run validation** including:
  - Missing amplicon or guide sequences
  - Guide RNA not found in amplicon (checks forward + reverse complement)
  - Unusual guide lengths (flags outside 17–23 bp)
  - Duplicate sample names
  - Incomplete R1/R2 pairs
- **Global parameter configuration** — quantification window, min reads, FLASH overlap settings, output folder
- **Generates a correctly formatted `.batch` file** with one click
- **Copies the exact terminal command** to run CRISPRessoBatch

---

## Usage

1. Download `crispresso_batch_tool.html`
2. Open it in any modern web browser (Chrome, Firefox, Safari)
3. Work through the 5-step interface:
   - **Step 1** — Load your FASTQ files
   - **Step 2** — Fill in amplicon and guide sequences per sample
   - **Step 3** — Set global CRISPRessoBatch parameters
   - **Step 4** — Run validation
   - **Step 5** — Download your `.batch` file and copy the run command

No server, no dependencies, no internet connection required after opening the file.

---

## Requirements

- A modern web browser
- [CRISPResso2](https://github.com/pinellolab/CRISPResso2) installed in your environment (for actually running the analysis)

### Installing CRISPResso2

```bash
conda install -c bioconda CRISPResso2
```

or via pip:

```bash
pip install CRISPResso2
```

---

## Supported File Naming

The tool auto-detects pairs using standard Illumina naming:

```
SampleName_S1_L001_R1_001.fastq.gz   ← R1
SampleName_S1_L001_R2_001.fastq.gz   ← R2
```

Files that do not match this pattern will be flagged as unrecognized and excluded from the batch.

---

## Example .batch Output

```
name\tfastq_r1\tfastq_r2\tamplicon_seq\tguide_seq
Sample_A1\tSample_A1_S1_L001_R1_001.fastq.gz\tSample_A1_S1_L001_R2_001.fastq.gz\tATCGATCG...\tGGTATCGA...
Sample_A2\tSample_A2_S2_L001_R1_001.fastq.gz\tSample_A2_S2_L001_R2_001.fastq.gz\tATCGATCG...\tGGTATCGA...
```

---

## Example Run Command

The tool generates a ready-to-run command like:

```bash
CRISPRessoBatch --batch_settings batch.batch \
  --output_folder CRISPRessoBatch_output \
  --quantification_window_size 1 \
  --min_frequency_alleles_around_cut_to_plot 0.2 \
  --min_reads_to_use_region 1000
```

---

## How it relates to the CRISPResso2 tool suite

The CRISPResso2 suite contains several tools for different experiment types:

| Tool | Use case |
|---|---|
| **CRISPRessoBatch** | ← **This tool targets this** — multiple amplicon sequencing samples |
| CRISPRessoPooled | Multiple amplicons from a single pooled FASTQ |
| CRISPRessoWGS | Whole-genome sequencing (requires pre-aligned BAM) |
| CRISPRessoCompare | Comparing two samples (e.g. treated vs. untreated) |
| CRISPRessoAggregate | Aggregating results from previous runs |

---

## Roadmap

- [ ] Results aggregator — collect editing efficiency stats across all output folders into a single CSV/Excel summary
- [ ] 96-well plate heatmap visualization of editing rates
- [ ] Support for CRISPRessoPooled batch file generation
- [ ] HDR efficiency tracking across samples

---

## Contributing

Pull requests welcome. If you run into a naming convention that isn't detected correctly, please open an issue with an example filename.

---

## License

MIT

---

## Acknowledgements

Built around [CRISPResso2](https://github.com/pinellolab/CRISPResso2) by the Pinello Lab.
