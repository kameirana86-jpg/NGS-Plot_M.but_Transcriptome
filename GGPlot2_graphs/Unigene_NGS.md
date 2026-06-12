# To activate Conda environment
```{cmd}
conda activate r4.5
```
#To activate R environment
```{cmd}
R
```
# To check directory
```{cmd}
getwd()
```
# To set change directory
```{cmd}
setwd("/home/rana/M.but_Transcriptome")
```
# Install if needed:
# install.packages(c("ggplot2", "dplyr", "stringr"))
# BiocManager::install("Biostrings")
```{cmd}
library(Biostrings)
library(ggplot2)
library(dplyr)
library(stringr)
```

# Read FASTA as a DNAStringSet
```{cmd}
fasta_file <- "Unigenes_NGS.fasta"
unigene_set <- readDNAStringSet(fasta_file)
```
# Get sequence lengths
```{cmd}
seq_len <- width(unigene_set)
```
# Put lengths into bins like your plot
```{cmd}
df <- data.frame(length = seq_len) %>%
  mutate(
    Length_Range = cut(
      length,
      breaks = c(0, 200, 300, 400, 500, 600, 700, 800, 900, 1000,
                 5000, 10000, 15000, 20000, Inf),
      labels = c(
        "Length < 200",
        "Length >= 200 && <= 300",
        "Length > 300 && <= 400",
        "Length > 400 && <= 500",
        "Length > 500 && <= 600",
        "Length > 600 && <= 700",
        "Length > 700 && <= 800",
        "Length > 800 && <= 900",
        "Length > 900 && <= 1000",
        "Length > 1000 && <= 5000",
        "Length > 5000 && <= 10000",
        "Length > 10000 && <= 15000",
        "Length > 15000 && <= 20000",
        "Length > 20000"
      ),
      right = TRUE,
      include.lowest = TRUE
    )
  )
```
# Count sequences per bin
```{cmd}
plot_df <- df %>%
  count(Length_Range) %>%
  mutate(Length_Range = factor(Length_Range, levels = levels(df$Length_Range)))
```
# Create color index for left-to-right gradient
```{cmd}
plot_df$ColorIndex <- seq_len(nrow(plot_df))
```
# Plot
p <- ggplot(plot_df,
            aes(x = Length_Range,
                y = n,
                fill = ColorIndex)) +
  
  geom_bar(stat = "identity", color = "black", linewidth = 0.3) +
  # Blue → Light Green gradient
  scale_fill_gradient(low = "#2C7FB8",
    high = "#A1D99B") +
  # Remove legend
  guides(fill = "none") +  
  # Make axes start at zero
  scale_y_continuous(expand = c(0, 0)) +
  scale_x_discrete(expand = c(0, 0)) +
  labs(title = "Assembly Length Distribution",
  x = "Unigene Assembly Length (bp)",
  y = "No. of Unigenes") +
  theme_classic(base_size = 12) +
  theme(plot.title = element_text(hjust = 0.5, face = "bold", size = 16),
  axis.title = element_text(face = "bold", size = 16, color = "black"),
  axis.text = element_text(size = 14, color = "black", face = "bold"),
  axis.text.x = element_text(angle = 45, hjust = 1),
  axis.line = element_line(color = "black", linewidth = 1),
  axis.ticks = element_line(color = "black", linewidth = 0.8)
  )

# Display plot
```{cmd}
print(p)
```
# Save plot
```{cmd}
ggsave("unigene_length_distribution.png", p, width = 12, height = 6, dpi = 300)
```
