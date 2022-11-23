# 21.11.2022 - First trials (PCA)
### Questions
How do I enter the fgcz RStudio (to for example edit VcfStats.Rmd; instead of using vim)?
> https://fgcz-genomics.uzh.ch
> Better: https://fgcz-c-043.uzh.ch/rstudio/

How do I access the file? (eg /srv/GT/analysis/jonas/ezRun/inst/templates/VcfStats.Rmd)

What is the difference between misc and srv (ie /srv/GT/analysis/jonas/R_LIBS vs. /misc/GT/analysis/jonas/R_LIBS)
> /srv is an alias to /misc; recommended to use /srv

Is this the discussed R package? https://github.com/haneylab/geNet

Or this maybe? https://github.com/thibautjombart/adegenet 
> -> it's this one (and ade4)

How can I copy something when using vim?
> ie yy to copy a line, but doesn't copy outside of vim

Could you remind me briefly which things more or less I can keep the same and which files/parts of code I should change when trying to implemenet something new?
> see below ("3 files")

### Updates, notes & code

#### aliases
To make the alias persistent you need to declare it in the ~/.bash_profile or ~/.bashrc file
> in my case, the ~/.bashrc file directs to a ~/.bash_aliases file, create this and add aliases

Setting everything up and checking if everything previously done still works

First try to add some PCA stuff to the Vcf App (later make a separate one)
```
$ ssh jobucher@fgcz-c-047.uzh.ch
$ cd /srv/GT/analysis/jonas/jonas_test_sushi_20221115/master
```

How can I copy in vim?
  
  
Rmarkdown, for basic stats and visualizations (main report)
```
@fgcz-c-047:/srv/GT/analysis/jonas/ezRun
$ vim R/app-VcfStats.R
```

(this runs VcfStats.Rmd to generate main reports)
where is this?
-> here: ezRun/inst/templates/VcfStats.Rmd
(at least that is where I made the change last time)

There are 3 files:
* /srv/GT/analysis/jonas/jonas_test_sushi_20221115/master/lib/VcfStatsApp.rb (ruby):
  contains params for columns (default names and choices for ram, cores etc) and "def commands"
  
* /srv/GT/analysis/jonas/ezRun/R/app-VcfStats.R (R file)
   contains definition of filepaths; runs command; calls Rmd file to generate main reports
   
* /srv/GT/analysis/jonas/ezRun/inst/templates/VcfStats.Rmd
  uses data generated in R file to make plots/figures
  

Add PCA calculations after gc() (in app-Vcf...)

                    
Do I need to load R libraries in app-VcfStats.R ?

Updated Rmd file (in browser; ~/sushi_project_JB); copy back to /srv/GT/analysis/jonas/ezRun/inst/templates/:
```
(sushi) jobucher@fgcz-c-047:~/sushi_project_JB$ scp VcfStats.Rmd /srv/GT/analysis/jonas/ezRun/inst/templates/ 
$ bundle5000
```
(note: some aliases used)

