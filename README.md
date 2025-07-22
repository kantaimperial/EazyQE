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
Output example for `collect_qe_energies`
<img width="786" height="372" alt="image" src="https://github.com/user-attachments/assets/75745be1-9798-4d2d-8b31-14ee981da74c" />

This process is referencing to [vaspup2.0](https://github.com/kavanase/vaspup2.0)

## Stractural relaxation
```
Copy the converged directory
Edit the INCAR.in
(Ecut or kpoints) 
calculation = “vc-relax”
```
run the calculation

get the relaxed structure

```
extract_final_structure OUTCAR.out INCAR.in
(qe_to_POS can extract structure from INCAR.in that is readable by VESTA)
```

## DOS calculation
scf calculation with relaxed sructure
```
mkdir dos 
cp INCAR.in (after extract_final_structure) job dos/
cd dos
vim INCAR.in (calclation = “scf”)
```
run the calculation

then nscf calculation
```
vim INCAR.in 
(calclation = “nscf”, Double kpoint, smearing = tetrahedra (sc))
```
run the calculation 

Analyze the results
`get_gap OUTCAR.out`

DOS calculation and plot
```
generate_pdos_input
sbatch job_pdos
findgap XXX.dos
plot_pdos (e.g. –-atoms Bi –orbital Bi.p)
```
plotting is referencing to [sumo](https://smtg-bham.github.io/sumo/sumo-dosplot.html)

## Band calculation

scf for the band calculation
```
mkdir band
cp INCAR.in job band/
cd band
vim INCAR.in (calclation = “scf”)
```
run the calculation
```
qe_bandgen7 INCAR.in (qe_bandgen7 will be updated)
(kpoint is generated via [Pymatgen HighSymmKpath scheme](https://pymatgen.org/pymatgen.symmetry.html)
mv INCAR_band.in INCAR.in
vim INCAR.in 
(nbnd =XX from number of electrons in OUTCAR.out)
```
run the calculation

Band calculation
```
generate_band_input
sbatch job_band
```
```
plot_band band.dat --EFermi XX 
(XX is VBM value determiend by get_gap in DOS calualtion
```



