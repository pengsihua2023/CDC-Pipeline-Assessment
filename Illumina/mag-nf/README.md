# nf-core/mag Illumina (CDC) — Assembly + Kraken2 Classification Summary

Taxonomic classification of the **CDC Illumina short-read sample**
(`Illumina_llnl_66ce4dde`) processed with the real **nf-core/mag** (Nextflow)
pipeline. nf-core/mag performs QC → assembly (MEGAHIT and metaSPAdes); the
assembled **contigs were then classified with Kraken2** (Standard DB,
2024-06-05). The two files are the Kraken2 reports for the two assemblers.

> Note: nf-core/mag's built-in Kraken2 step did not run (placeholder DB), so
> Kraken2 was applied to the MEGAHIT/metaSPAdes contigs as a post-processing
> step. These reports are that output.

## Output files

| File | Assembler classified | Format |
|------|----------------------|--------|
| `MEGAHIT_contigs_kraken2_report.txt` | MEGAHIT contigs | Kraken2 report (tree) |
| `SPAdes_contigs_kraken2_report.txt`  | metaSPAdes contigs | Kraken2 report (tree) |

Kraken2 report columns: `percent, clade_count, direct_count, rank, taxid, name`
(here `count` = number of **contigs**, not reads).

## Results

| Metric | MEGAHIT | metaSPAdes |
|--------|---------|-----------|
| Classified contigs | 48,223 | 825,618 |
| Unclassified contigs | 41 | 5,033 |
| Species (rank = S) | 419 | 2,564 |
| Bacteria | 99.92% | 99.38% |
| Archaea | – | 13 contigs |
| Eukaryota | – | 19 contigs |
| **Viruses** | **0** | **0** |

- The community is **overwhelmingly bacterial** in both assemblies.
- metaSPAdes yields far more contigs and species (825,618 contigs / 2,564
  species) than MEGAHIT (48,223 / 419), reflecting its more fragmented assembly.
- Top species (by contig count):
  - **MEGAHIT**: *Mesorhizobium opportunistum*, *Caulifigura coniformis*,
    *Microvirga ossetica*, *Microlunatus phosphovorus*, *Kluyvera intermedia*
  - **metaSPAdes**: *Caulifigura coniformis*, *Microlunatus phosphovorus*,
    *Piscinibacter gummiphilus*, *Chondromyces crocatus*,
    *Mesorhizobium opportunistum*

## Viruses: none detected (expected for an assembly-based method)

**Neither assembler produced any viral contigs** (no `Viruses` / taxid 10239
node in either report). Two reasons:

1. **Sample**: the CDC sample's viral fraction is extremely low (read-level
   Kraken2 finds only a handful of viral reads).
2. **Method**: this is **assembly-based**. Low-abundance viruses lack the
   coverage to assemble into contigs, so they vanish before classification —
   contig-level methods are the least sensitive to rare taxa.

For low-abundance virus detection on this sample, **read-level Kraken2
(TaxProfiler / TaxTriage)** is more sensitive (it recovers a few viral reads),
whereas contig-level pipelines (this one, KrakenMetaReads-nf) and
signature-coverage (GOTTCHA2) recover none.

## Notes

- Counts are **per contig**, not per read — not directly comparable to
  read-level tools.
- Same Standard Kraken2 DB (2024-06-05) as the read-level runs, so differences
  vs those tools reflect the **assembly step**, not the database.
- This nf-core/mag result is essentially equivalent to the KrakenMetaReads-nf
  result (both = assembly + Kraken2 on contigs); minor count differences come
  from assembler settings/versions.
