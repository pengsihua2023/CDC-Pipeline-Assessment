# nf-core/taxprofiler Nanopore (CDC) — Classification Summary

Taxonomic profiling of the **CDC Nanopore long-read sample**
(`Nanopore_llnl_66d1047e`, 228,951 reads) with the real **nf-core/taxprofiler**
(Nextflow) pipeline: reads classified with **Kraken2** (Standard DB, 2024-06-05).
Long reads do **not** use Bracken (Bracken's model is for short reads), so the
raw Kraken2 report is the final result here.

## Files in this folder

| File | Content | Rows |
|------|---------|------|
| `kraken2_Viral_ref-bracken_combined_reports.txt` | **Raw taxprofiler combined Kraken2 report** (source of the two tables below) | 3,123 |
| `Nanopore_CDC_TaxProfiler_species.tsv` | **Species-level list** (rank = S), consolidated | 1,672 |
| `Nanopore_CDC_TaxProfiler_all_taxids.tsv` | **All ranks** (superkingdom → strain), consolidated | 3,118 |

The two `.tsv` tables are the clean, consolidated views; the `combined_reports.txt`
is the raw pipeline output they were derived from.

### Columns
- `*_species.tsv`: `taxid, name, clade_reads, direct_reads, percent`
  (sorted by reads, descending)
- `*_all_taxids.tsv`: `rank, taxid, name, clade_reads, direct_reads, percent`
  (`clade_reads` = reads assigned to that clade incl. descendants)

## Results

| Superkingdom | Reads | Share |
|--------------|-------|-------|
| Bacteria | 227,354 | 99.30% |
| Eukaryota | 28 | ~0 |
| Archaea | 18 | ~0 |
| **Viruses** | **0** | — |

- **1,672 species** detected (rank = S); the community is **overwhelmingly
  bacterial**.
- Top species by read count:

  | Reads | TaxID | Species |
  |-------|-------|---------|
  | 6,612 | 80878 | *Acidovorax temperans* |
  | 5,113 | 252130 | *Uruburuella suis* |
  | 4,580 | 1501662 | *Streptococcus parasuis* |
  | 4,242 | 1904944 | *Stenotrophomonas* sp. LM091 |
  | 3,876 | 1537215 | *Pseudooceanicola algae* |
  | 3,821 | 1868793 | *Jeotgalibaca porci* |

## Viruses: none detected

Unlike the **Illumina** TaxProfiler run (which recovered 7 viral reads), this
**Nanopore** run finds **no viral reads at all** (no Viruses / taxid 10239 node).
The CDC sample is virus-sparse, and the Nanopore library here yielded even fewer
viral reads than Illumina, so nothing reached the classifier as viral. This is
consistent with the other Nanopore tools on this sample (GOTTCHA2, assembly-based
Kraken2 also = 0 viruses).

## Notes

- Counts are **per read**, comparable to the Nanopore TaxTriage run (same Kraken2
  + Standard DB), but not to contig-level pipelines.
- The DB label is `Viral_ref` for historical reasons, but it is the full
  **Standard** database (Bacteria + Archaea + Viruses + …), not a virus-only DB.
- **No Bracken correction is applied** for long reads — the combined report is
  labeled "…bracken…" by the pipeline, but Bracken is inappropriate for long
  reads, so the Kraken2 assignments are used directly.
- The 1,672-species long tail includes many low-count species; treat very
  low-read species as possible false positives.
