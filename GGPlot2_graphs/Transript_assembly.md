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
# Install packages if not already installed
# install.packages("ggplot2")
# install.packages("Biostrings")
```{cmd}
library(Biostrings)
library(ggplot2)
```
# Read transcript assembly fasta file
```{cmd}
fasta <- readDNAStringSet("Transcript_assembly.fasta")
```
# Calculate transcript lengths
```{cmd}
transcript_lengths <- width(fasta)
```
# Define length bins
```{cmd}
bins <- c(0, 200, 300, 400, 500, 600, 700, 800, 900,
          1000, 5000, 10000, 15000, 20000, Inf)

labels <- c(
  "Length < 200",
  "Length >= 200 & <= 300",
  "Length > 300 & <= 400",
  "Length > 400 & <= 500",
  "Length > 500 & <= 600",
  "Length > 600 & <= 700",
  "Length > 700 & <= 800",
  "Length > 800 & <= 900",
  "Length > 900 & <= 1000",
  "Length > 1000 & <= 5000",
  "Length > 5000 & <= 10000",
  "Length > 10000 & <= 15000",
  "Length > 15000 & <= 20000",
  "Length > 20000"
)
```
# Categorize transcript lengths
```{cmd}
length_category <- cut(
  transcript_lengths,
  breaks = bins,
  labels = labels,
  include.lowest = TRUE,
  right = TRUE
)
```
# Count transcripts in each category
```{cmd}
df <- as.data.frame(table(length_category))
colnames(df) <- c("Length_Range", "Count")
```
# Plot
```{cmd}
# Create gradient colors for bars
bar_colors <- colorRampPalette(
  c("#08306B", "#238B45", "#A1D99B")
)(nrow(df))

# Plot
p <- ggplot(df, aes(x = Length_Range, y = Count)) +
  geom_bar(
    stat = "identity",
    fill = bar_colors,
    color = "black",
    linewidth = 0.4
  ) +
  labs(
    title = "Assembly Length Distribution",
    x = "Transcript Assembly",
    y = "No of Transcripts"
  ) +
  theme_bw(base_size = 14) +
  theme(
    plot.title = element_text(
      hjust = 0.5,
      face = "bold",
      size = 20
    ),
    axis.title = element_text(
      face = "bold",
      size = 20,
      color = "black"
    ),
    axis.text.x = element_text(
      angle = 45,
      hjust = 1,
      face = "bold",
      color = "black",
      size = 14
    ),
    axis.text.y = element_text(
      face = "bold",
      color = "black",
      size = 15
    ),
  panel.grid.major = element_blank(),
  panel.grid.minor = element_blank(),
  legend.position = "none"
  )
print(p)
```
# Save high-resolution figure
```{cmd}
ggsave(
  "Assembly_Length_Distribution.png",
  plot = p,
  width = 12,
  height = 7,
  dpi = 600
)
```
