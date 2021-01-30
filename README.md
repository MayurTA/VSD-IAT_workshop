# VSD-IAT Workshop on Advanced Physical Design using OPENLANE/SKY130

## CONTENTS
- [DAY 1 : Inception of Opensource EDA, OpenLANE and SKY130 PDK ](#day-1--inception-of-opensource-eda-openlane-and-sky130-pdk)
 - [Few common VLSI terms}(#few-common-vlsi-terms)
 - [RTL to GDSII flow](#rtl-to-gdsii-flow)
 - [About Openlane](#about-openlane)
 - [LAB 1 : Getting started with OpenLane](#lab-1--getting-started-with-openlane)
  - [Running synthesis](#running-synthesis)
- [DAY 2 : Floorplanning and introduction to Library Cells](#day-2--floorplanning-and-introduction-to-library-cells)
  
  
## DAY 1 : Inception of Opensource EDA, OpenLANE and SKY130 PDK 
 <img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D1_images/Screenshot_2021-01-27_214716.png" width="40%">

Consider an Arduino board. The design of such a microcontroller is dealt in Embedded Systems desing. There is an important block in this microcontroller, which is the microprocessor. The design of such microprocessors or other such chips is done in the VLSI industry. It involves desinging the chip from an abstract logical point of view down to fabricating the chip on a real semiconductor wafer. This flow is generally known as RTL to GDSII flow. And that is what we will explore in this project. 

 ### Few common VLSI terms
 
 <img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D1_images/Screenshot_2021-01-27_214454.png" width="60%">
 
 The image shown is of a typical chip
- __DIE__ - The outermost white border is called __DIE__. Silicon wafers are divided into Dies and each such die can be an independant chip. 
- __PADS__ - The blue segments just within the die border are called __PADS__. They contain _pins_ which the chip uses to communicate with the external world. 
- __CORE__ - The central black region is called the __CORE__. Core is the main part of the chip. It contains all the different functional blocks that handle all the processes the chip is designed to perform. 
- __IP__ is _Intellectual Property_. It refers to the funcational blocks desinged for a specific purpose. 
- __Foundry__ is a semiconductor fabrication plant where devices such as integrated circuits are manufactured. They provide all the necessary files required to design an IC which can be taped out in their plant.
- __PDK__ - Process Design Kit. It is a collection of files used to model a fabrication process for the EDA tools used to design an IC. It contains process design rules, device models, standard cell libraries, I/O libraries etc.
- __RTL__ - Registre Transfer Level. It is a gate level netlist corresponding the logical functionality of the design. It is defined using Hardware Description Languages(HDL) auch as verilog and VHDL.

### RTL to GDSII flow
- __SYNTHESIS__ - Conversion of RTL to a circuit consisting of components from a Standard Cell Library(SCL). Standard Cell Library is a collection of cells with certain functionality like AND gate, Or gate, etc. with a fixed height and variable width( which is an integer multiple of certain discrete units called Site Widths). 
- __FLOOR AND POWER PLANNING__ - Abstract layout of the entire chip is planned. 
  - Chip floor planning - Partition of chip die into different blocks and placement of i/o pads.
  - Macro floor planning - Dimensions of the blocks are estimated, locations of pins are decided and rows are defined
  - Power planning - Locations of power pads and power straps are decided. It consists of many upper layer metals arranged parallely for uniform power distribution across the entire chip
 - __PLACEMENT__ - Cells are placed on the floorplan constructed in the previous step. Two steps : 
   - Global placement - Finding optimal positions for all cells
   - Detailed placement - Placement obtained from global placement are further optimized
 - __CLOCK TREE SYNTHESIS__ - Creation of clock distribution network to ensure that clock is delivered with minimum skew and in good shape to all the sequential elements. Usually implemented as a H-tree.
 - __ROUTING__ - Using available metal layers to interconnect the cells and blocks. 
   - Global Routing - Routing guides are generated
   - Detailed Routing - Uses routing guides to implement actual wiring
 - __SIGN OFF__ - Various verifications are performed
   - Physical verifications - Design Rule Checks (DRC) and Layout vs Schematic (LVS)
   - Timing verifications - Static Timing Analysis (STA)
   
 ### About Openlane
  Openlane is an open source flow for a true open source tape-out experience. It is a culmination of various open source EDA tools. It's main goal is to produce a clean GDSII without human intervention. Openlane is tuned for SkyWater 130nm open pdk.
  
  Openlane ASIC flow :
 - __RTL Synthesis__ - Implemented using _Yosys_ and _abc_
 - __Static timing analysis__ - Implemented using _OpenSTA_
 - __Design for Testability (DFT)__ - Implemented using _Fault_
 - __Physical implementation__ - Implemented using _OpenROAD_. Involves Place and Route(PnR) and Clock Tree Synthesis (CTS)
 - __Logical Equivalence Checking (LEC)__ - Implemented using _Yosys_. To ensure functional equivalence after netlist is modified during optimizations
 - __RC Extraction__ 
 
 ### LAB 1 : Getting started with OpenLane
 Openlane comes with many built in designs. In this project, we will be exploring the flow with one such design, __picorv32a__. It is a CPU core and we will see how all the steps in RTL to GDSII flow are implemented in Openlane by using this chip. And a few directory names mentioned in this project might be user specific, but most of them will be same. For example, _openLANE_flow_ directory mentioned in this project is named just _openlane_ typically. 
 
 
 To start openlane, we open the shell in _openLANE_flow_(_openlane_) directory and run the command,
 ```
 ./flow.tcl -interactive
 ```
 <img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D1_images/Screenshot 2021-01-22 191101.png" width="70%">
 
 Now we import openlane packages specifying its version,
```
package require openlane 0.9
```
<img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D1_images/Screenshot 2021-01-22 191329.png" width="75%">

Next we specify the design that we intend to work on, which is _picorv32a_ in our case,
```
prep -design picorv32a
```
<img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D1_images/Screenshot 2021-01-22 193753.png" width="70%">

This command merges two lefs and places it in a new folder which is named as date and time while running the command, inside directory designs/picorv32a/runs/.

<img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D1_images/Screenshot 2021-01-22 200120.png" width="60%">

#### __Running synthesis__
```
run_synthesis
```
This runs the synthesis where _yosys_ translates RTL into circuit using generic components and _abc_ maps the circuit to Standard Cells.

Here we define a term _Flop Ratio_. Flop ratio is the ratio of total number of flip flops to total number of cells present in the design.

<img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D1_images/Screenshot 2021-01-22 214754.png" width="50%">
 
## DAY 2 : Floorplanning and introduction to Library Cells 
### FLOORPLANNING 
#### 1. Defining width and height of Core and Die
First step in the floorplan is to define the dimensions of core and die, which in turn contraints the  dimensions of the SoC and the IPs contained in it. We define two terms in this regard - _Utilization Factor_ and _Aspect Ratio_.

__Utilization Factor__-
Utilization factor represents the percentage of the core area occupied by the netlist(with cells abutting each other and excluding the wires). So it is defined as the ratio of Area ocupied by the netlist and Total area of the core. 
                                         
__Aspect Ratio__-
Aspect ratio is the ratio of Height and Width of the core and tells if the core is rectungular or square.

#### 2. Defining locations of pre-placed cells
In the netlist, there will be some portions which repeat many times at different locations. So, we divide the entire netlist into certain blocks so that the repeating blocks can be duplicated easily as and when required. These blocks are placed on the floor before runnning the autommated PnR, and hence the name _pre-placed cells_. Automated tools cannot re-locate these pre-placed cells. 

#### 3. Surronding pre-placed cells with de-coupling capacitors
When the power supply wire is long, the parasitics in the wire result in a voltage drop. Thus, all the blocks do not get the necessary power required by them. So, we de-couple the blocks using capacitors which can feed the necessary voltage to the blocks. This also reduces _cross talk_.

#### 4. Power Planning 
All the blocks cannot be provided with de-coupling capacitors as it would increase the chip area. And also, having a single supply and ground line for all the blocks might result in _ground bounce_ and _voltage droop_ effects. To tackle this, we add multiple supply lines. 

#### 5. Pin Placement
All the input ports are placed on one side and the output ports on the other side. Ordering might depend on block placement. 

#### 6. Logical cell placement blockage
Area for the pins are reserved, thus blocking that area for automated PnR tools. 

### LAB 2 : Floorplanning, placement and magic
 #### __Few useful flags and commands__
- To create runs folder with custom name
```shell
prep -design picorv32 -tag trial_run1
```
  This creates a new runs folder with the name _trial_run1_
- To overwrite previous run
```shell
prep -design picorv32 -tag trial_run1 -overwrite
```
- To change variables in current run
```
set env(CLOCK_PERIOD) 15.000
```
  Sets the clock period to 15
- To view variables in current run
```
echo $env(CLOCK_PERIOD)
```
 #### __Running floorplan__
```
run_floorplan
```
<img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/Images/image.png" width="75%">

After running the above command, a new file named _piorv32a.floorplan.def_ will be created in the directory _runs/trial_run1/results/floorplan/_ which looks like this,

<img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/Images/Screenshot%202021-01-23%20150009.png" width="60%">

The DIEAREA variable contains the (x1 y1)(x2 y2) co ordinates where x1,y1 is the lower left vertex and x2,y2 is the upper right vertex of the die. This information can be used to calculate the area of the die. 

 #### __Opening floorplan in MAGIC__
 To view the floorplan created, we need to open it in _magic_ as follows,
```
magic -T /home/mayurta/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &
```
The above commmand first reads the tech file which is _sky130A.tech_, reads lef file which is _merged.lef_ and def file which is _picorv32a.floorplan.def_.

<img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/Images/Screenshot%202021-01-23%20161903.png" width="60%">

In the layout, many i/o pins can be seen at the border of the layout, which are equidistant from each other by default(which can be changed in the _/home/mayurta/Desktop/work/tools/openlane_working_dir/openLANE_flow/configuration/README.md_ file). 

<img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/Images/Screenshot%202021-01-23%20162058.png" width="40%">

And many tap cells can be seen all over the layout, whcih connect n-well to Vdd and substrate to ground to prevent _latch-up_. These tap cells are diagonllay equidistant from each other.

<img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/Images/Screenshot%202021-01-23%20162218.png" width="40%">

A few standard cells can also been at the lower left corner of the layout. 

<img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/Images/Screenshot%202021-01-23%20162346.png"  width = "40%">

#### __Running placement__
The following command places all the standard cells pertaning to the netlist, on the floorplan created from the previous step. 
```
run_placement
```
All the checks should be passed as follows,

<img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/Images/Screenshot%202021-01-23%20192527.png" width="40%">

#### __Opening floorplan in MAGIC__
Now open the just created _piorv32a.placement.def_ in magic using the command similar to the one from previous step.
```
magic -T /home/mayurta/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```
<img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/Images/Screenshot%202021-01-23%20192939.png" width="60%">

<img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/Images/Screenshot%202021-01-23%20193030.png" width="60%">

## DAY 3 : Designing library cell using MAGIC layout and ngspice charactereization
### 16 mask CMOS process
A CMOS inverter is fabricated on actual silicon wafer using 16 masks. The process contains of various steps as follows :
- Selecting a substrate
- Creating active regions for transistors
- Formation of n-well and p-well
- Formation of gates
- Lightly doped drain (LDD) formation
- Source and drain formation
- Formation of contacts and interconnects
- Higher metal level formation

### LAB 3 : Simulation and characterization of an inverter and plugging it into picorv32
#### __Setting up the inverter files__
Instead of designing the inverter from scratch, we git clone the folder containing a pre-designed inverter and work with it. The link to be cloned from was already given in the workshop. We first go to the openLANE_flow(openlane) directory and clone the inverter there as follows, 
```
git clone https://github.com/nickson-jose/vsdstdcelldesign.git
```
This command creates a new folder named _vsdstdcelldesign_ inside our _openLANE_flow_ folder.
<img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D3_images/Screenshot2021-01-24123134.png" width="60%">

Now, we copy the tech file into the _vsdstdcelldesign_ directory and open the inverter design with magic. For copying, go to the directory where tech file is present i.e pdks/sky130/libs.tech/magic and use the command `cp sky130A.tech ABSOLUTE_PATH_TO_VSDSTDCELLDESIGN` as follows, 

<img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D3_images/Screenshot2021-01-24123728.png" width="60%">

#### __Opening the inverter in MAGIC__
Now, we can open the inverter in magic by typing,

```
magic -T sky130.tech sky130_inv.mag
```

<img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D3_images/Screenshot2021-01-24124655.png" height="50%">

To simulate the inverter, we need a _.spice_ file corresponding to the _.mag_ file. We first extract the _.mag_ file, whcih creates a _.spice_ file in the same directory.

<img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D3_images/Screenshot2021-01-24171831.png" width="40%">

Then we convert the _.ext_ into _.spice_ including all the parasitics.

<img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D3_images/Screenshot2021-01-24172043.png" width="40%">

<img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D3_images/Screenshot2021-01-24172213.png" width="60%">

Then we edit the _.spice_ file to include model files, define power supply nodes and analysis type.

<img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D3_images/Screenshot2021-01-24194614.png" width="60%">

#### __Runing the simulation in ngspice__
Next, we run the simlulation by typing, 
```
ngspice sky130_inv.spice
```
<img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D3_images/Screenshot2021-01-24194710.png" width="60%">

To plot the simulation results, 
```
plot y vs time a
```
This plots output(node y) vs time and also the input(node a)..

<img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D3_images/Screenshot2021-01-24_194923.png" width="60%">
Timing characterization of the cell can be performed in ngspice by calculating delays and transition times. 

## DAY 4 : Pre-layout timing analysis and importance of good clock tree
### LAB 4 : Clock tree synthesis and tritonCTS
MAGIC contains all the detailed information about a cell. For PnR, such detailed information is not necessary. So, we use a different file format __LEF__ for placement and routing stage. LEF( Library Exchange Format) contains only the abstract information about the cell and hence is also used for protecting the IPs. So, before plugging our Inverter into the layout of _picorv32_, we need to convert the _.mag_ file of inverter into _.lef_. 

For routing, certain guidelines are to be strictly followed. Two of such guidelines relevant in our case are,
1) The input and output ports must lie on the intersection of horizontal and vertical tracks
2) Width of the standard cell must be odd multiples of track pitch and height must be odd multiples of vertical track pitch

#### __Verifying the gudidelines and coverting to lef file__
Tracks are like lines used by the PnR to place the metal wires for routing. The track information can be found in the file _tracks.info_ inside the directory _pdks/sky130A/libs.tech/openlane/sky130A_fd_sc_hd_. 

<img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D4_images/Screenshot_2021-01-25_103955.png"  height = "50%">

Each line contains a X(horizontal) or Y(vertical) track info with the first number representing track offset and the second number is track pitch.

To check whether the first guideline is followed by our inverter, we identify the input and output ports and check if they lie on the intersection of tracks of the corresponding metal by aligning the grids in MAGIC layout to that of the tracks using the `grid` command in _tkcon window_. In our case, the porst lie on _licon_ metal, so we align the grid corresponding to those values,
```
grid 0.46um 0.34um 0.23um 0.17um
```
<img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D4_images/Screenshot_2021-01-25_112119.png"  width = "50%">
We see that the ports do lie intersection of tracks. Next the second guideline is also verfified by counting the number of boxes covered the inverter along length and breadth. 

Next we rename the inverter _mag_ file(not necessary) and extract the _lef_ file by typing the command in _tkcon_ window,
```
lef write
```
This creates a new file the same directory.

<img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D4_images/Screenshot_2021-01-25_120208.png"  width = "50%">

#### __Plugging the inverter lef file into picorv32a__

For plugging the inverter into picorv32, wee first copy the inverter _lef_ file into the _src_ directory inside picorcv32.

<img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D4_images/Screenshot_2021-01-25_120435.png"  width = "70%">

We aslo require the tool to map inverter cell design and picorv32. So also copy the library files into src. 

<img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D4_images/Screenshot_2021-01-25_121410.png"  width = "70%">

For Openlane to recognise our inverter inside picorv32, we add the following lines in to the _config.tcl_ file which is inside pirorv32 directory,
 ```
 set ::env(EXTRA_LEFS) [glob $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/src/*.lef]
 ```
 We also add these other lines inside the same _config.tcl_ for openlane to recognise the timing information of our inverter,
 
 <img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D4_images/Screenshot_2021-01-25_174238.png "  width = "70%">

 Next we open the Openlane flow, require packages and prep the design. Then we run the following commands in Openlane window so that _lef_ file of our inverter gets addeed to merged lef file. 
 
 ```
 set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
 add_lefs -src $lefs 
 ```
 Then we run the synthesis.
 
 <img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D4_images/Screenshot_2021-01-25_180803.png "  width = "70%">

 There is huge timing violations. Here _wns_ is _worst negative slack_ and _tns_ is _total negative slack_.  So, now we should some changes and make our flow more timing driven.
 We check for three variables(variables in README.md file present inside openLANE_flow/configuration directory):
 - SYNTH_STRATEGY - We try to strike a balance between area and delay by using an appropriate strategy. The default strategy tunrs out to be 2 which is more area driven. So, we set the strategy to 1, which is more delay oriented. This might result in a bit increased area, but delay will be reduced 
 - SYNTH_BUFFERING - This adds buffers to high fan_out lines. It would be better if it is ON
 - SYNTH_SIZING - This varies the size of the cells in the flow. This also is betterr to be ON
 ```
 set ::env(SYNTH_STRATEGY) 1
 set ::env(SYNTH_SIZING) 1
 ```
 Running synthesis again, we find that the area has increased and timing has improved. 
 
 <img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D4_images/Screenshot_2021-01-25_190750.png"  width = "60%">
 
 We once confirm if the inverter did get added into picorv32 by checking the _merged.lef_ in runs/finalrun/tmp.
 
 <img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D4_images/Screenshot_2021-01-25_191747.png"  width = "50%">
 
 Yes! Inverter is found in the picorv32a _merged.lef_. So, next we run floorplan and placement. 
 
 
#### __Timing analysis in OpenSTA__
 Next we try to improve the timing still more by using OpenSTA. Before that we need to set it up first. We need two files with format _.sdc_ and _.conf_, in our case, _my_base.sdc_ and _sta.conf_. These files were already available with us in the _exatras_ directory of the cloned _vsdstdcelldesign_ folder. We copy the _.sdc_ file into _src_ directory of _picorv32a_. Then we modify the contents in _.conf_ as follows, specifying the paths to respective _.lib_ files and _.sdc_ file. 
 
  <img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D4_images/Screenshot_2021-01-26_105216.png"  width = "70%">
  
  The _my_base.sdc_ file in our case looks like this, 
  <img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D4_images/Screenshot_2021-01-26_121935.png"  width = "70%">
 
 And we copy the _.conf_ file into _openLANE_flow_ directory. There we open terminal and type `sta sta.conf`. This opens and runs our timing files in OpenSTA. The results are as follows, 
 
 <img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D4_images/Screenshot_2021-01-25_221613.png"  width = "60%">
 
 By scrolling up, we can see that fanout of the nets are more. We now go back to the openlane window and set `SYNTH_MAX_FANOUT` to 4 and run OpenSTA again. 
 
 <img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D4_images/Screenshot_2021-01-25_223554.png"  width = "60%">
 
 The timing sure has improved. But it is better to get it below -1. Next optimization we perform is, we scroll up and look for nets with _version1_ buffers, having more capacitance and driving more fanouts. We upsize such buffers by replacing them with _version4_ buffers. Here is one such buffer,
 
 <img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D4_images/Screenshot_2021-01-26_105809.png"  width = "60%">
 
 We run the following commands to get more information it and replace and run the analysis again,
 
 <img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D4_images/Screenshot_2021-01-26_110543.png"  width = "60%">
 
 Timing has improved again as expected. 
 
  <img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D4_images/Screenshot_2021-01-26_110855.png"  width = "60%">
  
  The replacing of cells modifies the local copy of netlist. So now we push the changes made to the netlist into the original file present in _picorv32a/runs/finalrun/results/synthesis/_. We use the command `write_verilog location-of-the-verilog-file`.
 We need to keep in mind that the modification has been to the _.v_ file present in synthesis stage and that when we run Openlane again, we should run synthesis again, doing which will undo all the changes done inside OpenSTA stage.
 
Then we run floorplan and placement again as we have modified the netlist. 

#### __Clock tree synthesis__
Clock tree synthesis is performed by TritonCTS. It is run by the following command,
```
run_cts
```

After clock tree synthesis we perform timing analysis again. Instead of running OpenSTA from outside Openlane, we can run it in the flow itself inside Openroad. The following set of commands descride the steps,

#### __Running OpenSTA using OpenRoad__
```
openroad
read_lef location-of-lef                  (i.e. runs/finalrun/tmp/merged.lef
read_def location-of-def                  (i.e runs/finalrun/results/cts/picorv32a.cts.def)
```
After the .lef and .def have been read, we need to create a db 
```
write_db db-name                          (i.e. pico_cts.db)
read_db db-name
```
Then we read other required files
```
read_verilog verilog-file-location        (i.e. finalrun/results/synthesis/picorv32a.synthesis_cts.v)
read_liberty $::env(LIB_SYNTH_COMPLETE)
link_design design-name                   (i.e. picorv32a)
read_sdc location-of-sdc                  (i.e. /openLANE_flow/designs/picorv32a/src/my_base.sdc)
set_propagated_clocks [all_clocks]
report_checks -path_delay min_max -format full_clock_expanded -digits 4
```
<img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D4_images/Screenshot_2021-01-26_163821.png"  width = "60%">

## DAY 5 : Final steps for RTL2GDS using tritonRoute and openSTA

### LAB 5 : Introduction to routing using tritonRoute 
#### __Routing__
To check which stage was last executed in Openlane, we can use the following command,
```
echo $::env(CURRENT_DEF)
```
The command basically tells us which .def file was updated last time which also corresponds to the previously executed stage.


__NOTE__ : Power-ground distribution is generally done durimg floorplan. But in Openlane, is is done after placement. 


Before routing, we first need to generate the power distribution network.
```
gen_pdn
```
We can check for the environment variables available for Routing for more control over the process(inside the README.md in openLANE_flow/Configuration/). One such variable is ROUTING_STRATEGY. A value between 0-3 makes the routing runtime faster but compromised optimization(around half an hour). A value of 14 results in a highly optimized routing but takes around 4-5 hours for completion. 
```
run_routing
```
<img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D5_images/Screenshot_2021-01-26_184013.png"  width = "60%">

<img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D5_images/Screenshot_2021-01-26_191047.png"  width = "60%">

Routing is performed in two stages:
- Fast route - Implemented using _FastROAD_. It generates routing guides.
- Detailed route - Implemented using _TritonRoute_. It uses the routing guides generated in fast route to find the best route and makes connections

#### __SPEF extraction__
SPEF - Standard Parasitc Exchange Format gives information about all the parasitics present in the circuit. But SPEF extractor is not yet included in Openlane. But in this lab, we have an external spef extractor. Go to directory work/tools/SPEF_EXTRACTOR and type,
```
python3 main.py location_of_def location_of_lef          
```
where location_of_def was runs/finalrun/tmp/merged.lef and location_of_lef was runs/finalrun/results/routing/picorv32a.def.

<img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D5_images/Screenshot_2021-01-26_192749.png"  width = "60%">

After extraction, a new spef file gets created in the same directory as of def file. Now, in runs/finalrun/results/synthesis/, we find the following .v files,

<img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D5_images/Screenshot_2021-01-26_194118.png"  width = "60%">

The picorv32a.synthesis_diodes.v gets created just before routing, when antenna diode insertion takes place. To perform STA again, we need to use the last file. 
