# shinySRT-guide

## Tutorials for creating ShinyCell interactive applications. Guidance on installing and using shinySRT
Here, we provide some tutorials and guidance on shinySRT:
### shinySRT package
ShinySRT is a web application developed utilizing the Shiny framework, explicitly designed for the sharable and interactive visualization of spatially resolved transcriptomics data. This application is adept at processing various formats of spatial transcriptome data, facilitating the development of an interactive interface conducive to thorough data analysis. The interactive interface of shinySRT is open-source, offering significant customization potential to align with the unique requirements of users.

Key features of shinySRT include:
-	Developed under R, utilizing a Shiny application that generates an interactive and shareable interface via the web. 
-	Provides compatibility with various prominent formats of spatial transcriptome data. 
-	Facilitates the import of multiple ST datasets into the Shiny web application. 
-	Allows for the customization of spatial spot selection. 
-	Supports multivariate comparisons, enabling the analysis of how the dependent variable changes across different groups of independent variables. 
-	Offers features for visualizing images and downloading data sheets. 
-	Simplifies the creation of the Shiny interface in a single step, and the Shiny app is entirely open source and customizable.

This file is broken down into the following section:
-	How to install shinySRT 
-	Quick Start Guide 
-	Functional Guidence. 

### How to install shinySRT
To begin, it’s important to verify whether the necessary installation packages for shinySRT have already been installed (The best version of R is 4.2 or above, to prevent some R packages are not good to install or incompatibility). ShinySRT can then be installed from GitHub as follows:

``` r
# If you employ Conda, it is imperative to install the essential packages requisite for the analysis of single-cell and spatial genomic data.
# mamba install conda-forge::r-cairo conda-forge::r-hdf5r conda-forge::r-curl conda-forge::r-devtools conda-forge::r-biocmanager conda-forge::r-rfast conda-forge::quadprog -y

if (!require('pacman')) install.packages('pacman')

BiocManager::install(c('scran','Seurat'))

devtools::install_github(c('YuLab-SMU/ggtree', 'silhouette99/shinySRT'))

pacman::p_load(
  'Cairo',
  'hdf5r',
  'data.table',
  'magrittr',
  'dplyr',
  'glue',
  'readr',
  'reticulate',
  'ggplot2',
  'graphics',
  'gridExtra',
  'patchwork',
  'RColorBrewer',
  'maps',
  'grid',
  'ggtree',
  'ggrepel',
  'ggdendro',
  'Matrix',
  'scales',
  'aplot',
  'keys',
  'ggiraph',
  'ggpubr',
  'shiny',
  'shinyhelper',
  'DT',
  'shinydashboard',
  'scran',
  'scatterpie',
  'quadprog',
  'Rfast',
  'SingleCellExperiment',
  'SpatialExperiment'
)

```

### Quick Start Guide
In summary, the shinySRT software package imports SRT objects and exports the required data, including expression matrices, metadata, spatial coordinates, etc., in a more compact standard format. shinySRT generates code based on the samples, all of which are necessary for the shiny app.
In the following example, we use publicly available SRT data provided by the 10x Genomics website, which is also utilized in the Seurat spatial transcriptomics data analysis tutorial.
A shiny app for spatial transcriptomics can be quickly generated following these steps:

``` r
library(Seurat)
library(SeuratData)
library(shinySRT)

InstallData("stxBrain")
brain <- LoadData("stxBrain", type = "anterior1")
brain2 <- LoadData("stxBrain", type = "anterior2")

dat <- merge(brain,brain2)

## Shinyapp will be create in 'example'
dir.create('example')
setwd('example')

CreateshinySRT(brain,title = 'shinySRT exmaple', sp_normalize = F, gene.mapping = F)

# run shiny app 
shiny::runApp('shinyspatial_app/')

```

