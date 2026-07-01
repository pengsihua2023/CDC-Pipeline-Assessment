# sourmash Illumina (CDC) — Classification Summary

Taxonomic profiling of the **CDC Illumina short-read sample**
(`Illumina_llnl_66ce4dde`) with **sourmash** (FracMinHash `sketch` → `gather` →
`tax metagenome`, k = 31). The reads were searched against **two databases in
separate gathers** so that virus sensitivity is not lost:

- **Viruses** — `ncbi-viruses-2025.01` (scaled = 50)
- **Bacteria + Archaea** — `gtdb-reps-rs226` (scaled = 1000)

Each gather produces its own `tax metagenome` summary. This folder's final
classification results are the two `*.summarized.csv` files; the other outputs
(`.gather.csv`, `.kreport.txt`, `.krona.tsv`, `.human.txt`) are intermediate or
display formats of the same data.

## Final result files

| File | Domain searched | Final result? |
|------|-----------------|---------------|
| `Illumina_llnl_66ce4dde.viral.summarized.csv` | Viruses (NCBI 2025.01) | ✅ viral profile |
| `Illumina_llnl_66ce4dde.prok.summarized.csv`  | Bacteria + Archaea (GTDB rs226) | ✅ prokaryote profile |

`summarized.csv` columns: `query_name, rank, fraction, lineage, query_md5,
query_filename, f_weighted_at_rank, bp_match_at_rank, query_ani_at_rank,
total_weighted_hashes`. Each row is one taxon at one rank, with its fraction of
the sample and full lineage.

## Results

| Metric | Viral (NCBI) | Prokaryote (GTDB) |
|--------|--------------|-------------------|
| Species detected | **129** | **5,061** |
| Classified fraction (superkingdom) | Viruses 0.042% | Bacteria 32.8%, Archaea ~0% |
| Unclassified fraction | 99.96% | 67.2% |

- **Prokaryotes dominate**: 5,061 bacterial/archaeal species, ~33% of the sample
  classified by GTDB (the rest is unclassified — not in the GTDB reps set).
  Top species: *Mesorhizobium opportunistum*, *Caulifigura coniformis*,
  *Microvirga ossetica*, *Chondromyces crocatus*, *Microlunatus phosphovorus*.
- **Viruses are present but extremely rare**: 129 viral species, but the whole
  viral superkingdom is only **0.042%** of the sample. Top hits are mostly
  phages / environmental viruses (*uncultured virus*, *Salmonella phage
  S.Kent 1-2-1*, *uncultured human fecal virus*, ...), each at ~1e-4 or less.

## How to read it

- `fraction` = share of the sample's k-mers assigned to that taxon. Note the
  viral and prokaryote fractions come from **different gathers/scaled values**
  and are each normalized within their own run — do not add them directly.
- `unclassified` rows are large because each gather only sees its own database
  (viral gather can't classify bacteria, and vice versa).
- Names follow each database's scheme: GTDB uses `d__/p__/.../s__` prefixes
  (e.g. `s__Mesorhizobium opportunistum`); NCBI viruses use plain names. There
  are **no NCBI taxids** in sourmash output (lineage-based).

## Notes & caveats

- sourmash here **did** detect viruses (129 species), unlike GOTTCHA2 and the
  assembly-based pipelines (0). This is because it ran a dedicated, high-
  resolution viral gather (scaled = 50). Still, the viral abundance is tiny
  (~0.04%), consistent with all tools: the CDC sample is virus-sparse.
- Different reference databases (GTDB + NCBI-viruses-2025.01) than the Kraken2
  Standard DB used by other tools, so species counts are not directly
  comparable across platforms.
- Short reads carry abundance, so these summaries are abundance-weighted
  (`--use-abundances`).
