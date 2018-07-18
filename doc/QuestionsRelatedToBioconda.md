# Questions found on support.bioconductor.org / biostars and Bioc-devel.

https://stat.ethz.ch/pipermail/bioc-devel/2017-February/010503.html

https://www.biostars.org/p/257202/

https://stat.ethz.ch/pipermail/bioc-devel/2017-January/010367.html

https://stat.ethz.ch/pipermail/bioc-devel/2017-February/010503.html


## Code used to find questions

First download all years of archives (takes approx 30 sec to run)

```
library(lubridate)
library(R.utils)

years <- c(rep("2014", 12),
           rep("2015", 12),
           rep("2016", 12),
           rep("2017", 12), 
           rep("2018", 8))

months <- levels(month(ymd("20170101"), label=TRUE, abbr=FALSE))

files <- paste0("https://stat.ethz.ch/pipermail/bioc-devel/",
                years,"-", months, ".txt.gz")


lapply(files, function(x) {
    download.file(x, destfile=basename(x))
    gunzip(basename(x))
})

```

Once you have them stored, 

```
grep -C 2 "conda" *.txt
```