The generated shiny app can be found in the directory ‘shinyspatial_app’ under the current directory. To locally run the shiny app, you can use the command shiny::runApp('shinyspatial_app/'). Additionally, you can deploy it on online platforms such as shinyapps.io or host it on a server.

![](plot1s.png)


The visualization interface generated by ShinySRT mainly consists of five sections, demonstrating the primary spatial transcriptomics data statistics and visualizations. The image below shows the standard interface of ShinySRT, featuring a sidebar on the left. This sidebar includes sections for toggling between different modules (highlighted in orange), as well as an input area for importing new spatial annotation information into the metadata of the interface (highlighted in red). As shown in the above figure, the first module of the interface is used to display the relationship between the spatial annotation information of spots and gene expression. By adjusting the "select group" and "gene names" options, you can display this information in the spatial plot below. The upper spatial plot with discontinuous colors shows the annotation information of the spots, with each color representing a different group. The lower spatial plot with gradient colors shows the gene expression levels, with color intensity reflecting the expression strength. The spatial plots are interactive, allowing for the selection of specific regions for further localized studies. The "Statistical table" shows the gene expression levels in the spots. The "sub" column in the table can be changed through the "select group" option. In the example, the "select group" option is set to "seurat_clusters," so the spatial plot below shows the spatial distribution of the seurat_clusters groups, and the statistical table shows the gene expression levels in these groups. The 'nCells' column indicates the number of spots in each group, the "nExpress_(gene name)" column indicates the number of spots in each group where gene expression exceeds the threshold (threshold = max(gene expression) * Gene expression threshold), the "Mean" column indicates the average gene expression level in each group, and "pctExpress" = "nExpress_(gene name)"/"nCells".

![](plot2s.png)

The second module focuses on the relationship between genes, specifically the statistical and visual representation of the co-expression of two genes. In this module, there are spatial plots that display metadata grouping information as well as gene co-expression plots, with color gradients reflecting the levels of co-expression. This module allows for adjusting the expression thresholds of two genes to determine if they are co-localized in spots. At the top, the spatial plot shows annotation information, while the spatial plot at the bottom displays the co-expression of the two genes. In the legend, red represents the expression of gene 1, green represents the expression of gene 2, and yellow indicates co-expression. The "Gene1 expression threshold" and "Gene2 expression threshold" functions are similar to the "Gene expression threshold" in the first module. The interface table shows the co-localization of the two genes (Ttr > 0: the number of spots where Ttr expression is greater than 0 and Hbb-bs = 0; Ptgds > 0: the number of spots where Hbb-bs expression is greater than 0 and Ttr = 0; Both: the number of spots where both Ttr and Hbb-bs expressions are greater than 0; None: the number of spots where both Ttr and Hbb-bs expressions are equal to 0). The violin plot reflects the co-expression scores of the two genes, with the X-axis representing groups that can be adjusted using the "Add group" function, and the Y-axis showing the co-expression scores (√(gene1*gene2)). The violin plot can also display localized gene co-expression by selecting spots on the spatial plot.

![](plot3s.png)

The third module visualizes the statistical results of gene expression. It utilizes vlnplot and boxplot to display the statistical results, with the x-axis representing different annotation information. In this module, the spatial plot at the bottom still displays annotation information and is primarily used for local selection and analysis within the current module. The violin plot or box plot is located above, with the X-axis representing different annotations and the Y-axis representing gene expression. The menu on the left side of the violin plot allows for changing the X and Y axes and modifying the type of plot displayed. In addition to changing the meanings of the X and Y axes, a third variable can be added for multi-factor comparison using the "group by" function. For example, we can observe gene expression in each subgroup and understand differences between samples. In this case, by selecting "slice_sample" in the "group by" option, we can explore differences between subgroups within each sample while also examining differences across samples.

![](plot4s.png)


The fourth module provides a bar chart to visualize the proportional statistics of annotation information across different groups. This module's structure is similar to the third module.

![](plot5s.png)


