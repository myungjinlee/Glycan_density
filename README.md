# GLYCO

GLYCO (GLYcan COverage) is a program to calculate glycan coverage of glycoproteins (e.g., number of glycan atoms per protein surface residues/epitope residues).

**1. Before you run GLYCO: There are some requirements you may have to check before running the program.<br />**
   - 1.1. FreeSASA (https://freesasa.github.io/) and python3 should be installed.<br />
   - 1.2. Coordinate section of input PDB files should follow the standard ATOM/HETATM record format of PDB (http://www.wwpdb.org/documentation/file-format-content/format33/sect9.html) from column 1 to 54.<br />
   - 1.3. Protein residues in PDB files should be named as below. Especially, please check if your histidine is defined as one of below histidine names.<br />
    ALA ARG ASN ASP CYS GLN GLU GLY HSD HID HIS HIE ILE LEU LYS MET PHE PRO SER THR TRP TYR VAL<br />

**2. Download GLYCO** 

**3. Run GLYCO<br />**
   - GLYCO takes the following arguments. Depending on the module and number of frames you have, the required arguments vary:<br />
    
   | Argument      | Input                                      | Requirement                  |
   | ------------- |--------------------------------------------| :----------------------------|
   | -pdb          | pdbname.pdb                                | mandatory when single PDB    |
   | -in_folder    | Input folder that has multiple PDBs        | mandatory when multiple PDBs |
   | -cutoff       | cutoff to analyze glycan in Angstrom       | mandatory                    |
   | -module       | module name (res or ep)                    | mandatory                    |
   | -glycan       | list of glycan names with comma separators | mandatory                    |
   | -out_folder   | output folder name to save result          | mandatory                    |
   | -freesasa     | path of FreeSASA executable                | mandatory when module "res"  |
   | -epitope      | file that has a list of epitope residues   | mandatory when module "ep"   |
   | -probe         | probe radius to define surface             | optional (1.4 A by default)   |
   | -sur_cutoff    | cutoff to define surface                   | optional (30 A^2 by default)  |
   | -num_parallel  | (if multiple PDBs) number of frames to submit in parallel   | optional (1 by default)   |
   | -num_proc_in   | (if multiple PDBs) number of CPU cores        | optional (maximum number of cores by default)|
   | -average       | (if multiple PDBs from the same structure) no input | optional  |
     
   If you want to ignore silence warnings from Python, you could use ``` python3 -W glyco.py ``` and add argments after that.<br />
   
   - 3.1. A single frame (PDB)<br />
     - 3.1.1. Glycan atoms of each residue -  module: "res":<br />
     
       - Count number of glycan atoms for each surface residue on your protein<br />
       ```
       python3 glyco.py -pdb 5fyl.pdb -cutoff 20 -module res -glycan BMA,AMA,BGL -freesasa /home/lee/freesasa -out_folder output 
       ```
       You can add arguments ```-probe -sur_cutoff -num_proc_in  ```as needed. <br />
       
       - Output<br /> 
       -- 5fyl_res_glycount.txt: number of glycan atoms per residue<br />
       -- 5fyl_bfactor.pdb: PDB file with glycan atoms as b-factor (You can visualize it with PyMOL.) <br />
       
     - 3.1.2. Glycan atoms of epitope residues - module: "ep":<br />
       
       - Calculate number of glycan atoms of epitope residues<br />
       ```
       python3 glyco.py -pdb 5fyl.pdb -cutoff 20 -module ep -glycan BMA,AMA,BGL -epitope epitope.txt -out_folder output
       ```
       You can add argument ```-num_proc_in  ```as needed. <br />
       *epitope.txt should be in the following format: residue name, chain ID, residue number<br />
         (epitope.txt)<br />
          ARG&nbsp; A&nbsp; 309<br />
          THR&nbsp; A&nbsp; 200<br />
          MET&nbsp; B&nbsp; 196<br />
          ASP&nbsp; C&nbsp; 305<br />
       
        - Output<br /> 
        -- ep_glysum.txt: summation of number of glycan atoms of the input epitope residues. This value excludes the overlapped, redundant glycan atoms shared among an epitope. <br /><br />
       Once you calculate buried surface area of your epitope and divide ep_glysum by the buried surface area, you can get epitope-glycan coverage. Calculating buried surface area is not provided by GLYCO, but there are many ways you can estimate the epitope-buried surface area such as Pisa(https://www.ebi.ac.uk/msd-srv/prot_int/cgi-bin/piserver) or making your own script with FreeSASA output. 
 
   - 3.2. Multiframes: If you have multiple frames of pdb files, you can submit multiple jobs in parallel.<br />
     - 3.1.1. Glycan atoms of each residues - module: "res":<br />
       - Count number of glycan atoms<br />
        *Input PDBs should be named as PREFIX_INDEX.pdb (e.g., frame_1.pdb, frame_2.pdb).<br />
        *(num_proc_in x num_parallel) should not exceed the total/available number of CPUs in your system.<br />
       ```
       python3 glyco.py -in_folder input -cutoff 20 -module res -glycan BMA,AMA,BGL -freesasa /data/leem/freesasa -num_proc_in 22 -num_parallel 2 -out_folder results -average
       ```
       - Output<br /> 
        -- PREFIX_INDEX_res_glycount.txt: number of glycan atoms per residue <br />
        -- PREFIX_INDEX_bfactor.pdb: PDB file with glycan atoms as b-factor (You can visualize it with PyMOL.) <br />
        -- ave_res_glycount.txt: averaged number of glycan atoms per residue <br /> 
     
     - 3.1.2. Glycan atoms of epitope regions - module: "ep":<br />
       - Count number of glycan atoms per epitope residue
         
       ```
       python3 glyco.py -in_folder input -cutoff 20 -module ep -glycan BMA,AMA,BGL -num_proc_in 28 -num_parallel 1 -epitope /home/leem/glyco/multiframes/epitope/epitope.txt -out_folder results -average
       ```
       - Output<br /> 
        -- ave_ep_glycount.txt: averaged number of glycan atoms per epitope <br />  
       
 Please report any bugs or questions to Myungjin Lee, Ph.D. (myungjin.lee@nih.gov)
      
