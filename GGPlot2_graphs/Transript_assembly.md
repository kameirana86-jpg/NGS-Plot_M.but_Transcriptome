#To activate Conda environment
```{cmd}
conda activate r4.5
```
#To activate R environment
```{cmd}
R
```
# packages required to be installed
```{cmd}
if (!requireNamespace("tidyverse", quietly = TRUE)) install.packages("tidyverse")
if (!requireNamespace("RColorBrewer", quietly = TRUE)) install.packages("RColorBrewer")
library(tidyverse)
library(RColorBrewer)
library(stringr)
library(grid)
library(ggplot2)
```
