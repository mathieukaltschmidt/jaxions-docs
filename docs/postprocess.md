---
layout: default
title: Python tools
nav_order: 5
---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## The `pyaxions` library

In the `scripts/pyaxions` folder several python modules are facilitating the visualisation and analysis of data. We briefly describe some of the most useful tools.

- `jaxions.py`
- `spectrum.py`
- `simgen.py`
- `nyx-tools.py`
- `specfromF.py`

```python
import sys
sys.path.append('path/to/jaxionsdir/jaxions/scripts/')
from pyaxions import jaxions as pa
```

## Checking the parameters
{: #checkp}

An auxiliary tool to check whether the chosen simulation parameters are physically adequate can be found in `scripts/params.py`. It includes the follwing parameters:

- `--n`: power-law index $$ n $$ in the axion mass growth, $$ m_a\propto T^{-n/2}$$
- `--N`: resolution of the lattice in each dimension
- `--L`: physical size of the lattice in units of $$ L_1 $$
- `--msa`: the inverse size of the string core
- `--zf`: the final simulation time (normalised conformal time)
- `--kref`: select a mode to study,  in units of $$ L_1^{-1} $$

```
python3 params.py --n 7.0 --N 4096 --L 6 --msa 1.5 --zf 4.0 --kref 10.0
```

## The randomstrings.py module
{: #randomstrings}

The module `randomstrings.py` can be used to create various initial conditions with individual or multiple string loops in many different shapes. Besides some special cases such as circular loops or polyhedra with a fixed number of vertices, it is also possible to create randomly twisted and displaced loops to study their behaviour and gain a better understanding of the individual constituents of the full string network.
In a careful theoretical consideration we generalised and implemented the computation of the axion field around an arbitrary string configuration in the main code. The details of this procedure will be published soon and referenced here.

The general workflow for creating a specific `string` initial condition is as follows:

-  Choose one of the currently available options (see overview below) and create a `string.dat` file in the current working directory of the planned simulation (Important!).

Caution: All the files produced by the different techniques mentioned above are named `string.dat` per default. Make sure you didn't overwrite your desired setting.

-  Run the simulation as usual by specifying the respective command line flag in the typical `vax-ex.sh` script,

```
INCO=" --ctype string --zi 0.1 --sIter 10"
```
where `--ctype string` specifies the correct ICs, `--zi` is the conformal time where the simulation is initialised and `--sIter` specifies the number of smoothing steps applied after the computation of the axion field around the string(s).

The currently available options are:

- `onestring`: Create a single circular loop, polyhedra with $$ n $$ vertices or a knot.
- `randomstrings`: Create arbitrary many loops of random sizes and shapes by controlling the respective parameters.

If you want to make sure the initialisation worked properly, just use `./vax-ex.sh create` and have a look at the computed axion field for your IC before running the entire simulation. You can use

- `fu`: Create slice plots of the axion configuration with all the relevant data.

As an example, here is a simple python code to create a circular loop and look at the result in the three coordinate planes:

```python
import numpy as np
from pyaxions import randomstrings as rs

N = 256 #Grid points in one dimension
R = N//4 #Radius of the loop
L = 2*np.pi*R # Length of the string loop (Fix size of twisted loops to perfect circular loop size)

x,y,z, ep = rs.onestring(N, R, m='l') #Creates the string.dat file and saves the coordinates and endpoints (relevant for computation of the axion field)
#x,y,z = rs.randomstrings(N,L,ITER=10, PATH='./') #If you want to test more complex shapes

#Plot the result
fig,ax=plt.subplots(1,3,figsize=(17,5))
ax[0].scatter(x,y,s=3)
ax[1].scatter(y,z,s=3)
ax[2].scatter(z,x,s=3)
for a in ax:
    a.set_ylim(0,N);a.set_xlim(0,N)
```

## Running a series of multiple simulations
{: #simgen}

Work in progress ..

## Easier comparison with AMR simulations with axionyx

Jaxions can be used to prepare initial conditions for the adaptive-mesh code `axionyx` (cf. Schwabe et al. 2007.08256'). Often it is useful to benchmark the AMR simulations with high-resolution static-grid simulations in Jaxions. The `nyx-tools.py` module features some python tools to facilitate comparison of these simulations within the `pyaxions` framework.

More precisely, currently it features

- `overview`: Print a short overview of the key parameter choices.
- `readInputs`: Read (and store) the simulation parameters from the `inputs` file, that is usually used the run the `axionyx` simulations.
- `getValue`: Access specific parameter values of the axionyx simulation data.

Caution: At the moment, unlike the respective functions used to analyse the Jaxions data, these features don't access the actual data files but rather the input data, that was used to run the simulation. If you changed the `inputs` file afterwards this might lead to errors!!

Additionally, the following functions can be used to access and preprocess computed energy spectra, that are usually stored as .txt files in a separate directory `spectra/` for axionyx:

- `getSpecfiles`: Finds, renames and sorts all the files in `spectra/` and outputs all the files in a list.
- `processSpectrum`: Class that reads the spectrum files and assigns the physical parameters to every data set. Precisely,
    - `nm`: number of modes
    - `k`: momenta
    - `k_below` all momenta below the radial mass cutoff, i.e. $$ k < m_sa * N/L$$
    - `t`: conformal times
    - `log`: respective values of $$ \log h$$
    - `esp`: energy spectra
    - `P`: rescaled spectra

More details on all the available options and features can be accessed by calling for example `help(readInputs)` in the respective python environment. We don't go into more detail here as these tools won't be used by the majority of the Jaxions users.


Under construction ...
