# HiCapTools

## What is HiCapTools?

HiCapTools (Probe Design and Proximity Detection) is a software package
that can design sequence capture probes for targeted chromosome capture
applications and analyze sequencing output to detect proximities between
fragments. It is packaged into two modules : `ProbeDesigner` and
`ProximityDetector`. `ProbeDesigner` can design sequence capture probes for
sequences of interest. `ProximityDetector` takes mapped and processed
targeted Hi-C data and detects proximities between probes and rest of
the genome.

Table of Contents
=================

   * [HiCapTools](#hicaptools)
      * [What is HiCapTools?](#what-is-hicaptools)
      * [How to Compile HiCapTools?](#how-to-compile-hicaptools)
      * [How to Run HiCapTools?](#how-to-run-hicaptools)
         * [Running the ProbeDesigner](#running-the-probedesigner)
            * [Required Arguments](#required-arguments)
            * [Optional Arguments](#optional-arguments)
            * [Required Inputs](#required-inputs)
               * [1. Base Config](#1-base-config)
               * [2. Negative Control Probe Design](#2-negative-control-probe-design)
         * [Running the ProximityDetector](#running-the-proximitydetector)
            * [Required Arguments](#required-arguments-1)
            * [Optional Arguments](#optional-arguments-1)
            * [Required Inputs](#required-inputs-1)
               * [I. configFile.txt](#i-configfiletxt)
               * [II. ExperimentFile.txt](#ii-experimentfiletxt)
       * [File Formats](#file-formats)
            * [Input Files](#input-files)
               * [Transcript Files](#transcript-files)
               * [SNV Files](#snv-files)
               * [Forbidden regulatory regions File](#forbidden-regulatory-regions-file)
               * [Probe File](#probe-file)
               * [Negative control region File](#negative-control-region-file)
               * [Experiment File](#experiment-file)
            * [Output Files](#output-files)
               * [I. ProbeDesigner](#i-probedesigner)
               * [II. ProximityDetector](#ii-proximitydetector)
               * [III. Common](#iii-common)
       * [References and Acknowledgements](#references-and-acknowledgements)

## How to Compile HiCapTools?

#### Recommended

Use CMake to compile the application.

#### Dependencies

-   C++11 compliant compiler (eg. GCC 4.9 or higher) is required

-   Cmake 3.0 or higher is required

-   Bamtools is required.

-   Zlib is required

#### Steps

Suggested steps in Linux

1. Run the bash script 'buildHiCapTools.sh. The compiled executable
    ’HiCapTools’ is placed inside the ’bin’ directory.

2.  Run the following command on the terminal after replacing the path
    to the location of HiCapTools.
```
$ export LD_LIBRARY_PATH=/path/to/HiCapTools/bamtools/:$LD_LIBRARY_PATH
```                

## How to Run HiCapTools?

HiCapTools is run with the following command

``` {fontsize="\small"}
$ ./HiCapTools <option> [arguments]
```

The options are `ProbeDesigner` and `ProximityDetector`.

### Running the ProbeDesigner

To use the probe designer, run HiCapTools with the `ProbeDesigner` option.

#### Required Arguments

| Argument(s)  | Values | Summary |
| ------------- | ------------- |------------- |
| -c <BR /> --chr  | chrN <BR /> chrAll | chromosome for which Probes are to be designed in the format ’chrN’ where N <BR />is chromosome number. To design probes for all chromosomes at once use ’chrAll’ |

#### Optional Arguments

| Argument(s)  | Values | Summary |
| ------------- | ------------- |------------- |
| -config  | file path | the path to the new config file, if changed from the default name and location in bin/config directory |



#### Required Inputs

To run the HiCapTools `ProbeDesigner`, the ’ProbeConfig.txt’ file in the bin/config directory must be present and its fields populated. The
fields of the ’ProbeConfig.txt’ file are divided into two categories - ’Base Config’ fields and ’Negative Control Probe Design’ fields.

##### 1. Base Config

- Base File Name *(STRING:REQUIRED)* :   The name with which the output file names will start.

- Digested Genome File *(STRING: OPTIONAL)* :   The file path to the Genome digest file. The digest file should be in the HiCUP_digester output format. If this field is left empty, the genome digest will be generated from the fasta file by HiCapTools. 

- RE cut site motif *(STRING: REQUIRED)* :   The cut site motif of the restriction enzyme used to digest the genome. It should be given in the format ’X^XXX,EnzymeName’ with the cut site indicated by a ’^’. E.g. ^GATC,MboI; A^AGCTT,HindIII

- Genome assembly *(STRING: REQUIRED)* :   The genome assembly build version of the reference genome that is used to map the pairs. It should be in the format ’buildVersion,source’. Eg. hg19,UCSC

- Transcript List File *(STRING: OPTIONAL/REQUIRED)* :   The path to the file containing the list of all transcripts in the genome used. Either Transcript List file or SNV List file is required. Both files can be used together. The file should be in the transcript file format as described in the [File Formats](#file-formats) section. 

- SNV List File *(STRING: OPTIONAL/REQUIRED)* :   The path to the file containing the list of known Single Nucleotide Variants in the genome used. Either Transcript List file or SNV List file is required. Both files can be used together. The file must be in the SNV File format as described in the [File Formats](#file-formats) section

- Repeat File *(STRING: OPTIONAL)* :   The path to the file containing repeat regions in the BED format. The field is to be left empty if not used. The repeat file for hg19 can be downloaded from the following link http://hgdownload.soe.ucsc.edu/goldenPath/hg19/database/rmsk.txt.gz . Please note that this is a large file (~140MB)

- Mappability File *(STRING: OPTIONAL)* :   The path to the file containing mappability information in the bigWig format(.bw). The field is to be left empty if not used. Mappability files for hg19 can be downloaded from the following link http://hgdownload.soe.ucsc.edu/goldenPath/hg19/encodeDCC/wgEncodeMapability/ Please note that this is a large file (>200MB)

- bigWigSummary executable path *(STRING: OPTIONAL)* :   The file path to the bigWigSummary executable. The executable for linux x86_64 included in the package in the bin directory and its path is entered by default.

- Probe Length *(INTEGER: REQUIRED)* :   The required length of a probe in the current design. The default probe length is 120 base pairs. The probe length should ideally be between 50 and 1000 base pairs.

- Minimum distance between Probes *(INTEGER: REQUIRED)* :  The minimum distance allowed between two Probes in the current design. The default value is 1000 base pairs.

- Maximum distance from Probe to feature start (TSS if the feature is transcript) :   *(INTEGER: REQUIRED)* The maximum distance allowed from a Probe to the TSS of the promoter it targets. The default value is 2500 base pairs.

- Cluster Promoters *(INTEGER: REQUIRED)* :   If the distance between alternative promoters of the same transcript is less than the value given here, they will be clustered. The default value is 3000 base pairs.

- Extent of Repeat Overlaps *(INTEGER: OPTIONAL)* :   The number of allowed repeat elements within a probe. The default value is 6.

- Mappability Threshold *(FLOAT: OPTIONAL)* :   The mappability threshold. The default value is 0.7. The range of values of mappability is between 0 and 1.

- Fasta File *(STRING: REQUIRED)* :   The path to the fasta file containing the genomic sequence.

- Fasta Index File *(STRING: OPTIONAL)* :   The path to the index file(.fai) for the above fasta file. This field can be left empty if the index has the same name and is in the same directory as the fasta file. 


##### 2. Negative Control Probe Design

- Design negative probe set *(STRING: REQUIRED)* :   The option to enable the generation of negative control probes(intergenic, intronic, exonic) for this design. Its value can be Yes or No. The negative control probes will be designed against the whole genome and will be selected randomly to the number needed. If empty, the value will be set to No.

- Minimum fragment length for negative probe  *(INTEGER: REQUIRED)*  : The minimum length of the restriction enzyme fragment for which negative control probes are designed. The recommended and default value is 500 base pairs.

- Number of Intergenic Negative control fragments *(INTEGER: OPTIONAL)* :   The number of intergenic negative control fragments to place probes. Empty field will be taken as zero. Two probes will be placed on each negative control fragment.

- Number of Intronic Negative control fragments *(INTEGER: OPTIONAL)* :   The number of intronic negative control fragments to place probes. Empty field will be taken as zero. Two probes will be placed on each negative control fragment.

- Number of Exonic Negative control fragments *(INTEGER: OPTIONAL)* :   The number of exonic negative control fragments to place probes. Empty field will be taken as zero. Two probes will be placed on each negative control fragment.

- Minimum distance to a known promoter for negative control probes *(INTEGER: OPTIONAL)* : The minimum distance around a promoter in which negative control probes will not be placed. The recommended value is 50000 base pairs(default) or higher. The default value will be used if field is empty.

- Minimum distance to a known gene for intergenic negative control probes *(INTEGER: OPTIONAL)* : The minimum distance around a gene in which intergenic negative control probes will not be placed. The recommended value is 50000 base pairs(default) or higher. The default value will be used if field is empty.

- Use user provided forbidden regions? *(STRING: OPTIONAL)* :   The option to add extra genomic regions (such as known regulatory elements) which are to be avoided in the placement of negative control probes. The value must be Yes or No. Empty field will be taken as No.

- User provided forbidden regions File *(STRING: OPTIONAL/REQUIRED)* : The path to the file in BED format which contains the extra regulatory regions to be avoided in the design of negative control probes. This field should be populated if above option is Yes. 

- Minimum distance to any user provided forbidden regions *(INTEGER: OPTIONAL)* : The minimum distance around the provided regulatory regions in which negative control probes will not be designed. The recommended value is 50000 base pairs(default) or higher. The default value will be used if field is empty.


### Running the ProximityDetector

To use the proximity detector, run HiCapTools with the `ProximityDetector` option.

#### Required Arguments

| Argument(s)  | Values | Summary |
| ------------- | ------------- |------------- |
| -c <BR /> --chr  | chrN <BR /> chrAll | chromosome for which Proximities are to be found in the format ’chrN' where N is chromosome number. To detect proximities for all chromosomes at once use ’chrAll’. Note that it is assumed that the sequences are mapped on a UCSC assembly such as hg19 or mm10. |
| -m <BR /> --outputmode  | ComputeStatsOnly <BR /> PrintProximities | either ’ComputeStatsOnly’ or ’PrintProximities’. With ‘ComputeStatsOnly’, ProximityDetector computes the number of reads in the input bam files and only calculates the numbers of reads overlapping Probes in different cases. With ‘PrintProximities’, it also outputs the Proximities calculated. |
| -p <BR /> --proximitytype | Neg<BR /> NonNeg <BR /> Both | Value must be ’Neg’, ’NonNeg’ or ’Both’. With ‘Neg’, `ProximityDetector` will output only negative control Probe interactions, while with ‘NonNeg’ it outputs only feature Probe interactions and with ’Both’ it outputs both. |

#### Optional Arguments

| Argument(s)  | Values | Summary |
| ------------- | ------------- |------------- |
| -config  | file path | the path to the new config file, if changed from the default name and location in bin/config directory |

#### Required Inputs

To run the HiCapTools `ProximityDetector`, the ’configFile.txt’ file and the ‘ExperimentFile.txt’ in the bin/config directory must be present and its fields populated. The fields of these files are described below.

##### I. configFile.txt

- Experiment File Name Path *(STRING REQUIRED)* :   The path to the ‘ExperimentFile.txt’. The default path points to location of the ExperimentFile in HiCapTools. Change the path only if Experiment file is moved to another location.

- Minimum Number of Supporting Pairs *(INTEGER: REQUIRED)* :   The minimum number of supporting pairs to call a proximity. The default value is 2.

- Minimum Junction Distance *(INTEGER: REQUIRED)* :   The value for the minimum junction distance to call a proximity. The default value is 1000.

- Padding *(INTEGER: REQUIRED)* :   The number of base pairs around a probe that will be taken into account while deciding overlap. The default value is 500 base pairs.

- Read Length *(INTEGER: REQUIRED)* :  The sequence read length. The default value is 80.

- Base File Name *(STRING: REQUIRED)* :   The name with which the output file names will start.

- Calculate p_values *(STRING: REQUIRED)* :   Option to calculate p values based on proximity profile of negative control probes. Its value must be either ‘Yes’ or ‘No’. Negative control probe and region files must be entered in ExperimentFile.txt if Calculate p_values is ‘Yes’.

- Bin Size for Probe-Distal *(INTEGER: REQUIRED)* :   The distance bin size for averaging probe-distal proximities of negative probes. The default value is 1000 base pairs.

- Window Size for Probe-Distal *(INTEGER: REQUIRED)* :   The window size for probe-distal proximity bins over which the rolling mean and standard deviation are calculated for smoothing. The default value is 101 bins.

- Bin Size for Probe-Probe *(INTEGER: REQUIRED)* :   The distance bin size for averaging probe-probe proximities of negative probes. The default value is 20000 base pairs.

- Window Size for Probe-Probe *(INTEGER: REQUIRED)* :   The window size for probe-probe proximity bins over which the rolling mean and standard deviation are calculated for smoothing. The default value is 3 bins.

##### II. ExperimentFile.txt

- Feature Probe File *(STRING: REQUIRED)* :   The path to the Feature Probe file in gff3 format described in [File Formats](#file-formats) section.

- Negative control Probe File *(STRING: OPTIONAL/REQUIRED)* :   The path to Negative Control Probe file in gff3 format. This field is required if Calculate p_values is Yes. It can be empty otherwise.

- Digested Genome File *(STRING: OPTIONAL)* :   The path to genome digest file that was used to design probes. The digest file should be in the HiCUP_digester output format. If this field is left empty, the genome digest will be generated from the fasta file by HiCapTools. 

- RE cut site motif *(STRING: OPTIONAL/REQUIRED)* :   The cut site motif of the restriction enzyme used to digest the genome. It should be given in the format ’X^XXX,EnzymeName’ with the cut site indicated by a ’^’. E.g. ^GATC,MboI; A^AGCTT,HindIII. This field is required if Digested Genome File is left empty.

- Genome assembly *(STRING: OPTIONAL/REQUIRED)* :   The genome assembly build version of the reference genome that is used to map the pairs. It should be in the format ’buildVersion,source’. Eg. hg19,UCSC. This field is required if Digested Genome File is left empty.

- Fasta File *(STRING: OPTIONAL/REQUIRED)* :   The path to the fasta file containing the genomic sequence. This field is required if Digested Genome File is left empty.

- Transcript List File *(STRING: OPTIONAL/REQUIRED)* :   The path to the file containing the list of annotated transcripts in the genome. It should be same file used to design probes. It should also be sorted by gene name(field 1). Either Transcript List file or SNV List file is required. Both can be used together. Please see [File Formats](#file-formats) section.

- SNV List File *(STRING: OPTIONAL/REQUIRED)* :   The path to the file containing the list of extra regions (such as SNVs or known enhancers) used. Either Transcript List file or SNV List file is required. Both files can be used together. The file must be in the BED format with the name additional optional field.

- Negative control region File *(STRING: OPTIONAL/REQUIRED)* :   The path to the Negative Control Region File in BED format from HiCapTools `ProbeDesigner`. This field is required if Calculate p_values is ‘Yes’ in configFile.txt.

- Target tags *(STRING: REQUIRED)* :  The target of each probe should be defined explicitly using the target tags. Probes can target promoters, SNVs or negative control or other regions. The values in the ‘target’ tag in the Attributes field with which Probes are annotated in the Probe gff3 files are shown below. If more than one target term is associated with a probe, put them on the same line separated by commas. The below values do not have to be changed if the probes used were designed using HiCapTools `ProbeDesigner`.

    -   Promoters=promoter

    -   SNVs=SNV

    -   Negative controls=neg_ctrl

    -   Other=other

- Number of Experiments *(INTEGER: REQUIRED)* :   The number of experiments(BAM files) from which proximities must be called.

- Experiment details fields :   The three experiment details fields must be copied, pasted and populated with values as many times as the Number of Experiments.

    - Experiment BAM File Name Path *(STRING: REQUIRED)* :   The path to the BAM file containing reads from the HiCap experiment.

    - Experiment Name *(STRING: REQUIRED)*  :   The name of the experiment.

    - Probe Design Name *(STRING: REQUIRED)* :   The value of the tag ‘design’ in the Attributes field in the Probe file used in the experiment. This is the name of the probe design and can be relevant when multiple probe designs are contained within one probe file.

### File Formats

#### Input Files

##### Transcript Files

The transcript file can be constructed from the UCSC Table browser by choosing the ’Genes and Gene Predictions’ from group, and relevant option from table and getting the output. 
The file MUST then be SORTED by gene name and the fields separated by tabs. You can produce a sorted file using the sortTranscriptFile.sh script (sh sortTranscriptFile.sh /path/to/unsortedfile) available in the scripts directory. The headers are as from the UCSC refGene table. 

If one wishes to use a subset of genes or transcripts for probe design or proximity detection, one can paste or upload the names or accessions of the subset using option "identifiers (names/accessions): " in the UCSC Table Browser.
    
```
#bin  name  chrom strand  txStart txEnd cdsStart  cdsEnd  exonCount exonStarts  exonEnds  score name2 cdsStartStat  cdsEndStat  exonFrames
1034  NM_130786 chr19 - 58858171  58864865  58858387  58864803  8 58858171,58858718,58861735,58862756,58863648,58864293,58864657,58864769,  58858395,58859006,58862017,58863053,58863921,58864563,58864693,58864865,  0 A1BG  cmpl  cmpl  1,1,1,1,1,1,1,0,  
1034  NR_015380 chr19 + 58863335  58866549  58866549  58866549  4 58863335,58864744,58865079,58865734,  58864410,58864840,58865223,58866549,  0 A1BG-AS1  unk unk -1,-1,-1,-1,

```            

##### SNV Files

The SNV files must be in the BED format with the additional optional field ‘name’. The file must have a single track line.

##### Forbidden regulatory regions File

This file must be in the standard BED format with minimum three fields.

##### Probe File

The Probe files outputted by the `ProbeDesigner` and required as input by the `ProximityDetector` are in the gff3 format with some custom
    tags in the Attributes field. The custom tags are

 *   _side_ can be ‘L’ or ‘R’ depending on whether the probe is
        designed upstream or downstream of target position/fragment.

 *   _target_ can be ‘promoter’, ‘SNV’ or ‘neg_ctrl’ if probes are
        designed by HiCapTools `ProbeDesigner`.

 *   _design_ the base file name provided in ProbeConfig.txt

 *   _featuresinvicinity_ the other features in the vicinity of this
        probe other than primary target as calculated from Cluster
        Promoters option. This is a comma separated list.

 *   _targettss_ the TSS which the probe targets for a Feature Probe
        (not applicable for negative control probes)

 * _distancetotss_ the distance to the TSS the probe targets for a
        Feature Probe (not applicable for negative control probes)

    Example:
```
 ##gff-version 3.2.1 
 ##genome-build UCSC hg19 
 chr1 . probe 112298382 112298502 . . . Name=DDX20; transcriptid=NM_007204; side=R; target=promoter; design=T1; featuresinvicinity=none; targettss=112298189; distancetotss=313
 ```

##### Negative control region File

The Negative control region File outputted by the `ProbeDesigner`, and required as input by the `ProximityDetector` if P-values are to be calculated, are in the BED format with the additional optional field ‘name’.


##### Experiment File

This file is required as input for the `ProximityDetector`. This should be in the BAM format.


#### Output Files

##### I. ProbeDesigner
* ProbeDesignLog File : This file contains the message log from a ProbeDesigner run.

* Probe file : This file contains the probe information in the gff3 file format and is required as input to ProximityDetector. It is named in the format "BaseFileName.assemblyVer_chrN.RestrictionEnzyme.date.gff3".

* Probe Sequence File : This file contains the sequence information of the probes and is named in the format "BaseFileName.assemblyVer.ProbeSequences.RestrictionEnzyme.date.txt". 

* Probe Design Summary file : This file contains the summary information of number of probes designed against features. It is named in the format "BaseFileName.assemblyVer.ProbeDesignSummary_chrN.RestrictionEnzyme.date.txt". 

* Negative control Probe file : This file in the the gff3 format contains negative control probe information and is required as input to ProximityDetector. It is generated only when the "Design negative probe set option" is set to Yes.

* Negative control Probe region File :This file in the BED format contains the list of restriction enzyme fragments for which negative control probes have been designed.  It is generated only when the "Design negative probe set option" is set to Yes.

* Negative control Probe sequence file : This file in the TXT format contains the sequence information of the negative control probes. It is generated only when the "Design negative probe set option" is set to Yes.
    
`
##### II. ProximityDetector
* ProxDetectLog File : This file contains the message log from a ProximityDetector run.

* Background Levels files : These files contains the values of mean and standard deviation for background levels of 'Probe-Distal' and 'Probe-Probe' proximities calculated from the proximities of the negative control probe set. A file each for 'Probe-Distal' and 'Probe-Probe' background levels will be generated for each input experiment file.

* Probe_Distal Proximities files : The files contain information about the proximities with distal interacting regions gainst the Probe sets called from the input experiment files. They are named in the format 'BaseFileName.AssemblyVer.chrN.Proximities.Probe_Distal.date.txt'. The file with proximities from the negative control probe set will be indicated with a 'NegCtrl' in the file name. The following information is reported for each proximity
    1. Probe : Target feature, location in chromosome, type of probe
    2. Distal region : location in chromosome
    3. Proximity : Distance between interacting fragments, and Supporting pairs and p-value from each input experiment file.
* Probe_Probe Proximities files : The files contain information about the proximities with Probe regions against the Probe sets called from the input experiment files. These files are named in the format 'BaseFileName.AssemblyVer.chrN.Proximities.Probe_Probe.date.txt'. The file with proximities from the negative control probe set will be indicated with a 'NegCtrl' in the file name. The following information is reported for each proximity
    1. Probe region 1: Target feature, location in chromosome, type of probe
    2. Probe region 2: Target feature, location in chromosome, type of probe
    3. Proximity : Distance between interacting fragments, and Supporting pairs and p-value from each input experiment file.
        
##### III. Common
* Restriction Enzyme Digest File : This file will be generated if the 'Digested Genome File' field is left blank in ProbeDesigner/ProximityDetector.

### References and Acknowledgements

-  [bioio](https://github.com/dancooke/bioio) (Daniel Cooke)   
-  [HiCUP](https://www.bioinformatics.babraham.ac.uk/projects/hicup/)(Babraham Bioinformatics)
