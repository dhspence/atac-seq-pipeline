# Input JSON

An input JSON file includes all input parameters and metadata for running pipelines. Items 1), 2) and 3) are mandatory. Items 4) and 5) are optional so that our pipeline will use default values if they are not defined. However, 

* Mandatory

1. Reference genome.
2. Input data file paths/URIs.
3. Adapters to be trimmed.

* Optional

4. Pipeline parameters.
5. Resource settings for jobs.

## Templates

We provide two template JSON files for both single ended and paired-end samples. We recommend to use one of these input JSON files instead of that used in the tutorial section. These template JSON files include all parameters of the pipeline with default values defined.

* [template](../examples/template_se.json) for single ended sample
* [template](../examples/template_pe.json) for paired-end sample

Let us take a close look at the following template JSON. Comments are not allowed in a JSON file but we added some comments to help you understand each parameter.
```javascript
{
    ////////// 1) Reference genome //////////
    // Stanford servers: [GENOME]=hg38,hg19,mm10,mm9
    //   Sherlock: /home/groups/cherry/encode/pipeline_genome_data/[GENOME]_sherlock.tsv
    //   SCG4: /reference/ENCODE/pipeline_genome_data/[GENOME]_scg.tsv

    // Cloud platforms (Google Cloud, DNAnexus): [GENOME]=hg38,hg19,mm10,mm9
    //   Google Cloud: gs://encode-pipeline-genome-data/[GENOME]_google.tsv
    //   DNAnexus: dx://project-BKpvFg00VBPV975PgJ6Q03v6:data/pipeline-genome-data/[GENOME]_dx.tsv
    //   DNAnexus(Azure): dx://project-F6K911Q9xyfgJ36JFzv03Z5J:data/pipeline-genome-data/[GENOME]_dx_azure.tsv

    // On other computers download or build reference genome database and pick a TSV from [DEST_DIR].
    //   Downloader: ./genome/download_genome_data.sh [GENOME] [DEST_DIR]
    //   Builder (Conda required): ./conda/build_genome_data.sh [GENOME] [DEST_DIR]

    "atac.genome_tsv" : "/path_to_genome_data/hg38/hg38.tsv",

    ////////// 2) Input data files paths/URIs //////////

    // Read endedness
    "atac.paired_end" : true,

    // If you start from FASTQs then define these, otherwise remove from this file.
    // You can define up to 6 replicates.
    // FASTQs in an array will be merged after trimming adapters.
    // For example, 
    // "rep1_R1_L1.fastq.gz", "rep1_R1_L2.fastq.gz" and "rep1_R1_L3.fastq.gz" will be merged together.
    "atac.fastqs_rep1_R1" : [ "rep1_R1_L1.fastq.gz", "rep1_R1_L2.fastq.gz", "rep1_R1_L3.fastq.gz" ],
    "atac.fastqs_rep1_R2" : [ "rep1_R2_L1.fastq.gz", "rep1_R2_L2.fastq.gz", "rep1_R2_L3.fastq.gz" ],
    "atac.fastqs_rep2_R1" : [ "rep2_R1_L1.fastq.gz", "rep2_R1_L2.fastq.gz" ],
    "atac.fastqs_rep2_R2" : [ "rep2_R2_L1.fastq.gz", "rep2_R2_L2.fastq.gz" ],

    // If you start from BAMs then define these, otherwise remove from this file.
    // You can define up to 6 replicates. The following example array has two replicates.
    "atac.bams" : [
        "raw_rep1.bam",
        "raw_rep2.bam"
    ],

    // If you start from filtered/deduped BAMs then define these, otherwise remove from this file.
    // You can define up to 6 replicates. The following example array has two replicates.
    "atac.nodup_bams" : [
        "nodup_rep1.bam",
        "nodup_rep2.bam"
    ],

    // If you start from TAG-ALIGNs then define these, otherwise remove from this file.
    // You can define up to 6 replicates. The following example array has two replicates.
    "atac.tas" : [
        "rep1.tagAlign.gz",
        "rep2.tagAlign.gz"
    ],

    ////////// 3) Adapters to be trimmed //////////

    // You can use auto-detection for adapters.
    // List of adapters can be detected:
    //   AGATCGGAAGAGC (Illumina), CTGTCTCTTATA (Nextera) and TGGAATTCTCGG (smallRNA)
    "atac.auto_detect_adapter" : false,

    // If you don't start from FASTQs, remove these adapter arrays from this file.
    // else if you chooose to use auto-detection for adapters, then remove adapter arrays from this file.
    // Otherwise define adapters for each FASTQ.
    // Adapters should have the same dimension as FASTQs.
    "atac.adapters_rep1_R1" : [ "AATTCCGG", "AATTCCGG", "AATTCCGG" ],
    "atac.adapters_rep2_R2" : [ "AATTCCGG", "AATTCCGG" ],
    "atac.adapters_rep1_R1" : [ "AATTCCGG", "AATTCCGG", "AATTCCGG" ],
    "atac.adapters_rep2_R2" : [ "AATTCCGG", "AATTCCGG" ],

    ////////// 4) Pipeline parameters //////////

    // Pipeline title and description
    "atac.title" : "Example (paired end)",
    "atac.description" : "This is a template input JSON for paired ended sample.",

    // Pipeline type (atac or dnase). DNase-Seq pipeline is not an official ENCODE pipeline yet.
    "atac.pipeline_type" : "atac",

    // Pipeline will not proceed to post alignment steps (peak-calling, ...).
    // You will get QC report for alignment only.
    "atac.align_only" : false,

    // Pipeline will not generate pseudo replicates and will skip all analyses related to them.
    "atac.true_rep_only" : false,

    // cutadapt (trim_adapter) parameters
    "atac.cutadapt_min_trim_len" : 5,
    "atac.cutadapt_err_rate" : 0.1,

    // multimapping reads
    "atac.multimapping" : 0,

    // bowtie2 parameters
    "atac.bowtie2_score_min" : "",

    // Choose a dup marker between picard and sambamba
    // picard is recommended, use sambamba only when picard fails.
    "atac.dup_marker" : "picard",

    // Threshold for mapped reads quality (samtools view -q)
    "atac.mapq_thresh" : 30,

    // Skip dup removal in a BAM filtering stage.
    "atac.no_dup_removal" : false,

    // Regular expression to filter out reads
    // Any read that matches with this reg-ex pattern will be removed from outputs
    "atac.regex_filter_reads" : "chrM",

    // Subsample reads (0: no subsampling)
    // Subsampled reads will be used for all downsteam analyses including peak-calling
    "atac.subsample_reads" : 0,

    // Cross-correlation analysis
    "atac.enable_xcor" : false,

    // Subsample reads for cross-corr. analysis only (0: no subsampling)
    // Subsampled reads will be used for cross-corr. analysis only
    "atac.xcor_subsample_reads" : 25000000,

    // Keep irregular chromosome names
    // Use this for custom genomes without canonical chromosome names (chr1, chrX, ...)
    "atac.keep_irregular_chr_in_bfilt_peak" : false,        

    // Cap number of peaks called from a peak-caller (MACS2)
    "atac.cap_num_peak" : 300000,
    // P-value threshold for MACS2 (macs2 callpeak -p)
    "atac.pval_thresh" : 0.01,
    // Smoothing window for MACS2 (macs2 callpeak --shift -smooth_win/2 --extsize smooth_win)
    "atac.smooth_win" : 150,

    // IDR (irreproducible discovery rate)
    "atac.enable_idr" : false,
    // Threshold for IDR
    "atac.idr_thresh" : 0.1,

    // ATAqC (annotation-based analysis which include TSS enrichment and etc.)
    "atac.disable_ataqc" : false,

    ////////// 5) Resource settings //////////

    // Resources defined here are PER REPLICATE.
    // Therefore, total number of cores will be "atac.bowtie2_cpu" x [NUMBER_OF_REPLICATES]
    // because bowtie2 is a bottlenecking task of the pipeline.
    // Use this total number of cores if you manually qsub or sbatch your job (using local mode of our pipeline).
    // "disks" is used for Google Cloud and DNAnexus only.

    "atac.trim_adapter_cpu" : 2,
    "atac.trim_adapter_mem_mb" : 12000,
    "atac.trim_adapter_time_hr" : 24,
    "atac.trim_adapter_disks" : "local-disk 100 HDD",

    "atac.bowtie2_cpu" : 4,
    "atac.bowtie2_mem_mb" : 20000,
    "atac.bowtie2_time_hr" : 48,
    "atac.bowtie2_disks" : "local-disk 100 HDD",

    "atac.filter_cpu" : 2,
    "atac.filter_mem_mb" : 20000,
    "atac.filter_time_hr" : 24,
    "atac.filter_disks" : "local-disk 100 HDD",

    "atac.bam2ta_cpu" : 2,
    "atac.bam2ta_mem_mb" : 10000,
    "atac.bam2ta_time_hr" : 6,
    "atac.bam2ta_disks" : "local-disk 100 HDD",

    "atac.spr_mem_mb" : 16000,

    "atac.xcor_cpu" : 2,
    "atac.xcor_mem_mb" : 16000,
    "atac.xcor_time_hr" : 6,
    "atac.xcor_disks" : "local-disk 100 HDD",

    "atac.macs2_mem_mb" : 16000,
    "atac.macs2_time_hr" : 24,
    "atac.macs2_disks" : "local-disk 100 HDD",

    "atac.ataqc_mem_mb" : 16000,
    "atac.ataqc_mem_java_mb" : 16000,
    "atac.ataqc_time_hr" : 24,
    "atac.ataqc_disks" : "local-disk 100 HDD"
}
```

