# CDC Sample — Illumina vs Nanopore Sequencing Platform Comparison

Comparison of the **two sequencing platforms** on the same CDC sample, based on
the per-platform cross-pipeline summaries:
- `summary-Illumina_pipeline.md` — Illumina short reads (`Illumina_llnl_66ce4dde`)
- `summary_Nanopore_pipeline.md` — Nanopore long reads (`Nanopore_llnl_66d1047e`)

Each platform was analyzed with the same set of taxonomic-classification
pipelines, so differences below reflect the **sequencing technology**, not the
tools.

## Sequencing depth (the key difference)

| Platform | Approx. reads/fragments | Relative depth |
|----------|-------------------------|----------------|
| **Illumina** (short) | ~5,000,000 reads | ~22× more |
| **Nanopore** (long) | ~228,000 reads | baseline |

Illumina delivered **~22× more reads**. This depth gap drives most of the
differences that follow.

## Side-by-side results (same pipeline, two platforms)

| Pipeline | Illumina species | Nanopore species | Illumina viruses | Nanopore viruses |
|----------|------------------|------------------|------------------|------------------|
| TaxProfiler (Kraken2, reads) | 5,312 | 1,672 | **7 reads / 6 sp.** | **0** |
| TaxTriage (Kraken2, reads) | 5,312 | 1,676 | **7 reads / 6 sp.** | **0** |
| CLARK (reads) | 2,082–2,568 | 2,945–3,698 | 5 (1 read each) | 5 (1 read each) |
| GOTTCHA2 (signature) | 356 | 271 | **0** | **0** |
| sourmash (FracMinHash) | 5,061 prok + 129 viral | 471 prok + 25 viral | **129 viral** | **25 viral** |
| KrakenMetaReads-nf (contigs) | 426 / 1,386 | 320 | **0** | **0** |
| nf-core/mag (contigs) | 419 / 2,564 | **not supported** | **0** | — |

## Key findings

### 1. Same core community, both platforms
Both platforms recover the **same ~99% bacterial community** and agree on the
dominant taxa. Overlapping top species across platforms include
*Jeotgalibaca porci*, *Pseudooceanicola algae*, *Pseudazoarcus pumilus*,
*Uruburuella suis*, *Stenotrophomonas* sp. The biological conclusion — a
well-defined bacterial community with negligible viruses — is **platform-
independent**.

### 2. Illumina detects more species (mostly a depth effect)
For the read-level Kraken2 tools, Illumina reports **~3× more species** than
Nanopore (5,312 vs ~1,672). This is largely the **~22× depth advantage**: more
reads sample more of the rare tail. (CLARK is the exception — it reports *more*
taxa on Nanopore, but that Nanopore CLARK run was **unfiltered** while the
Illumina run used a γ≥0.2 filter, so the extra Nanopore taxa are lower-confidence,
not a real gain.)

### 3. Virus detection: Illumina > Nanopore
The rare viral signal is **only recoverable on Illumina**, and only barely:
- **Illumina**: read-level Kraken2 finds **7 viral reads / 6 species**.
- **Nanopore**: the **same** read-level Kraken2 tools find **0**.

Two reasons Nanopore loses the viral signal:
1. **Lower depth** (~22× fewer reads) — the already-tiny viral fraction may not
   be sampled at all.
2. **Higher per-read error rate** — degrades exact k-mer matching, hurting the
   detection of low-abundance taxa most.

Only **sourmash** surfaces a viral list on both platforms (129 on Illumina, 25 on
Nanopore) via its dedicated high-resolution viral gather — but abundances are
negligible on both (0.04% / 0.29%).

### 4. Method-vs-platform interaction is consistent
On **both** platforms the method ranking for low-abundance viruses holds:
```
read-level k-mer ≈ FracMinHash  >  signature coverage (GOTTCHA2)  ≈  assembly-based
```
GOTTCHA2 and assembly-based pipelines report **0 viruses on both platforms**.
The platform mainly shifts *how much* of the tail each method can reach, not the
ranking.

### 5. Assembly-based methods are weakest on Nanopore
Contig-level tools suffer most on Nanopore: the smaller, error-prone long-read
assembly yields few contigs (KrakenMetaReads: only 1,208 Flye contigs → 320
species, vs hundreds of thousands of Illumina contigs). And **nf-core/mag cannot
process Nanopore at all** (short-read-only), removing one pipeline entirely.

### 6. Host (human) background more prominent on Nanopore output
CLARK ranks *Homo sapiens* as the #2 taxon on the Nanopore sample — the human
host background is clearly visible; worth noting for downstream host-removal.

## Bottom line

| Aspect | Winner | Note |
|--------|--------|------|
| Sequencing depth | **Illumina** (~22×) | Drives most differences |
| Species richness recovered | **Illumina** (~3× more) | Depth-driven |
| Low-abundance virus detection | **Illumina** | Nanopore = 0 by k-mer/assembly |
| Pipeline compatibility | **Illumina** | nf-core/mag Nanopore-incompatible |
| Core community agreement | **Tie** | Both give the same ~99% bacterial result |
| Read length / assembly contiguity | **Nanopore** | (not the limiting factor for this virus-sparse sample) |

**Overall:** for **this virus-sparse CDC sample**, **Illumina is the better
platform for sensitive detection** — more depth, more species, and the only
platform where the trace viral signal is recoverable. Nanopore reproduces the
same dominant bacterial community but, at ~22× lower depth and higher error rate,
misses the rare tail and the viral signal, and is incompatible with one pipeline
(nf-core/mag).

## Caveats

- Different databases across tools; species counts are trends, not exact
  equivalents.
- The Nanopore CLARK result is unfiltered vs the γ-filtered Illumina CLARK result
  — not an apples-to-apples comparison for that one tool.
- Counts mix per-read (read-level) and per-contig (assembly) units.
- **No ground truth** for the CDC sample → this is a platform/method comparison
  by cross-tool agreement, not an accuracy (F1) benchmark. Depth and error-rate
  effects are inferred from the read counts and consistent tool behavior, not
  measured against a truth set.
