# GeneticGRBs

This repository contains the Python code that implements a Genetic Algorithm (GA) for the optimization of the seven parameters of the [Stern & Svensson (1996)](https://iopscience.iop.org/article/10.1086/310267) (SS96) stochastic pulse avalanche model, for the generation of simulated gamma-ray burst (GRB) light curves (LCs), developed in [this paper]() ([arXiv version](https://arxiv.org/abs/2403.18754)).

- _Reference Paper_: [Long gamma-ray burst light curves as the result of a common stochastic pulse-avalanche process]().

- _Corresponding Author_: Lorenzo Bazzanini, University of Ferrara ( `bzzlnz[at]unife[dot]it` )

- _Code Contributors_ (in alphabetical order): G. Angora, L. Bazzanini, L. Ferro, C. Guidorzi, and A. Tsvetkova.



## Table of Contents  
- [Description](#description)  
- [Installation](#installation)  
- [Usage](#usage)
  - [Generation of a set of simulated LCs](#generation-of-a-set-of-simulated-lcs)
  - [Running the GA optimization](#running-the-ga-optimization)
- [Other](#other)



## Description

The light curves of long GRBs show a wide variety of morphologies, which current LC simulation models based on the internal shock paradigm still fail to fully reproduce. The reason is that, despite the recent significant advance in understanding the energetics and dynamics of long GRBs, the nature of their inner engine, how the relativist outflow is powered, and the dissipation mechanisms are still not understood. This limits our ability to properly describe and simulate those transients. 

A promising way to gain insights into these topics is modeling of GRB light curves as the result of a common stochastic process. In the Burst And Transient Source Experiment (BATSE) era, a stochastic pulse avalanche model was proposed and tested by comparing ensemble-average properties of simulated and real light curves, proposed by [SS96](https://iopscience.iop.org/article/10.1086/310267). 

Using machine learning, we optimized this stochastic model's parameters by exploiting the GA's capability to thoroughly explore the parameter space. We revived this model by applying it to two independent datasets, BATSE and Swift/BAT. The average properties are successfully reproduced. Notwithstanding the different populations and passbands of both data sets, the corresponding optimal parameters are interestingly similar. In particular, for both sets the dynamics appears to be close to a critical state, which is key to reproduce the observed variety of time profiles. Our results propel the avalanche character in a critical regime as a key trait of the energy release in GRB engines, which underpins some kind of instability.


<p align="center">
<img src="avalanche.png"  alt="" width = "450" />
</p>

The GA has been implemented using [`PyGAD`](https://github.com/ahmedfgad/GeneticAlgorithmPython) ([Gad 2023](https://link.springer.com/article/10.1007/s11042-023-17167-y)), a FOSS Python library containing a collection of several machine learning algorithms.

The Python code that implements the SS96 stochastic model is also contained in this repo; it was originally forked from [this public repository](https://github.com/anastasia-tsvetkova/lc_pulse_avalanche) by one of the co-authors, but has undergone significant changes.

To identify the statistically significant peaks in the LCs we have used [`MEPSA`](https://www.fe.infn.it/u/guidorzi/new_guidorzi_files/code.html) ([Guidorzi 2015](https://www.sciencedirect.com/science/article/pii/S2213133715000025)), an algorithm aimed at identifying peaks within a uniformly sampled, background subtracted/detrended time series affected by statistical uncorrelated Gaussian noise, conceived specifically for the analysis of GRB LCs. 



## Installation

To run the code, it is advised to create a self-contained `conda` environment with all the required libraries installed. To create the aformentioned environment (which we called `pygad3`) just run the following four lines of code (or follow the instructions in the file `./conda_pygad3_env.txt`, which contains also the version of _all_ the installed packages):
```bash
# 1. create the conda environment 
conda create -n pygad3 python=3.10

# 2. activate the environment
conda activate pygad3

# 3. install all the required libraries (with the required version)
pip install numpy==1.26.4 matplotlib==3.8.3 seaborn==0.13.2 pandas==2.2.0 astropy==6.0.0 pygad==3.3.1 tqdm==4.66.2 scipy==1.12.0 cloudpickle==3.0.0 h5py==3.10.0 astroml==1.0.2.post1 scikit-learn==1.4.1.post1

# 4. install some extra utilities for VSCode
conda install ipykernel --update-deps --force-reinstall
```


## Usage
In order to run the GA minimization procedure, the BATSE or the _Swift_/BAT light curves are needed. Instead, if you just want to simulate a new set of LCs (given a set of seven SS96 parameters) the code in this repository should be sufficient.

#NOTE: before running anything, you have to unzip all the nine archives in the `./lc_pulse_avalance` folder (seven `swift_errs_*.txt.zip`, `kde_pdf_Swift_peak_count_rates.txt.zip`, and `kde_pdf_BATSE_peak_count_rates.txt.zip`). To do that, you can just run the following command:
```bash
# move to the right directory
cd ./lc_pulse_avalance
# extract all the files
tar -xzf swift_errs_1.txt.zip; tar -xzf swift_errs_2.txt.zip; tar -xzf swift_errs_3.txt.zip; tar -xzf swift_errs_4.txt.zip; tar -xzf swift_errs_5.txt.zip; tar -xzf swift_errs_6.txt.zip; tar -xzf swift_errs_7.txt.zip; tar -xzf kde_pdf_Swift_peak_count_rates.txt.zip; tar -xzf kde_pdf_BATSE_peak_count_rates.txt.zip
```

### Generation of a set of simulated LCs

To generate the LCs, edit the `config.ini` file (`./lc_pulse_avalanche/config.ini`) by choosing the instrument (`instrument = batse` or `instrument = swift`, if you want to simulate BATSE or Swift LCs, respectively), the number of LCs (`N_grb = XXX`), and assign the value to the seven SS96 parameters. Make sure that the variable `user` is set to `user='external_user'` at the beginning of both `simulate_GRBs.py` and `statistical_test.py` files. Then run:
```bash
# move to the right directory
cd ./lc_pulse_avalanche
# activate the conda env
conda activate pygad3
# simulate a set of LCs
python simulate_GRBs.py config.ini
```
The code will create a directory (`./simulated_LCs`), and inside, each time you call the code it will create a directory named `Year-Month-Day_Hour_Minute_Second` with all the requested simulated LCs. Moreover, a copy of the config file (`config_Year-Month-Day_Hour_Minute_Second.txt`) will be created in `./simulated_LCs` for later reference.


The value of the optimized parameters (see our paper above) are:
```
# BATSE
mu      = 1.10 
mu0     = 0.91
alpha   = 2.57
delta1  = -1.28
delta2  = 0.28
tau_min = 0.02
tau_max = 40.2

# Swift/BAT
mu      = 1.34
mu0     = 1.16
alpha   = 2.53
delta1  = -0.75
delta2  = 0.27
tau_min = 0.03
tau_max = 56.8
```



### Running the GA optimization
```bash
# move to the right directory
cd ./genetic_algorithm
# set the `user` variable (`geneticgrbs.py`, line 41; `avalanche.py`, `statistical_test.py`)
user={'bach','gravity'}
# set the `instrument` variable (`geneticgrbs.py`, line 98)
instrument={'batse','swift'}
# set the parameters of the model (`geneticgrbs.py`)
{'num_generations','N_grb','range_PARAMS', etc.}
# activate the conda env
conda activate pygad3
# run the GA
python geneticgrbs.py
```



## Other
If you have any question or you are interested in contributing do not hesitate to contact the authors.

Please cite the following associated paper if you use this code in your work:
```
@ARTICLE{2024arXiv240318754B,
       author = {{Bazzanini}, Lorenzo and {Ferro}, Lisa and {Guidorzi}, Cristiano and {Angora}, Giuseppe and {Amati}, Lorenzo and {Brescia}, Massimo and {Bulla}, Mattia and {Frontera}, Filippo and {Maccary}, Romain and {Maistrello}, Manuele and {Rosati}, Piero and {Tsvetkova}, Anastasia},
        title = "{Long gamma-ray burst light curves as the result of a common stochastic pulse-avalanche process}",
      journal = {arXiv e-prints},
     keywords = {Astrophysics - High Energy Astrophysical Phenomena, Astrophysics - Instrumentation and Methods for Astrophysics},
         year = 2024,
        month = mar,
          eid = {arXiv:2403.18754},
        pages = {arXiv:2403.18754},
          doi = {10.48550/arXiv.2403.18754},
archivePrefix = {arXiv},
       eprint = {2403.18754},
 primaryClass = {astro-ph.HE},
       adsurl = {https://ui.adsabs.harvard.edu/abs/2024arXiv240318754B},
      adsnote = {Provided by the SAO/NASA Astrophysics Data System}
}
```
