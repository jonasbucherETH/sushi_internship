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

BiocManager::install("gdsfmt")<img width="670" alt="pca_shiny_1" src="https://user-images.githubusercontent.com/71451797/207640057-d29a2eb0-13a1-4558-9e4f-6aed874071d0.png">


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
* Population groups: Think about input. Possibilities:
  * Required (or optional) input of population file by user

* Interactive? ggplotly(p_i, tooltip = "sample.id")
* Variable vector overlay?

* If shapes & colours used for population & samples, then probably best to have shapes by samples and colours by populations

# 24.11.2022 - gdsfmt & SNPRelate continued

### Questions

### Updates, notes & code

Changes to PCA; including (temporary) population group implementation

Commit for Masa to test

Commit/push not working (not showing on github)

# 25.11.2022 - SUSHI test of PCA, implementation details

### Questions

* What is vcf-tools exactly? (in .R)
  * Because the cmd itself is vcftools 
* What does gc() do?
  * Search for function: grep -r "gc" R|grep function
* How/where are the results from the cmd / gc() stored?

### Updates, notes & code

Check why commit/push didn't work: It actually did work, but somehow the commits are not showing on my profile in the overview

Not plotting for masa; failed after for me:
```
Can not open file './data/snp.gds'. Datei oder Verzeichnis nicht gefunden
```
Need to open it differently; check how it can be inherited from .R file

Try: genofile <- snpgdsOpen(genofile)

scp ~/sushi_project_JB/VcfStats.Rmd /srv/GT/analysis/jonas/ezRun/inst/templates/ 

Commit, push and test on SUSHI

```
$ ssh jobucher@fgcz-genomics.uzh.ch 
$ rinst
$ exit
$ mast
$ bundle5000
```

Doesn't work; try without the reading line altogether, as genofile is read in .R file with spgdsOpen

-> this worked, but plot not loaded 

* need to call the plot differently?

Try with loading ggplot2 library and with calling ggplotly(p) in the end; and reducing fig.size

-> worked!!

#### PCA ideas/notes (continued)

* Population groups: Think about input. Possibilities:
  * Required (or optional) input of population file by user

* Interactive?
  * ggplotly(p_i, tooltip = "sample.id")
  * shiny
  
* Variable vector overlay?

* If shapes & colours used for population & samples, then probably best to have shapes by samples and colours by populations

* Idea: Additional input file with sample.id as identifier
  * columns of different grouping variable
  * Actual variables to group by can be picked interactively, plot colours & shapes gets changed in browser
  * if too complicated; specify grouping variables before (or only input the columns to be grouped by)
  * Default = coloured by sample.id


#### shiny test
```{r shiny test, fig.width=20, fig.height=8, echo=FALSE, message=FALSE, warning=FALSE}
library(shiny)
library(shinythemes)

# user interface
ui <- basicPage(
  plotOutput("plot1", click = "plot_click"),
  verbatimTextOutput("info")
)

server <- function(input, output) {
  output$plot1 <- renderPlot({
    plot(p)
  })

  output$info <- renderText({
    paste0("x=", input$plot_click$x, "\ny=", input$plot_click$y)
  })
}

# Create Shiny object
shinyApp(ui = ui, server = server)
```


#### MDS (multidimensional scaling)

* How to get distance matrix? -> PLINK
  * check available module in master/lib with cmd "module avail"
  * add @modules = [Tools/PLINK/1.9beta6.21] to .rb file
  * Put the command to get the distance matrix in app-VcfStats.R
  * Examples and possible options to try:
 
``` 
plink --vcf filtered_vcfs_genome_merged/A.vcf.gz --allow-extra-chr 0 --threads 8 --pca --out out_pca/A

--mds-plot <dimension count> ['by-cluster'] ['eigendecomp'] ['eigvals']
```

Reference module (for vcf): Tools/vcftools/0.1.16 

(Try to change & run command (bottom of .rb file) to test plink)

Test 1st implementation of cmd in .R through SUSHI

