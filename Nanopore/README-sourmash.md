# sourmash Nanopore (CDC) — Combined Classification Lists

Consolidated lists of what **sourmash** detected in the **CDC Nanopore
long-read sample** (`Nanopore_llnl_66d1047e`), merging sourmash's two
per-database results (viruses + bacteria/archaea) into one table each.

## Files in this folder

| File | Content | Rows |
|------|---------|------|
| `Nanopore_CDC_sourmash_species_ALL.tsv` | **Species level only** — the list of detected species | 496 |
| `Nanopore_CDC_sourmash_all_ranks_ALL.tsv` | **All taxonomic ranks** (superkingdom → strain) — the full multi-level profile | 962 |

The two files are a pair from the **same sourmash run**: `species_ALL` is the
species-level slice; `all_ranks_ALL` keeps every rank (so you can see the
domain/phylum/…/genus breakdown, not just species). They share the same columns
except `all_ranks_ALL` has an extra **`rank`** column.

Rank breakdown of `all_ranks_ALL` (962 rows): superkingdom 2, phylum 15,
class 21, order 49, family 100, genus 254, species 496, strain 25.

## How it was produced

Nanopore reads → **Flye (`--meta`) assembly** → sourmash `sketch` → `gather`
(k = 31) → `tax metagenome`, run against **two databases separately**, then
merged (species rank only):

| Source database | scaled | Origin file |
|-----------------|--------|-------------|
| NCBI viruses 2025.01 | 50 | `*.viral.summarized.csv` |
| GTDB rs226 (Bacteria+Archaea) | 1000 | `*.prok.summarized.csv` |

## Columns

| Column | Meaning |
|--------|---------|
| `source` | Which database the taxon came from: `Viruses` or `Bacteria/Archaea` |
| `rank` | Taxonomic rank (only in `all_ranks_ALL`; superkingdom…strain) |
| `name` | Taxon name (GTDB `s__`/`p__`… prefix removed) |
| `fraction` | Share of the sample assigned to this taxon (sourmash k-mer fraction) |
| `f_weighted` | Abundance-weighted fraction (`f_weighted_at_rank`) |
| `bp_match` | Matched base pairs |
| `lineage` | Full lineage (GTDB `d__/p__/…/s__` or NCBI viral lineage) |

Rows are sorted by `fraction`, descending (`all_ranks_ALL` is grouped by source
then rank).

## What it shows

- **496 species total** (`species_ALL`): **471 Bacteria/Archaea** (GTDB) +
  **25 Viruses** (NCBI).
- At the domain level (`all_ranks_ALL`, superkingdom rows), the sample is
  **dominated by bacteria** (~88% of k-mers classified by GTDB); viruses are a
  **tiny 0.29%**.
- Top bacteria: *Jeotgalibaca porci* (8.65%), *Chlorobaculum tepidum* (3.34%),
  *Sphingobacterium lactis* (2.02%), *Microbacterium oleivorans_D*,
  *Pseudazoarcus pumilus*.
- Top viruses (all ~1e-4, mostly phages): *uncultured virus*, *Salmonella phage
  S.Kent 1-2-1*, *Acinetobacter phage MD-2021a*, *Listeria phage LM 4-11-1*,
  *uncultured human fecal virus*.

## How to read / caveats

- `fraction` for viral vs prokaryote rows come from **separate gathers with
  different `scaled`**, each normalized within its own run — **do not add them
  directly**.
- **No NCBI taxids**: sourmash is lineage-based. GTDB names carry lineage
  suffixes (e.g. `Microbacterium oleivorans_D`); NCBI viruses use plain names.
- Use `species_ALL` for a species list; use `all_ranks_ALL` to see the
  higher-rank breakdown (domain/phylum/…/genus). Both are derived from
  sourmash's `*.summarized.csv`; those and the `.gather/.kreport/.krona/.human`
  files are the raw per-database outputs.
- Notably, sourmash **does** report viruses here (25 species) — because it ran a
  dedicated high-resolution viral gather (scaled = 50) — whereas GOTTCHA2 and the
  contig-only Kraken2 runs report 0. Even so, the viral abundance is minimal,
  consistent with the virus-sparse CDC sample.
- Different reference databases (GTDB + NCBI-viruses) than the Kraken2 Standard
  DB used by other tools, so species counts are not directly comparable across
  platforms.
