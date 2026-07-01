# KrakenMetaReads-nf Illumina (CDC) — Assembly-based Classification Summary

Taxonomic classification of the **CDC Illumina short-read sample**
(`Illumina_llnl_66ce4dde`) using the **KrakenMetaReads-nf** workflow:
short reads are **assembled into contigs** (MEGAHIT and metaSPAdes), then the
**contigs are classified with Kraken2** (Standard DB, 2024-06-05). The two files
are the Kraken2 reports for the two assemblers.

## Output files

| File | Assembler classified | Format |
|------|----------------------|--------|
| `sample_CDC_Illumina_megahit_report.txt` | MEGAHIT contigs | Kraken2 report (tree) |
| `sample_CDC_Illumina_spades_report.txt`  | metaSPAdes contigs | Kraken2 report (tree) |

Kraken2 report columns: `percent, clade_count, direct_count, rank, taxid, name`
(here `count` = number of **contigs**, not reads).

## Results

| Metric | MEGAHIT | metaSPAdes |
|--------|---------|-----------|
| Classified contigs | 48,252 | 343,824 |
| Unclassified contigs | 42 | 1,199 |
| Species (rank = S) | 426 | 1,386 |
| Bacteria | 99.91% | 99.65% |
| Archaea | – | 4 contigs |
| Eukaryota | – | 2 contigs |
| **Viruses** | **0** | **0** |

- metaSPAdes produces many more contigs than MEGAHIT (343,824 vs 48,252) and
  therefore far more species (1,386 vs 426); the communities are otherwise
  consistent and **overwhelmingly bacterial**.
- Top species (by contig count):
  - **MEGAHIT**: *Mesorhizobium opportunistum*, *Caulifigura coniformis*,
    *Microvirga ossetica*, *Microlunatus phosphovorus*, *Kluyvera intermedia*
  - **metaSPAdes**: *Caulifigura coniformis*, *Microlunatus phosphovorus*,
    *Piscinibacter gummiphilus*, *Pseudolabrys taiwanensis*,
    *Nakamurella multipartita*

## Viruses: none detected (expected for an assembly-based method)

**Neither assembler yielded any viral contigs** (no `Viruses` / taxid 10239
node in either report). This is the expected behavior here, for two reasons:

1. **Sample**: the CDC sample's viral fraction is extremely low (read-level
   Kraken2 finds only a handful of viral reads).
2. **Method**: this is an **assembly-based** pipeline. Low-abundance viruses
   lack the coverage needed to assemble into contigs, so they disappear before
   classification — contig-level methods are the least sensitive to rare taxa.

For low-abundance virus detection on this sample, **read-level classification
(TaxProfiler / TaxTriage Kraken2)** is more sensitive; those did recover a few
viral reads, whereas contig-level (this workflow) and signature-coverage
(GOTTCHA2) approaches recover none.

## Notes

- Counts are **per contig**, not per read, so absolute numbers are not directly
  comparable to read-level tools (Kraken2 reads, CLARK, etc.).
- Same Standard Kraken2 DB (2024-06-05) as the read-level Kraken2 runs, so
  differences vs those tools reflect the **assembly step**, not the database.
- metaSPAdes' higher contig/species count reflects its more fragmented assembly,
  not necessarily more true species — interpret the long tail with caution.
