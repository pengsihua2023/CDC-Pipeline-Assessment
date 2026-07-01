# CLARK Illumina (CDC) — Species Detection Summary

Taxonomic classification of the **CDC Illumina short-read sample**
(`Illumina_llnl_66ce4dde`, paired-end) with **CLARK** (k = 20, species-level,
multi-domain database). Read-level assignments were filtered at **gamma ≥ 0.2**
(`virus_results_CDC.abundance.g0.2.csv`) to remove low-confidence (mostly
bacterial-noise) calls before tabulating abundances.

## Source

- Classifier: CLARK, k-mer = 20, mode 0 (full), gamma filter ≥ 0.2
- Input table: `virus_results_CDC.abundance.g0.2.csv`
- Total classified reads (after gamma filter): **3,164,813**

## Output tables

| File | Description | Entries |
|------|-------------|---------|
| `CLARK_Illumina_CDC_all_taxa.tsv` | All detected taxa, **not collapsed** (one row per CLARK TaxID) | **2,568 taxa** |
| `CLARK_Illumina_CDC_species_merged.tsv` | Detected taxa **collapsed to species** (binomial = first two name tokens) | **2,082 species** |

### Columns

**`CLARK_Illumina_CDC_all_taxa.tsv`** (sorted by read count, descending)
`TaxID, Name, Count, Proportion_All(%), Proportion_Classified(%), Lineage`

**`CLARK_Illumina_CDC_species_merged.tsv`** (sorted by read count, descending)
`Species, n_taxids, representative_taxid, all_taxids, Count, Proportion_All(%), Proportion_Classified(%), Lineage`
- `n_taxids` – number of original TaxIDs merged into this species
- `representative_taxid` – the highest-read TaxID within the species
- `all_taxids` – semicolon-separated list of all merged TaxIDs (traceable)

## Key findings

- **2,568 taxa** detected; collapsing to species yields **2,082 species**
  (112 species combined more than one original TaxID, i.e. strain/sub-species
  level entries merged into their species).
- The community is **overwhelmingly bacterial**. Top species by read count:

  | Reads | Species |
  |-------|---------|
  | 122,991 | *Microvirga ossetica* |
  | 120,421 | *Jeotgalibaca porci* |
  | 91,916 | *Tolumonas auensis* |
  | 91,213 | *Pseudazoarcus pumilus* |
  | 86,700 | *Epidermidibacterium keratini* |

- **Viruses are extremely rare**: only **5 viral/phage taxa**, each with a
  single read (`Arvduovirus ArV2`, `Begomovirus jatrophae`, `Edenvirus eden`,
  `Pandoravirus quercus`, `Saldibavirus natrii`). Consistent with all other
  platforms/tools on this CDC sample, the viral signal is negligible.

## Notes & caveats

- **Species merging is name-based** (binomial), not strict NCBI taxonomy, because
  `taxonkit`/taxdump were unavailable locally. Most merges are correct, but
  `"Genus sp."` entries from different unnamed species can be over-merged; the
  `all_taxids` column preserves the originals for inspection.
- Tables are derived from the **gamma ≥ 0.2** high-precision set. The unfiltered
  abundance table (`virus_results_CDC.abundance.csv`) was **not** produced — the
  pipeline's fallback abundance step failed on the per-read CSV, but the gamma
  filtered table used here is the recommended high-precision output.
- TaxIDs are standard NCBI numeric IDs (no name placeholders).