The fifth module displays gene expression patterns in different regions through a “bubble chart or heatmap.” It also allows for clustering along both rows and columns. The heat map and dot plot are similar to the statistical plots in previous modules, exploring differences between subgroups while also investigating differences among subgroups within each sample. "Group by" is used to determine the x-axis of the dot plot/heatmap. "Second Group by" can add a second variable. For example, in the dot plot shown here, each cluster's gene expression is displayed. When we select "slice_sample" in "Second Group by", the dot plot will display the gene expression of each cluster in different samples.

Based on the interface description above, it is evident that shinySRT is a comprehensive tool for visualizing SRT (Spatial Transcriptomics) data. The shinySRT interface supports the simultaneous display of multiple ST (Spatial Transcriptomics) samples and facilitates cross-sample comparisons. This functionality is crucial for both data analysis and visualization, providing significant biological insights. The ability to showcase multiple samples and perform cross-sample comparisons is particularly important in understanding biological variations and responses.
To illustrate this, we use previously mentioned public datasets (S2 File) that include three samples: brain slices from mice in a resting state shortly after Brucella infection, brain slices from mice at the central nervous system disease onset stage (25 days post-infection), and brain slices from mice at the stage of neurological symptom manifestation (45 days post-infection). These samples are used to investigate the transcriptional and functional responses of the brain to chronic Brucella infection. The display of multiple samples and the ability to compare across these samples allow researchers to uncover differences in the brain's state at various stages of infection. This comparative approach can reveal how the brain's transcriptional landscape and cellular functions evolve from the initial infection through to the manifestation of disease symptoms, providing critical insights into the progression and impact of chronic Brucella infection. 
In the "SpotInfo vs GeneExpr" module, we examine the expression of genes across different slices in three samples, focusing on infection-related genes (Aif1, Adgre1, Arg1, and Chil3) and their correlation with infection status (illustrated in the top graph of the bottom image). The "Statistical table" can display the gene expression status of selected spots. In the shinySRT interface, which includes multiple samples, users can view cross-sample gene expression by selecting "slice_sample" in the "select group" section (highlighted in green below). This will update the "Statistical table" to show gene expression statuses for the two samples (highlighted in orange below). For localized comparison between two samples, such as examining infection-related gene expression in the cortex area, users can directly select the region on the spatial map. The "Statistical table" will then display the localized gene expression data accordingly (highlighted in red at the bottom).

![](sfig1-1.png)

In the "GeneExpr vs GeneExpr" module, the co-expression of two genes can be displayed. The image below shows the co-localization of Tb927.6.4280 and Ttr (Tb927.6.4280: a specific gene of Brucella, Ttr: a marker gene of circumventricular organs, with Brucella infection tending to localize to these organs). The statistical table (highlighted in orange) and the violin plot (Coexp Score = √(gene1*gene2)) can display the co-expression status of these two genes. By default, the "Add group" setting is set to "No" (highlighted in the green box at the top), and the chart shows the co-expression state of genes across multiple samples without distinguishing between them, resulting in a violin plot without sample or group differentiation. When the "Add group" option is switched to "slice_sample" (highlighted in the green box at the bottom), an additional column is added to the chart (highlighted in red), which distinguishes between different samples. The chart then displays the gene co-expression status for each sample separately and generates violin plots for different samples. As the infection progresses, it localizes to the circumventricular organs and stabilizes over time.

![](sfig2-1.png)

The "Viol/Box data chart" module allows switching "Spot information – x" to "slice_sample" (highlighted in the red box), enabling violin plots to display gene expression patterns across different samples and perform localized comparisons. Additionally, shinySRT can incorporate new variables for comparison (highlighted in the orange box). The functionality of the gene expression heatmap/dot plot module is similar to that of the aforementioned module. In summary, shinySRT not only displays multiple samples within the same interface but also facilitates cross-sample comparisons.

![](sfig3-1.png)