Check if it worked in browser (http://fgcz-c-047.uzh.ch:5000), using this file for testing: /srv/gstore/projects/p1535/test_vcf_dataset/ragi_highcov_sa0001_1k.vcf.gz
> still shows "Jonas test header"

I forgot one or two steps on the way

```
$ ssh jobucher@fgcz-genomics.uzh.ch 
$ rinst
$ cd /srv/GT/analysis/jonas/jonas_test_sushi_20221115/master
$ bundle5000 

no acceptor (port is in use or requires root privileges) (RuntimeError)
```

Check the process
```
$ ps aux|grep rails

jobucher 21595  0.0  0.0   6144   896 pts/0    S+   17:31   0:00 grep rails
```
This does not seem to be the issue; I think I am using the wrong server or something


Job failed (on 47)
```
unknown param: partition
unknown param: isLastJob
Fehler in library(adegenet) : es gibt kein Paket namens ‘adegenet’
Ruft auf: <Anonymous> -> withCallingHandlers -> runMethod -> library
error exists: jobucher@student.ethz.ch
mail sent to: jobucher@student.ethz.ch
Ausführung angehalten
```

Asked Natalia, likely a version problem (4.2.0 vs 4.2.2) -> adegenet not installed
She installed it, check again if it works

```
Fehler in df2genind(snp_df, ploidy = 2) : **X is not a matrix**
Ruft auf: <Anonymous> -> withCallingHandlers -> runMethod -> df2genind
error exists: jobucher@student.ethz.ch
mail sent to: jobucher@student.ethz.ch
Ausführung angehalten
```


For relatively small datasets (up to a few thousand SNPs) SNPs can be handled as usual codominant markers such as
microsatellites using genind objects. In the case of genome-wide SNP data (from hundreds
of thousands to millions of SNPs), genind objects are no longer efficient representation
of the data. In this case, we use genlight objects to store and handle information with
maximum efficiency and minimum memory requirements.
-> genind for now, maybe genlight in general? or use different things depending on size? (question for later)


#### in master
```
$ bundle exec rails s -e production -b fgcz-c-047.uzh.ch -p 5000
$ R CMD INSTALL /srv/GT/analysis/jonas/ezRun
# this is always the final step (run this after testing on RStudio)
```


# 22.11.2022 - PCA trials continued

### Questions

* How do I generate the PCA file?
* Are all assigned variables from the .R file available in the .Rmd file?

* Do I need to push as well before trying or is commit enough? And do I need to run both "bundle ..." and "R CMD ..." and if yes, in which order?
Commit should be enough 

* Do I have to be on fgcz-genomics.uzh.ch to run "R CMD ..." ?

* Is there population information?

### Updates, notes & code

#### How to easily test code/files

* Test R files: Download script from gstore or something
* Test Rmd files: With script/outputs from R file (should have been tested previously); load them to RMarkdown, then it's possible to test stuff there

Scripts: gstore/scome_file/scripts/foobar.sh

Comment out last part (below JOB IS DONE WE PUT THINGS IN PLACE AND CLEAN AUP)

Change SCRATCH_DIR


##### Try 1 using gdsfmt & SNPRelate

Both are already installed on the server

* Commit app-VcfStats.R
* Copy changed Rmd file:
(sushi) jobucher@fgcz-c-047:~/sushi_project_JB$ scp VcfStats.Rmd /srv/GT/analysis/jonas/ezRun/inst/templates/ 

$ scp ~/sushi_project_JB/VcfStats.Rmd /srv/GT/analysis/jonas/ezRun/inst/templates/ 

* Run bundle & try on server (bundle)

Process already running -> kill it
$ ps aux|grep rails
$ kill -9 xxxxx

Went through some completely avoidable struggles; did not change to jobucher@fgcz-genomics.uzh.ch for "rinst"

$ ssh jobucher@fgcz-genomics.uzh.ch 
$ rinst
$ exit 
$ cd /srv/GT/analysis/jonas/jonas_test_sushi_20221115/master (alias "mast")mast
$ bundle5000 

```
unknown param: partition
unknown param: isLastJob
Fehler in library(gdsfmt) : **es gibt kein Paket namens ‘gdsfmt’**
Ruft auf: <Anonymous> -> withCallingHandlers -> runMethod -> library
error exists: jobucher@student.ethz.ch
mail sent to: jobucher@student.ethz.ch
Ausführung angehalten
```

Install R packages from command line:
```
(sushi) jobucher@fgcz-c-047:/srv/GT/analysis/jonas/R_LIBS$ R 
if (!require("BiocManager", quietly = TRUE))
    install.packages("BiocManager")

BiocManager::install("gdsfmt")

if (!require("BiocManager", quietly = TRUE))
    install.packages("BiocManager")

BiocManager::install("SNPRelate")
```

Try again to run & check in browserexit

```
Hint: it is suggested to call `snpgdsOpen' to open a SNP GDS file instead of `openfn.gds'.
Fehler in .InitFile2(cmd = "Principal Component Analysis (PCA) on genotypes:",  : 
  There is no SNP!
```

```
Fehler in .InitFile2(cmd = paste(ifelse(inherits(gdsobj, "SeqVarGDSClass"),  : 
  There is no SNP!
Ruft auf: <Anonymous> ... withCallingHandlers -> runMethod -> snpgdsLDpruning -> .InitFile2
```

Try: pca <- snpgdsPCA(genofile)

Same error, but file conversion seems to be working (the error is in SNPRelate)
There needs to be SNP in gds 

This works
```
Principal Component Analysis (PCA) on genotypes:
Excluding 857 SNPs on non-autosomes
Finished	EzAppVcfStats	vcf_stats		VcfStats_pca_test8_2022-11-22--16-43-45_temp3368	2022-11-22 16:44:09
```

Another try with snpind, if it doesn't work: run until genofile is read; see if I can work with output file in R

alternative VCF to gds conversion:
* The SeqArray package provides a function seqVCF2GDS() to reformat a VCF file, and it allows merging multiple VCF files during format conversion


# 23.11.2022 - gdsfmt & SNPRelate

### Questions

### Updates, notes & code

Run app-VcfStats.R with PCA part only until reading of genofile:

```
$ ssh jobucher@fgcz-genomics.uzh.ch 
$ rinst
$ exit
$ cd /srv/GT/analysis/jonas/jonas_test_sushi_20221115/master
$ bundle5000
```

Submission test: http://fgcz-c-047.uzh.ch:5000

Worked, check output: genofile ("snp.gds") not saved

Change to file.path(output_dir, "snp.gds")

Try again, worked! Download file then copy (when in downloads):

$ scp snp.gds jobucher@fgcz-c-047.uzh.ch:~/sushi_project_JB/data/ 

in .Rmd:
```
SNP pruning based on LD:
Excluding 857 SNPs on non-autosomes
```

Possible things to try:
* autosome.only=FALSE

-> First PCA works, now figure out what to do and improve the visualisation, possibly try other packages

#### PCA
Population groups: Think about input. Possibilities:
* Required (or optional) input of population file by user

Interactive? Variable vector overlay?
