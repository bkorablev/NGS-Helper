# CRISPResso2 Batch Prep Tool

A single self-contained HTML file for building [CRISPRessoBatch](https://github.com/pinellolab/CRISPResso2) `.batch` files and aggregating results. Opens locally in Chrome — no installation, no server, no internet connection required after opening the file.

> **Note:** This tool targets **CRISPRessoBatch** (multi-sample amplicon sequencing batch mode). For CRISPRessoWGS (whole-genome BAM) or CRISPRessoPooled (pooled amplicons from a single FASTQ), see the CRISPResso2 suite.

---

## Features at a Glance

- Auto-detects Illumina R1/R2 pairs from standard naming
- Editable sample table with fill-down, Enter-key row navigation, per-row delete, and per-sample quantification window center (`wc`)
- Pre-run validation: missing sequences, guide not in amplicon, unusual guide lengths, duplicate names, incomplete pairs
- Generates a correctly formatted `.batch` file and ready-to-run terminal command
- Results aggregator with a dashboard of five analysis views: Summary, Editing Breakdown, Indel Distribution, Top Alleles, and Nucleotide Composition
- Sample-to-sample comparison panel with Excel export
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
An editable table pre-populated with detected sample names. Columns: sample name, R1/R2 filenames, amplicon sequence, guide RNA, optional HDR template, and quantification window center (`wc`). The `wc` column has a preset dropdown (−3 for Cas9, +1 for Cpf1/Cas12a) with a custom numeric entry option. Supports Excel-like Enter key navigation between rows, fill-down buttons to propagate a value to all rows below, and per-row delete.

**Step 3 — Global Parameters**
Set CRISPRessoBatch parameters that apply to all samples: output folder, quantification window size, minimum allele frequency to plot, and skip-failed-samples toggle. Only non-default values are included in the generated run command.

**Step 4 — Validate**
Pre-run checks including missing amplicon or guide sequences, guide RNA not found in amplicon (forward and reverse complement both checked), unusual guide lengths (flags outside 17–23 bp), duplicate sample names, and incomplete R1/R2 pairs.

**Step 5 — Export**
Preview the formatted tab-separated `.batch` file, copy the generated `CRISPRessoBatch` run command, and download the `.batch` file.

### Results Aggregator (Tab 6)

Browse and select a CRISPRessoBatch output folder. The tool automatically finds and parses all `CRISPResso_on_<samplename>` subfolders and presents results through a dashboard of analysis views.

---

## Results Dashboard

After loading a results folder, a tile dashboard gives one-click access to five views:

### Summary

Tabular overview of all samples. Columns:

Sample name · Reads in Input · Reads Aligned · Aligned % · Unmodified reads + % · NHEJ reads + % · HDR reads + % (when HDR template was used) · Ambiguous reads + %

- Rows are sortable and selectable for cross-sample comparison
- Excel export (`.xlsx`) with Summary and Raw data sheets

### Editing Breakdown

Stacked SVG bar chart per sample showing the proportion of Unmodified / NHEJ / HDR / Ambiguous reads, color-coded to match the tool theme.

**Sample comparison panel:** select any rows in the Summary table to open a floating comparison panel showing their breakdown bars side by side. Includes its own Excel export.

### Indel Distribution

Per-sample bar chart of insertion and deletion sizes, derived from `Reference.Indel_histogram.txt` (or `Indel_histogram.txt` for non-HDR runs). Toggle to show/hide frameshift markers. Sample selector chips at the top allow switching between samples without leaving the view.

### Top Alleles

Sequence-level visualization of the most frequent editing outcomes for a selected sample, drawn from the allele frequency tables. Displays each allele's sequence with mismatches, insertions, and deletions highlighted, alongside read count and frequency. Distinguishes alleles that align to the Reference amplicon from those classified as HDR.

### Nucleotide Composition

Per-position stacked base-frequency heatmap (A / T / C / G, with optional deletion track) derived from `Nucleotide_frequency_table.txt` (or `Reference.Nucleotide_frequency_table.txt` for HDR runs).

- **Full amplicon view** at the top shows the entire reference with a compact heatmap. The sgRNA position is bracketed when its sequence is known.
- **50 nt zoom panel** below shows a magnified view of any 50-nucleotide window. Hover over the full amplicon chart to pan the zoom window; the active window is highlighted with a selection rectangle on the full chart. Position is preserved when the mouse leaves. Switching samples resets the zoom to the sgRNA midpoint.
- Y-axis toggle between *% of aligned reads* and *% of bases at position*.
- Compatible with both standard and HDR CRISPResso output folder layouts.

---

## KO Classification (optional)

Disabled by default. Enable the toggle in the Results tab — no reload required.

**Settings:**
- Expected allele copies (ploidy) — e.g. 2 for diploid (RPE-1), 3 for triploid (K562)
- In-frame deletion override (bp) — in-frame deletions ≥ this size are reclassified as likely disruptive

**How it works:**
Parses allele frequency tables per sample (prefers `Reference.Alleles_frequency_table_around_sgRNA_*.txt` for HDR runs, falls back to `Alleles_frequency_table_around_sgRNA_*.txt`, then zip). Groups alleles by deletion/insertion signature and takes the top N alleles by frequency, where N = ploidy setting.

**Per-allele types:** Wildtype · Frameshift · In-frame · Large in-frame deletion · Substitution only

**Sample-level classification:**

| Status | Condition |
|---|---|
| **Full KO** | All alleles are Frameshift or Large in-frame deletion |
| **Likely Partial KO** | All alleles edited, but mix includes In-frame mutations |
| **Partial KO** | At least one allele is Wildtype alongside edited alleles |
| **No KO** | All alleles Wildtype |

Warnings are shown for in-frame mutations and large deletions that may span exon-intron boundaries. KO status and allele breakdown are included in the Excel export.

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


## Contributing

Pull requests welcome. If you run into a naming convention that isn't detected correctly, please open an issue with an example filename.

---

## License

MIT

---

## Acknowledgements

Built around [CRISPResso2](https://github.com/pinellolab/CRISPResso2) by the Pinello Lab.
