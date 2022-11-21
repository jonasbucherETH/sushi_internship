# sushi_internship

## Updates & questions

### 21.11.2022 - First trials (PCA)
#### Questions
How do I enter the fgcz RStudio (to for example edit VcfStats.Rmd; instead of using vim)?
https://fgcz-genomics.uzh.ch

What is the difference between misc and srv (ie /srv/GT/analysis/jonas/R_LIBS vs. /misc/GT/analysis/jonas/R_LIBS)
> /srv is an alias to /misc; recommended to use /srv

Is this the discussed R package? https://github.com/haneylab/geNet

Or this maybe? https://github.com/thibautjombart/adegenet 
-> it's this one

How can I copy something when using vim?
yy to copy a line, but doesn't copy outside of vim

Could you remind me briefly which things more or less I can keep the same and which files/parts of code I should change when trying to implemenet something new?

#### Updates, notes & code (messy now, needs some changes)

Setting everything up and checking if everything previously done still works

First try to add some PCA stuff to the Vcf App (later make a separate one)
```
$ ssh jobucher@fgcz-c-047.uzh.ch
$ cd /srv/GT/analysis/jonas/jonas_test_sushi_20221115/master
```

How can I copy in vim?

def commands
  run_RApp("EzAppVcfStats", lib_path: "/srv/GT/analysis/jonas/R_LIBS")
  
  
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


# in master
```
$ bundle exec rails s -e production -b fgcz-c-047.uzh.ch -p 5000
$ R CMD INSTALL /srv/GT/analysis/jonas/ezRun
# this is always the final step (run this after testing on RStudio)
```
