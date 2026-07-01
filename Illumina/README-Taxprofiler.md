# nf-core/taxprofiler Illumina (CDC) — Classification Summary

Taxonomic profiling of the **CDC Illumina short-read sample**
(`Illumina_llnl_66ce4dde`) with the real **nf-core/taxprofiler** (Nextflow)
pipeline: reads are classified with **Kraken2** (Standard DB, 2024-06-05) and
abundances re-estimated at species level with **Bracken**.

## Output file

| File | Description |
|------|-------------|
| `CDC_short_kraken2_Viral_ref-bracken_combined_reports.txt` | Bracken-corrected, taxprofiler-combined Kraken2 report (read-level, species-resolved) |

- Total reads: **4,999,000**
- Format (taxprofiler combined): `perc, tot_all, tot_lvl, 1_all, 1_lvl,
  lvl_type, taxid, name`
  - `tot_all` = reads assigned to that clade (incl. descendants)
  - `lvl_type` = rank code (D / P / C / O / F / G / **S** = species / U)
  - `name` = indented taxon name

> This is the **Bracken-corrected** report, the recommended final result for
> short reads (Bracken redistributes Kraken2 reads to species level for more
> accurate abundance).

## Results

| Superkingdom | Reads (`tot_all`) | Share |
|--------------|-------------------|-------|
| Bacteria | 4,969,519 | 99.41% |
| Archaea | 79 | ~0 |
| Eukaryota | 66 | ~0 |
| **Viruses** | **7** | 0.0001% |
| Unclassified | 29,158 | 0.58% |

- **5,312 species** reported (rank = S).
- The community is **overwhelmingly bacterial**. Top species (by read count):

  | Reads | TaxID | Species |
  |-------|-------|---------|
  | 151,385 | 1882682 | *Microvirga ossetica* |
  | 141,662 | 372663 | *Isoptericola dokdonensis* |
  | 132,735 | 593909 | *Mesorhizobium opportunistum* |
  | 126,012 | 2067960 | *Pseudazoarcus pumilus* |
  | 124,959 | 1868793 | *Jeotgalibaca porci* |
  | 102,146 | 1891644 | *Epidermidibacterium keratini* |

## Viruses: detected, but only 7 reads

Unlike the assembly-based (KrakenMetaReads, nf-core/mag) and signature
(GOTTCHA2) pipelines — which find **0** viruses on this sample — this
**read-level Kraken2** run does recover viruses, but only **7 reads** in the
entire Viruses superkingdom (0.0001%). This confirms the pattern seen across all
tools:

- The CDC sample is **virus-sparse** (essentially no viral signal).
- **Read-level k-mer classification is the most sensitive** to such rare taxa
  (a few reads are enough to report), whereas assembly and signature-coverage
  methods miss them entirely.

## Notes

- Counts are **per read** (not per contig), so they are comparable to the other
  read-level Kraken2 runs (e.g. TaxTriage) but **not** to the contig-level
  pipelines.
- Same Standard Kraken2 DB (2024-06-05) as TaxTriage / KrakenMetaReads / mag-nf,
  so differences vs those tools reflect read-vs-contig input, not the database.
- The DB label is `Viral_ref` for historical reasons, but it is the full
  **Standard** database (Bacteria + Archaea + Viruses + …), not a virus-only DB.
- The 5,312-species long tail includes many low-count species; treat very
  low-read species with caution (possible false positives).