### Functional Guidence
In addition to its core functionalities, shinySRT also incorporates additional features for user convenience and usability. Here, we provide further details.
### add_meta
Users can add new metadata through the add_meta function. Once added, the new metadata options can be found in the dropdown menu.

``` r
library(blockrand)
setwd('example')
meta <- readRDS('meta.Rds')

## randomly generate a new metadata
new_meta <- blockrand(n=nrow(meta), num.levels = 5, levels = c("1cluster","2cluster",'3cluster','4cluster','5cluster'))

## add ‘treatment’ group into the meta.Rds and meta_group.Rds
add_meta(new_meta, dirs = "shinyspatial_app", colname = "treatment")
```

![](https://github.com/silhouette99/shinySRT/blob/master/image/content8.png)


![](https://github.com/silhouette99/shinySRT/blob/master/image/content9.png)

After using “add_meta,” new groups are added to both “meta.Rds” and “meta_group.Rds” respectively.

## scran_norm
In shinySRT, a method for scran data normalization has been added. The results of this normalization are utilized for visualizing unnormalized data, identifying highly variable genes (HVGs), and performing deconvolution. The scran data normalization method is also employed in Giotto for the normalization of spatial transcriptomics and single-cell data.

mtx <- matrix(sample(5000),nrow = 50,ncol = 100)
mtx <- scran_norm(mtx)

``` r
# scran_norm(
#   mymatrix,
#   log_offset = 1,
#   logbase = 2,
#   scalefactor = 6000,
#   library_size_norm = TRUE,
#   log_norm = TRUE
# )
```

scran_hvg
Highly Variable Genes (HVGs) applicable for identifying spatial transcriptomics and single-cell transcriptomics (used for deconvolution).

``` r
library(SeuratData)

InstallData("stxBrain")
brain <- LoadData("stxBrain", type = "anterior1")

raw_mtx <- GetAssayData(brain,slot = 'counts')
mtx <- scran_norm(raw_mtx)

hvg <- scran_hvg(mtx,brain@meta.data,colcluster = 'orig.ident')

# scran_hvg(
#   mtx,
#   meta,
#   colcluster,
#   method = "scran",
#   pval = 0.01,
#   logFC = 0.5,
#   min_feats = 3
# )
```

DWLS deconvolusion
shinySRT has incorporated the functionality of deconvolution, which utilizes DWLS (Doubly Weighted Least Squares) as employed in Giotto. Users can use the runDWLSDeconv function to obtain the distribution of cellular components for each spot in spatial transcriptomics data by integrating single-cell data.

```
## The files "brain_sc_expression_matrix.txt.gz" and "brain_sc_metadata.csv" originate from the example data used in Giotto.
mymatrix = data.table::fread('brain_sc_expression_matrix.txt.gz')
genes <- mymatrix$V1
mymatrix <- mymatrix[,-1] %>% as.matrix()
cells <- colnames(mymatrix)
mymatrix <- as(mymatrix, 'dgCMatrix')
rownames(mymatrix) <- genes
meta <- read.csv('brain_sc_metadata.csv')

mtx <- scran_norm(mymatrix = mymatrix)

## HVG for each cell type
varg <- scran_hvg(mtx = mtx,
                  meta = meta,
                  colcluster = 'Class')
sign_gene <- varg[, head(.SD, 10), by = "clusters"]

sign_matrix <-
  DWLSmatrix(
    matrix = mtx,
    sign_gene = sign_gene$genes,
    cell_cols = 'Class',
    meta = meta
  )

norm_mtx = GetAssayData(brain, slot = 'data')
cell_metadata = brain@meta.data
cluster_column = 'seurat_clusters'

runDWLSDeconv(
  norm_mtx = norm_mtx,
  cell_metadata = cell_metadata,
  cluster_column = 'seurat_clusters',
  sign_matrix = sign_matrix
)
```

Deconvolution can also be performed directly through the CreateshinySRT function in one step. Simply use the parameters scmtx and scmeta to add the expression matrix and metadata of the single-cell data. After running, a new “deconvolution.Rds” file will be added to the shinyspatial_app directory, and the code lines for the sixth module will be added to the code. The results of DWLS can be displayed on the interface.

![](content10.png)


ShinySRT is suitable for various types of SRT data. Below is a simple demonstration of generating interfaces using other data.
SingleCellExperiment (SCE) and SpatialExperiment (SPE) are the same S4 type of storage object, SCE is generally the storage object for single cell data, in the example data of spatialLIBD, we found that there is also the SCE object for spatial transcriptomes [SPE data](https://www.dropbox.com/s/f4wcvtdq428y73p/Human_DLPFC_Visium_processedData_sce_scran_spatialLIBD.Rdata?dl=1), the following is the example code:

``` r
library(spatialLIBD)

# spe <- fetch_data(type = "spe")
load(
  'Human_DLPFC_Visium_processedData_sce_scran_spatialLIBD.Rdata'
)

## 
sce2 <- sce[, which(sce$sample_name %in% c(151507, 151508))]
dat <- sce2
## Memory reduction
dat@colData <- dat@colData[,c("sample_name","tissue","imagerow","imagecol","Cluster","position","subject_position")]

dir.create('sce')
setwd('sce')

library(shinySRT)

CreateshinySRT(dat,title = 'single cell experiment',maxlevel = 20)
```

Spatial Experiment object is a S4 type of data storage, the following is for the use of SPE data, but it is worth mentioning that SpatialExperiment provides a very small number of sample data spots, the display is not good.The following also includes the use of 10x public data [Mouse Brain Serial Section 2 (Sagittal-Anterior)](https://www.10xgenomics.com/resources/datasets/mouse-brain-serial-section-2-sagittal-anterior-1-standard), read by SpatialExperiment to generate SPE objects:

``` r
## Spatial Experiment sample data
library(SpatialExperiment)
example(read10xVisium, echo = FALSE)


## 10x Visium load in SPE
spe2 <- SpatialExperiment::read10xVisium(
  samples = 'anterior/',
  sample_id = "anterior",
  type = "sparse",
  data = "filtered",
  images = "lowres",
  load = TRUE
)

library(shinySRT)

dir.create('spe')
setwd('spe')
CreateshinySRT(dat = spe2,title = 'spatial experiment',gex.assay = 'counts')
```

Spatial transcriptome single-cell precision is a development trend, vizgene is a kind of single-cell precision spatial transcriptome platform that has been commercially available, the good point is that the data can be processed by the seurat, the following is the example code, the sample data is the vizgene public platform data [vizgene](https://console.cloud.google.com/storage/browser/public-datasets-vizgen-merfish;tab=objects?prefix=&forceOnObjectsSortingFiltering=false)vizgene.

``` r
library(Seurat)
options(Seurat.object.assay.version = "v5")
library(future)
plan("multisession", workers = 10)
library(ggplot2)
library(shinySRT)

vizgen.obj <- LoadVizgen(data.dir = "vizgen/", fov = "vizgene")

vizgen.obj <- SCTransform(vizgen.obj, assay = "Vizgen", clip.range = c(-10, 10))
vizgen.obj <- RunPCA(vizgen.obj, npcs = 30, features = rownames(vizgen.obj))
vizgen.obj <- RunUMAP(vizgen.obj, dims = 1:30)
vizgen.obj <- FindNeighbors(vizgen.obj, reduction = "pca", dims = 1:30)
vizgen.obj <- FindClusters(vizgen.obj, resolution = 0.3)

dir.create('shinySRT')
setwd('shinySRT')

CreateshinySRT(vizgen.obj,title = 'Seurat vizgene')
```

h5ad is a common way of storing data in python, which can be called directly without memory, and scanpy is a very mature spatial transcriptome data processing tool in python. The following is still using 10x Visim public data [Mouse Brain Serial Section 2 (Sagittal-Anterior)](https://www.10xgenomics.com/resources/datasets/mouse-brain-serial-section-2-sagittal-anterior-1-standard), including the data h5ad transformation and ShinySRT example code:

``` python
import scanpy as sc
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
sc.logging.print_versions()
sc.set_figure_params(facecolor="white", figsize=(8, 8))
sc.settings.verbosity = 3
import os 
os.chdir('/mnt/raid62/pzz/shiny_data/other/') 
results_file = 'Anterior2.h5ad' 


adata = sc.read_visium("/mnt/raid62/pzz/shiny_data/seurat/tosce/outs2/posterior/")
adata.var_names_make_unique()
adata



adata.var["mt"] = adata.var_names.str.match("^MT-|^mt-") 
sc.pp.calculate_qc_metrics(adata, qc_vars=["mt"], inplace=True)
adata.var["RP"] = adata.var_names.str.match("^RPS|^RPL|^Rps|^Rpl") 
sc.pp.calculate_qc_metrics(adata, qc_vars=["RP"], inplace=True)

# fig, axs = plt.subplots(1, 4, figsize=(16, 4))
# sns.distplot(adata.obs["total_counts"], kde=False,bins=60, ax=axs[0]) 
# sns.distplot(adata.obs["n_genes_by_counts"], kde=False, bins=60, ax=axs[1])
# sns.distplot(adata.obs["pct_counts_mt"], kde=False, bins=60, ax=axs[2])
# sns.distplot(adata.obs["pct_counts_RP"], kde=False, bins=60, ax=axs[3])
# plt.savefig("QC_plot.pdf")
# 
# fig, axs = plt.subplots(1, 4, figsize=(16, 4))
# sns.distplot(adata.obs["total_counts"][adata.obs["total_counts"] < 10000], kde=False, bins=40, ax=axs[0]) 
# sns.distplot(adata.obs["total_counts"][adata.obs["total_counts"] > 40000], kde=False, bins=40, ax=axs[1]) 
# sns.distplot(adata.obs["n_genes_by_counts"][adata.obs["n_genes_by_counts"] < 4000], kde=False, bins=60, ax=axs[2])
# sns.distplot(adata.obs["pct_counts_mt"][adata.obs["pct_counts_mt"] >10], kde=False, bins=30, ax=axs[3])
# plt.savefig("QC_plot1.pdf")

sc.pp.filter_cells(adata, min_counts=5000) #filtered out 80 cells that have less than 5000 counts
sc.pp.filter_cells(adata, max_counts=50000) #filtered out 39 cells that have more than 50000 counts
adata = adata[adata.obs["pct_counts_mt"] < 25]
print(f"#cells after MT filter: {adata.n_obs}") #cells after MT filter: 2502
sc.pp.filter_genes(adata, min_cells=10)


sc.pp.normalize_total(adata, inplace=True)
sc.pp.log1p(adata)
sc.pp.highly_variable_genes(adata, flavor="seurat", n_top_genes=2000)
sc.pp.pca(adata)
sc.pp.neighbors(adata)
sc.tl.umap(adata)
sc.tl.leiden(adata, key_added="clusters")
sc.pl.umap(adata, color=["total_counts",  "clusters"], wspace=0.4)
plt.savefig("umap_cluster.pdf")
sc.pl.spatial(adata, img_key="hires", color=["total_counts", "clusters"])
plt.savefig("spatial_cluster.pdf")

adata.write(results_file)
```

``` r
library(shinySRT)
dir.create('shinySRT')
setwd('shinySRT')

CreateshinySRT(dat = 'Anterior.h5ad',title = 'spatial experiment')
```

There are many types of spatial transcriptome data and it is difficult to cover all of them, for this you can construct a list on your own and use it to use shinySRT, the following is the code for the way and format of constructing the list, the data used is the public data from SPATA2 [SPATA 313_T](https://www.dropbox.com/s/iptcb9bqo5oo5xj/313_T.RDS?dl=1)

``` r
library(png)
library(SPATA2)
library(shinySRT)

## eg. SPATA2 obj
dat <- readRDS('313_T.RDS')
dat <- SPATA2::updateSpataObject(dat)

## background image
images <- list(dat@images$`313_T`@image)
names(images) <- unique(dat@fdata$`313_T`$sample)

## coordination
coordinates <- list(dat@coordinates$`313_T`[, c("barcodes", "x", "y")])
names(coordinates) <- unique(dat@fdata$`313_T`$sample)

coordinates <- lapply(names(coordinates), function(x) {
  cos <- coordinates[[x]]%>%as.data.frame()
  rownames(cos) <- paste(names(coordinates), cos$barcodes, sep = '_')
  cos <- cos[,-1]
  
  colnames(cos) <- c('y','x')
  cos <- cos[c(2,1)]
  ## convert x direction
  cos$x <- ncol(image[[x]]) - cos$x
  cos
})

coordinates <- do.call(rbind,coordinates)

## meta 
meta <- dat@fdata$`313_T` %>% as.data.frame()
rownames(meta) <- paste(meta$sample, meta$barcodes, sep = '_')
meta$slice_sample <- meta$sample

## matrix
data = dat@data$`313_T`$counts
colnames(data) <- paste(meta$sample, meta$barcodes, sep = '_')

## names
obj <- list(image = images,data = data,coordination = coordinates,metadata = meta,reduction = NULL)

dir.create('lists')
setwd('lists')

CreateshinySRT(obj,title = 'shiny list')
```

In order to improve efficiency and reduce barriers, shinySRT can also directly read raw data generated by SpaceRanger processing as well as custom datasets (including matrix, position, image, and metadata) , and perform a simple Seurat processing workflow. Finally, it obtains a Seurat object, which users can utilize to create shinySRT.

![](https://github.com/silhouette99/shinySRT/blob/master/image/content12.png)

``` r
library(shinySRT)

dir.create('shinyexample')
setwd('shinyexample')

## matrix: SRT data directory
## single SRT data
## rawdata process
dat <- single_op_dir(dir = 'GBM/',resolution = 1,npcs = 20)

CreateshinySRT(dat)
```

Sometimes users may need to analyze multiple datasets. shinySRT requires only a few commands. Users just need to place the data into a unified directory and provide the path to the directory. The directory structure is as follows:

![](https://github.com/silhouette99/shinySRT/blob/master/image/content11.png)

The directory contains four samples (1, 2, 3, 4), and the data types for reading include SpaceRanger (h5), SpaceRanger (mtx), and custom dataset. These four sample directories are collectively placed in the same directory named “multi_raw”. It is important to note that all these samples must be of the same species.

``` r
library(shinySRT)

## rawdata process
dat <- shinySRT::multi_dir_spatial('multi_raw/' ,resolution = 1,npcs = 20)

dir.create('multi_sample')
setwd('multi_sample')

CreateshinySRT(dat)
```

We have established a related website for shinySRT, where users can upload their SRT data to access an interface tailored to their data for interactive visualization analysis of SRT. The website allows uploading data in three file formats: Binary dataset, SpaceRanger, and custom datasets. The specific files uploaded can be viewed on the webpage. For detailed instructions, users can refer to the "About" page on the website. The basic operations are as follows: Step one, click "Browse" and select the corresponding file (Binary datasets: upload Seurat/SPE objects or Scanpy h5 objects). Step two, wait for the progress bar to complete, and click "Run Analysis" (there are also prompts on the left). Step three, wait for the prompt on the left to change from "Files ready, click 'Run Analysis'" to "Please click 'Go to shiny'". Afterward, click "Go to shiny", and the shinySRT interface will appear below the page.

<div align=center>
<img src="netface.png" width="1200" height="800"/>
</div>

