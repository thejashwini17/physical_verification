# Physical Verification using SKYWater 130nm


## What this workshop provides:

* This workshop aims to provide a thorough understanding of the Physical Verification methodology using Magic, NetGen, and OpenLane Flow in order to be able to correct the various issues that arise during the design cycle as Design Rule Check (DRC) or Layout vs Schematic (LVS) violations. We also go into great detail on the Sky130 Process Design Kit (PDK) over the course of 5 days. Every topic in the course had challenging labs that were completed on the VSD-IAT platform on the Sky130 PDK.

**Organized by**: Kunal Gosh, Co-founder, VSD Corp. Pvt. Ltd. <br />
**Instructor** : Tim Edwards, works as Senior Vice President for Efabless and a open tools developer. <br />
**Assisted by**- Sumanto Kar, Sr. Project Technical Assistant, IIT Bombay. <br />
**Sponsored by**: Google <br />

### Course contents:

#### Day 1: Introduction to Sky130
* Introduction to Skywater PDK
* Opensource EDA Tools
* Understanding Skywater PDK (Devices, Layers, Libraries)
* Opensource Tools And Flows
#### Day 2: DRC/LVS Theory
* Understanding GDS Format
* Extraction Commands, Styles and Options In Magic
* Advanced Extraction Options In Magic
* GDS Reading Option In Magic
* GDS Writing, Input, Output Styles and Output Issues
* DRC Rules In Magic
* Extraction Rules And Errors In Magic
* LVS Setup For Netgen
* Verification By XOR
#### Day 3: Frontend & Backend DRC
* Introduction To Basic Silicon Manufacturing Process
* Backend Metal Layer Rules
* Local Interconnect Rules
* Front-End Rules, Transistors Implants, ID and Boundary Layers, Wells And Same Net Rules
* Deep N-Well And High Voltage Rules
* Device Rules
* Miscellaneous Rules Latch-up Antenna Stress Rules
* Density Rules
#### Day 4: PNR and Physical Verification
* The OpenLANE Flow
* RTL2GDS For Demo Design
* Interactive OpenLANE Run
* Interactive OpenLANE Run - Final Steps
* Techniques To Avoid Common DRC Errors
* Techniques To Manually Fix Violations
#### Day 5: Running LVS
* Physical Verification Of Extracted Netlist
* How LVS Matching Works
* LVS Netllist Vs Simulation Netlists
* The Netgen Core Matching Algorithm
* Netgen Prematch Analysis, Hierarchical Checking And Flattening
* Pin Checking And Property Checking
* Series Parallel Combining
* Symmetry Breaking
* Interpreting Netgen Results

### Day 1: Sky130 PDK Overview
The SkyWater PDK is the result of a partnership between Google and SkyWater Technology Foundry. It offers a completely open source Process Design Kit (PDK), making it possible for the first time to use libraries and setups for open source tools built around a real manufacturable process that can be used to fabricate real designs at SkyWater's facility. This repository is aiming towards the SKY130 process node, with 130 designating the minimum feature length (length of the smallest transistor that can be manufactured in the process) of 130 nanometers.

Several open repositories make up the SkyWater Open PDK, primarily:
* [PDK Library and Files] (https://github.com/google/skywater-pdk) 
* [Documentation] (https://skywater-pdk—136.org.readthedocs.build)

#### Steps to install Sky130 PDK
1. Clone the repository in the terminal  
```git clone "https://github.com/RTimothyEdwards/open_pdks"```  
2. Enter the open_pdks folder using command 
```cd open_pdks```  
3. Tell open_pdks to compile and install the Sky130 PDK from the Google Skywater sources  
```configure --enable-sky130-pdk```  
4. Run make to automatically grab the SkyWater and a few other 3rd-party repositories and submodules from github using 
```make```
5. Build libraries from the various individual files and repositories.  
```sudo make install``` 

#### The tools which are used in the workshop:

* magic 
* xschem
* netgen
* ngspice

#### The libraries supported by open_pdks are:

* Digital standard cells (ex: sky130_fd_sc_hd) 
![image](https://user-images.githubusercontent.com/115495342/195986137-21b9cead-e72c-43fe-a7bc-998a9dc9ec20.png)
* Primitive devices/analog (ex: sky130_fd_pr)
![image](https://user-images.githubusercontent.com/115495342/195986168-b850abac-e422-4523-964a-76f2f817f0f1.png)
* I/O cells (ex: sky130_fd_io)
![image](https://user-images.githubusercontent.com/115495342/195986195-ff5cf470-f237-4e4f-9ba7-68f1d6729f86.png)
* 3rd party libraries (ex: sky130_ml_xx_hd)
![image](https://user-images.githubusercontent.com/115495342/195986212-294d7d76-f805-4e21-bc7b-67795b969b5e.png)

