# EazyQE
Bash scripts for producing the input files and analyze/plot the results for Quantum Espresso.

Calculation Map for Quantum Espresso
<img width="1079" height="610" alt="image" src="https://github.com/user-attachments/assets/2ed5baca-74b5-4bf6-87b9-c49e6b1cc330" />


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
<img width="535" height="309" alt="image" src="https://github.com/user-attachments/assets/d2af1545-bd55-4069-8bd2-238564ff7d56" />



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
qe_bandgen INCAR.in (qe_bandgen will be updated)
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
<img width="480" height="322" alt="image" src="https://github.com/user-attachments/assets/84a2d8b1-9539-49c9-b425-262ea871960a" />



