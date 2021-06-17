# Day 3

## Runing a Seurat tutorial

Please log into https://vlab.humboldt.edu and create a working directory `day_3`.

You first need to download the data to `day_3` from the tuorial found here https://satijalab.org/seurat/articles/pbmc3k_tutorial.html

Install the following packages and follow the tutorial

***NOTE*** when the Rstudio asks "Do you eant to install from sources the pakge which needs compilation?" answer "NO"

```
install.packages("dplyr")
install.packages("Seurat")
install.packages("patchwork")


library(dplyr)
library(Seurat)
library(patchwork)
```

Once your libraries are working, move the data to the working directory and `setwd()` appropiately
