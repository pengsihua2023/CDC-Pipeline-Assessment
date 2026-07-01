# GOTTCHA2 Illumina (CDC) — Species Detection Summary

Taxonomic profiling of the **CDC Illumina short-read sample**
(`Illumina_llnl_66ce4dde`) with **GOTTCHA2** (species-level signature database
`gottcha_db.species.fna`). This document summarizes the consolidated species
list so a reader can interpret the result at a glance.

## Source

- Classifier: **GOTTCHA2** (`gottcha2 profile -xm sr`), signature-based, paired-end
- Database: **new Standard GOTTCHA2 DB** `gottcha_db.species.fna` (~198 GB,
  multi-domain — Bacteria, Archaea, Eukaryota, and ~13k viruses)
- Raw GOTTCHA2 outputs: `Illumina_llnl_66ce4dde_R1.tsv` (main),
  `.lineage.tsv`, `.full.tsv`
- Consolidated species table: `CDC_Illumina_GOTTCHA2_species_list.tsv`

## Output file

| File | Description | Entries |
|------|-------------|---------|
| `CDC_Illumina_GOTTCHA2_species_list.tsv` | Species-level detections, one row per species | **356 species** |

### Columns

`superkingdom, taxid, name, read_count, rel_abundance`

- `read_count` – reads assigned to the species' signature
- `rel_abundance` – GOTTCHA2 relative abundance (signature/depth normalized)

## Key findings

- **356 species** detected (all at `superkingdom = Bacteria`).
- Superkingdom-level read assignment shows the community is **overwhelmingly
  bacterial**, with only trace archaeal/eukaryotic and **zero viral** signal:

  | Superkingdom | Reads | Rel. abundance |
  |--------------|-------|----------------|
  | Bacteria | 4,755,684 | 0.999999 |
  | Archaea | 3 | ~0 |
  | Eukaryota | 17 | ~0 |
  | **Viruses** | **0** | — |

  (Archaea/Eukaryota reads are below the species threshold, so all 356 reported
  species are bacterial.)
- Top species by read count:

  | Reads | TaxID | Species |
  |-------|-------|---------|
  | 198,850 | 1868793 | *Jeotgalibaca porci* |
  | 170,279 | 1882682 | *Microvirga ossetica* |
  | 162,116 | 2527983 | *Caulifigura coniformis* |
  | 149,358 | 43948 | *Tolumonas auensis* |
  | 126,951 | 2067960 | *Pseudazoarcus pumilus* |
  | 126,841 | 1891644 | *Epidermidibacterium keratini* |

## Why no viruses? (re-run with the new full Standard DB)

This run used the **new ~198 GB Standard database that contains all microbes**
(Bacteria + Archaea + Eukaryota + ~13k viruses) — confirmed by the trace
Archaea/Eukaryota assignments that the old bacteria-only DB could not produce.
**Even so, the result is essentially unchanged: still 0 viruses.**

The absence is therefore **not** a database limitation. GOTTCHA2 requires reads
to cover a taxon's **unique signature region** above a breadth/depth threshold.
In this CDC sample the viral fraction is extremely low (only a handful of viral
reads, as seen with read-level Kraken2), far too few to meet GOTTCHA2's
signature threshold — so all viruses are filtered out. This is consistent with
every other tool on this sample: the viral signal is negligible.

### Precise statement

It is **not** that the database lacks viruses, and **not** that GOTTCHA2 can
never detect viruses — it is that **GOTTCHA2's signature-coverage method is
insensitive to low-abundance viruses, so on this virus-sparse CDC sample it
detects none.** Two factors combine:

1. **Sample**: the CDC sample contains almost no viral reads.
2. **Method**: GOTTCHA2 only reports a taxon when reads cover enough of its
   unique signature region; a few reads cannot reach that threshold.

Evidence that GOTTCHA2 *can* detect viruses when they are abundant: on a
simulated virome (sample 708, ~14% viral) the **same GOTTCHA2 + database
recovered 87 viral species**. The difference is viral abundance, not capability.

For low-abundance virus detection, sensitivity ranks roughly:

```
Kraken2 / TaxProfiler / TaxTriage (read-level k-mer)
        >  GOTTCHA2 (signature coverage)  ≈  assembly-based (contig)
```

> For low-abundance virus detection on this sample, **read-level k-mer methods
> (Kraken2 / TaxProfiler / TaxTriage)** are more sensitive than GOTTCHA2's
> signature-coverage approach or assembly-based pipelines.

## Notes & caveats

- **49 of 356 taxids are name placeholders** (e.g. `Algicoccus_marinus`,
  `Neisseria_testudinis`) rather than numeric NCBI TaxIDs — GOTTCHA2 emits these
  when a reference could not be mapped to a numeric NCBI ID. The species name is
  still valid.
- Counts/abundances reflect GOTTCHA2's signature model, not raw read fractions,
  so they are not directly comparable to Kraken2/CLARK read counts.
