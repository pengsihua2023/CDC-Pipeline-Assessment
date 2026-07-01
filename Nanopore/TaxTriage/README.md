# TaxTriage Nanopore (CDC) — Classification Summary

Taxonomic classification of the **CDC Nanopore long-read sample**
(`Nanopore_llnl_66d1047e`, ~228,202 classified reads) with **nf-core/taxtriage**
(Nextflow). Reads are classified with **Kraken2** (Standard DB, 2024-06-05);
TaxTriage then produces a trimmed top-organism table and a MultiQC-ready table.

## Files in this folder (3 representations of one analysis)

| File | What it is | Use |
|------|------------|-----|
| `Nanopore_CDC1.kraken2.report.txt` | **Raw, full Kraken2 report** (all ranks, tree) | **Primary / most complete** — use for full results & virus checks |
| `Nanopore_CDC1.top_report.tsv` | TaxTriage **top-organism table** (species-level, abundance-filtered) | Quick view of the main organisms |
| `Nanopore_CDC1.krakenreport_mqc.tsv` | Same top table reformatted for **MultiQC** | Report figures only |

`top_report`/`mqc` columns: `abundance, clade_fragments_covered,
number_fragments_assigned, rank, taxid, name`.
Kraken2 report columns: `percent, clade_reads, direct_reads, rank, taxid, name`.

## Results (from the full Kraken2 report)

| Superkingdom | Reads | Share |
|--------------|-------|-------|
| Bacteria | 228,109 | 99.28% |
| Eukaryota | 28 | ~0 |
| Archaea | 18 | ~0 |
| **Viruses** | **0** | — |
| Unclassified | 1,563 | 0.68% |

- **1,676 species** in the full report; the **top_report keeps 145** of them
  (after TaxTriage's abundance/top filtering).
- The community is **overwhelmingly bacterial**. Top species by read count:

  | Reads | TaxID | Species |
  |-------|-------|---------|
  | 6,632 | 80878 | *Acidovorax temperans* |
  | 5,133 | 252130 | *Uruburuella suis* |
  | 4,608 | 1501662 | *Streptococcus parasuis* |
  | 4,252 | 1904944 | *Stenotrophomonas* sp. LM091 |
  | 3,889 | 1537215 | *Pseudooceanicola algae* |
  | 3,835 | 1868793 | *Jeotgalibaca porci* |

## Viruses: none detected

The full Kraken2 report contains **no viral reads** (no Viruses / taxid 10239
node), and consequently neither do the `top_report`/`mqc` tables. Unlike the
**Illumina** TaxTriage run (7 viral reads), this Nanopore library yielded no
viral reads at all. Consistent with the other Nanopore tools on this virus-sparse
sample (TaxProfiler, GOTTCHA2, assembly-based Kraken2 all = 0 viruses).

## Cross-tool note

TaxTriage and TaxProfiler both run **Kraken2 on the same Standard DB** at read
level, so their Nanopore results are essentially identical (both: ~99.3%
Bacteria, 0 viruses, ~1,672–1,676 species) — they validate each other.

## Notes

- Counts are **per read**, comparable to the Nanopore TaxProfiler run but not to
  contig-level pipelines.
- `top_report`/`mqc` are **filtered summaries** (145 species) — do not use them
  to judge presence/absence of low-abundance taxa. Use the raw Kraken2 report.
- The long 1,676-species tail includes many 1–few-read species; treat very
  low-count calls as possible false positives.
