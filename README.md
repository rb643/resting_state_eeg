# Resting State EEG with Python MNE

These scripts are to be used for fully-automated pre-processing of resting state EEG data that was recorded on a 64-channel BioSemi ActiveTwo system with external electrode 6 (placed on the mastoid) as a reference electrode. It assumes trigger codes are used for eyes-closed and eyes-open segments and presently only uses eyes-closed segments. The currently assumed pre-processing steps are as follows:

1. Trials and triggers codes are read and only eyes-closed segments are selected
2. Very basic pre-processing includes: demeaning, detrending, low-pass filter below 60Hz, re-referencing to mastoid reference, bandpass filtering of possible 50Hz line-noise and resampling to 256Hz.
3. Optional visual inspection after basic pre-processing
4. Optional reconstruction of noisy channels (though only use this if a channel is absolutely rubbish!)
5. Optional removal of noise using ICA decomposition 
6. Segmenting continuous recording into 4-second segments to be used for WPLI analyses later on
7. Autoreject bad epochs prior to WPLI analyses
8. Calculate the weighted phase lag index using multi-taper fast fourier transform for each of the 4 frequency bands separately


## What assumptions are made
* You are using a BioSemi ActiveTwo 64 Channel recording system
* You are using the external channels on the BioSemi to get an additional reference
  * In our case (and hence in the current script) channel 6 is used to record a mastoid reference
* You have included trigger codes for the eyes-open and eyes-closed starting point
  * In our case we have 102 and 103 as codes for the eyes-closed triggers and 100 and 101 for eye-open
* We do not use the full minute but the middle 50s to avoid including the transition from eyes-open to eyes-closed
* Since we don't look at any frequencies above 60Hz we can downsample to 256Hz to speed up processing
* We resegement the recording into 4s epochs to somewhat articificially create trials that are need for WPLI analyses later on (i.e. we need to be able to average over multiple segments)
* Our pre-processing settings are fairly basic and easy to adjust in the script


## What scripts do what
There is one main notebook that runs the whole pre-processing pipeline
(https://github.com/rb643/resting_state_eeg/blob/master/rsEEG_preproc_ica_autoreject.ipynb) which runs:
1. Basic pre-processing, filtering and auto-reject on segments and channels
2. Plots basic QC by showing number of rejected epochs (out of a total of 28) and the individual connectivity distributions
3. Runs cluster based non-parametric permutation group comparisons (https://www.ncbi.nlm.nih.gov/pubmed/17517438)
4. Runs basic graph metrics on thresholded matrices (MST + 10%)


## Dependencies
There are a few external scripts/toolboxes that this notebook depend upon:
* A full working installation of MNE Python (https://www.martinos.org/mne/stable/install_mne_python.html). MNE has updated the way it handles status/triggers for BioSemi, so we strongly recommend using the latest version (v0.17)
* A full working installation of Autoreject for MNE (http://autoreject.github.io). 
* A full working installation of Networkx `pip install networkx` to run graph analyses
* We use HDF5 to store intermediate datafiles `pip install h5py` and the Pytables dependency `conda install pytables`
* We use seaborn `pip install seaborn` and joypy `pip install joypy` for various plots


### Notes:
* So far this implementation has only been tested on Linux (Ubuntu 16.06 LTS) with an Anaconda implementation of Python 3.6

### Still to do
* Iterate graph metrics over difference densities
* Implement feature extraction and machine learning
* Complete data collection