## Reference genome

We currently support 4 genomes. You can [download](../genome/download_genome_data.sh) following supported genomes from our repo. You can also [build a genome database for your own genome](build_genome_database.md).

|genome|source|built from|
|-|-|-|
|hg38|ENCODE|[GRCh38_no_alt_analysis_set_GCA_000001405](https://www.encodeproject.org/files/GRCh38_no_alt_analysis_set_GCA_000001405.15/@@download/GRCh38_no_alt_analysis_set_GCA_000001405.15.fasta.gz)|
|mm10|ENCODE|[mm10_no_alt_analysis_set_ENCODE](https://www.encodeproject.org/files/mm10_no_alt_analysis_set_ENCODE/@@download/mm10_no_alt_analysis_set_ENCODE.fasta.gz)|
|hg19|UCSC|[GRCh37/hg19](http://hgdownload.cse.ucsc.edu/goldenpath/hg19/encodeDCC/referenceSequences/male.hg19.fa.gz)|
|mm9|UCSC|[mm9, NCBI Build 37](<http://hgdownload.cse.ucsc.edu/goldenPath/mm9/bigZips/mm9.2bit>)|

Choose one TSV file for `"atac.genome_tsv"` in your input JSON. `[GENOME]` should be `hg38`, `mm10`, `hg19` or `mm9`.

|platform|path/URI|
|-|-|
|Google Cloud Platform|`gs://encode-pipeline-genome-data/[GENOME]_google.tsv`|
|DNAnexus (CLI)|`dx://project-BKpvFg00VBPV975PgJ6Q03v6:data/pipeline-genome-data/[GENOME]_dx.tsv`|
|DNAnexus (CLI, Azure)|`dx://project-XXXXXXXXXXXXXX:data/pipeline-genome-data/[GENOME]_dx_azure.tsv`|
|DNAnexus (Web)|Choose `[GENOME]_dx.tsv` from [here](https://platform.DNAnexus.com/projects/BKpvFg00VBPV975PgJ6Q03v6/data/pipeline-genome-data)|
|DNAnexus (Web, Azure)|Choose `[GENOME]_dx.tsv` from [here](https://platform.DNAnexus.com/projects/XXXXXXXXXXXXXX/data/pipeline-genome-data)|
|Stanford Sherlock|`/home/groups/cherry/encode/pipeline_genome_data/[GENOME]_sherlock.tsv`|
|Stanford SCG|`/reference/ENCODE/pipeline_genome_data/[GENOME]_scg.tsv`|
|Local/SLURM/SGE/PBS|You need to [download](../genome/download_genome_data.sh) or [build a genome database](build_genome_database.md). |
