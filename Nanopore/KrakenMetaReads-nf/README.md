# KrakenMetaReads-nf Nanopore (CDC) — Assembly-based Classification Summary

Taxonomic classification of the **CDC Nanopore long-read sample**
(`Nanopore_llnl_66d1047e`) with the **KrakenMetaReads-nf** workflow for long
reads: reads are assembled with **Flye (`--meta`)**, then the **contigs are
classified with Kraken2** (Standard DB, 2024-06-05). The file is the Kraken2
report on the Flye contigs.

## Output file

| File | What it is |
|------|------------|
| `Sample_2_nanpore_CDC_flye_report.txt` | Kraken2 report on **Flye contigs** (tree format) |

Kraken2 report columns: `percent, clade_count, direct_count, rank, taxid, name`
(here `count` = number of **contigs**, not reads).

## Results

| Metric | Value |
|--------|-------|
| Classified contigs | 1,208 |
| Unclassified contigs | 3 |
| Species (rank = S) | 320 |
| Bacteria | 99.67% (1,207 contigs) |
| Eukaryota | 0.08% (1 contig) |
| **Viruses** | **0** |

- Only **1,208 contigs** were assembled from the Nanopore reads — a small
  assembly, so the species list (320) is modest.
- The community is **almost entirely bacterial**. Top species by contig count:

  | Contigs | TaxID | Species |
  |---------|-------|---------|
  | 138 | 1868793 | *Jeotgalibaca porci* |
  | 66 | 1097 | *Chlorobaculum tepidum* |
  | 46 | 2067960 | *Pseudazoarcus pumilus* |
  | 45 | 372663 | *Isoptericola dokdonensis* |
  | 35 | 402297 | *Nocardioides daphniae* |
  | 31 | 240521 | *Rhizobium daejeonense* |

## Viruses: none detected (expected for an assembly-based method)

**No viral contigs** were produced (no `Viruses` / taxid 10239 node). Two
reasons:

1. **Sample**: the CDC sample's viral fraction is extremely low (read-level
   Kraken2 finds only a handful of viral reads).
2. **Method**: this is **assembly-based**. Low-abundance viruses lack the
   coverage to assemble into contigs, so they vanish before classification —
   contig-level methods are the least sensitive to rare taxa.

For low-abundance virus detection on this sample, **read-level Kraken2
(TaxProfiler / TaxTriage)** is more sensitive; assembly (this workflow) and
signature-coverage (GOTTCHA2) recover none.

## Notes

- Counts are **per contig**, not per read — not directly comparable to
  read-level tools (CLARK, TaxProfiler, TaxTriage) which report far more taxa.
- The small contig count (1,208) reflects the difficulty of assembling this
  Nanopore sample; contig-level results here are the least sensitive of the
  Nanopore tools, so treat presence/absence with that in mind.
- Same Standard Kraken2 DB (2024-06-05) as the other tools, so differences vs
  those reflect the **assembly step**, not the database.
