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

* magic - layout, DRC validation
* xschem - create a schematic
* netgen - LVS Validation
* ngspice - Simulation Validation

#### The libraries supported by open_pdks are:

* Digital standard cells (ex: sky130_fd_sc_hd) 

![image](https://user-images.githubusercontent.com/115495342/195986137-21b9cead-e72c-43fe-a7bc-998a9dc9ec20.png)

* Primitive devices/analog (ex: sky130_fd_pr)

![image](https://user-images.githubusercontent.com/115495342/195986168-b850abac-e422-4523-964a-76f2f817f0f1.png)

* I/O cells (ex: sky130_fd_io)

![image](https://user-images.githubusercontent.com/115495342/195986195-ff5cf470-f237-4e4f-9ba7-68f1d6729f86.png)

* 3rd party libraries (ex: sky130_ml_xx_hd)

![image](https://user-images.githubusercontent.com/115495342/195986212-294d7d76-f805-4e21-bc7b-67795b969b5e.png)

Open_PDKs uses a common installed filesystem structure, where the SkyWater PDKs are placed under the directory like ```/usr/share/pdk/sky130A/```. Under this main SK130 PDK directory, are two sub-directories ```libs.tech```, which contains all subdirectories for the open source tool setups, and ```libs.ref```, which contains the reference libraries in various formats. The project directory follows a similar format, with a ```project_root/``` directory containing subdirectories for each tool or flow needed.

#### Sky130 PDK Metal Stack
The thickness of the metal layers of the Sky130 PDK are progressive, ranging from the top metal 5 layer, which is 1.26 µm, to the lowest metal 1 layer, which is 0.36 µm. TiN (Titanium Nitride), often known as LI or local interconnect, is a 0.1-m layer that sits above polysilicon and beneath the metal 1 layer.

![image](https://user-images.githubusercontent.com/115495342/195986447-c2ddf388-d67f-41c1-a2ec-c60346682bf3.png)

### Physical Verification and Design Flows
 Physical verification is a sign-off flow after Physical Design is used to check whether you have a mask layout that matches what you think the circuit should be. The general design flow for physical verification is given below.

![image](https://user-images.githubusercontent.com/115495342/195988092-b6f963af-ad28-4a33-9167-3a37c628ba96.png)

The two major steps involved in physical verification are 
* Design Rule Checking - used to ensure that your layout matches all the rules provided by the foundry for that specific process, and 
* Layout Vs. Schematic - which is to ensure that your layout netlist matches with your schematic netlist.


### Lab 1 - Checking Tool Installations in the open-source plaform

1. Magic: It can be run by giving the command ```magic``` in the command line interface/terminal. This gives two window will be popped up one is layout1 and other one is console is a tcl interpreter used to run commands for layout and to perform actions in the layout1 window. 
![image](https://user-images.githubusercontent.com/115495342/195988307-9c4432a9-2f1f-4f7d-9047-a9d743f73941.png)

We can also get the tcl interpreter in the terminal itself instead of the seperate console window by using the option ```magic -noconsole```. 
![image](https://user-images.githubusercontent.com/115495342/195988538-3aaf0149-2de8-4d17-b0a6-e00d2fc58c30.png)

Magic can also be run without the graphics layout window using the option ```magic -dnull - noconsole```, and should be called as such when running from a script.
![image](https://user-images.githubusercontent.com/115495342/195988549-1ec0356e-1a6b-4a63-8ccb-e46fc104f4e2.png)

To run magic in batch mode, create a file called ```test.tcl``` use the command ```magic -dnull -noconsole filename.tcl```.
![image](https://user-images.githubusercontent.com/115495342/195989620-ea05fcaf-0d6f-40e2-9589-99c7c24ad903.png)

2. Netgen: To run Netgen using the command ```netgen``` in the terminal. It is completely command driven and has no graphics interface. 
![image](https://user-images.githubusercontent.com/115495342/195989440-26ad7bfd-0358-464f-affe-feb6a643ab4d.png)

We can get the tcl interpreter in the terminal itself instead of the seperate console window by using the option ```netgen -noconsole```.
![image](https://user-images.githubusercontent.com/115495342/195989479-431717c9-5026-4624-a1af-2da87b6f031d.png)

To run netgen in batch mode, cab use the command ```netgen -batch source filename.tcl```. 
![image](https://user-images.githubusercontent.com/115495342/195989573-eb522f3f-12a2-42b2-8114-1f8544a45e0c.png)

Netgen can also provides a GUI window which is written in python script that can be accessed using path ```usr/local/lib/netgen/pyhton/lvs_manager.py```, though this interface hides many useful options that cannot be accessed with just this window itself.

![image](https://user-images.githubusercontent.com/115495342/195989713-055d2f5c-11e7-4d01-9131-d9e56d1a1eeb.png)

3. Xschem: It can be accessed using the command as ```xschem``` in the terminal and this should bring up a schematics window. 
![image](https://user-images.githubusercontent.com/115495342/195989779-935a03dc-8dab-45ae-a345-ff6f7f438883.png)

Xschem can be run in batch mode with the command ```xschem --tcl filename.tcl -q```.
![image](https://user-images.githubusercontent.com/115495342/195989823-25f687cc-51ac-48df-a61a-068c4dc9206f.png)

4. Ngspice: It can be used by run using the command ```ngspice``` in the linux command line. Ngspice has its own prompt and runs its own set of interpreter commands that aren't based on tcl.
![image](https://user-images.githubusercontent.com/115495342/195989873-e5d4672d-effb-4f5b-ae09-5419b86cce4e.png)

It can be run in batch mode using the option ```ngspice -b```.

![image](https://user-images.githubusercontent.com/115495342/195989928-5cf0bbc5-499b-4b07-9884-f11f58136a21.png)

Let's use an inverter design as a simple example to become familiar with these open source tools. For each of the open source tools be using, should first create a directory called ```inverter``` for the design and initialise subdirectories under that directory. 
![image](https://user-images.githubusercontent.com/115495342/195990267-50ddfd3f-af06-4bfc-aed8-ba2cdc25e50d.png)

Later should set up each directory for its respective tool to run properly with the SkyWater PDKs. This can be done by creating a symbolic link between the just initialised su-bdirectories and the SKY130 submodules created with the open_pdks installer. This is done using the command ```ln -s /file_path``` in the desired sub-directory. As we will run ngspice simulations using xschem, so we setup ngspice under the xschem subdirectory itself.
![image](https://user-images.githubusercontent.com/115495342/195990382-a961e295-718a-483d-9198-923c012d74a4.png)

Let's use the command ```xschem``` to launch Xschem. You are then introduced to the SKY130 devices on the dashboard for xschem that appears with lot of example schematic symbols, as seen below. By selecting the appropriate rectangle and pressing the E key on the keyboard, users can access examples. By using CTRL+E, the menu can be accessed once again. The schematic can be resized with the F key to fit the window.
![image](https://user-images.githubusercontent.com/115495342/195990566-1dea2a92-0fab-415f-adab-bcb470d4c175.png)

Now, can able to check the magic installation using the command ```magic``` in the /mag sub-directory. This should bring up the two magic windows, with the layout window displaying "Technology: sky130A", along with many colors and icons displaying the available layers in this technology, as show below.
![image](https://user-images.githubusercontent.com/115495342/195990656-78d2ac66-caa7-4596-b068-ed96e5a1656b.png)

Use the command ```magic -d XR``` to invoke a graphics package which uses 3D acceleration to get better rendering than the default graphics. There is also an OpenGL based graphics package that can be accessed using ```magic -d -OGL```. 
![image](https://user-images.githubusercontent.com/115495342/195990772-efa09f1d-0ec9-49fd-a24a-bbe7117c1a1a.png)

Magic shortcuts that are useful
1. Cursor box adjustments are made with the left and right mouse buttons.
2. Zoom out with Shift + Z.
3. selecting a layer with the middle mouse button or P (also known as painting)
4. E to Erase anything in the cursor box by pressing the key (can also be done by clicking the middle mouse button on an empty part of the layout)
5. You can view the complete layout with V.
6. Pressing CTRL+P displays the device's parameter settings.
7. In order to pick layers, press the S key.
8. You can access information about the selected layer by using the what command in the magic console. You can type commands in the magic console without switching between windows as long as you press the Enter key first.
9. The I key can be used to choose a device, while the M key moves them

Now create devices using the two Devices drop down buttons. Let us select the nmos (mosfet) under "Devices 1" and set the width to 2 um, length to 0.5 um and fingers to 3. This should result in the following device being created.
![image](https://user-images.githubusercontent.com/115495342/195991144-a2aee167-c535-4cfe-ade4-956ac3578305.png)

Later in layout1 window press 'v' to view
![image](https://user-images.githubusercontent.com/115495342/195991230-a726fa47-166c-45cc-9eaa-8e833e6f4b07.png)

When uncheck the ```add guard ring``` option
![image](https://user-images.githubusercontent.com/115495342/195991275-8f866392-afec-4ea6-8891-f9caa684780c.png)

By selecting the layers, type ```what``` we can get name of the selected layers
![image](https://user-images.githubusercontent.com/115495342/195991361-975ec6d4-b478-46f1-95a2-46b4a0b26645.png)

### Lab: Creating an Inverter Design in transistor level

Let's launch xschem and select File to open a new schematic. To access SkyWater components, select the SkyWater library directory path and then pick the ```fd_pr_library```. 
![image](https://user-images.githubusercontent.com/115495342/195991631-c5b9e5c9-3472-4519-a35e-c88473e93ffb.png)

We require a fundamental nfet and pfet to build an inverter. Therefore, choose a pfet3_0.1v8.sym  and nfet3_0.1v8.sym  device from the insert window and then locate it wherever you choose in the schematic.
![image](https://user-images.githubusercontent.com/115495342/195991654-a7e789c8-a5d6-487c-820e-31b39a486212.png)













