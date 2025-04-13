# SPARC_analysis


This repository collects some analysis performed on the data from the [SPARC database](http://astroweb.cwru.edu/SPARC/) ([Lelli et al., 2016](https://iopscience.iop.org/article/10.3847/0004-6256/152/6/157)). 
Reference data include:
- Galaxy Sample, `Table1.mrt` file; 
- Newtonian Mass Models, `Rotmod_LTG` zip file;

This repository includes:
- `sparcReading.ipynb`. This code reads the SPARC data and saves visualization plots for the rotation curves and surface brightness profiles. It includes a function to retrieve the index of a given galaxy in the `Table1.mrt` table.
- `sparcDensities.ipynb`. This code computes, for each galaxy, effective spherically symmetric density profiles for disk, bulge (when available) and gas, starting from the rotation curves data. The density profiles are computed as exponential curves, $\rho(r)=a e^{r/r_0}$. The computed fit parameters are saved in a `.dat` file.
- `sparcRVelements.ipynb`. This code detects reference elements in each rotation curve (linear fit in midrange region, and characteristic radius $R$ and velocity $V$ according to different definitions). It tests the following hypothesis 
    > The quantity $\hbar/(RV)$ is constant across the galaxies 

    by providing histograms and a scattered plots for the selected definition of $R,V$.



## `sparcReading.ipynb` notebook
This file provides two functions for reading the SPARC data, which are used in the other notebooks of this repository as well. Data for each galaxy are plotted and saved to subfolders. The plots include:
- observed rotation curves, with baryonic components
- surface brightness profiles for disk and bulge (when available)
 
The notebook includes the possibility to retrieve the index of a given galaxy in the `Table1.mrt` table.

## `sparcDensities.ipynb` notebook
The code in this notebook computes an effective radially symmetric volumetric density profile for Disk, Bulge (when available) and Gas, based on rotation curves data, $\rho(r) = \frac{1}{r^2}\frac{d}{dr}\frac{v^2(r) r}{4\pi G}$. <br>
Then it fits the effective density datapoints with an exponential profile, $\rho(r) = a e^{-r/r_0}$. 

Resulting fit parameters (amplitudes $a$, characteristic radii $r_0$) for every galaxy are saved to `.dat` file. <br>
The following computed profiles are plotted and saved in subfolders:
- effective volumetric density $\rho(r)$ in logscale;
- effective surface brightness $\Sigma(r) = r\rho(r)$;
- effective velocities $v(r)= \sqrt{\frac{4\pi G \int_0^r \rho(s) s^2 ds}{r}}$.

In addition, the notebook provides some tests on the resulting density parameters:
- *Test (check on amplitudes):* Visually compare (via scattered plots) the amplitudes $a_D$,$a_B$, $a_G$ across different galaxies, to see if they are in any relation. Galaxies are colored by Hubble type (see `table1.dat`). Spearman correlation Test is performed. <br>
*Result:* Amplitudes for Disk, Bulge and Gas are not strongly related.
- *Test (check on characteristic radii):* Visually compare (via scattered plots) the characteristic radii $r_{0,D}$,$r_{0,B}$, $r_{0,G}$ across different galaxies, to see if they are in any relation. Galaxies are colored by Hubble type (see `table1.dat`) Spearman correlation Test is performed. <br>
*Result:* Characteristic radii for Disk, Bulge and Gas are not strongly related.
- *Test (comparison with SPARC):* Compare effective estimates and SPARC tabulated data for: total luminosity, disk radius and total hydrogen mass. Spearman correlation Test is performed.
	- effective total luminosity: $L_{Tot}= 4 \pi \int_0^\infty (\rho_{Disk}(s)+\rho_{Bulge}(s)+\rho_{Gas}(s) ) s^2 ds$
	- effective disk radius: $r_0$
	- effective total hydrogen mass: $M_{H} = 4 \pi \int_0^\infty \rho_{Gas}(s) s^2 ds$

*Result:* SPARC and effective quantities are not strongly correlated for total luminosity and total hydrogen mass. For the disk radius, they are related (approximately proportional, with the effective radius underestimating the SPARC radius)


## `sparcRVelements.ipynb` notebook
### Elements definition and visualization
This code detects reference elements in each rotation curve. The elements are computed and save in a `.dat` file with the following structure:<br>
| Galaxy name | index | intercept | slope | r_indexL | r_indexR |vFlat | n0 | r_n0 | v_n0L | v_n0E | ...  | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

| ... | nN | r_nN | v_nNL | v_nNE | 
| --- | --- | --- | --- | --- |

with:
- `Galaxy name` denoting the name of the galaxy
- `index` denoting the index of the galaxy in `table1.dat` from SPARC. Galaxies are selected, neglecting:
    - low quality curves (Q=3)
    - low inclination (i<30)
    - low nr of data points (#<8)
    - Vflat not reached (Vflat==0)
- `r_indexL`,`r_indexR` denoting the left and right indexes (referred to the r vector measured for that Galaxy) defining the start and end of midrange region
- `nj` denoting the percentage to compute the reference radius (for `j=0,...,N`)
- `r_nj=nj*r_out` denoting the reference radius, computed as the `nj` percentage of the outermost measured radial position `r_out`
- `v_njL=slope*r_nj+intercept` the corresponding velocity along the line approximating the rotation curve in the midrange region
- `v_njE` the exact velocity observed in (the radial position closest to) `r_nj`. 

The code also provides plots of the rotation curves with the desired elements.

### Testing the *$\hbar/(RV)$ is constant* hypothesis
The code in the `sparcRVelements.ipynb` notebook also tests the hypothesis that *the quantity $\hbar/(RV)$ is constant across the galaxies*. <br>
The test is performed for a specific definition of $(R,V)$, to be selected among the possibilities presented in the previous part of the notebook and saved in the `.dat` file.<br> 
First, an histogram is computed, to visualize the most frequent value of $\hbar/(RV)$ across the galaxies and the full width at half maximum. <br>
Then, a scattared plot with $\hbar/(RV)$ against $R$ is provided to check the presence of residual dependencies. A residual exponential dependency is found. An improved definition of $R,V$ is required for a more refined test on the consistency of $\hbar/(RV)$ across different galaxies.
