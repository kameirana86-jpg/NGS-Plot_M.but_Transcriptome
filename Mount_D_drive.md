# Step 1: Check whether the D: drive is mounted
``` {cmd}
ls /mnt
```

# You should see something like: c  d

# If you see d, then run:
```{cmd}
cd /mnt/d/D/NGS_RANA/01_Raw_Data
```
# Then:
```{cmd}
ls
```
# Check the FASTQ files, Run:
```{cmd}
ls *.fastq*
```
# You should see something like: 
Meizotropisn-buteiformis-seeds_S97_L008_R1_001.fastq
Meizotropisn-buteiformis-seeds_S97_L008_R2_001.fastq

# If fastp is installed, run this command
Use the compressed (.fastq.gz) files: This may take several minutes depending on the file size.
```{cmd}
fastp \
-i Meizotropisn-buteiformis-seeds_S97_L008_R1_001.fastq.gz \
-I Meizotropisn-buteiformis-seeds_S97_L008_R2_001.fastq.gz \
-o clean_R1.fastq.gz \
-O clean_R2.fastq.gz \
-h fastp.html \
-j fastp.json
```
# If your files are not compressed (.fastq), use:
```{cmd} fastp \
-i Meizotropisn-buteiformis-seeds_S97_L008_R1_001.fastq \
-I Meizotropisn-buteiformis-seeds_S97_L008_R2_001.fastq \
-o clean_R1.fastq.gz \
-O clean_R2.fastq.gz \
-h fastp.html \
-j fastp.json
```
# Open the report
After the analysis finishes, list the files:
```{cmd}
ls
```
# You should now see:
clean_R1.fastq.gz
clean_R2.fastq.gz
fastp.html
fastp.json

# To open the HTML report in your Windows browser from WSL, run:
```{cmd}
explorer.exe fastp.html
```
