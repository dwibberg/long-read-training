Porechop
------


As we see some strange GC content at the 5' end of our nanopore reads, the first bases may indicate an adaptor contamination. For workflows including de novo assembly refined with nanopolish or medaka adaptor trimming is not necessary, but in other workflow scenarios (like mapping for the ARTIC pipeline) this can be important to do and good there are tools which can handle this, as e.g. **porechop**.

Porechop is a tool for finding and removing adapters from Oxford Nanopore reads. Adapters on the ends of reads are trimmed off, and when a read has an adapter in its middle, it is treated as chimeric and chopped into separate reads. Porechop performs thorough alignments to effectively find adapters, even at low sequence identity::

  porechop --help

  usage: porechop -i INPUT [-o OUTPUT] [--format {auto,fasta,fastq,fasta.gz,fastq.gz}] [-v VERBOSITY] [-t THREADS] [-b BARCODE_DIR] [--barcode_threshold BARCODE_THRESHOLD] [--barcode_diff BARCODE_DIFF] [--require_two_barcodes] [--untrimmed] [--discard_unassigned]
                  [--adapter_threshold ADAPTER_THRESHOLD] [--check_reads CHECK_READS] [--scoring_scheme SCORING_SCHEME] [--end_size END_SIZE] [--min_trim_size MIN_TRIM_SIZE] [--extra_end_trim EXTRA_END_TRIM] [--end_threshold END_THRESHOLD] [--no_split] [--discard_middle]
                  [--middle_threshold MIDDLE_THRESHOLD] [--extra_middle_trim_good_side EXTRA_MIDDLE_TRIM_GOOD_SIDE] [--extra_middle_trim_bad_side EXTRA_MIDDLE_TRIM_BAD_SIDE] [--min_split_read_size MIN_SPLIT_READ_SIZE] [-h] [--version]

  Porechop: a tool for finding adapters in Oxford Nanopore reads, trimming them from the ends and splitting reads with internal adapters

  Main options:
    -i INPUT, --input INPUT               FASTA/FASTQ of input reads or a directory which will be recursively searched for FASTQ files (required)
    -o OUTPUT, --output OUTPUT            Filename for FASTA or FASTQ of trimmed reads (if not set, trimmed reads will be printed to stdout)
    --format {auto,fasta,fastq,fasta.gz,fastq.gz}
                                          Output format for the reads - if auto, the format will be chosen based on the output filename or the input read format (default: auto)
    -v VERBOSITY, --verbosity VERBOSITY   Level of progress information: 0 = none, 1 = some, 2 = lots, 3 = full - output will go to stdout if reads are saved to a file and stderr if reads are printed to stdout (default: 1)
    -t THREADS, --threads THREADS         Number of threads to use for adapter alignment (default: 16)

  Barcode binning settings:
    Control the binning of reads based on barcodes (i.e. barcode demultiplexing)

    -b BARCODE_DIR, --barcode_dir BARCODE_DIR
                                          Reads will be binned based on their barcode and saved to separate files in this directory (incompatible with --output)
    --barcode_threshold BARCODE_THRESHOLD
                                          A read must have at least this percent identity to a barcode to be binned (default: 75.0)
    --barcode_diff BARCODE_DIFF           If the difference between a read's best barcode identity and its second-best barcode identity is less than this value, it will not be put in a barcode bin (to exclude cases which are too close to call) (default: 5.0)
    --require_two_barcodes                Reads will only be put in barcode bins if they have a strong match for the barcode on both their start and end (default: a read can be binned with a match at its start or end)
    --untrimmed                           Bin reads but do not trim them (default: trim the reads)
    --discard_unassigned                  Discard unassigned reads (instead of creating a "none" bin) (default: False)

  Adapter search settings:
    Control how the program determines which adapter sets are present

    --adapter_threshold ADAPTER_THRESHOLD
                                          An adapter set has to have at least this percent identity to be labelled as present and trimmed off (0 to 100) (default: 90.0)
    --check_reads CHECK_READS             This many reads will be aligned to all possible adapters to determine which adapter sets are present (default: 10000)
    --scoring_scheme SCORING_SCHEME       Comma-delimited string of alignment scores: match, mismatch, gap open, gap extend (default: 3,-6,-5,-2)

  End adapter settings:
    Control the trimming of adapters from read ends

    --end_size END_SIZE                   The number of base pairs at each end of the read which will be searched for adapter sequences (default: 150)
    --min_trim_size MIN_TRIM_SIZE         Adapter alignments smaller than this will be ignored (default: 4)
    --extra_end_trim EXTRA_END_TRIM       This many additional bases will be removed next to adapters found at the ends of reads (default: 2)
    --end_threshold END_THRESHOLD         Adapters at the ends of reads must have at least this percent identity to be removed (0 to 100) (default: 75.0)

  Middle adapter settings:
    Control the splitting of read from middle adapters

    --no_split                            Skip splitting reads based on middle adapters (default: split reads when an adapter is found in the middle)
    --discard_middle                      Reads with middle adapters will be discarded (default: reads with middle adapters are split) (required for reads to be used with Nanopolish, this option is on by default when outputting reads into barcode bins)
    --middle_threshold MIDDLE_THRESHOLD   Adapters in the middle of reads must have at least this percent identity to be found (0 to 100) (default: 90.0)
    --extra_middle_trim_good_side EXTRA_MIDDLE_TRIM_GOOD_SIDE
                                          This many additional bases will be removed next to middle adapters on their "good" side (default: 10)
    --extra_middle_trim_bad_side EXTRA_MIDDLE_TRIM_BAD_SIDE
                                          This many additional bases will be removed next to middle adapters on their "bad" side (default: 100)
    --min_split_read_size MIN_SPLIT_READ_SIZE
                                          Post-split read pieces smaller than this many base pairs will not be outputted (default: 1000)

  Help:
    -h, --help                            Show this help message and exit
    --version                             Show program's version number and exit


Use **porechop** on the basecalled dataset (basecall_tiny.fastq.gz). The results should be stored as::

  ~/workdir/data_artic/basecall_tiny_porechopped.fastq.gz

Also use the following options::

  -t 14 (to use 14 threads)
  -v 2 (to get more verbose output)
  
In addition, you should write the output of porechop into a file called "porechop.log" for later inspection.


When you are done running porechop, run **fastqc** again on the trimmed data for comparison, the results of this fastqc run should be stored in::

  ~/workdir/fastqc/basecall_tiny_porechopped/


Again - if you are stuck, check the next page for futher help/instructions.
  
References
^^^^^^^^^^

**Porechop** https://github.com/rrwick/Porechop

**FastQC** https://www.bioinformatics.babraham.ac.uk/projects/fastqc/
