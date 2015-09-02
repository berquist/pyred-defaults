
                        README
            Documentation of February 25th, 2015


The Tripos Mol2 file format is not a correct input file format for PyRED!
The P2N file format is not a correct input file format for PyRED!

Use instead the PDB file format as input file format:
To be recognized a PDB input file has to bear a particular filename and the 
'.pdb' extension (see below)!

DO NOT FORGET TO ADD THE HYDROGEN ATOMS IN THE PDB INPUT FILE IF NEEDED!


I. The PDB file format used by PyRED

Adaptation of http://www.wwpdb.org/documentation/format33/sect9.html
COLUMNS        DATA  TYPE    FIELD        DEFINITION
-----------------------------------------------------------------------------
 1 -  6        Record name   "ATOM" (or "HETATM")
 7 - 11        Integer       serial       Atom serial number
13 - 16        Atom          name         Atom name
18 - 20        Residue name  resName      Residue name
23 - 26        Integer       resSeq       Residue sequence number
31 - 38        Real(8.3)     x            Orth. coordinates for X in Angstroms
39 - 46        Real(8.3)     y            Orth. coordinates for Y in Angstroms
47 - 54        Real(8.3)     z            Orth. coordinates for Z in Angstroms
77 - 78        LString(2)    element      Element symbol, right-justified
79 - 80        LString(2)    Formal charge on the atom

Example of PDB file composed of two conformations - requirements:
 - The different conformations are separated by the 'MODEL' keyword
 - Identical atom order for the different conformations
 - Two atoms belonging to the same residue cannot share the same name

Conformations 'gauche' and 'anti' of ethanolate:

         1         2         3         4         5         6         7         8
12345678901234567890123456789012345678901234567890123456789012345678901234567890
-----|----| ---| --|  ---|    -------|-------|-------|                      --
HETATM    1  CA1 EOH     1      1.1645 -0.4192  0.0000                       C
HETATM    2 HA11 EOH     1      2.1088  0.1164  0.0000                       H
HETATM    3 HA12 EOH     1      1.1258 -1.0533  0.8786                       H
HETATM    4 HA13 EOH     1      1.1258 -1.0533 -0.8786                       H
HETATM    5  CB1 EOH     1      0.0000  0.5509  0.0000                       C
HETATM    6 HB11 EOH     1      0.0488  1.1929  0.8777                       H
HETATM    7 HB12 EOH     1      0.0488  1.1929 -0.8777                       H
HETATM    8  O   EOH     1     -1.1890 -0.1968  0.0000                       O1-
MODEL
ATOM      1  CA1 EOH     1       1.209  -0.236  -0.019                       C
ATOM      2 HA11 EOH     1       2.067   0.424   0.060                       H
ATOM      3 HA12 EOH     1       1.268  -0.957   0.793                       H
ATOM      4 HA13 EOH     1       1.275  -0.779  -0.956                       H
ATOM      5  CB1 EOH     1      -0.090   0.553   0.045                       C
ATOM      6 HB11 EOH     1      -0.146   1.113   0.977                       H
ATOM      7 HB12 EOH     1      -0.131   1.271  -0.764                       H
ATOM      8  O   EOH     1      -1.222  -0.262  -0.110                       O1-


If present the formal charge (2 characters; exemple: 1-, 3+...) of atoms can 
be used to define the total charge of a molecule. Indeed, columns 79-80 can
be used instead of the MOLECULE'$n'-TOTCHARGE keyword (which is reported in
the Project.config file; '$n' is the molecule number) to provide the total
charge of a molecule, when it is not neutral.


The chemical element column (1 or 2 characters) allows differentiating atoms
with ambiguous atom names:
i.e. Ca (Calcium), Cd (Cadmium), Ce (Cerium), Cl (Chlorine) versus C (carbon)
  or Na (sodium), Nb (Niobium), Nd (Neodymium), Ni (Nickel) versus N (Nitrogen)
  or Si (Silicium), Sc (Scandium), Se (Selenium), Sn (Tin) versus S (Sulfur)
  or Pd (Palladium), Pt (Platinium), Pa (Protactinium) versus (Phosphorus)
  etc...

See examples below:
         1         2         3         4         5         6         7         8
