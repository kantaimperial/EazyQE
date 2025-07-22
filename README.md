# EazyQE
Bash scripts for producing the input files and analyze/plot the results for Quantum Espresso.

## Installation
```
git clone https://github.com/kantaimperial/EazyQE.git
```
After installing EasyQE, add the PAHT to .barhrc and source it.

## Find converged `K_POINTS` and `ecut`

### generate input file from cif
```
cif2in  XXX.cif
```
edit INCAR.in as you want

### single-shot callurations for convergence test
```
mkdir input
cp INCAR.in input
cd input
generate_config
#modify CONFIG
convergence_test
collect_qe_energies (get the converged K_POINTS and ecut (1mev/atom))
```
This process is referenced to [vaspup2.0] (https://github.com/kavanase/vaspup2.0)
