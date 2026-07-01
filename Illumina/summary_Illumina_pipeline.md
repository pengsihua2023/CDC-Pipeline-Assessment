# CDC Illumina — Cross-Pipeline Comparison Summary

Comparison of **7 taxonomic classification pipelines** run on the same
**CDC Illumina short-read sample** (`Illumina_llnl_66ce4dde`). This document
consolidates the per-pipeline summaries in this folder.

## Pipelines at a glance

| Pipeline | Method | Classifier | Database | Input level |
|----------|--------|-----------|----------|-------------|
| **TaxProfiler** | read-level k-mer | Kraken2 (+Bracken) | Standard 2024-06-05 | reads |
| **TaxTriage** | read-level k-mer | Kraken2 | Standard 2024-06-05 | reads |
| **CLARK** | read-level k-mer | CLARK (k=20, γ≥0.2) | CLARK multi-domain | reads |
| **GOTTCHA2** | signature coverage | GOTTCHA2 | Standard GOTTCHA2 (~198 GB, +viruses) | reads |
| **sourmash** | FracMinHash gather | sourmash (k=31) | GTDB rs226 + NCBI-viruses-2025.01 | reads |
| **KrakenMetaReads-nf** | assembly + k-mer | MEGAHIT/SPAdes → Kraken2 | Standard 2024-06-05 | contigs |
| **nf-core/mag** | assembly + k-mer | MEGAHIT/SPAdes → Kraken2 | Standard 2024-06-05 | contigs |

## Species detected & virus signal

| Pipeline | Level | Species detected | Viruses detected |
|----------|-------|------------------|------------------|
| TaxProfiler | reads | 5,312 | **7 reads / 6 species** |
| TaxTriage | reads | 5,312 | **7 reads / 6 species** |
| CLARK | reads | 2,082 (merged) / 2,568 taxa | 5 taxa (1 read each) |
| GOTTCHA2 | reads (signature) | 356 | **0** |
| sourmash | reads | 5,061 prok + 129 viral | **129 viral species** (0.04%) |
| KrakenMetaReads-nf | contigs | 426 (MEGAHIT) / 1,386 (SPAdes) | **0** |
| nf-core/mag | contigs | 419 (MEGAHIT) / 2,564 (SPAdes) | **0** |

> Species counts are **not directly comparable** across pipelines — they differ
> by method (read vs contig), database, and filtering. Use them for trends, not
> absolute equivalence.

## Key findings

### 1. The sample is overwhelmingly bacterial
Every pipeline agrees: **~99% of the classified signal is Bacteria**, with only
trace Archaea/Eukaryota. The dominant species recur across tools — e.g.
*Jeotgalibaca porci*, *Microvirga ossetica*, *Caulifigura coniformis*,
*Mesorhizobium opportunistum*, *Pseudazoarcus pumilus*, *Tolumonas auensis*,
*Microlunatus phosphovorus* — a strong cross-pipeline consensus on the core
community.

### 2. Viruses are essentially absent — and method determines whether you see them
The CDC sample is **virus-sparse** (only ~7 viral reads in ~5M). Whether a
pipeline reports any virus depends almost entirely on its **method**, not the
database:

| Method class | Virus detection | Why |
|--------------|-----------------|-----|
| **Read-level k-mer** (TaxProfiler, TaxTriage) | a few reads (7) | 1–2 reads are enough to call a taxon → most sensitive |
| **FracMinHash** (sourmash) | 129 viral species @ ~0.04% | dedicated high-resolution viral gather (scaled=50) |
| **Signature coverage** (GOTTCHA2) | 0 | needs breadth/depth over a unique signature — a few reads can't reach it |
| **Assembly-based** (KrakenMetaReads, mag) | 0 | rare viruses can't assemble into contigs → lost before classification |

**Sensitivity ranking for low-abundance viruses:**
```
read-level k-mer ≈ FracMinHash  >  signature coverage  ≈  assembly-based
```

### 3. Same-method pipelines validate each other
- **TaxProfiler ≈ TaxTriage**: identical (both Kraken2 + Standard DB, read level)
  — 99.41% Bacteria, 7 viral reads, 5,312 species.
- **KrakenMetaReads-nf ≈ nf-core/mag**: both = assembly + Kraken2 on contigs; near-
  identical results, minor differences from assembler settings/versions.

### 4. Read-level vs contig-level = a sensitivity gap
Read-level tools report **thousands** of species (2,000–5,300); contig-level
tools report **hundreds** (MEGAHIT ~420) because low-abundance taxa never
assemble. Contig methods trade sensitivity for higher-confidence (assembled)
evidence.

### 5. GOTTCHA2's low count is by design, not a database gap
GOTTCHA2 reports only 356 species and 0 viruses despite a database that
**contains** ~13k viruses. Its signature-coverage threshold is conservative:
it filters out anything without enough unique-region coverage. (On a virus-rich
sample — 708 simulated virome, ~14% viral — the same GOTTCHA2 recovered 87 viral
species, proving capability; the difference here is sample abundance.)

## Bottom line

- **Consensus community**: a well-defined, ~99% bacterial community that all 7
  pipelines recover consistently.
- **Viruses**: negligible in this sample. To detect the rare viral signal, use
  **read-level k-mer (Kraken2 / TaxProfiler / TaxTriage)** or **sourmash**;
  **GOTTCHA2 and assembly-based pipelines will report zero** on such a
  virus-sparse sample.
- **Choosing a tool**: read-level = most sensitive (best for rare-pathogen
  screening); contig-level = higher-confidence but misses rare taxa; GOTTCHA2 =
  high-precision/conservative; sourmash = fast, lineage-based, with a dedicated
  viral database.

## Caveats

- Different databases across tools (Kraken2 Standard vs CLARK vs GOTTCHA2 vs
  GTDB+NCBI-viruses) → species counts not 1:1 comparable.
- Counts are per-read for read-level tools and per-contig for assembly tools —
  different units.
- No ground truth for this CDC sample, so this is a **cross-tool agreement /
  method comparison**, not an accuracy (F1) benchmark.
