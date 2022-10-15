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

### Lab2: GDS read and Input Styles

First, we shaould create a project directory and set it up for running a Magic environment.
![image](https://user-images.githubusercontent.com/115495342/195998350-4c55bcae-71f8-44be-98be-fdb03ad5198e.png)

Now work on GDS read and therefore styles in magic. Open magic in your workspace and enter ```cif istyle xxx``` . The xxx could be anything, but the point is that the output shows the currently enabled style as well as all the available options.
![image](https://user-images.githubusercontent.com/115495342/195999123-ed7f498d-0a66-44ca-9671-5729ffb1f828.png)

Now, using the default enabled style i.e. ```gds read``` from the sky130 directory. Type:  
```gds read /usr/share/pdk/sky130A/libs.ref/sky130_fd_sc_hd/gds/sky130_fd_sc_hd.gds```

![image](https://user-images.githubusercontent.com/115495342/195999196-3b0b87e7-c313-4928-b026-92e8ab6b7084.png)

### Lab: Port and Port index
Select a simple cell from Options --> Cell manager such as sky130_fd_sc_hd__and2_1:
![image](https://user-images.githubusercontent.com/115495342/195999233-8c390b4c-cc9b-4f49-968b-eb9ef73b8879.png)

set the style using `cif istyle sky130()`

![image](https://user-images.githubusercontent.com/115495342/195999284-9e52b034-0da2-44ca-aa7c-627c81614e49.png)

The labels in the layout view are marked in yellow color, which means they are treated as regular text. Now, we shall use the sky130(vendor) style. To do this, run `cif istyle sky130(vendor)` and then read the gds files from the library. The current and2_1 layout will automatically be overwritten.

![image](https://user-images.githubusercontent.com/115495342/195999448-cbf10020-ec4b-478b-b572-7ea2a65a8514.png)

 Now labels are colored blue, which means they are treated as ports.
 
 Now, to get information about ports, select a label and then type ```what``` in the console. Another way to do this is to use ```port first``` to get information about the port indexed by magic as 1.

![image](https://user-images.githubusercontent.com/115495342/195999762-d01fce88-e4d9-4440-a9c5-e75c5dd9687b.png)

Let's look at the and2_1 subcircuit definition by locating the library directory and opening the file labeled sky130_fd_sc_hd.spice.
![image](https://user-images.githubusercontent.com/115495342/195999950-8a48b5f1-fd24-41e7-a6d8-406b50599f23.png)

Here, if we search for the and2_1 cell definition, we can see the following.

![image](https://user-images.githubusercontent.com/115495342/195999845-455416e1-cc0b-4265-902a-7a2c41962ded.png)

### Lab - Abstract Views

Open Magic window and read the lef library file using `lef read /usr/share/pdk/sky130A/libs.ref/sky130_fd_sc_hd/lef/sky130_fd_sc_hd.lef` and load the same cell and2_1 cell from the Cell Manager. The Abstract view of the cell as shown below.
![image](https://user-images.githubusercontent.com/115495342/196000080-d73c0689-576e-4789-ae30-e218e559dd9c.png)

Lets us check port information, port order metadata isn't present in the lef files.
![image](https://user-images.githubusercontent.com/115495342/196000190-5523b0fc-61eb-4f78-8eab-6afb192bec24.png)

Run the readspice script like before and load the cell again.
![image](https://user-images.githubusercontent.com/115495342/196000278-6e621e5d-6cc2-4f28-977d-840288391da5.png)

Abstract view is not same representation of the layout. Create a new layout using `load test` and instantiate the and cell using `getcell sky130_fd_sc_hd__and2_1`. An abstract view of the and2_1 cell as below.
![image](https://user-images.githubusercontent.com/115495342/196000367-ed776a96-b63f-48a6-be95-c6d06a8e6114.png)

Try to write this lef file to a gds file using `gds write test`, we get the following error message.
![image](https://user-images.githubusercontent.com/115495342/196000440-eda64d1e-28ce-4f61-90c2-0fe83099f7aa.png)

Open new Magic session, try to read the gds file.
![image](https://user-images.githubusercontent.com/115495342/196000631-4f701f82-6c19-4e7b-9fc1-55dd9e94ecec.png)

### Lab: Basic Extraction
Open magic
![image](https://user-images.githubusercontent.com/115495342/196000807-97cfb773-0abd-4e30-9b16-033b6475d393.png)

Spice file is creaated
![image](https://user-images.githubusercontent.com/115495342/196000823-6f335973-5464-4d4d-92d4-a098f7238e9d.png)

![image](https://user-images.githubusercontent.com/115495342/196000861-54d68e62-d3e0-445d-8e40-13ba9a05e2b7.png)

To run the extraction, but with parasitic capacitances included, use following commands.
![image](https://user-images.githubusercontent.com/115495342/196000944-c752d6b9-a4ae-4634-8d46-996509be003b.png)

The generated netlist is shown below, and it contains lines starting with C to denote the parasitic capacitances.
![image](https://user-images.githubusercontent.com/115495342/196000968-41a60c22-9419-471c-8db4-8cc69fbddc40.png)

Now take cthresh of 0.1, we get reduced number of parasitics in the netlist.
![image](https://user-images.githubusercontent.com/115495342/196001125-f90b7770-d9c5-4201-af50-7d56c5812710.png)

![image](https://user-images.githubusercontent.com/115495342/196001083-88d4fd9a-8463-4154-bb18-c1eafaf6283d.png)

To run a full RC extraction
![image](https://user-images.githubusercontent.com/115495342/196001221-d7adc504-1ed0-472b-8c23-249fd23a6d98.png)

### Lab - Setup for DRC

To set up standard DRC,use the following commands to run a python script.
![image](https://user-images.githubusercontent.com/115495342/196001347-c3ccbc17-9b5a-4133-ae0a-3ee38c73b996.png)

The output comes in a .txt file.
![image](https://user-images.githubusercontent.com/115495342/196001368-0bbc7caa-8b3c-47b4-a868-69226fa3de5f.png)
![image](https://user-images.githubusercontent.com/115495342/196001405-c952d963-c294-484d-a124-0925a3c9ca51.png)

There are DRC errors in the vendor .mag file for the and2_1 subcell as the standard cell layouts do not have internal connections to the well and substrate to save, and the layout depends on tap cells to make those connections. Since we haven't seen these DRC errors earlier in Magic it is because the DRC script runs a full DRC check, while the default DRC style in Magic was a fast DRC.
Change the DRC style to full, and force a DRC check, we can see DRC errors present in the standard cell.
![image](https://user-images.githubusercontent.com/115495342/196001553-4461c356-de59-4b89-a101-24330d1ba55d.png)

![image](https://user-images.githubusercontent.com/115495342/196001577-5ee35ed0-34cb-46e6-93a8-2a95ce26ce4b.png)

Add and align a tap cell in the existing layout, so that there are no more DRC errors in the top level.
![image](https://user-images.githubusercontent.com/115495342/196001677-2a5770dc-9504-4de2-b475-692ae5634aff.png)

If we descend into the and2_1 cell layer though, the DRC errors are still present. But in the top level layout, these errors get fixed.
![image](https://user-images.githubusercontent.com/115495342/196001663-f7421e32-0081-4d5d-9b28-12819460c090.png)

### Lab: Setup for LVS
Create a subdirectory for Netgen. Copy the Netgen setup and run Netgen on the and2_1 netlist files
![image](https://user-images.githubusercontent.com/115495342/196001813-1d661474-dadf-4b28-a366-b581855a8346.png)

After executed, should get that the netlists match.
![image](https://user-images.githubusercontent.com/115495342/196001846-4e6d90e5-6c08-49b7-b0b1-4be47e317e36.png)

### Lab: Setup for XOR
Open Magic and load a locally saved version of the and2_1 layout so that it can be editable.
![image](https://user-images.githubusercontent.com/115495342/196001952-f761e13b-ce18-40d2-ad85-184db5f967fd.png)

Next, we will make a small but noticable change on the layout using the erase command.
![image](https://user-images.githubusercontent.com/115495342/196002005-ab226d32-c882-476e-b7d8-c99ae2b13184.png)

To perform the XOR operation against the standard cell run commands as shown below. Here, the -nolabels option is used so that the operation is only performed on layout geometry.
![image](https://user-images.githubusercontent.com/115495342/196002063-ac62b286-a010-48d9-a0c2-3b43596b2795.png)

Final layout contains the small layer of polysilicon which was erased. This operation scales very well for larger circuits as well. Let us try this on the test3 layout created earlier. Load the file, then flatten it into another layout called xortest. Now select the and2_1 cell and move it over to the right by 1 place, we should get the following.
![image](https://user-images.githubusercontent.com/115495342/196002147-aba2f191-3b54-41b3-8d2a-c83be0c19f92.png)

Xor this moved layout with the flattened layout xor_test, we get the resulting layout.
![image](https://user-images.githubusercontent.com/115495342/196002178-c6bef49d-c4e2-4e99-a358-ca4cf5a15f6e.png)


## Day 3: Design Rule Checking

### Fundamentals of Design Rule Checking

Tolerances for the fabricated designs vary depending on the silicon production method. These tolerances are based on the probabilities of expected failure/defects found in a manufactured batch, expressed in parts per million, and are dependent on the conditions and equipment employed in the manufacturing environment. As a result, in order to prevent chip failure, each procedure has a unique set of guidelines that must be followed. These guidelines are provided in the layout's geometry. These are referred to as design rules, and design rule verification is the process of complying to these rules.

### Lab - Width and Spacing Rules
Start by cloning the following git repository:  
```git clone https://github.com/RTimothyEdwards/vsd_drc_lab.git```  
cd into the lab folder and run ```./run_magic``` in the command line to start magic.
![image](https://user-images.githubusercontent.com/115495342/196002306-dfe2ffcb-6c25-48ee-9ada-51958b395469.png)
![image](https://user-images.githubusercontent.com/115495342/196002334-b33f43a7-d31f-47e5-af60-14021448ec67.png)


From the file menu, open exercise1 which should open a .mag layout containing 4 types of DRC violations as shown
![image](https://user-images.githubusercontent.com/115495342/196002316-0556d601-1a93-4320-bebe-faf99a05c16b.png)

#### Ex1: Width rule
![image](https://user-images.githubusercontent.com/115495342/196002377-18e947ee-7b1c-47c5-8df1-20ccea6cb14e.png)

The way to fix this manually is to increase the width of the metal2 segment upto or above the required 0.14um. Set your cursor box around the area and use the middle mouse key to paint the entire area within the box with metal 2.
Or we can also fix by using commands `box width 0.14um` and `paint m2`.
![image](https://user-images.githubusercontent.com/115495342/196002562-18abf7c4-8321-4ace-8186-331b8cb875f0.png)

Now the error has gone. 

#### Ex2: Spacing error
Similarly, for the spacing error manually  we need to fix the spacing by moving one of the components by selecting and usi g the arrow keys to move. Or else by using command `move e 0.14um` 
Before fix:
![image](https://user-images.githubusercontent.com/115495342/196002639-14e2d4c8-375a-4503-b7aa-e46e2e7497b1.png)

After fix:
![image](https://user-images.githubusercontent.com/115495342/196002654-afaf3270-46c9-49ec-ba17-cf00c0963437.png)

#### Ex3: Lab - Wide Spacing and Notch Rules
##### Wide spacing
An asymmetrical spacing error and is solved in the same way like moving one of the component

Before fix:
![image](https://user-images.githubusercontent.com/115495342/196002814-1531ad1d-a7c6-4504-882e-7576387a5548.png)

After fix:
![image](https://user-images.githubusercontent.com/115495342/196002824-7ac1e257-3503-410b-9a0e-ac65951ece1a.png)

#### Notch 
The error is solved by selecting half the figure and stretching it away from the notch using Shift+arrow key on numpad.

Before fix:
![image](https://user-images.githubusercontent.com/115495342/196002871-33f77598-cb09-4f27-8403-980bc3d60265.png)

After fix:
![image](https://user-images.githubusercontent.com/115495342/196002881-f197960b-ae1a-4aa3-aab8-63e55ccb8d79.png)

### Lab: via size, multiple via, via overlap and auto-generated via

Go to file and open exercise 2. This one is all about vias and the violations they throw. 
![image](https://user-images.githubusercontent.com/115495342/196002934-52689d40-4e9c-4de7-a62c-006ed8544383.png)

1. Via size is simply solved by stretching to the side and then vertically.
Before fix:
![image](https://user-images.githubusercontent.com/115495342/196002965-ec2b9a61-89b1-4072-8724-7184f639f5e7.png)

After fix:
![image](https://user-images.githubusercontent.com/115495342/196002973-f0799d88-9d69-425b-a084-285c4fde3b83.png)

2. Multiple vias is an area on which contact cuts will be drawn onto by the algorithm. 
Before fix:
![image](https://user-images.githubusercontent.com/115495342/196003080-3ce5fdb7-57e8-48e4-98f5-5ce28807d777.png)

To see the cuts, type ```cif see MCON``` after boxing the figure within your cursor box and you should see this
After fix:
![image](https://user-images.githubusercontent.com/115495342/196003056-bcb17ac4-0a30-4568-8759-89861c65a427.png)

3.  Via overlapping: way to solve this is to surround the point with the desired width of metal1 (0.03um) and then by an additional 0.06um padding either horizontally or vertically. 
Before fix:
![image](https://user-images.githubusercontent.com/115495342/196003169-f2d07154-23ee-4a26-9921-f90e8e716a40.png)

After fix:
![image](https://user-images.githubusercontent.com/115495342/196003218-68050a97-5046-4478-a495-f040498b559d.png)

### Lab:  Minimum Area and Minimum Hole Rule

#### Minimum area rule
Load exercise3.mag, select the metal and use the B key, we see that the current area is 0.2um2.
![image](https://user-images.githubusercontent.com/115495342/196003418-2490cf82-ca8b-4aff-ba29-33aa9229644f.png)

To fix select area and stretch the layer to meet the requirement.
![image](https://user-images.githubusercontent.com/115495342/196003556-a45b22cf-1949-4dd6-9d13-4fe89b853126.png)

#### minimum hole area violation

To see this DRC error, we need to run Magic in the full DRC mode. We can do this by clicking on the menu button DRC --> DRC complete. Next we must wait Magic to update the DRC count, and then run a DRC report.
![image](https://user-images.githubusercontent.com/115495342/196003620-1b8bf6fe-8ce1-4cfc-8439-0d5d6bf8d18a.png)

If we manually set the cursor box to the size of the hole, we see it is 0.07um2 which is smaller than allowed. To fix this we must manually erase sections of the metal till the hole is big enough to pass DRC.
![image](https://user-images.githubusercontent.com/115495342/196003694-1f17afbc-ec54-409c-9a67-2cfaaceecb49.png)

#### Lab: Wells and Deep N-Wells'
 Open exercise4.mag which has wells and taps, so DRC check needs to be set at full to check for these.
![image](https://user-images.githubusercontent.com/115495342/196003745-abe0e24b-c955-43d1-87fd-5eaf07d92299.png)

Example 4a: Here we have a basic well error, as the wells do not have taps. The n-well shows an error as it is currently floating, though the "p-well" does not since this process doesn't actually consider p-wells unless they are in deep n-wells, and are instead counted as p-substrates.
![image](https://user-images.githubusercontent.com/115495342/196003840-5920e1da-7dc6-4ed8-b1ca-28e8ef931225.png)

To fix this error, paint a layer of n type material into the n well. The layer is called nsubstratendiff. Though this does not fix the DRC error.
![image](https://user-images.githubusercontent.com/115495342/196003886-a4ba66ad-c48e-484b-a556-d1d8a13ea67f.png)

The n-well should not be floating, as the tap should be connected to a layer of local interconnect. So by painting a layer of nsubstratencontact. While this gets rid of the n-well DRC error, it creates smaller errors like overlap and surround.
![image](https://user-images.githubusercontent.com/115495342/196003893-a3627d1e-a3e5-469a-b185-4776fb87778c.png)

Adjust the layers using what we have learnt till now by growing, stretching and adding in local interconnect, and we get no DRC errors.
![image](https://user-images.githubusercontent.com/115495342/196003979-96f92e08-cd38-44b1-aee7-3ef278158939.png)






















### Day 5: Running LVS

Layout vs Schematic (LVS) and Design Rule Checking (DRC) are the two most important verification procedures before a chip design is sent to the foundry for manufacturing. However, as compared to DRC, a chip that fails LVS will pass the foundry checks and go into manufacturing, but stands at a risk of returning as a non-working chip.

![image](https://user-images.githubusercontent.com/115495342/196004104-bc8e3481-4795-4207-b81e-b05cf3fa57f1.png)

LVS tools are incredibly quick and effective at telling you whether the two netlists match or not. These tools, however, do a terrible job of explaining why netlists do not match when they do. It is crucial for a verification engineer to understand how to interpret the findings of an LVS tool and identify the issue.

#### Lab: Intro to LVS
Use the following commands
1. `git clone https://github.com/RTimothyEdwards/vsd_lvs_lab.get`
2. `cd vsd_lvs_lab`
3. `cd exercise_1`
4. `ls`
 
`netA.spice`   `netB.spice`

Read file `netA.spice`
![image](https://user-images.githubusercontent.com/115495342/196004332-aff905e8-e41d-4e19-9f5c-3eb586f2fc90.png)

Read file `netB.spice`
![image](https://user-images.githubusercontent.com/115495342/196004557-cbc37c2a-a7a6-445d-8c9a-2ff2ae2f8464.png)

Open netgen and running lvs using the command ```lvs netA.spice netB.spice``` we get the following output
![image](https://user-images.githubusercontent.com/115495342/196004675-3d2649f6-d7de-4cde-bab6-e5dadb4211db.png)

Knowing the details about the comparison lie in the comp.out file, so reading that we can able to see
![image](https://user-images.githubusercontent.com/115495342/196004734-30124624-6369-4e3f-bb7b-84770f3ed730.png)

As not much is seen from 2 netlists that are identical, so let's edit the first pin pf cell 3 in netA.spice from "C" to "B"
![image](https://user-images.githubusercontent.com/115495342/196004766-39b3748d-a980-4247-9814-029c229ce7fb.png)

As seen clearly the instances where the mismatches occur, which pins and which nets are missing from which cells in either of the two nets.

### Lab: LVS with Blackbox Subcircuits
 In exercise 3, the following netlists that have empty subcircuit definitions. Netgen will treat these subcircuits as blackbox entries.
 The file `netA.spice` and `netB.spice` contains:
 ![image](https://user-images.githubusercontent.com/115495342/196005062-c328fe87-1ba4-49cb-9722-44778064e69c.png)
 
 Run the lvs on these two netlists gives the result as:
 ![image](https://user-images.githubusercontent.com/115495342/196005236-d3536f52-6c9c-47be-89a4-ea3af19efa34.png)
 
### Lab: LVS For Power-On-Reset Circuit
Generate the schematic netlist. Invoke xschem window.
![image](https://user-images.githubusercontent.com/115495342/196005401-e4b36706-4967-44c0-bb3f-a5c939f8be38.png)

![image](https://user-images.githubusercontent.com/115495342/196005422-53c3066a-11da-4db3-82b2-770516c2f680.png)


 
































