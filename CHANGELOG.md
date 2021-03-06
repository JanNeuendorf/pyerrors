# Changelog

All notable changes to this project will be documented in this file.

## [1.0.1] - 2020-11-03
### Fixed
- Bug in pyerrors.covariance fixed that appeared when working with several
  replica of different length.

## [1.0.0] - 2020-10-13
### Added
- Compatibility with the BDIO Native format outlined [here](https://ific.uv.es/~alramos/docs/ADerrors/tutorial/). Read and write function added to input.bdio
- new function `input.bdio.read_dSdm` which can read the bdio output of the
  program `dSdm` by Tomasz Korzec
- Expected chisquare implemented for fits with xerrors 
- New implementation of the covariance of two observables which employs the
  arithmetic mean of the integrated autocorrelation times of the two
  observables. This new procedure has proven to be less biased in simulated
  data and is also much faster to compute as the computation time is of O(N)
  whereas the evaluation of the full correlation function is of O(Nlog(N)).
- Added function `gen_correlated_data` to `misc` which generates a set of
  observables with given covariance and autocorrelation.

### Fixed
- Bias correction hep-lat/0306017 eq. (49) is no longer applied to the
  exponential tail in the critical slowing down analysis, but only to the part
  which is directly estimated from rho. This can lead to slightly smaller
  errors when using the critical slowing down analysis. The values for the
  integrated autocorrelation time tauint now include this bias correction (up
  to now the bias correction was applied after estimating tauint). The errors
  resulting from the automatic windowing procedure are unchanged.

## [0.8.1] - 2020-06-09
### Fixed
- Bug in fits.standard_fit fixed which occurred when attempting a fit with zero
  degrees of freedom.

## [0.8.0] - 2020-06-05
### Added
- `merge_obs` function added which allows to merge Obs which describe different replica of the same observable and have been read in separately. Use with care as there is no safeguard implemented which prevent you from merging unrelated Obs.
- `standard fit` and `odr_fit` can now treat fits with several x-values via tuples.
- Fit functions have a new kwarg `dict_output` which allows to change the
  output to a dictionary containing additional information.
- `S_dict` and `tau_exp_dict` added to Obs in which global values for individual ensembles can be stored.
- new function `read_pbp` added which reads dS/dm_q from pbp.dat files.
- new function `extract_t0` added which can extract the value of t0 from .ms.dat files of openQCD v 1.2

### Changed
- When creating an Obs object defined for multiple replica/ensembles, the given names are now sorted alphabetically before assigning the internal dictionaries. This makes sure that `my_Obs` has the same dictionaries as `my_Obs * 1` (`derived_observable` always sorted the names). WARNING: `Obs` created with previous versions of pyerrors may not be completely identical to new ones (The internal dictionaries may have different ordering). However, this should not affect the inner workings of the error analysis.

### Fixed
- Bug in `covariance` fixed which appeared when different ensemble contents were used.

## [0.7.0] - 2020-03-10
### Added
- New fit funtions for fitting with and without x-errors added which use automatic differentiation and should be more reliable than the old ones.
- Fitting with Bayesian priors added.
- New functions for visualization of fits which can be activated via the kwargs resplot and qqplot.
- chisquare/expected_chisquared which takes into account correlations in the data and non-linearities in the fit function can now be activated with the kwarg expected_chisquare.
- Silent mode added to fit functions.
- Examples reworked.
- Changed default function to compute covariances.
- output of input.bdio.read_mesons is now a dictionary instead of a list.

### Deprecated
- The function `fit_general` which is based on numerical differentiation will be removed in future versions as new fit functions based on automatic differentiation are now available.

## [0.6.1] - 2020-01-14
### Added
- mesons bdio functionality improved and accelerated, progress report added.
- added the possibility to manually supply a jacobian to derived_observable via the kwarg `man_grad`. This feature was not implemented for the user, but for internal optimization of most basic arithmetic operations which now do not require a call to the autograd package anymore. This results in a speed up of 2 to 3, especially relevant for the multiplication of large matrices.

### Changed
- input.py and bdio.py moved into submodule input. This should not affect the user API.
- autograd.numpy was replaced by pure numpy wherever it was possible. This should result in a slight speed up.

### Fixed
- fixed bias_correction which broke as a result of the vectorized derived_observable.
- linalg.eig does not give an error anymore if the eigenvalues are complex by just truncating the imaginary part.

## [0.6.0] - 2020-01-06
### Added
- Matrix pencil method for algebraic extraction of energy levels implemented according to [Y. Hua, T. K. Sarkar, IEEE Trans. Acoust. 38, 814-824 (1990)](https://ieeexplore.ieee.org/document/56027) in module `mpm.py`.
- Import API simplified. After `import pyerrors as pe`, some submodules can be accessed via `pe.fits` etc.
- `derived_observable` now supports functions which have single- or multi-dimensional numpy arrays as input and/or output (Works only with automatic differentiation).
- Matrix functions accelerated by using the new version of `derived_observable`.
- New matrix functions: Moore-Penrose Pseudoinverse, Singular Value Decomposition, eigenvalue determination of a general matrix (automatic differentiation included from autograd master).
- Obs can now be compared with < or >, a list of Obs can now be sorted.
- Numerical differentiation can now be controlled via the kwargs of numdifftools.step_generators.MaxStepGenerator.
- Tuned standard parameters for numerical derivative to `base_step=0.1` and `step_ratio=2.5`.

### Changed
- Matrix functions moved to new module `linalg.py`.
- Kolmogorov-Smirnov test moved to new module `misc.py`.

## [0.5.0] - 2019-12-19
### Added
- Numerical differentiation is now based on the package numdifftools which should be more reliable.

### Changed
- kwarg `h_num_grad` changed to `num_grad` which takes boolean values (default False).
- Speed up of rfft calculation of the autocorrelation by reducing the zero padding.
