# Daisy
#### Horizontal Gene Transfer Detection by Mapping Sequencing Reads

Daisy is a pipeline for horizontal gene transfer (HGT) detection from
sequencing data. It requires sequencing data from the HGT organism and
reference sequences from the acceptor/recepient genome and the donor genome.

Daisy is a pipeline written in Python that uses Samtools and Bamtools for SAM
file processing and extraction of unmapped reads, the C++ SeqAn tools Yara and
Gustaf for mapping, and contains a Python based evaluation routine.

Contact: kathrin.trappe@fu-berlin.de

## Installing Daisy
Daisy is undergoing some refactoring and upgrading, please let us know if you encounter any problems.

Daisy needs to have Python installed. For versions < 3.2, please install the package
`subprocess32` additionally.
The easiest way to get Daisy is to
download **daisy.py** and the script **hgt_eval.py** and place both scripts in your
`~/bin/` directory.
Daisy depends on the following established open-source tools which have to
be installed either in your `~/bin/` as well or be globally avaible for all
users on your server.

###### Samtools, Bedtools
Daisy requires the old `0.1.19 samtools release` (due to parameter changes in more recent smatools versions). To install Samtools and Bedtools, please follow the installation guides given
on
http://samtools.sourceforge.net/
and
http://bedtools.readthedocs.org/en/latest/


###### Yara, Stellar, Gustaf, SAK
All tools are distributed with SeqAn - The C++ Sequence Analysis Library (see
http://www.seqan.de). Please check out the latest
developer version of SeqAn on:
http://github.com/seqan/seqan/tree/develop/
Follow the installation guides given on
http://seqan.readthedocs.org/en/master/Tutorial/GettingStarted/LinuxMakefiles.html

Precompiled binaries (Linux 64-bit, Windows, Mac OS X) of Yara, Stellar, Gustaf,
and SAK can be downloaded via the SeqAn project pages:
http://www.seqan.de/projects/


###### Phage database
As an additional step, Daisy maps the reads against a phage database, and flags HGT
candidates having relevant hits. We recommend using the phage database available from
http://www.ebi.ac.uk/genomes/phage.html

## Using Daisy
##### Example
Download the folder "data/example" and make sure you have all tools ready.
The example run is a subsample from the simulated data set below. The reads
stem only from the inserted phage sequence plus 2000bp surrounding sequence.
The run takes only a few minutes. Within the example folder, you can run it via
```
python ~/bin/daisy.py -r1 Ecoli_K12_mod_HPylori_1322000-1350000_mod_1115289-1147285.1.fa
                      -r2 Ecoli_K12_mod_HPylori_1322000-1350000_mod_1115289-1147285.2.fa
                      -ar ../Ecoli_K12.fa -dr ../Helicobacter_pylori_ML1.fasta
                      -a "gi|170079663|ref|NC_010473.1|" -d "gi|766541424|dbj|AP014710.1|"
```
If you have downloaded the phage database, add its path to the program call
using the `--phage_ref` option (e.g. `--phage_ref phage_all.fasta`).
The produced result files should be equivalent to the corresponding *gold* files
provided in the folder, but exact breakpoint positions might vary due to input parallelization.
To enforce *exactly* same results, use option `-nth 1` (one thread).

##### H. pylori data set
The H. pylori data set is the complete simulated data set evaluated in the paper.
From within the HPylori folder, you can re-run it via
```
python ~/bin/daisy.py -r1 Ecoli_K12_mod_HPylori_1322000-1350000_mod.1.fasta
                      -r2 Ecoli_K12_mod_HPylori_1322000-1350000_mod.2.fasta
                      -ar ../Ecoli_K12.fa -dr ../Helicobacter_pylori_ML1.fasta
                      -a "gi|170079663|ref|NC_010473.1|" -d "gi|766541424|dbj|AP014710.1|"
                      -new
```
Daisy checks for the presence of already computed files. So if you omit the `-new` parameter, Daisy will recognize the existing files and run through without changing results.
Use the `-task` parameter to assign job names. You can also specify each pipeline step to be run or not run separately (see help message).

##### Multiple donor candidates
If you want to test multiple donor candidates at once, use the `-d2` parameter with a textfile containing the desired donor gis. The program expects one gi per line. The donor reference file then has to contain all donor references (multifasta). Alternatively, you can provide a second donor reference file with parameter `-dr2`.
```
python ~/bin/daisy.py [...] -dr multifasta.fasta -d2 donor_candidates.txt
```

## Output Formats
Daisy currently supports the VCF output format for reporting HGT candidates
meeting the pre-defined threshold.
Additionally, all HGT candidates together with their sampling results are
written to a TSV file.

##### Variant Call Format (VCF)
The output is according to VCF 4.2. We report the single HGT boundaries as
inter-chromosomal translocations as SV type, connect the boundary pairs via
identical IDs and introduce the event tag 'HGT'.

See http://www.1000genomes.org/wiki/analysis/variant%20call%20format/vcf-variant-call-format-version-41
for information about the VCF file format specifications.

## Reference
Trappe K., Marschall T., Renard B.Y. (2016). 
Detecting horizontal gene transfer by mapping sequencing reads across species boundaries. 
[Bioinformatics 32(17):i595-i604](https://academic.oup.com/bioinformatics/article-lookup/doi/10.1093/bioinformatics/btw423)
