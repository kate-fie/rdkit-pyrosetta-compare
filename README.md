## rdkit-pyrosetta-compare
## Comparing pose and affinity prediction of ligands from constrained energy minimisation to inspiration fragments

**Purpose:** Compare RDKit and PyRosetta for pose and affinity prediction of ligands calculated by constrained energy 
minimisation to inspiration fragments to understand if RDKit and PyRosetta placements can be used interchangeably. 

**Dataset:** 69 ligands (designed manually from a fragment screen), 1 protein, 10 repeat placements of each ligand (690 total placements), 
found in [data/](data/).
Placements are pickled in tar.gz file. Each folder of placement method contains 690 placements named with inchi key of ligand. 
The important files for each placement are:
- `.holo_minimised.pdb`: The minimised protein and ligand. 
- `.minimised.json`: Contains calculated energy and RMSD values to the fragments.
- `.minimised.mol`: Minimised ligand. 

**Method:**
Both placements were carried out using the package [Fragmenstein](https://github.com/matteoferla/Fragmenstein) (I highly
recommend checking it out if you are interested in fragment-based drug design!). They were run with [run_place.py](to_place/run_place.py)
on an HPC cluster.

For both methods a pre-generated conformer is created and minimised (different than docking where some tools use MCMC sampling of conformers).
This conformer is created by mapping the atoms in the ligand to fragments and restricting their coordinates to match the fragments.

*rdkit_place:* A pre-generated conformer of the ligand is placed in the fixed protein neighbourhood 5 Å from the ligand's 
centre of mass and expands any aromatic atoms in the protein to full aromatic ring. The ligand is minimised with RDKit's MMFF94 forcefield. 
The protein is kept fixed. Read in more detail [here](https://www.blopig.com/blog/2023/11/the-workings-of-fragmensteins-rdkit-neighbour-aware-minimisation/)

*pyrosetta_place:* A pre-generated conformer of the ligand is placed in the protein and minimised using PyRosetta with 
the ref2015 scorefunction. The protein is *not* fixed.

**Results:**
Results were gathered in [get_results.ipynb](get_results.ipynb). To execute the notebook and generate results yourself:
1. Set the `RUN_EVERYTHING = True`
2. Configure `RDKIT_PYROSETTA_PATH` environment variable
   3. Add the following line to your bash or zshrc profile: `export RDKIT_PYROSETTA_PATH=👾👾👾/rdkit-pyrosetta-compare` where 👾👾👾 is the home path.

**Analysis:**
[rdkit-pyrosetta-compare.ipynb](rdkit-pyrosetta-compare.ipynb)

In any task in the realm of virtual screening, there need to be many filters applied to a dataset of ligands to downselect
the 'best' ones on a number of parameters to produce a manageable size. One popular filter is if a compound has a physical
pose and good affinity as predicted by tools such as docking or energy minimisation. In my pipeline for downselecting 
elaborations of compounds proposed as fragment follow-ups, I calculate the pose and ΔΔG by energy minimizing the ligand
with atom restraints to matching atoms in the fragment inspiration. I either use RDKit using its MMFF94 forcefield 
or PyRosetta using its ref2015 scorefunction, all made possible by the lovely tool [Fragmenstein](https://github.com/matteoferla/Fragmenstein). 

With RDKit as the minimizer the protein neighborhood around the ligand is fixed and placements take on average 21 s whereas
with PyRosetta placements, they take on average 238 s (and I can run placements in parallel luckily). 
I would ideally like to use RDKit as the placement method since it is so fast and I would like to perform 500K within a few days
but, I wanted to confirm that RDKit is 'good enough' compared to the slightly more rigorous tool PyRosetta (it allows 
residues to relax and samples more conformations with the longer runtime I think). 

To find out if I can trust RDKit's placement with the MMFF94 forcefield, check out my analysis of 10 repeated placements 
of 69 fragment follow-up ligands with 1 protein template. I wrote out the notebooks so you can easily follow my calculations of 
ligand and residue RMSD between methods and checks of the conformations with PoseBusters, outputting some surprising results!
And if you're running into the same questions of comparing pose and affinity prediction methods, I hope you find the code useful. 

