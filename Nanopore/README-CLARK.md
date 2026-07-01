# CLARK Nanopore (CDC) — Species Detection Summary

Taxonomic classification of the **CDC Nanopore long-read sample**
(`Nanopore_llnl_66d1047e`) with **CLARK** (k = 20, species-level, multi-domain
database). These two tables are consolidated from CLARK's abundance output
(read-level, ~229,765 classified reads).

## Output files

| File | Description | Entries |
|------|-------------|---------|
| `CLARK_nanopore_all_taxa.tsv` | All detected taxa, **not collapsed** (one row per CLARK TaxID) | **3,698 taxa** |
| `CLARK_nanopore_species_merged.tsv` | Detected taxa **collapsed to species** (binomial = first two name tokens) | **2,945 species** |

### Columns

**`CLARK_nanopore_all_taxa.tsv`** (sorted by read count, descending)
`TaxID, Name, Count, Proportion_All(%), Proportion_Classified(%), Lineage`

**`CLARK_nanopore_species_merged.tsv`** (sorted by read count, descending)
`Species, n_taxids, representative_taxid, all_taxids, Count,
Proportion_All(%), Proportion_Classified(%), Lineage`
- `n_taxids` – number of original TaxIDs merged into this species
- `representative_taxid` – highest-read TaxID within the species
- `all_taxids` – semicolon-separated list of all merged TaxIDs (traceable)

## Key findings

- **3,698 taxa** detected → **2,945 species** after collapsing (170 species
  merged more than one original TaxID, i.e. strain-level entries).
- The community is **predominantly bacterial**, with a notable **host (human)**
  component. Top species by read count:

  | Reads | Species |
  |-------|---------|
  | 6,631 | *Acidovorax temperans* |
  | 5,831 | *Homo sapiens* (host) |
  | 5,133 | *Uruburuella suis* |
  | 4,610 | *Stenotrophomonas* sp. |
  | 4,608 | *Streptococcus parasuis* |
  | 3,889 | *Pseudooceanicola algae* |

- **Viruses are negligible**: only **5 viral/phage taxa**, each with a single
  read (*Asfivirus haemorrhagiae*, *Erwinia phage vB_EamM_Caitlin*,
  *Readingvirus PSA1*, *Vellamovirus rhodeisland44*, plus one likely-spurious
  *Treponema phagedenis* — a bacterium). Consistent with all tools on this
  sample: the viral signal is essentially absent.

## Notes & caveats

- **Not gamma-filtered.** This is CLARK's full abundance output; low-confidence
  assignments (a known CLARK issue with a multi-domain DB) are still present.
  A gamma ≥ 0.2 high-precision table can be produced if higher precision is
  needed.
- **Species merging is name-based** (binomial), not strict NCBI taxonomy
  (taxonkit/taxdump unavailable locally). Most merges are correct, but
  `"Genus sp."` entries from different unnamed species can be over-merged; the
  `all_taxids` column preserves the originals.
- One `UNKNOWN` row is present (CLARK's unassigned bucket).
- Counts are **per read**; TaxIDs are standard NCBI numeric IDs.
- Different database/method than the Kraken2-based tools, so species counts are
  not directly comparable across platforms.