```
sh: 1: vcf-stats: not found
Fehler in ezSystem(cmd) : 
  vcf-stats /srv/gstore/projects/p1535/test_vcf_dataset/ragi_highcov_sa0001_1k.vcf.gz -p vcf_stats/vcf_stats 
 failed
Ruft auf: <Anonymous> -> withCallingHandlers -> runMethod -> ezSystem
Zusätzlich: Warnmeldung:
In system(cmd, intern = intern, ...) :
  Fehler bei der Ausführung des Befehls
```

vcf-stats module not loaded, but plink yes

-> needed to put it in same line, comma-separated

Had a typo

Note for command parameters/options:

* square brackets [optional option]
* angle brackets <required argument>
* curly braces {default values}
* parenthesis (miscellaneous info)
  
Test MDS with <dimension count> = 10
  
```
Error: Multiple instances of '_' in sample ID.
If you do not want '_' to be treated as a FID/IID delimiter, use --double-id or
--const-fid to choose a different method of converting VCF sample IDs to PLINK
IDs, or --id-delim to change the FID/IID delimiter.
Fehler in ezSystem(cmd) : 
  plink --vcf /srv/gstore/projects/p1535/test_vcf_dataset/ragi_highcov_sa0001_1k.vcf.gz --cluster --mds-plot 10 --out vcf_stats/vcf_stats 
 failed
```
  
Talk to Masa: Try with different input? Add --double-id (maybe on condition)?
  
# 28.11.2022 - MDS continued (slightly ill; home office)

### Questions
  
* Which test dataset should I use?
  
-> use this dataset as an example: https://fgcz-sushi.uzh.ch/data_set/p1535/73589

The dataset (dataset.tsv) has a new column, Population, and the file, populations.tsv has two columns, Sample and Population.

One individual has "XXX" as a population name, and please avoid using this individual for plotting.

### Updates, notes & code
  
Goals:
  * Make MDS work
  * Maybe PCA refinement
  
--double-id
  
FGCZ website on 5000 is timing out
  
Try alternative VPN: uzhvpn1.uzh.ch with uzh credentials -> works
  
(instead of sslvpn.ethz.ch/student-net)
  
"Empty run" log:
```
No trace type specified:
Based on info supplied, a 'bar' trace seems appropriate.
```
  
# 29.11. - 30.11.2022 - MDS & more 

### Questions
  
* How many dimension for MDS plot?
  * "If you don't need exact reconstruction, the Johnson-Lindenstrauss Lemma says you can get close to preserving all interpoint distances with O(log I)
dimensions" (I = dimension of matrix = samples)
* Do I have to import file again? (And how?)
  * Yes, but only dataset.tsv, the rest is in gStore
* How to add population to mds/pca? load the file in .R?
  
* How to test with specific input file in R (not Rmd file)?
  

### Updates, notes & code
  
Test plink MDS-plot command locally
$ cd
$ cd sushi_project_JB
$ module load Tools/PLINK/1.9beta6.21
$ plink --vcf /srv/gstore/projects/p1535/test_vcf_dataset/ragi_highcov_sa0001_1k.vcf.gz --cluster --mds-plot 10 --out .data/
Error: Failed to open .data/.log.  Try changing the --out parameter.
  
--out is a prefix for files generated: change to plink_test1
  
Error: Multiple instances of '_' in sample ID.
  
Try --double-id
$ plink --vcf /srv/gstore/projects/p1535/test_vcf_dataset/ragi_highcov_sa0001_1k.vcf.gz --double-id --cluster --mds-plot 10 --out plink_test1
  
Error: Invalid chromosome code 'sa0001' on line 115 of .vcf file.                                                                                                           (Use --allow-extra-chr to force it to be accepted.)
  
$ plink --vcf /srv/gstore/projects/p1535/test_vcf_dataset/ragi_highcov_sa0001_1k.vcf.gz --double-id --allow-extra-chr --cluster --mds-plot 10 --out plink_test1
  
MDS solution written to plink_test1.mds . 
  
~/sushi_project_JB/data$ scp -r jobucher@fgcz-genomics.uzh.ch:/srv/gstore/projects/p1535/test_vcf_dataset ./ 
  
Try with "new" dataset that includes population
$ plink --vcf /srv/gstore/projects/p1535/test_vcf_dataset/ragi_highcov_sa0001_1k.vcf.gz --double-id --allow-extra-chr --cluster --mds-plot 10 --out plink_test1
  
