# sushi_internship

## Updates & questions

### 21.11.2022 - First trials (PCA)
#### Questions
How do I enter the fgcz RStudio (to for example edit VcfStats.Rmd; instead of using vim)?
> https://fgcz-genomics.uzh.ch
> 
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

#### Updates, notes & code (messy now, needs some changes)

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
Fehler in df2genind(snp_df, ploidy = 2) : X is not a matrix
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
