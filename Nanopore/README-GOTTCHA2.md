# GOTTCHA2 Nanopore (CDC) — Species Detection Summary

Taxonomic profiling of the **CDC Nanopore long-read sample**
(`Nanopore_llnl_66d1047e`) with **GOTTCHA2** (species-level signature database
`gottcha_db.species.fna`, the full multi-domain DB that includes Bacteria,
Archaea, Eukaryota and ~13k viruses). These two tables are consolidated from
GOTTCHA2's `*.tsv` / `*.lineage.tsv` outputs.

## Output files

| File | Description | Entries |
|------|-------------|---------|
| `Nanopore_GOTTCHA2_species.tsv` | Species-level detections (one row per species) | **271 species** |
| `Nanopore_GOTTCHA2_all_taxids.tsv` | All detected taxa at **every rank** (superkingdom → strain) | **1,014 rows** |

### Columns
- species file: `superkingdom, taxid, name, read_count, total_bp_mapped, rel_abundance`
- all-taxids file: `level, taxid, name, read_count, total_bp_mapped, rel_abundance`

Rank breakdown of `all_taxids`: superkingdom 2, phylum 20, class 34, order 91,
family 188, genus 407, species 271, strain 1.

## Key findings

- **271 species** detected; total signature-assigned reads ≈ **129,206**.
- The community is **entirely bacterial** — all 271 species are `Bacteria`.
  **No viruses, archaea, or eukaryotes** passed GOTTCHA2's signature-coverage
  thresholds.
- Top species by read count:

  | Reads | TaxID | Species |
  |-------|-------|---------|
  | 5,129 | Neisseria_suis | *Neisseria suis* |
  | 4,259 | 1450134 | *Stenotrophomonas bentonitica* |
  | 3,887 | 1537215 | *Pseudooceanicola algae* |
  | 3,772 | 1868793 | *Jeotgalibaca porci* |
  | 3,621 | 1482074 | *Hartmannibacter diazotrophicus* |
  | 3,495 | 1622 | *Ligilactobacillus murinus* |

## Why no viruses?

The GOTTCHA2 database **does contain viruses** (~13k viral species), so the
absence is **not** a database limitation. GOTTCHA2 only reports a taxon when
reads cover enough of its **unique signature region** (a breadth/depth
threshold). This CDC sample is virus-sparse (read-level Kraken2 finds only a
handful of viral reads), far too few to meet that threshold — so all viruses are
filtered out.

**Precise statement:** it is not that the DB lacks viruses, nor that GOTTCHA2
can never detect viruses — GOTTCHA2's signature-coverage method is **insensitive
to low-abundance viruses, so on this virus-sparse sample it detects none**. (On
a virus-rich sample, e.g. the 708 simulated virome at ~14% viral, the same
GOTTCHA2 + DB recovered 87 viral species.) For low-abundance virus detection,
read-level Kraken2 (TaxProfiler / TaxTriage) is more sensitive.

## Notes & caveats

- **35 of 271 taxids are name placeholders** (e.g. `Neisseria_suis`) rather than
  numeric NCBI TaxIDs — GOTTCHA2 emits these when a reference could not be mapped
  to a numeric NCBI ID. The species name is still valid.
- Counts/abundances reflect GOTTCHA2's signature model, not raw read fractions,
  so they are not directly comparable to Kraken2/CLARK read counts.
