# Purpose
Theses scripts written in python 3 allow for proteome comparison between different species, analysis of InterProScan results and generation of nice bar diagrams.

# Requirements

Required packages are stated in `requirements.txt` file, you can install them with command:

`pip install -r requirements.txt`

# How to use
The main script is `run.py`, it can be used from the console with command `python run.py`.
It takes different arguments depend on which type of analysis we want to execute.

List of arguments:
- `-f --fasta` it takes names of files that includes proteomes of organisms in FASTA format. 
- `-r --rep_names` here goes names of analized organisms in manner you want to appear in files or on charts.
- `-t --type` indicates which type of analysis we want to execute. For now there are 3 possible analysis to perform:
  - 'org' - BLAST file should be made in a way where proteome of specified organism is blasted against a base made out of proteomes of all other analized organisms. Requires minimum -f, -r, -b arguments.
  - 'base' - BLAST files should be simply organisms proteomes blasted against selected database. Requires minimum -f, -r, -b, -db, -db_names arguments.
  - 'inter' - This type just needs InterProScan results files in .tsv format. Requires minimum -f, -r, -ips arguments.
- `-b --blast_results` names of files that contains results of BLAST analysis 
- `-ips --interproscan` this argument takes names of .tsv files that contains results of InterProScan
- `-a --interproscan_analysis` it is optional argument in which you can set InterProScan analysis you are interested in. If none provided, the first provided InterProScan results file will be scanned and all found analysis will be considered.
- `-db --databases` takes files of bases in FASTA format.
- `-db_names --databases_names` names of databases in a manner that will appear in files and on charts.
- `-e --e_cutoff` negative value of power of e in BLAST hits e-value. By default set to 10, it means all hits with e-value higer than e^-10 will be ignored.
- `-tex --use_tex` if set to True charts will use LaTeX - names of organisms will be italicized and font will be more readable.

# Results
Each type of analysis returns `proteomes_statistics.txt` file that includes number and avarage length of proteins in each proteome.

- 'org' returns file `cross_results.txt` and a FASTA files with extension `.unique` for each organism with proteins that had zero blast hits with e-value below specified threshold - those are proteins unique for those organism.
- 'base' returns plot for each provided database, with how many proteins from each organism had blast hit to that database. It also returns a file `base_results.txt` that include these statistics.
- 'inter' returns plot for each InterProScan resource, with how many proteins from each organism had blast hit to that resource.

# Examples
- example 'org' analysis can be started with command:

`run.py -f A.fasta B.fasta C.fasta D.fasta -r A B C "D organism" -t org -b A_against_BCD.blast B_against_ACD.blast C_against_ABD.blast D_against_ABC.blast -e 5`

Note that if you want to include space in organism name in `-r` argument, you have to put it in quotes (like "D organism"). Also files in `-f` `-r` and `-r` arguments have to come in the same order - first files and name describes first organism, second in order describes another and etc. 

The outcome of the code will be files already stated in Results section. 
In `cross_results.txt` each line is build in a manner "<organism> <list of organisms> <number>". 
It describes how many proteins (<number>) from <organism> had valid (with e-value below 1\*e^-5) hit from all of the organisms stated in <list of organisms>.
Each file with `.unique` extension is based on FASTA file of proteome of corresponding organism, but it contains only those sequences that did not have any blast hit to any other analysed organism (with e-value below 1\*e^-5).

- example 'base' analysis can be started with command:

`run.py -f A.fasta B.fasta C.fasta -r A B C -t base -b A_blast.baseA B_blast.baseA C_blast.baseA A_blast.baseB B_blast.baseB C_blast.baseB -e 5 -db baseA.fasta baseB.fasta -db_names "Some A base" "Other B base" -use_tex True`

Analysis for several bases can be run at once, but blast files and database FASTA files have to be ordered - first comes blast files for first database, than for next etc. , they also have to be ordered in the same way as input for `-f` and `-r` arguments. 

- example 'base' analysis can be started with command:

`run.py -f A.fasta B.fasta C.fasta -r A B C -t base -b A_blast.baseA B_blast.baseA C_blast.baseA A_blast.baseB B_blast.baseB C_blast.baseB -e 5 -db baseA.fasta baseB.fasta -db_names "Some A base" "Other B base" -use_tex True`

Analysis for several bases can be run at once, blast files have to be in current working directory and their names have to be 

- example 'inter' analysis can be started with command:

`run.py -f A.fasta B.fasta C.fasta -r A B C -t base -e 5 -db baseA.fasta baseB.fasta -db_names "Some A base" "Other B base" -use_tex True`

Blast files should be present in working directory and in this example for organism A they would look like "A.fasta.basea" ,"A.fasta.baseb". 
Basiacally they have to be named with same name as organism fasta file, but with added base name as an extension.
Bases here are named baseA and baseB, so extensions should be in lower case - accordingly '.basea' and '.baseb'