12345678901234567890123456789012345678901234567890123456789012345678901234567890
-----|----| ---| --|  ---|    -------|-------|-------|                      -- 
ATOM      1  CA  GLY     1       1.218  -0.218  -0.000                       C
 -> This case corresponds to an alpha-carbon atom with the CA atom name
           
ATOM      1  CA1 UNK     1       1.218  -0.218  -0.000                      Ca2+
ATOM      1  CA2 UNK     1       3.218  -0.218  -0.000                        2+
 -> These cases correspond to two calcium atoms with the CA1 and CA2 atom names
    in a single residue
           
ATOM      1  NA  MOL     1       1.218  -0.218  -0.000                       N
 -> This is a nitrogen atom with the NA atom name within the MOL residue
           
ATOM      1  NA  NA1     1       1.218  -0.218  -0.000                      Na1+
ATOM      1  NA  NA1     2       3.218  -0.218  -0.000                        1+
 -> These correspond to two sodium atoms with the NA atom name in two different
    residues



II. The different modes of execution of PyRED

II.1 If OPT_Calc = On, MEPCHR_Calc = On (and Re_Fit = Off):

Just provide a list of PDB files with the following names
  - one PDB file per molecule (a PDB file can contain several conformations):

Mol_red$n.pdb  ('$n' is the molecule number, which is incremented
                 $n starts at 1 and not at 0)

 See examples below:

Example 1: a single PDB file is provided:
           This corresponds to a single molecule job
 Mol_red1.pdb

Example 2: five PDB files are provided: this job involves five molecules
 Mol_red1.pdb
 Mol_red2.pdb
 Mol_red3.pdb
 Mol_red4.pdb
 Mol_red5.pdb

Example 3: This job involves only three molecules
 Mol_red1.pdb
 Mol_red2.pdb
 Mol_red3.pdb
 Mol_red5.pdb (is not used as input by PyRED because
               Mol_red4.pdb is not found)


II.2 If OPT_Calc = Off, MEPCHR_Calc = On (and Re_Fit = Off):

For each PDB file at least one geometry optimization output (obtained by using
a quantum chemistry program) has to be given.
Thus the conformations of a molecule are provided in:
    - a single PDB file, and in
    - different geometry optimization outputs

See examples below:

Example 1: This corresponds to a single conformation - single molecule job:
 Mol_red1.pdb Mol_red1.log

Example 2: This job involves five molecules
 Mol_red1.pdb Mol_red1.log                                   (1 conformation)
 Mol_red2.pdb Mol_red2-1.log Mol_red2-2.log                  (2 conformations)
 Mol_red3.pdb Mol_red3.log                                   (1 conformation)
 Mol_red4.pdb Mol_red4.log                                   (1 conformation)
 Mol_red5.pdb Mol_red5-1.log Mol_red5-2.log Mol_red5-3.log   (3 conformations)


II.3 If Re_Fit = On:

If Re_Fit = On  (OPT_Calc = Off and MEPCHR_Calc = Off are forced)

A previous job has to present in the working directory:
Data of this previous job are located in the directory defined by the DIR
variable (see the 'System.config' file).



III. The System.config file

The presence of the 'System.config' file in the working directory is not
mandatory.

Information provided in the 'System.config' file are related to the tasks
performed by the PyRED program.

Adding the 'System.config' file in the working directory allows overwriting
the default tasks (OPT_Calc = On, MEPCHR_Calc = On, Re_Fit = Off, etc...) 
carried out PyRED.

In the absence of the 'System.config' file PyRED can still be executed.
However, in this case only default tasks are carried out by PyRED.



IV. The Project.config file

The presence of the 'Project.config' file in the working directory is not
mandatory.

Information provided in the 'Project.config' file are related to the molecules
involved in the PyRED job.

Adding the 'Project.config' file in the working directory allows modifying the
default information (molecule total charge, spin multiplicity, title, etc...) 
about the molecules involved in the PyRED job.

In the absence of the 'Project.config' file PyRED can still be executed.
However, in this case only default information about the molecules involved in
the PyRED job are used.



V. The frcmod.user file

The presence of the 'frcmod.user' file in the working directory is not
mandatory.

The user can provide missing or mandatory force field parameters within the 
frcmod.user file.


