# GUniFrac
Generalized UniFrac Distances and Distance-based Multivariate Analysis of Variance

## Overview
 The package implements Generalized UniFrac distances for comparing microbial communities and three extensions to permutational multivariate analysis of variance  (PERMANOVA) using distance matrix. The three extensions are
 * PERMANOVA using the Freedman-Lane permutation scheme; 
 * PERMANOVA omnibus test using multiple matrices; 
 * An analytical approach to approximating PERMANOVA p-value.


## Installation 

```
install.packages(c("ape", "vegan", "Matrix", "matrixStats"))
install.packages("devtools")
devtools::install_github("jchen1981/GUniFrac")
```



### An Example
We illustrate the usage of  package using simulated data.

```
require(GUniFrac)

data(throat.otu.tab)
data(throat.tree)
data(throat.meta)


# Rarefaction
otu.tab.rff <- Rarefy(throat.otu.tab)$otu.tab.rff

# Calculate the UniFrac distance
unifracs <- GUniFrac(otu.tab.rff, throat.tree, alpha=c(0, 0.5, 1))$unifracs

dw <- unifracs[, , "d_1"]          # Weighted UniFrac
du <- unifracs[, , "d_UW"]         # Unweighted UniFrac     
dv <- unifracs[, , "d_VAW"]        # Variance adjusted weighted UniFrac
d0 <- unifracs[, , "d_0"]          # GUniFrac with alpha 0  
d5 <- unifracs[, , "d_0.5"]        # GUniFrac with alpha 0.5 

# Test the smoking effect based on unweighted UniFrac distance, adjusting sex.

# PERMANOVA using Freedman-Lane permutation scheme ('Sex' should be put before 'SmokingStatus')
adonis3(as.dist(unifracs[, , 'd_UW']) ~ Sex + SmokingStatus, data = throat.meta)

# Or we can permute within each sex
adonis3(as.dist(unifracs[, , 'd_UW']) ~ SmokingStatus, data = throat.meta, 
strata = throat.meta$Sex)

# Compare the p-values to the analytical approach
dmanova(as.dist(unifracs[, , 'd_UW']) ~ Sex + SmokingStatus, data = throat.meta)

# PERMANOVA omnibus test combining weighted and unweighted distance matrices
PermanovaG2(unifracs[, , c("d_1", "d_UW")] ~ Sex + SmokingStatus, data = throat.meta)  
```


