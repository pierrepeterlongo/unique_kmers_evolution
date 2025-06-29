# Progressive Unique Kmer Counter (pukc)

A Rust-based tool for visualizing the number of solid canonical kmers while streaming reads.

---

## Features

- Supports **FASTA** and **FASTQ**
- Supports **gzip-compressed** files (`.gz`). **WARNING** In this case the extrapolation of number of reads and kmers is false. TODO: add an option to inform the number of reads. 
- Real-time WebSocket output for monitoring
- **Growth**: The number of new solid k-mers between read intervals.
- **Acceleration**: The second derivative of k-mer discovery, indicating whether the rate of discovery is increasing, decreasing, or stabilizing
- Early termination based on configurable acceleration threshold
- For fasta and fastq files: estimates the number of reads and extrapolates an estimate of the total number of kmers. 
- /!\ This does not work for .gz files (as we cannot easily estimate the size of the file and so the number of reads)


---

## Installation 

```bash

git clone https://github.com/pierrepeterlongo/progressive_unique_kmers_counter.git
cd progressive_unique_kmers_counter
cargo install --path .  
```

## Usage

```bash

Usage: pukc [OPTIONS] --k <K> --input <INPUT>

Options:
  -k, --k <K>
          Length of k-mers
  -i, --input <INPUT>
          Input FASTA file
      --stop-acceleration <STOP_ACCELERATION>
          Stop if acceleration is below this threshold [default: 10]
      --nb-reads <NB_READS>
          Inform the number of reads
          [default: 0] (in this case the number of reads is estimated)
      --plot
          Plot the intermediate results in plot.html (may slowdown the process)
  -h, --help
          Print help
  -V, --version
          Print version
```

## Example
```
pukc --k 25 --input input.fq --stop-acceleration 20
```
- In this case the solid 25-mers are counted as long as the acceleration over 50000 reads is higher than 20.
- Then the number of kmers is extrapolated 

## Full example.
1/ Download the fasta.gz or fastq.gz entry from [SRR33792312](https://trace.ncbi.nlm.nih.gov/Traces/?view=run_browser&acc=SRR33792312&display=metadata). This file contains 2.2M reads (2113824 precisely).
2/ Run :
```bash
pukc --k 25 --input /tmp/SRR33792312.fasta.gz  --nb-reads 2113824

...

Estimated total unique k-mers: 5642838
```

- Note1: this execution takes approximately 2s. 
- Note2: the exact number of distinct solide 25-mers in this file is in fact 5239190.

## Visualization
- Using the `--plot` option **visualize** the evolution of the results opening file `plot.html` in a browser (reload the page once the program runs)
