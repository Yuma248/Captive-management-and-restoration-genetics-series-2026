## BIOL3722_2026 Conservation and Ecological Genetics Lab

A tutorial covering VCF quality control, diversity statistics,
relatedness/kinship estimation, and breeding-group selection with `swingeR`.

## Repository layout

```
Captive-management-and-restoration-genetics-series-2026/
├── README.md                      # Overview of the practicals and repository contents
├── data/                          # Input datasets used throughout the practicals
│   ├── Info                       # Description of the datasets and file contents
│   ├── SPBRmeta.txt               # Sample metadata for 50 Southern Pygmy Perch individuals (e.g. sex)
│   ├── SPBR_50_6328.gds           # GDS file containing genotypes for 50 individuals at 6,328 SNPs
│   └── SPBR_50_6328.vcf.gz        # Compressed VCF file containing genotypes for 50 individuals at 6,328 SNPs
├── docs/                          # Rendered HTML tutorials and supporting course materials
└── swingeR_0.4.0.tar.gz           # Source for the swingeR R package
```


## Data files

ddRAD data for the SPP breeding program in VCF format, and metadata including sex information for each sample. All in the folder data/

##SwingeR package
To install swingeR, in R run the following commands:

``` {r}
install.packages("remotes")
remotes::install_github("Yuma248/swingeR")
```


