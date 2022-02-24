The angle analysis algorithms were run in MATLAB 2018b.

Depending on the ensemble size, the analysis time of all but analyseSelectedStructures_selectBases.m varied from several minutes 
(DNA_F1F2_ensemble) to 10 hours on a normal desktop computer. The algorithm analyseSelectedStructures_selectBases.m has a runtime
of several minutes for all ensembles. To perform only this part of analysis, .pdb file with selected structures 
(e.g. DNA_F1F2_ensemble_7_selected_with_E086) can be used directly to obtain the kinking angles as in the manuscript.

All the input parameters are specified in 'ParametersForSimulations.xlsx' file.

To perform the complete angle analysis, four individual MATLAB alogrithms should be used in the order as specified below.



1. producePriors.m

This algorithm generates priors (accessible volume) of the donor and acceptor dyes.
Open the algorithm in editor mode and specify the atom IDs (the order number 
of the atom within the DNA sequence of the respective ensemble to which the dye is attached) 
by uncommenting (remove %) one of the three options:

% [donor, acceptor] 
% atomIDs = [1164,565]; % DNA only 
% atomIDs = [2935,2336]; % F2+DNA
% atomIDs = [4522,3923]; % F1F2+DNA 

The numbers indicated in the squared brackets correspond to donor and acceptor IDs. In our DNA 
construct, the donor dye Atto550 is attached via C2 linker to C7 of Thymine 37 (atomID 1164, 2935 and 
4522 in the respcective ensemble) and the acceptor dye Alexa647 is attached via C2 linker to C7 of 
thymine 18 (atomID 565, 2336 and 3923 in the respective ensemble).Dye parameters which are the same for 
all three structural ensembles (DNA_ensemble, DNA_F2_ensemble and DNA_F1F2_ensemble) should be also specified: 

dyeDiamD = 12;
dyeDiamA = 12;
linkerDiam = 4.5;
linkerLengthD = 13;
linkerLengthA = 13;
skelDist = 2;

Click on 'run' button. The prompt will request 'load pdb file'. Load the respective .pdb file (e.g. when 
atomIDs = [4522,3923]; % F1F2+DNA was uncommented load the DNA_F1F2_ensemble.pdb). When the analysis is finished in the
'select file to write' prompt, name the resulting .mat file (e.g. DNA_F1F2_priors) and save it.



2. simulateFRETOfStructures.m
	
This algorithm calculates and sorts the FRET efficiencies using the priors (accessible volumes of the dyes) information 
from previous algorithm. Open the algorithm in editor mode and input the following parameters:


Riso = 7.0; % isotropic Förster radius (nm)
trajN = 5000; % number of donor excitations
seidelAss = false; % dynamic and unrestricted averaging in orientation and static position averaging 
randAx = true; % random cone axes for donor and acceptor

% acceptor
kRA = 1/0.70; % rate of rotation = 1 / rotational correlation time (ns)
depA = 0.126/0.4; % depolarization factor = rinf / 0.4

% donor
kD = 1/3.53; % rate of depopulation of excited donor in absence of acceptor = 1 / fluorescence lifetime of donor in absence of acceptor (ns)
kRD = 1/1.34; % rate of rotation = 1 / rotational correlation time (ns)
depD = 0.042/0.4; % depolarization factor = rinf / 0.4

Click on 'run' button. The prompt will request to 'load priors'. Load the priors .mat file from previous algorithm (e.g. DNA_F1F2_priors.mat).
When the analysis is finished, the prompt 'select file to write' will appear. Name the file (e.g. DNA_F1F2_ensemble_effciencies.mat) and 
save it. Additionally a figure will appear showing the range of FRET efficiencies of the respective structural ensemble.



3. selectStructures.m

This algorithm compares the experimentally obtained FRET efficiency and simulated FRET efficiency in simulateFRETOfStructures.m and then 
selects only those structural models from the respective ensemble which have the FRET efficiency corresponding to the experimental 
efficiency (input parameter) within 5% error. Open the algorithm in editor mode and input the respective FRET efficiency 
(0.50 for DNA, 0.69 for DNA in presence of F1F2 and 0.86 for DNA in presence of F1F2) and 5% error:

expData = 0.86; % for DNA+F1F2 
sigma = 0.05;

Click on 'run' button. The prompt will request 'FRET efficiencies' from previous algorithm (e.g. DNA_F1F2_ensemble_effciencies.mat) and 
immediately after, another prompt 'load pdb file' will request the .pdb file of the respective structural ensemble 
(e.g. DNA_F1F2_ensemble.pdb). When the analysis is finished, the prompt 'select file to write' will appear to name and save 
the resulting .pdb file (e.g. DNA_F1F2_ensemble_7_selected_with_E086).

Note, for DNA_ensemble and DNA_F2_ensemble, this algorithm can be run with different expData (i.e. experimental FRET efficiency; 
expData is varied: 0.5, 0.69 and 0.86 for DNA_ensemble (Figure 4) and 0.69 and 8.86 for DNA_F2_ensemble (Supplementary Sigure S4)).



4. analyseSelectedStructures_selectBases.m

This algorithm calculates the angle between the 3'- and 5'-stems of the DNA construct for structures selected in selectStructures.m.
As described in the 'Methods' part, the DNA axes are defined the from the nick to the tetraloop considering all atoms 
belonging to the respective stem, but omitting the four bases of the tetraloop and the base pair next 
to the loop (DNA schematics and sequence in Figure 1b). Open the algorithm in editor mode and input the residue number 
to define the axes of the DNA stems by specifying the respective residue number:

ax5Start1 = 1; % first residue of strand 1 of the 5'stem
ax5End1 = 10;   % last residue of strand 1 of the 5'stem
ax5Start2 = 17; % first residue of strand 2 of the 5'stem
ax5End2 = 26;   % last residue of strand 2 of the 5'stem

ax3Start1 = 27; % first residue of strand 1 of the 3'stem
ax3End1 = 41;   % last residue of strand 1 of the 3'stem
ax3Start2 = 48; % first residue of strand 2 of the 3'stem
ax3End2 = 62;   % last residue of strand 2 of the 3'stem

Click on 'run' button. The algorithm will calculate the mean angle theta between the 3'- and 5'-stem of the DNA 
and the standard deviation. Two types of mean angle theta will be calculated: 

meanRingtheta (red region in the figure in Supplementary Figure S12)  
meanOritheta (green circle in Supplementary Figure S12).

meanRingtheta is for DNA angles in presence of protein (DNA+F2 and DNA+F1F2) and meanOritheta is for angles 
corresponding to DNA alone. The resulted angles are displayed in generated figures: angle distribution 
(as in Figure 4c, Supplementary Figures S4c, S9c, S10c and S11c), selected structures represented in unit sphere as in  
(as in Figure 4b, Supplementary Figures S4b, S9b, S10b and S11b).
