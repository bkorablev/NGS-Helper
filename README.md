# CRISPResso2 Batch Prep Tool

A single self-contained HTML file for building [CRISPRessoBatch](https://github.com/pinellolab/CRISPResso2) `.batch` files and aggregating results. Opens locally in Chrome — no installation, no server, no internet connection required after opening the file.

> **Note:** This tool targets **CRISPRessoBatch** (multi-sample amplicon sequencing batch mode). For CRISPRessoWGS (whole-genome BAM) or CRISPRessoPooled (pooled amplicons from a single FASTQ), see the CRISPResso2 suite.

---

## Features at a Glance

- Auto-detects Illumina R1/R2 pairs from standard naming
- Editable sample table with fill-down, Enter-key row navigation, and per-row delete
- Pre-run validation: missing sequences, guide not in amplicon, unusual guide lengths, duplicate names, incomplete pairs
- Generates a correctly formatted `.batch` file and ready-to-run terminal command
- Results aggregator: parses a CRISPRessoBatch output folder and produces a summary table, stacked bar charts, and Excel export
- Optional KO classification using allele frequency tables — no reload required

---

## Usage

1. Download `crispresso_batch_tool.html`
2. Open it in Chrome
3. Work through the two functional areas:

### Batch Prep (Steps 1–5)

**Step 1 — Load FASTQs**
Drag and drop or browse for Illumina paired-end FASTQ files. The tool auto-detects R1/R2 pairs from standard Illumina naming (`SampleA_S1_L001_R1_001.fastq.gz`) and reports complete pairs vs unpaired files.

**Step 2 — Edit Samples**
An editable table pre-populated with detected sample names. Columns: sample name, R1/R2 filenames, amplicon sequence, guide RNA, and optional HDR template. Supports Excel-like Enter key navigation between rows, a fill-down button to propagate a value to all rows below, and per-row delete.

**Step 3 — Global Parameters**
Set CRISPRessoBatch parameters that apply to all samples: output folder, quantification window size, minimum allele frequency to plot, and skip-failed-samples toggle. Only non-default values are included in the generated run command.

**Step 4 — Validate**
Pre-run checks including missing amplicon or guide sequences, guide RNA not found in amplicon (forward and reverse complement both checked), unusual guide lengths (flags outside 17–23 bp), duplicate sample names, and incomplete R1/R2 pairs.

**Step 5 — Export**
Preview the formatted tab-separated `.batch` file, copy the generated `CRISPRessoBatch` run command, and download the `.batch` file.

### Results Aggregator (Tab 6)

Browse and select a CRISPRessoBatch output folder. The tool automatically finds and parses all `CRISPResso_on_<samplename>` subfolders.

**Summary table columns:**
Sample name · Reads in Input · Reads Aligned · Aligned % · Unmodified reads + % · NHEJ reads + % · HDR reads + % (if HDR template was used) · Ambiguous reads + %

**Also includes:**
- Stacked SVG bar chart per sample (Unmodified / NHEJ / HDR / Ambiguous), color-coded to match the tool theme
- Loading spinner with status messages during parsing
- Excel export (`.xlsx`) with two sheets: Summary and Raw data

---

## KO Classification (optional)

Disabled by default. Enable the toggle in the Results tab — no reload required.

**Settings:**
- Expected allele copies (ploidy) — e.g. 2 for diploid (RPE-1), 3 for triploid (K562)
- In-frame deletion override (bp) — in-frame deletions ≥ this size are reclassified as likely disruptive

**How it works:**
Parses `Alleles_frequency_table.zip` per sample. Groups alleles by deletion/insertion signature and takes the top N alleles by frequency, where N = ploidy setting.

**Per-allele types:** Wildtype · Frameshift · In-frame · Large in-frame deletion · Substitution only

**Sample-level classification:**

| Status | Condition |
|---|---|
| **Full KO** | All alleles are Frameshift or Large in-frame deletion |
| **Likely Partial KO** | All alleles edited, but mix includes In-frame mutations |
| **Partial KO** | At least one allele is Wildtype alongside edited alleles |
| **No KO** | All alleles Wildtype |

Warnings are shown for in-frame mutations and large deletions that may span exon-intron boundaries. An allele breakdown and KO status are included in the Excel export.

---

## Requirements

- **Chrome** (required for `webkitdirectory` folder selection in the Results tab)
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

```
SampleName_S1_L001_R1_001.fastq.gz   ← R1
SampleName_S1_L001_R2_001.fastq.gz   ← R2
```

Files that do not match this pattern will be flagged as unrecognized and excluded from the batch.

---

## Example .batch Output

```
name	fastq_r1	fastq_r2	amplicon_seq	guide_seq
Sample_A1	Sample_A1_S1_L001_R1_001.fastq.gz	Sample_A1_S1_L001_R2_001.fastq.gz	ATCGATCG...	GGTATCGA...
Sample_A2	Sample_A2_S2_L001_R1_001.fastq.gz	Sample_A2_S2_L001_R2_001.fastq.gz	ATCGATCG...	GGTATCGA...
```

---

## Example Run Command

The tool generates a ready-to-run command like:

```bash
CRISPRessoBatch --batch_settings NGS_Editing.batch \
  --output_folder CRISPRessoBatch_output_NGS_Editing \
  --name NGS_Editing
```

---

## Technical Notes

- All processing is local — no data ever leaves the browser
- Uses [JSZip](https://stuk.github.io/jszip/) (CDN) to unzip allele frequency tables in the browser
- Uses [SheetJS](https://sheetjs.com/) (CDN) for Excel export
- Single HTML file with no other external dependencies

---

## How it Relates to the CRISPResso2 Tool Suite

| Tool | Use case |
|---|---|
| **CRISPRessoBatch** | ← **This tool targets this** — multiple amplicon sequencing samples |
| CRISPRessoPooled | Multiple amplicons from a single pooled FASTQ |
| CRISPRessoWGS | Whole-genome sequencing (requires pre-aligned BAM) |
| CRISPRessoCompare | Comparing two samples (e.g. treated vs. untreated) |
| CRISPRessoAggregate | Aggregating results from previous runs |

---

## Roadmap

- [ ] 96-well plate heatmap visualization of editing rates
- [ ] CRISPRessoCompare batch pairing for treated vs. untreated samples
- [ ] Run history to save and reload previous batch configurations
- [ ] CRISPRessoPooled batch file generation support

---

## Contributing

Pull requests welcome. If you run into a naming convention that isn't detected correctly, please open an issue with an example filename.

---

## License

MIT

---

## Acknowledgements

Built around [CRISPResso2](https://github.com/pinellolab/CRISPResso2) by the Pinello Lab.
