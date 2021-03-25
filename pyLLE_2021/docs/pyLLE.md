---
layout: page
title: pyLLE Modules
sidebar_link: true
sidebar_sort_order: 6
---

# _llesolver.py

## MyLogger(self, fname)

Custom made logger as the logger default package cannot be pickled

## Latexify(self, **kwarg)

Class that handles saving the figures in a nice way compatible with
the column/page size of different latex templates.

- Input (\[\] = optional):
    - figname = name to save the figure (without extension)
    - fig = matplotlib handle to the figure
    - \[fig_width\]: default = '1column'
    - \[frmt\]: default = 'pdf'
    - \[fig_height\] : default = 6.5
    - \[font_size\] : default = 8

## LLEsolver(self, **kwargs)

Class to solve the Lugiato Lefever Equation
Initialization input (\[\] = optional):

- res \<dict\>
    - Qi \<float\>: intrinsic Q of the resonator
    - Qc \<float\>: coupling Q of the resonator
    - R \<float\>: ring radius
    - gamma \<float\>: Effective nonlinearity of the waveguide comprising the resonator.
    - dispfile \<str\> : str pointing to a .csv file where the azimuthal mode orders and corresponding resonance frequencies are saved
- sim \<dict\>
    - Tscan \<float\>: length of the simulation (in units of round trip)
    - mu_fit \<list\>: number of modes to fit
    - mu_sim \<list\>: number of mode that were simulated
    - domega_init \<float\>: initial detuning of the pump
    - domega_end \<float\>: final detuning of the pump
    - \[domga_stop\] \<float\>: where to stop the scan in detuning but keep doing the simulation
- debug \<bool\>: Save a trace in a log file in the working directory of the different actions pyLLE perform (default = True)

### LLEsolver.Analyze(self, plot=False, f=None, ax=None, label=None, plottype='all', zero_lines=True, mu_sim=None)

Call pyLLE.analyzedisp.AnalyzeDisp to get the dispersion of the resonator we want to simulate

### LLEsolver.Setup(self)

Setup the simulation for the Julia back-end.
Save the two main dictionary self.sim and self.res into a readable hdf5 file for Julia in the temporary location define by the os

### LLEsolver.SolveTemporal(self, tol=0.001, maxiter=6, step_factor=0.1)

Call Julia to solve the LLE

### LLEsolver.SolveSteadySteate(self)

Newton Method to find the root of the steady state equation

### LLEsolver.RetrieveData(self)

Load the output hdf5 saved by Julia and transform it in a user-friendly dictionary to be more pythonistic

### LLEsolver.PlotCombPower(self, do_matplotlib=False)

Plot a figure with 3 subplots.

- Top subplot = map of the spectra for the steps taken by the LLE (step sub-sampled to be 1000)
- middle subplot = temporal map of the intensity inside the resonator for the steps of the LLE
- bottom subplot = normalized comb power

- Output
    - f \<obj\>:  matplotlib/plotly figure handle
    - ax \<obj\>: matplotlib axes handle (if plotly, only returns f)

### LLEsolver.PlotCombSpectra(self, ind, f=None, ax=None, label=None, pwr='both', do_matplotlib=False, plot=True)

Plot the spectra for a given index in the 1000 sub-sampled LLE steps

- Input
    - ind \<ind\>: index in the LLE step to plot the spectra
    - f \<obj\>:  matplotlib figure handle (if None, new figure)
    - ax \<obj\>: matplotlib axe handle
    - label \<str\>: label for the legend
    - pwr \<str\>: 'both', 'ring', 'wg' depending on the spectra wanted (inside the ring, the waveguide or both)
- Output
    - freq \<numpy.array\>: frequency in Hz
    - Sout \<numpy.array\>: spectral density of power in the waveguide (dBm)
    - Sring \<numpy.array\>: spectral density of power in the ring (dBm)
    - f \<obj\>:  matplotlib/plotly figure handle
    - ax \<obj\>: matplotlib axes handle (if plotly, only returns f)

### LLEsolver.PlotSolitonTime(self, ind, f=None, ax=None, label=None, do_matplotlib=False)

Plot the spectra for a given index in the 1000 sub-sampled LLE step

- Input

    - ind \<ind\>: index in the LLE step to plot the spectra
    - f \<obj\>:  matplotlib figure handle (if None, new figure)
    - ax \<obj\>: matplotlib axe handle
    - label \<str\>: label for the legend

- Output
    - τ \<obj\>: Time in the resonator
    - U \<numpy.array\>: Temporal Electric field for the given step of the LLE
    - f \<obj\>: matplotlib/plotly figure handle
    - ax \<obj\>: matplotlib axe handle (if plotly, only returns f)

### LLEsolver.SaveResults(self, fname, path='./')

Save the whole class with pickle to be able to easily call it back or retrieve the results after saving

- Input
    - fname \<str\>: name to save. The '.pkl' extension will be added
    - path \<str\>: path to save the results (defaults './')


# _analyzedisp

## AnalyzeDisp(self, **kwargs)

Call to analyze the dispersion of a simulated resonator
Initialization input. Everything is in SI ([]=optional):

- Input:
    - f_center \<float\>: pump frequency
    - file \<str\>: .txt file to load
    - R \<float\>: radius of the resonator
    - rM_fit \<list\>.: lower and upper bonds of mode to fit the dispersion
    - rM_sim \<list\>.: lower and upper bonds of the modes to extrapolate for the simulation
    - f \<obj\>: matplotlib figure handle
    - ax \<obj\>: matplotlib axes handle
    - label \<list\>: list of the string for each plot to be labeled
    - plottype \<str\>: define the type of plot 'all' \[defaults\], 'sim', 'fit', 'fem', 'ind'

### AnalyzeDisp.GetDint(self)

Retrieve the dispersion of a resonator based on the frequency of
resonance and azimuthal mode order. The data are fit using a cubic spline method

- Output: 
    + self.PrM_fit: scipy.interpolate object which fitted the data
    + self.Dint_fit: fitted integrated dispersion for the simulated mode
    + self.neff_pmp: effective index at the pump frequency
    + self.ng_pmp: group index at the pump frequency


