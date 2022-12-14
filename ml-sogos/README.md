# ML - SOGOS Overview

Song Sangmin <sangsong@uw.edu> <github: psmsong>

Joyce Cai <joycecai@uw.edu> <github: JoyceCaiOcean>

UW Oceanography

Last updated: Oct 31 2022

## Steps for Use: 


1. Start by opening `scripts/sogos_data_preparation.ipynb`

- This notebook goes through the steps to prepare the ship, float, and glider data into Pandas Dataframes. 
- Processed dataframes are saved under the folder `gridded-vars/`
- Original data files are accessed under the folder `data/`

- For the gliders, we start with the 'L3' glider datasets ('level-3' processed dataset from APL, gridded and interpolated. See notes from Jeff Schilling, APL-UW, at bottom) 
- Glider data have been processed into "gp" (gridded-pressure) and "gi" (gridded-isopycnal) xarray Datasets. 
- Argo float data and GO-SHIP bottle data are imported from public access databases, described below.


2.  Once the processed DataFrames are ready, open `scripts/sogos_overview.ipynb`

- This notebook contains the ML scripts to run four different versions of a Random Forest model. The models are first trained on different sets of features from the ship/float data, then used for pH prediction along the glider track.


Many major functions are stored in the `sogos_module.py` module which can be imported into each notebook.


## Data Sources and Links:

[Link to gridded variables](https://uwnetid-my.sharepoint.com/:f:/g/personal/sangsong_uw_edu/Et5YKAWyry5KkSst28_unxsBE3Vc5TCbOGl-3lR4sTvSQQ?email=joycecai%40uw.edu&e=einIE4)

        - `gp_659_forMLGeo1026.nc`  (pressure-gridded 1m, glider #659)
        - `gp_660_forMLGeo1026.nc`  (pressure-gridded 1m, glider #660)

        - `gi_659_forMLGeo1026.nc`  (isopycnal-gridded .001, glider #659)
        - `gi_659_forMLGeo1026.nc`  (isopycnal-gridded .001, glider #659)

                - 'fsle_backwards.nc'           (1-day FSLE from AVISO)
                - 'satellite_data.nc'           (ADT product from AVISO)

- [Link to float data](https://uwnetid-my.sharepoint.com/:f:/g/personal/sangsong_uw_edu/Es-ESkVfIlpHhpFq7o5LTaoBtqv6pWj6rntxMyXieLEq8A?e=FeRRjs)
- [Argo ERDDAP Data Server](http://www.argodatamgt.org/Access-to-data/ERDDAP-data-server)


- [Link to ship data](https://uwnetid-my.sharepoint.com/:f:/g/personal/sangsong_uw_edu/ErLtPwS6pdZClgo0Flp9lq8Bz73FRmUlhR2zf329gDH-3w?e=hCzidh)
- [GO-SHIP I6 Cruise Line Bottle Data](https://cchdo.ucsd.edu/)
        
- Description of glider data variables are in `Seaglider_DataGuide.pdf`
- Description of Argo float and ship are in first paper from SOGOS program: [Link to Dove (2021)]https://agupubs.onlinelibrary.wiley.com/doi/10.1029/2021JC017178)


---
## Code Directory

**Folders**

- `scripts/` : main notebooks and modules
- `data/` : float, ship, and glider data as downloaded
- `gridded-vars/` : processed data saved as DataFrames, prepped for ML
<!-- - `figures/` : useful diagnostic and analysis figures -->

I recommend adding the `data/` folder in your remote copy.
Large files won't upload to Github so you can add the `data/` folder to your .gitignore file.


**Modules**

                sogos_module as sg                      useful common functions + plotting


**Glider Data Notes**

*Naming Conventions:*    
 
                gp_659                          L3 dataset gridded on pressure
                gi_659                          L3 dataset gridded on isopycnals
                df_659                          Flattened dataframe for variable operations
    
Two post-processing steps were developed externally in MATLAB and modified for gliders:
1. Oxygen optode time response correction (Adapted from Yui Takeshita, MBARI)
2. ESPER (Courtesy of Brendan Carter [Link to Github])

 ---
 ## Glider Preprocessing Notes from APL
 SOGOS_Apr19 L2 and L3 reprocessing notes from APL

- owner: Geoff Schilling gbs2@uw.edu - 2021/09/24
- documentation emailed to me (sangsong@uw.edu 2022/10/21)

## SG659
For SG659, glider .eng files for dives 27 through the end were reconstructed from scicon files for
depth and compass (see create_eng.py for details).  The following files:

sc0078b/psc6590078b_depth_depth.eng 
sc0096b/psc6590096b_depth_depth.eng 
sc0176b/psc6590176b_depth_depth.eng 
sc0332b/psc6590332b_depth_depth.eng 
sc0451b/psc6590451b_depth_depth.eng 
sc0476a/psc6590476a_depth_depth.eng 
sc0485a/psc6590485a_depth_depth.eng 

did not exist, so .eng files for those dives were not created.

## SG659 and SG660
Both glider data was the processed the IOP in-house version of the basestation
code.  This version is substantially the same as version 2.13 - released to
Hydroid in May of this year, but not yet available (that I know of).  The
primary change is the incorporation of the FMS system
(https://digital.lib.washington.edu/researchworks/handle/1773/44948) which
yields a number of improvements in the CTD corrections. 

The Hydroid version of the Seaglider flight code and basestation have some
differences in netcdf variable naming.  To process the optode and wetlabs data,
is renamed these variable columns - see the sg_calib_constants.m in both
gliders directories for the remapping scheme.

Unfortunately, our version of the basestation does not apply the PAR sensor
calibrations to the output, so the PAR data is as reported by the instrument.

## L23 Processing
To have a sanity check of the processing, I ran the re-processed data through
code to create L2 (1m binned) and L3 (interpolated with outliers removed) data
sets - see SOGO_Apr19_L23 for the data, and SOGO_Apr19_L23/plots for the output
plots. 

In both gliders data sets, something changed dramatically at the end of the
mission - for SG659 starting on dive 463 and for SG660 on dive 510, the
temperature jumps dramatically.  I didn't trace this all the way back to the
raw instrument output, but observed it is in the original netcdfs provided with
SG660, so I'm assuming this is a real and not processing related.

