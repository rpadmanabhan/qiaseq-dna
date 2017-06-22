# qiaseq-dna
This repository contains some example code for processing reads from QIAGEN QIAseq DNA enrichment kits.

python run examples
-------------------
The "run" directory contains three examples of how to use the read processing code in this repository:

1. **run_dedup** - Use the Picard MarkDuplicates utility from Broad Institute to remove PCR duplicate reads using the UMI and both paired-end read start locations on the reference genome.  This might be useful for subsequent germline variant calling, or structural variant detection applications.  Note that this script does not filter ligation chimera reads, nor does it remove PCR duplicate reads caused by internal re-priming by a downstream SPE primer. 

2. **run_consensus** - Use the "fgbio" package from Fulcrum Genomics to create a single consensus read pair for each input molecule.  This might be useful to prepare consensus read alignments for a subsequent SNP/indel variant calling procedure that does not use UMI-tagged reads.  Users might need to tune the fgbio parameters for their application.  We have not performed any variant calling performance benchmarking using the consensus BAM generated by this pipeline.

3. **run_sm_counter** - Use the "smCounter" variant calling procedure, described here:
[Detecting very low allele fraction variants using targeted DNA sequencing and a novel molecular barcode-aware variant caller", BMC Genomics, 2017 18:5](https://bmcgenomics.biomedcentral.com/articles/10.1186/s12864-016-3425-4)   
The smCounter variant caller uses a statistical model that requires both raw sequencer base calls and unique input molecule identification.

python packages
---------------------
#### core  
This package contains modules that trim common regions of the reads, align reads to the reference genome, identify putative original input molecules, and trim SPE primer regions from the genome alignments.  These steps generate a BAM file suitable for subsequent variant calling.

#### metrics
This package contains auxiliary modules that provide read accounting and enrichment summary metrics such as uniformity and fragment length distribution.

#### varcall
This package contains the smCounter variant caller and downstream VCF annotation using snpEff.

Docker image for third-party dependencies
-----------------------------------------
The python modules in this repository have many dependencies on third-party NGS software (e.g. BWA, samtools, etc.) and GNU Linux utilities (sort, zcat, etc).  Please **DO NOT ATTEMPT** to use the python modules in this git repository without first running the code on the example read set using our Docker image:

```bash
docker pull rpadmanabhan9/d11f09712ba2 

docker run python /srv/qgen/code/qiaseq-dna/run/run_consensus.py NEB_S2 > run.log 2>&1
```
The dependencies are fully documented in the Dockerfile in this repository.


