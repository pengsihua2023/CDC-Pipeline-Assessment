# CDC Nanopore — Cross-Pipeline Comparison Summary

Comparison of taxonomic classification pipelines run on the same
**CDC Nanopore long-read sample** (`Nanopore_llnl_66d1047e`, ~228,000 reads).
This document consolidates the per-pipeline summaries in this folder.

> **6 pipelines produced results.** **nf-core/mag does not support Nanopore
> metagenomic data** (it requires short reads), so there is no mag-nf result on
> this sample.

## Pipelines at a glance

| Pipeline | Method | Classifier | Database | Input level |
|----------|--------|-----------|----------|-------------|
| **TaxProfiler** | read-level k-mer | Kraken2 (no Bracken for long reads) | Standard 2024-06-05 | reads |
| **TaxTriage** | read-level k-mer | Kraken2 | Standard 2024-06-05 | reads |
| **CLARK** | read-level k-mer | CLARK (k=20, unfiltered) | CLARK multi-domain | reads |
| **GOTTCHA2** | signature coverage | GOTTCHA2 | Standard GOTTCHA2 (+viruses) | reads |
| **sourmash** | FracMinHash gather | sourmash (k=31) | GTDB rs226 + NCBI-viruses-2025.01 | contigs (Flye) |
| **KrakenMetaReads-nf** | assembly + k-mer | Flye → Kraken2 | Standard 2024-06-05 | contigs |
| **nf-core/mag** | — | — | — | **not supported (Nanopore)** |

## Species detected & virus signal

| Pipeline | Level | Species detected | Viruses detected |
|----------|-------|------------------|------------------|
| TaxProfiler | reads | 1,672 | **0** |
| TaxTriage | reads | 1,676 | **0** |
| CLARK | reads | 2,945 (merged) / 3,698 taxa | 5 taxa (1 read each) |
| GOTTCHA2 | reads (signature) | 271 | **0** |
| sourmash | contigs | 471 prok + 25 viral | **25 viral species** (0.29%) |
| KrakenMetaReads-nf | contigs | 320 | **0** |

> Species counts are **not directly comparable** across pipelines — they differ
> by method (read vs contig), database, and filtering. Use them for trends, not
> absolute equivalence.

## Key findings

### 1. The sample is overwhelmingly bacterial
Every pipeline agrees: **~99% of the classified signal is Bacteria**, with only
trace Archaea/Eukaryota. The dominant species recur across tools — e.g.
*Acidovorax temperans*, *Uruburuella suis*, *Streptococcus parasuis*,
*Pseudooceanicola algae*, *Jeotgalibaca porci*, *Stenotrophomonas* sp. — a
strong cross-pipeline consensus on the core community.

### 2. Host (human) reads are visible
CLARK reports **_Homo sapiens_ as the #2 taxon (5,831 reads)** — the human host
background. The Kraken2-based tools also carry a small Eukaryota fraction. Worth
noting for a clinical/wastewater-type sample.

### 3. Viruses are essentially absent — even more so than Illumina
The Nanopore library is **virus-sparse to the point of zero** for most tools:

| Method class | Virus detection | Note |
|--------------|-----------------|------|
| Read-level k-mer (TaxProfiler, TaxTriage) | **0** | Even read-level found none here — fewer viral reads than the Illumina run |
| FracMinHash (sourmash) | 25 viral species @ 0.29% | Dedicated high-resolution viral gather (scaled=50) still finds a faint signal |
| CLARK | 5 taxa (1 read each) | At noise level |
| Signature coverage (GOTTCHA2) | 0 | Threshold not met |
| Assembly-based (KrakenMetaReads) | 0 | Rare viruses don't assemble |

**Contrast with Illumina:** on the Illumina CDC sample, read-level Kraken2
recovered 7 viral reads; on **Nanopore it recovered 0**. The higher per-read
error rate and lower depth of the Nanopore library make the already-tiny viral
signal effectively undetectable by k-mer methods here.

### 4. Same-method pipelines validate each other
- **TaxProfiler ≈ TaxTriage**: essentially identical (both Kraken2 + Standard
  DB, read level) — ~99.3% Bacteria, 0 viruses, ~1,672–1,676 species.

### 5. Read-level vs contig-level = a sensitivity gap
Read-level tools report **1,600–3,700** taxa (CLARK/TaxProfiler/TaxTriage);
contig-level tools report far fewer — **KrakenMetaReads only 320 species** from
just 1,208 Flye contigs, because low-abundance taxa never assemble. The Nanopore
assembly is small, making contig methods the least sensitive here.

### 6. sourmash is the only tool reporting any real viral list
Because it ran a dedicated viral gather (scaled = 50) against NCBI-viruses-2025.01,
sourmash reports **25 viral species** (mostly phages, ~1e-4 each) where all other
tools report 0 — but the total viral abundance is still negligible (0.29%).

## Bottom line

- **Consensus community**: a well-defined, ~99% bacterial community that all
  pipelines recover consistently, plus a visible human-host background.
- **Viruses**: negligible, and on Nanopore essentially undetectable by k-mer and
  assembly methods (0). Only **sourmash's dedicated viral gather** surfaces a
  faint list.
- **Nanopore vs Illumina**: the same tools detect **fewer** taxa and **fewer/zero**
  viruses on Nanopore than on Illumina, reflecting lower depth and higher error
  rate of this long-read library.
- **Tool choice for Nanopore**: read-level k-mer (TaxProfiler/TaxTriage/CLARK)
  for the fullest species list; **nf-core/mag is not an option (no long-read
  support)**; contig methods (KrakenMetaReads) are least sensitive; sourmash adds
  a viral-specific view.

## Caveats

- Different databases across tools → species counts not 1:1 comparable.
- Counts are per-read (read-level tools) vs per-contig (assembly/sourmash here) —
  different units.
- CLARK result is **unfiltered** (no gamma filter), so its higher taxon count
  includes more low-confidence calls than the Illumina CLARK run.
- No ground truth for this CDC sample → this is a **cross-tool agreement /
  method comparison**, not an accuracy (F1) benchmark.
