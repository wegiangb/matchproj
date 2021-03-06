# MSGR - Matching Satellite and Ground Radar

Based on an IDL code from Rob Warren.

## Installation

Just type `python setup.py install`. The full list of required package is in requirements.txt, just type `pip install -r requirements.txt`.

## Required modules

You will need:
>- Python ARM Radar Toolkit [(Py-ART) ][1]
>- [SciPy][2]
>- [NumPy][2]
>- Python Data Analysis Library [(pandas)][3]
>- [Pyproj][4]

This code has been tested and conceived on Python 3.5 and should work for any versions 3 of Python. It will *NOT* work with python 2.X versions.

## Usage

`matchvol` need a configuration file to run, an example can be generated by typing `generate_config_matchvol --example`. Just pass the configuration file like that: `matchvol -s config.ini`.

You should only modify the config.ini file to match your own configuration and then run python matchvol.py in a terminal. In the config.ini file you can choose:
* Number of CPU for multiprocessing.
* Start and end date of processing in YYYYMMDD format.
* Input path for ground radar files, satellite files.
* Output path for saved data.
* The radar general information (name and ID, used for naming the output saving file), latitude, longitude, altitude, and beamwidth.
* Min and max range (in m) of ground radar.
* Different thresholds for comparison like:
    - Threshold on minimum satellite and ground radar reflectivity.
    - Minimum number of pair for comparison.
    - Minimum number of satellite profiles for comparison.
    - Maximum time difference between radar and satellite, in seconds.
* Choose between the use of dBZ or natural units for the statistical calculations.
* Declare that satellite is GPM (false for TRMM).
* Ground radar is C-Band (false for S-Band).
* Writing results in output directory.
* Correct ground radar attenuation using pyart.

## Satellite data

About the satellite data, here a copy of a mail from Rob Warren explaining how to get them:

The website where you can download TRMM and GPM data is https://storm.pps.eosdis.nasa.gov/storm/data/Service.jsp?serviceName=RestrictedOrder. You need to register before you can order data. One you've entered your pre-registered email address you can enter the details of your order. You want the following options:
*	Under 'Order type' select 'Standalone order'
*	Under 'Coincidence' select 'None or Satellite-Ground Validation Site'
*	Under 'Options' select 'Subset Geographic Area' then 'Subset Geographically' (leave 'Include only swaths with...' blank), and also select 'Parameter Subsetting'
*	Under 'Product Type' select '2AKu' under 'Algorithm' and check the box that comes up below. Note that this is for GPM. For TRMM you have to make two orders: one for 2A23 and one for 2A25, but if you're only working with GPM you don't need to worry about that.
*	Under 'Temporal Criteria' set the range of dates you want data for.
*	Under 'Special Area Of Interest' specify the limits of your domain (it should encompass the 150km range ring of your radar(s)). Give it a 'Location Alias'.
*	Under 'Parameter Subset' choose the following: 'dataQuality' from 'scanStatus', 'landSurfaceType' and 'flagPrecip' from 'PRE', 'flagBB', 'heightBB', 'widthBB', 'qualityBB', 'typePrecip', and 'qualityTypePrecip' from 'CSF', and 'zFactorCorrected' from 'SLV'. Provide an identifier, then select 'No' for 'Do you want to generate Read and Write routines for this subset', and set 'HDF' as the 'Output Data Format'.
*	Under 'Search Results' select all the files by clicking the top-most check box.
*	Under 'Script Type' select whichever you want: I used 'FTP URL' but you may prefer another.
Hitting submit should then get you what you want.

## Radar data

Because this codes uses pyart to read radar data, the only limitations are pyart's limitations. It should work with CF/Radial, UF, lassen, rainbow,, ODIMM HDF5, ...

In the case that the radar's data you are using do not use the "traditional" naming convention, especially for reflectivity (names are 'reflectivity', 'DBZ', 'DBZ_F'), you can add your own by modifying the read_radar.py file in ./MSGR/io/ and change (better add) one of the lines like this: `refl_slice = radar.fields['reflectivity']['data'][sweep_slice]  # Reflectivity`

[1]: https://github.com/ARM-DOE/pyart
[2]: http://www.scipy.org/
[3]: http://pandas.pydata.org/
[4]: http://jswhit.github.io/pyproj/