Add this in .R file, try to run on SUSHI (with empty Rmd file)
  
  -> worked
  
$ scp plink_mds.mds.txt jobucher@fgcz-c-047.uzh.ch:~/sushi_project_JB/data/
  
# 06.12. - 08.12.2022 - Illness

# 12.12. - 13.12.2022 
          
Trying to run:
    $ cmd <- paste("plink --vcf", "/srv/gstore/projects/p1535/test_vcf_dataset/ragi_highcov_sa0001_1k.vcf.gz", "--double-id", "--allow-extra-chr", "--cluster", "--mds-plot", 4 , "--out", prefix_mds)

  -> plink not found, need to load it first
  
$ module sypder plink
Tools/PLINK/1.9beta6.21

  -> add this as a command to R file:
  
$ module load Tools/PLINK/1.9beta6.21
  
Still doesn't work (also module is not recognized)
  
Loading module in CLI and then running the test-Vcf -> works

$ Rscript test-Vcf.R

#### Try ade4 and pcaExplorer (shiny) for extension of PCA functionality

I haven't figured out how to use ade4, as I need a dataframe first

Ideas for shiny:
* input$shape; input$color for shaping/colouring -> reactive()

  
Worked on the interactive PCA plot.
  
What is working so far:
  * Pick variable for color/shape 
  * Toggle to show/hide sample id's
  * Pick which PCs to plot on y- and x-axis
  * Variance explained of chosen PC shown in axis labels
  
What is not working so far / what I would still like to do :
  * Do a separate PCA / DimensionalityReduction App
  * Option for not picking a variable for color/shape
  
<img width="670" alt="pca_shiny_1" src="https://user-images.githubusercontent.com/71451797/207640236-01ba1da4-d417-49d5-b984-024cfd947a4b.png">

# 15.12.2022 - Create PCA/MDS app
  
Create separate app:
  * ~/git/ezRun/inst/templates/PCA_MDS.Rmd
  * ~/git/ezRun/R/app-PCAMDS.R
  * /srv/GT/analysis/jonas/jonas_test_sushi_20221115/master/lib/PCAMDSApp.rb 
  
PCAMDS.Rmd:
  *
 
app-PCAMDS.R:
  * grouping_vars -> get input from column "Grouping File" -> need to put that as input in .rb
  
PCAMDSApp.rb:
  * what is EzAppVcfStats? -> function in the R file
  
  
Test PCAMDS app:
  * run on sushi (bundle5000)
  * copy job script to ~/git/scripts
  * run with "bash some_script.sh"
  * Add column "Grouping File", and path to populations.txt as value of this column
  
```
$ ssh jobucher@fgcz-genomics.uzh.ch 
$ rinst
$ exit
$ mast
$ bundle5000
```
  
Why is the job script called "Variants_vcf_test_dataset.sh" 
job scripts: ["/scratch/PCAMDS_test_2_2022-12-15--15-23-50/scripts/Variants_vcf_test_dataset.sh"]
-> I think Variants is the type of analysis (see ruby file) and vcf_test_dataset is the dataset used.  
  
# 15.12.2022 - Fix PCA/MDS app
  
Fehler in ezSystem(cmd) : 
  vcf-stats /srv/gstore/projects/p1535/test_vcf_dataset/ragi_highcov_sa0001_1k.vcf.gz -p pca_mds/vcf_stats 
 failed
