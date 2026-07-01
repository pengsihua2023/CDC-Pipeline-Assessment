# TaxTriage Illumina (CDC) — Classification Summary

Taxonomic classification of the **CDC Illumina short-read sample**
(`Illumina_llnl_66ce4dde`) with **nf-core/taxtriage** (Nextflow). Reads are
classified with **Kraken2** (Standard DB, 2024-06-05); TaxTriage then produces a
trimmed top-organism table and a MultiQC-ready table.

## Output files (3 representations of one analysis)

| File | What it is | Use |
|------|------------|-----|
| `Illumina_CDC.kraken2.report.txt` | **Raw, full Kraken2 report** (all ranks, tree) | **Primary / most complete** — use for full results & virus checks |
| `Illumina_CDC.top_report.tsv` | TaxTriage **top-organism table** (species-level, abundance-filtered) | Quick view of the main organisms |
| `Illumina_CDC.krakenreport_mqc.tsv` | Same top table reformatted for **MultiQC** | Report figures only |

`top_report` / `mqc` columns: `abundance, clade_fragments_covered,
number_fragments_assigned, rank, taxid, name`.
Kraken2 report columns: `percent, clade_reads, direct_reads, rank, taxid, name`.

## Results (from the full Kraken2 report)

| Superkingdom | Reads | Share |
|--------------|-------|-------|
| Bacteria | 4,969,519 | 99.41% |
| Archaea | 79 | ~0 |
| Eukaryota | 66 | ~0 |
| **Viruses** | **7** | 0.0001% |
| Unclassified | 29,158 | 0.58% |

- **5,312 species** in the full report; the **top_report keeps 462** of them
  (after TaxTriage's abundance/top filtering).
- The community is **overwhelmingly bacterial**.

## Viruses: 7 reads / 6 species — but only visible in the raw report

The full Kraken2 report contains **7 viral reads across 6 species** (e.g.
*Pavtokvirus PEp14*, *Pandoravirus salinus*, *Fowl aviadenovirus D*, ...). These
are at the detection limit (1–2 reads each).

> **Important:** the **`top_report.tsv` / `mqc.tsv` contain NO viruses** — those
> 6 viral species fall below TaxTriage's abundance/top cutoff and are filtered
> out. To see the viral signal you **must use the raw
> `Illumina_CDC.kraken2.report.txt`**, not the top/mqc tables.

This is consistent with all tools on this virus-sparse CDC sample: read-level
Kraken2 recovers a handful of viral reads, while assembly-based (KrakenMetaReads,
nf-core/mag) and signature (GOTTCHA2) pipelines recover none.

## Cross-tool note

TaxTriage and TaxProfiler both run **Kraken2 on the same Standard DB** at
read level, so their results are essentially identical (both: 99.41% Bacteria,
7 viral reads, ~5,312 species) — they validate each other.

## Notes

- Counts are **per read**, comparable to TaxProfiler but not to contig-level
  pipelines.
- `top_report`/`mqc` are **filtered summaries** — do not use them to judge
  presence/absence of low-abundance taxa (viruses in particular). Use the raw
  Kraken2 report for that.
- The long 5,312-species tail includes many 1–few-read species; treat very
  low-count calls as possible false positives.
