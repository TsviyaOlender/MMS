# MMS
MMS: the Mutual Maximum Similarity algorithm 

The Mutual Maximum Similarity (MMS) algorithm is a systematic classifier for assigning a human-centric nomenclature to any OR gene based on inter-species hierarchical pairwise similarities. So far. MMS was applied to the OR repertoires of seven mammals and zebrafish. Altogether, we assigned symbols to 10,249 ORs. This nomenclature is supported by both phylogenetic and synteny analyses. A comprehensive explanation on the algorithm can be found at:  
A unified nomenclature for vertebrate olfactory receptors
Tsviya Olender, Tamsin E M Jones, Elspeth Bruford and Doron Lancet
BMC Evol Biol. 2020 Apr 15;20(1):42.doi: 10.1186/s12862-020-01607-6. 

We kindly request that you will cite this paper in your publication.

The script is written in perl and uses the libraries Config::Simple and File::Path
Additional dependencies: BLAST and MMSeqs2

Installation
MMS is design to run on the cluster of the Weizmann Institute. Running MMS on a different cluster requires the following modifications: 
1. The scripts MMS_step1_mmseqs.pl and MMS_step1_parallel.pl load some modules to the environment. Please update the modules to the versions that are installed in your system.
E.g. lines like
module('load ncbi-blast+/2.5.0');
module('load MMseqs2/2-1c7a89'); 

2. The script MMS_step1_parallel.pl submits jobs to LSF, the job scheduler of our cluster. To run it on a different cluster please modify the name of the queue at line 9.

Running MMS
The published version of MMS is based on BLAST searches. However, we are currently testing the use of MMseqs2 instead of the traditional BLAST. MMseqs2 works much faster, does not require parallelization (submitting LSF jobs) and reports a single HSP per gene. However, MMseqs2 is not as sensitive and we find that in rare cases it fails to identify high similarity relationship.
The two MMS versions are supplied in the current distribution. 
 
MMS is composed of 3 steps, which should be run consecutively.
Before you start, make sure that the database folder contains the sequences of all the classified organism. 
Edit the file OR_repertoires.txt, under the folder lib to contain names of all the pre-classified fasta files. These are expected to be located in the database folder. One fasta file par organism.
You also need to prepare a file with the run parameters (e.g parameter_cat.txt). This file look like:
[params]
humanLib = database/HORDE44pep_Oct20.fa
humanIndx = database/HORDE44pep_Oct20.DB
NohumanOrganismFileName = /home/labs/olenderlab/lvzvia/WIS_exp/HORDE/parallel_MMS/lib/OR_repertoires.txt

[setup_run]
organism = cat_nov20_mmseqs2
organismORlib = /home/labs/olenderlab/lvzvia/WIS_exp/HORDE/parallel_dataMining/cat_results/cat_lib1.fa
geneOrder = 0

Explanation
humanLib- the human ORs file, with the full path. This file should not be included in OR_repertoires.txt file.
organismORlib- MMseqs2 human index (with full path)
NohumanOrganismFileName- The file OR_repertoires.txt with the full path.
Organism- the name of the folder which will be created and will contain the results, typically the name of the classified organism.
organismORlib- the file with the candidate sequences (with full path)
geneOrder- The assigned symbol depends to some extend in the order of the classification. 0 means that the order of the classification is not determined. To determine the order supply a file (with full path) with the current names and the desired order.

Run step1
Step 1 does all the preparations: make the folders, prepar the files and runs BLAST/MMseqs2.
Example:
perl MMS_DIR/source/MMS_step1_parallel.pl parameter_file.txt

Run Step2
Determine the beH of each gene.
Example:
perl MMS_DIR/source/MMS_step2_BeHcalculation.pl folder_name
folder_name- the name that is supplied as “organism” in the parameter file

Run Step3
assign symbols
perl MMS_DIR/source/assign_symbol.4.pl folder_name

for any questions please email tsviya.olender@weizmann.ac.il