Ruft auf: <Anonymous> -> withCallingHandlers -> runMethod -> ezSystem

 
EzAppVcfStats$new()$run(input = input, output = output, param = param)
withCallingHandlers({
    if (param$process_mode == "SAMPLE") {
        if (input$getLength() > 1) {
            stop("process mode is SAMPLE but the input contains more than one sample.")
        }
    }
  
Things I change(d)/updates:
  * in bash job script: change line 41 from EzAppVcfStats to EzAppPCAMDS
  -> Error: object 'EzAppPCAMDS' not found
  * copy .R and .Rmd file from git/ezRun to srv/GT/analysis/jonas/ezRun, git commit and push, then install and run on SUSHI
  -> In file(filename, "wb") :
  kann Datei 'pca_mds/snp.gds' nicht öffnen: Datei oder Verzeichnis nicht gefunden
  * next: check ruby file/R file (prefixes) for inconsistencies
  * When I submit a job, it doesn't show in jobs -> now it does, but fails
  * Cannot get onto server anymore (ssh 47) -> works again now
  * Fehler in read.vcfR(file.path("/srv/gstore/projects", input$getColumn("Filtered VCF"))) : 
  konnte Funktion "read.vcfR" nicht finden -> need to load this library
  * Error: Failed to open mds/mds.log.  Try changing the --out parameter.
Fehler in ezSystem(cmd) : 
  plink --vcf /srv/gstore/projects/p1535/test_vcf_dataset/ragi_highcov_sa0001_1k.vcf.gz --double-id --allow-extra-chr --cluster --mds-plot 4 --out mds/mds 
 failed
  * Try without --out parameter altogether
  * Fehler in abs_path(input) : The file 'PCAMDS.Rmd' does not exist.
  * maybe I have to use something like "makeRmdReport" like in ScSeurat; check out other apps to compare
  * Found that in "styleFiles" there was VcfStats.Rmd instead of PCAMDS.Rmd -> changed it
  * Fehler in file.copy(from = html_files, to = "mds") : 
  mehr 'from' Dateien als 'to' Dateien
  * I probably need the prefix
  * Trying to run as bash script worked (plink ran), but there was a different error:
  MDS solution written to plink.mds .
Error: pandoc version 1.12.3 or higher is required and was not found (see the help page ?rmarkdown::pandoc_available).
  * Check pandoc version (rmarkdown::pandoc_version() -> ‘2.19.2’)
  * Try to create directory:
    dir.create(param[['name']])
    file.copy(from = html_files, to = param[['name']])
    cmd <- paste('mv rmarkdownLib', param[['name']])
  * mv: der Aufruf von stat für 'rmarkdownLib' ist nicht möglich: Datei oder Verzeichnis nicht gefunden
  * To Masa: different setup/use different app as a guide (instead of VcfStats)? 
           
# 19.12.2022 - Fix PCA/MDS app - continued
           
Things I change(d)/updates:
  *   cwd <- getwd()
  setwdNew(basename(output$getColumn("Report")))
  on.exit(setwd(cwd), add = TRUE)
  * kann Datei 'pca_mds/plink.mds' nicht öffnen: Datei oder Verzeichnis nicht 
  * with prefix output_dir/plink:
  Error: Failed to open pca_mds/plink.log.  Try changing the --out parameter.
Fehler in ezSystem(cmd) : 
  plink --vcf /srv/gstore/projects/p1535/test_vcf_dataset/ragi_highcov_sa0001_1k.vcf.gz --double-id --allow-extra-chr --cluster --mds-plot 4 --out pca_mds/plink 
 failed
  * Quitting from lines 39-324 (PCAMDS.Rmd) 
Fehler in data.frame(pca$eigenvect, grouping_vars, None = "", stringsAsFactors = FALSE) : 
  Argumente implizieren unterschiedliche Anzahl Zeilen: 0, 1
  * How to load the grouping file (grouping_vars)? Right now it is just a string of the link to the populations file. 
  
Copy stuff from downloads: scp PCA.rds jobucher@fgcz-c-047.uzh.ch:~/sushi_project_JB/data/
  
  
/srv/GT/analysis/jonas/ezRun/inst/templates$ cp ~/git/ezRun/inst/templates/PCAMDS.Rmd .
  
Copying Rstudio files to ezRun:
  * /srv/GT/analysis/jonas/ezRun/inst/templates$ scp ~/git/ezRun/inst/templates/PCAMDS.Rmd .
  * /srv/GT/analysis/jonas/ezRun/R$ scp ~/git/ezRun/R/app-PCAMDS.R .
  
  
TODO next (tomorrow): adapt Rmd code to dudi.pca; check how to keep more dimensions
  
# 20.12.2022 - Refine PCA/MDS app; start new app or do more dimensionality reduction
  
### Updates
  * Added nf=5 to dudi.pca (keep 5 axes)
  * Adapted the Rmd to fit dudi.pca
  * Fehler in read.table(file = file, header = header, sep = sep, quote = quote,  : 
Objekt 'plink.mds' nicht gefunden
  * Try without the MDS part; find out where plink.mds is stored
  * changed the mds read/import -> had forgotten the quotes
  * Job completed, but: Shiny applications not supported in static R Markdown documents; also printed out sessionInfo (and Input Dataset)
  * I have to pick either Rmd or shiny; cannot mix (or doesn't make sense) 
  * -> idea: make a static (Rmd) & an interactive (shiny) output (both as [Link] in SUSHI)
  * Use {.tabset} to create tabs, 1 level above tabs/headers:
    ```
    ## PCAMDS_Result {.tabset}
    ### PCA
    ### MDS
    ```
    
  
Find output from failed jobs:
  * open .log file
  * 2nd line, look on which server it ran
  * ssh trxcopy@fgcz-...
  * cd (scratch/PCAMDS_jonas_test6_2022-12-20--11-06-10_temp20810/ (for example)

  
Questions:
  * How to do a combined shiny/"normal" app? (with different tabs)
    -> Doesn't work, has to be separate
  * (How) Can I combine ggplotly and shiny?
    -> Probably doesn't make sense
  * How to create 2 different tabs in html from Rmarkdown? (now it puts them on same page)
  * How many dimensions for MDS?
  
TODO:
  * change Rmd -> done
  * get access to gitlab; upload my shiny app there
  * link app to the shiny location (in gitlab)
  * Note: ScSeurat has the links to the interactive part in the bottom, maybe I can also do that?
  
  
# 21.12.2022 - t-SNE and UMAP; maybe continue with shiny
  
  ### General Updates
  * I will not be able to access gitlab, only for employees
  
  ### General Questions
  * How do I test my shiny app (because I cannot get access to the fgcz gitlab)?
  * Do the modules added/loaded always have to be manually updated when there is a new version?
  * (How) Can I access plink output within same file?
  


  
### t-SNE (t-distributed stochastic neighbor embedding)
    
  * Use Rtsne (already in libraries)
  * How to get/estimate a good perplexity value
  * 3 * perplexity < nrow(X) - 1 (so for 42: perplexity < 14)
  * How to normalize vcf data / convert to matrix instead of distance matrix (because for t-SNE the data should be normalized if input is distance matrix)
  * Try t-SNE on server with maximum possible perplexity (and MDS dims increased to 5, to match PCA dims kept)
  * -> works
  * maybe more steps (max_iter = 5000)
  * comment: density/size of clusters and distance between clusters might not mean anything (see: https://distill.pub/2016/misread-tsne/)
  
  
  

#### UMAP (Uniform Manifold Approximation and Projection)
  
  
### Shiny App
  #### General notes for help (and exploreDEG notes) 
  * exploreDEG as template
  * images in www folder
  * withProgress reports progress (only makes sense for long computations probably)
  * observe = similar to reactive
  * reactive expressions = calculated values
  * observers = side-effect-causing actions
  * Use eventReactive to create a calculated value that only updates in response to an event
  * Use observeEvent whenever you want to perform an action in response to an event. (Note that "recalculate a value" does not generally count as performing an action)
  
  
Structure of a tab (ui): fluidRow > column > box
```
fluidRow(
    column(
        width = 3,
        box(
            title = "xxx",
            width = NULL,
            solidHeader = TRUE,
            status = "primary",
            collapsible = TRUE,
            collapsed = TRUE,
            tag$.("text"),
            selectInput(...)
        ),
        box(...
        )
    ),
    column(...)
  
```

  
  
  
  #### Updates
  * create app.R that calls all ui and server files (done)
  * split PCA into two files (ui, server)
  
  * for t-SNE: give option to change complexity (maybe something like [min_complexity=X, min+(max-min)*0.25, min+(max-min)*0.5, min+(max-min)*0.75, max_complexity=floor((nrow(dist_matrix)/3) - 1)]
  
  
  
  * See if it runs normally (on server), otherwise ask on Asana for guidance
  

  


