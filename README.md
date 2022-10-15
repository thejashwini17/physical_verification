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

It is best practice to make the circuit sections that will be converted to a layout self-contained in order to avoid including any extra components that are not required in the layout since we want to convert the schematic to a layout. Additionally, since there is no concept of a global supply pin or network in a layout, it is wise to avoid using any specialised power supply pins. When validating layouts later, problems may arise if certain procedures are not followed.

Pins can be found in the insert window's xschem library because they are not PDK-specific. These have the filename extensions ipin.sym, opin.sym, and iopin.sym. After placing the pins and hovering over the component in the schematic window, we can move them around by pressing the M key. Component copies can be made by pressing the C key. The Del key can also be used to remove components. The W key can now be used to connect components and insert wires between them. The next step is to give each pin a sensible name by activating the parameter window with the Q key.

![image](https://user-images.githubusercontent.com/115495342/195992010-f0138b39-7040-4a2a-abb2-598a38756ca1.png)

By selecting the devices with the mouse and pressing the Q key to open the parameter window, we may modify their properties. Since sram devices are the only ones that may use the default value of 0.15, we adjust the length of the parameter window to 0.18. We can choose to have three fingers and make each one 1.5 mm wide. But considering that we have nfet to three fingers, the overall width in the parameter window needs to be set to 4.5, which is three times the finger width. It is recommended not to make unnecessary modifications to the other parameters as they are PDK-specific.
Similarly, we can adjust the parameters of the pfet to 3 fingers, width of 1 per finger, and a length of 0.18. We must specify the body to be connected to the Vdd pin as it is a 3 pin pfet.

![image](https://user-images.githubusercontent.com/115495342/195992219-91bf90ae-f7f7-4347-b66c-b56e1d72dee9.png)

![image](https://user-images.githubusercontent.com/115495342/195992140-63a18157-e07f-4a31-aafd-3c832e9f5fda.png)

Go to file --> "Save as" and save as inverter.sch and click on OK.

Our primary objective right now is to layout this circuit, but before we can do so, we must functionally validate it. To do this, we must build an independent test bench using the schematic as a symbol. Select "Make symbol from schematic" from the symbol menu. We now open a new schematic from file to initiate our testbench schematic. Press the insert key and then browse to inverter.sym in the local directory after the schematic is open.

![image](https://user-images.githubusercontent.com/115495342/195992455-f4fdf5d7-f5d2-434d-8eea-8939c8463af0.png)
![image](https://user-images.githubusercontent.com/115495342/195992572-0daf8867-e683-414f-9bef-65e86982e2c4.png)

The testbench will be fairly simple. Following the connection of the power supplies, we will generate a ramp input and observe the output response. To do this, we may add two voltage sources—one for the input and one for the supply—from the standard xschem library. These can be connected, and the supply connections can also receive a GND node. The input and output signals that we wish to view in Ngspice must then be created as "opins." We should obtain the following if everything is done correctly.
![image](https://user-images.githubusercontent.com/115495342/195992734-d62939c4-566c-407d-98aa-03a698d39339.png)

Now need to add the parameters so go to V2 and set the value to 1.8 and go to V1 and set the value to a PWL sweep as shown.
![image](https://user-images.githubusercontent.com/115495342/195992912-3d51846c-d1c5-4c05-b969-827f94ff1e70.png)

Here, the PWL function's voltage and time parameters indicate that the supply will start at 0 volts before ramping up from 20 nanoseconds to its final value of 1.8 volts at 900 nanoseconds. Two more ngspice statements must be added after that, but since they apply to all components, text boxes are required for their placement. We choose the code_shown.sym component from the xschem library in order to insert a text box.

The first one will specify the location of the device models used in the device schematic. We will use a .lib statement that selects a top level file that tells ngspice where to find all the models and also specifying a simulation corner for all the models. We shall use the typical corner with ```value = ".lib /usr/share/pdk/sky130A/libs.tech/ngspice/sky130.lib.spice tt"```.
![image](https://user-images.githubusercontent.com/115495342/195993130-d59f6386-2d57-405e-b5fe-5f39a50a1e34.png)

s2 will run a transient analysis for 1 microsecond and plot the values for Vin and Vout.

```value = ".control
tran 1n 1u
plot V(in) V(out)
.endc"
```
![image](https://user-images.githubusercontent.com/115495342/195993205-213a5d32-d26f-40c3-9324-6634a9dc1851.png)

This will tell ngspice to run a transient simulation for 1 ns and monitor voltages for the in and out pins. Finally, we should get the completed testbench schematic as follows, and save this as inverter_tb.sch.
![image](https://user-images.githubusercontent.com/115495342/195993240-78aa0856-6c70-418f-85d6-ccb4390952e0.png)

We should click the Netlist button to generate the netlist, and then we can click the Simulate button to simulate it in Ngspice. The subsequent waveform, which verifies that our schematic functions as an inverter, should be generated.
![image](https://user-images.githubusercontent.com/115495342/195993393-11b4e07b-e058-4c8f-a23a-120f763ab601.png)

Now that the working of our inverter schematic has been validated, we just need a netlist of exclusively that, not the entire test bench. Open the inverter from the files menu, choose "LVS netlist: top level is a .subsckt" from the suimulation menu, and then click netlist. This verifies if we have properly defined a sub circuit for creating a layout cell with pins in the layout. Close xschem.

Now, we can able to import the schematic to magic to create the layout. Open magic by moving to the /mag directory and using ```magic -d XR``` . Go to file --> Import SPICE and select the inverter.spice file from the xschem directory. 

![image](https://user-images.githubusercontent.com/115495342/195993860-6d3a12ba-fcc0-4e9a-9921-16f020896e98.png)

As you can see, because to the complexity of analog place and route, the schematic import does not know how to do. They must be manually wired together and placed in the ideal locations. The input, output, and supply pins can be repositioned as we start by positioning the pfet device above the nfet. What should we obtain is as following.
![image](https://user-images.githubusercontent.com/115495342/195994093-9c2585fb-96fa-451c-a745-fabc35fc7315.png)

Next, we should able to set some of the parameters that are only adjustable in the layout which will make it more handy to wire the whole layout up. First, we need to set the "Top guard ring via coverage" to 100. This will put a local interconnect to metal1 via ta the top of the guard ring. Next, for "Source via coverage" put +40 and for "Drain via coverage" put -40. This will make an impact to split the source drain contacts, making it easy to connect them with a wire. For the nfet, we set the "Bottom guard ring via coverage" to 100, while the source and drain via coverages are set to +40 and -40, respectively, like the pfet.

Later, can start to paint the wires using metal1 layers. Now, connect the source of the pfet to Vdd and source of the nfet to Vss. Similarly, connect the drains of both mosfets to the output. Finally, the input is connected to all the poly contacts of the gate. 
![image](https://user-images.githubusercontent.com/115495342/195994293-7fcf04eb-4f90-4b78-b538-e83708991e46.png)


Now, we can sav the file and select the autowrite option. Later run the following commands in the magic console.

```
extract do local
extract all
```

The first command ```extract do local``` ensures that magic writes all results to the local directory. The second command ```extract all``` does the actual extraction. As the output is in magic has its own format, but we want to simulate the netlist in spice, so use the command ```ext2spice lvs``` which sets up the netlist to hierarchical spice output in ngspice format with no parasitic components which is good for simulation but not for running lvs. Next, we run the command ```ext2spice``` which generates the spice netlist. Now can quite magic window.

To run LVS, first clear any unwanted files from the mag subdirectory. The .ext files are just intermediate results from the extraction and can be removed using the command ```rm *.ext``` if needed. Also clean up extra .mag files using the command ```/usr/share/pdk/bin/cleanup_unref.py -remove .```, which were any paramaterised cells that were created and saved but not used in the design.

Now run LVS by entering the netgen subdirectory and using the command ```netgen -batch lvs "../mag/inverter.spice inverter" "../xschem/inverter.spice inverter"```. Remember to always use the layout netlist first and schematic netlist second as then in the side by side result the layout is on the left and the schematic is on the right. Each netlist is represented by a pair of keywords in quotes, where the first is the location of the netlist file and the second is the name of the subcircuit to compare. As we can see from the result below, there was an issue in the wiring and the netlists do not match. This is due to wiring errors in the layout.

![image](https://user-images.githubusercontent.com/115495342/195994571-5a27c417-060d-4a6d-9bf3-dd19918d3b24.png)

Now, if the layout is correct, the final step is to validate the layout netlist again with parasitics included. We can include both resistive and capacitive parasitics in magic netlists, though this process is complicated and requires manual interventions, excluding it from the scope of this exercise. Capacitive parasitics can be easily included though, by using the following commands in the magic console window during extraction.

```
extract do local
extract all
ext2spice lvs
ext2spice cthresh 0
ext2spice
```

Here, the command ```ext2spice cthresh 0``` tells magic to add all the parasitic capacitances to the spice netlist. 

## Day 2: Introduction to DRC and LVS

### Fundamentals of physical verification:-  

The difference between physical design and design failure is physical verification. Physical verification aims to identify any potential design errors that can inhibit proper functioning or even result in a foundry rejection of manufacturing. To be able to confirm and validate proper electrical and logical functionality as well as manufacturability, efficient tools are needed.Verification involves numerous steps such as: 

* Design Rule Check (DRC)
* Layout vs Schematic (LVS)
* XOR
* Antenna Checks
* Electrical Rule Check

As chips get denser, the scale of physical verification increases. While chip designs can be hierarchical, physical verification is not. The two primary aspects of physical verification are as follows:

1. Design Rule Checks (DRC)
 * Ensures that the design layout meets all the silicon foundry rules for mask making
2. Layout vs. Schematic (LVS)
 * Makes sure that the design layout electrically matches the design, as implemented in schematic form or any form that electrically describes the design specification

Modern LVS in verification works by comparing the netlists, one starting from an initial schematic described by an RTL (Verilog/VHDL) and then running through synthesis, and the other one moving backwards from the completed layout, back to the extraction to obtain a netlist for comparison. Some popular open source tools for the same are:
![image](https://user-images.githubusercontent.com/115495342/195996187-ad182f1c-3f9b-4aa4-bfe0-9d7523653718.png)

The Graphic Design System (GDS), more widely used by its successor, GDSII is a database file format which is the most widely used and preferred format for mask manufacturing foundries. It is a binary file format denoting geometric shapes, text labels, and other information about the layout in hierarchical form. This data is widely divided into:
1. Data (Rectangles, Polygons, Subcells)
2. Metadata (Labels, Cell names, Instance names)  





