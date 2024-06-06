## rdkit-pyrosetta-compare: Comparing pose and affinity prediction of ligands from constrained energy minimisation to inspiration fragments

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