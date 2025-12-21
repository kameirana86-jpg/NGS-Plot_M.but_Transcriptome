# Plot KOG Vertical Bar Chart
- Using ggplot
- First install GGPLOT
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

# read
```{cmd}
df <- read.csv("KOG_M_but.csv", check.names = FALSE, stringsAsFactors = FALSE)
df$`CDS Count` <- as.numeric(df$`CDS Count`)
```
# ensure full A->Z (missing letters get CDS Count = 0)
```{cmd}
alpha <- toupper(LETTERS)
df2 <- df %>%
  mutate(`KOG Function Class` = as.character(`KOG Function Class`)) %>%
  complete(`KOG Function Class` = alpha,
           fill = list(`CDS Count` = 0, `KOG class description` = NA)) %>%
  mutate(`KOG Function Class` = factor(`KOG Function Class`, levels = alpha)
  )
  ```
# create a wrapped label for legend entries so each legend item is multi-line
```{cmd}
df2 <- df2 %>%
  mutate(short_desc = ifelse(is.na(`KOG class description`), "NA",
                             str_wrap(`KOG class description`, width = 36))
)
```

# prepare a distinct palette (adapts to number of unique legend items)
```{cmd}
n_legs <- n_distinct(df2$short_desc)
base_cols <- brewer.pal(min(12, n_legs), "Paired")                 
palette_cols <- colorRampPalette(base_cols)(n_legs)               
names(palette_cols) <- levels(factor(df2$short_desc)
)
```
# plotting
```{cmd}
maxy <- max(df2$`CDS Count`, na.rm = TRUE)
pad <- maxy * 0.08   # add some headroom for text labels

p <- ggplot(df2, aes(x = `KOG Function Class`, y = `CDS Count`, fill = short_desc)) +
  geom_col(width = 0.78, colour = "black", size = 0.25) +
  geom_text(aes(label = ifelse(`CDS Count` == 0, "", `CDS Count`)),
            vjust = -0.4, size = 3.2, fontface = "bold") +
  scale_y_continuous(expand = c(0, 0), limits = c(0, maxy + pad)) +
  scale_fill_manual(values = palette_cols, na.value = "grey60") +
  labs(title = "KOG function categories (A -> Z)",
       x = "KOG Function Class",
       y = "CDS count",
       fill = "KOG class description") +
  theme_minimal(base_size = 13) +
  theme(
    # grid & panel
    panel.grid.major.x = element_blank(),
    panel.grid.minor = element_blank(),
    panel.grid.major.y = element_line(color = "grey92"),
    # axes & text
    axis.text.x = element_text(size = 12, vjust = 0.5),
    axis.title = element_text(face = "bold"),
    plot.title = element_text(hjust = 0.5, size = 16, face = "bold"),
    # legend: single tall column on right
    legend.position = "right",
    legend.direction = "vertical",
    legend.key.size = unit(0.45, "cm"),
    legend.key.height = unit(0.9, "cm"),
    legend.spacing.y = unit(0.2, "cm"),
    legend.text = element_text(size = 10),
    legend.title = element_text(size = 11, face = "bold")
  ) +
  guides(fill = guide_legend(ncol = 1, byrow = FALSE, override.aes = list(size = 5)))
```
# draw
```{cmd}
print(p)
```
# Fixing Legend Position and Plot Margins Using `grid`
```{cmd}
> library(grid)

p_fixed <- p +
  theme(
    legend.position = "right",
    legend.box.just = "left",

    # VERY IMPORTANT: increase right margin
    plot.margin = margin(t = 4, r = 50, b = 4, l = 4),

    legend.text  = element_text(size = 10),
    legend.title = element_text(size = 11),
    legend.key.height = unit(0.9, "cm")
  )
  ```
# save wide so legend remains a single column and resembles your PNG

```{cmd}
ggsave("KOG_barplot_style_A_to_Z.pdf", p, width = 16, height = 7, device = "pdf")
```
# Put legend OUTSIDE the plot + increase right margin
# This mimics the “maximized” behavior like observed in Image viewer.
```{cmd}
> library(grid)
p_fixed <- p +
  theme(
    legend.position = "right",
    legend.box.just = "left",

    # VERY IMPORTANT: increase right margin
    plot.margin = margin(t = 4, r = 50, b = 4, l = 4),

    legend.text  = element_text(size = 10),
    legend.title = element_text(size = 11),
    legend.key.height = unit(0.9, "cm")
  )
  ```
# Save with LARGE width (this is the key)
  - This is what actually prevents clipping.
# PNG (publication safe)
```{cmd}
ggsave(
  filename = "KOG_barplot.png",
  plot     = p_fixed,
  width    = 18,     # ← MUST be large
  height   = 10,
  units    = "in",
  dpi      = 300,
  limitsize = FALSE)
  ```

# Create PDF (BEST for manuscripts)
```{cmd}
ggsave(
  filename = "KOG_barplot.pdf",
  plot     = p_fixed,
  width    = 18,
  height   = 10,
  units    = "in",
  device   = cairo_pdf,
  limitsize = FALSE
)
```
# Use a vector SVG device. DPI is ignored for vector output, so you can drop it. Example ready for publication:

# Preferred (better font/text rendering)
```{cmd}
ggsave(
  filename  = "KOG_barplot.svg",
  plot      = p_fixed,
  device    = svg,   # use cairo_svg for reliable text rendering / font embedding
  width     = 18,
  height    = 10,
  units     = "in",
  limitsize = FALSE,
  bg        = "white"      # or "transparent" if you prefer
)
```

