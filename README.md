# VSD-IAT Workshop on Advanced Physical Design using OPENLANE/SKY130

## DAY 1 : Inception of Opensource EDA, OpenLANE and SKY130 PDK 


## DAY 2 : Floorplan and introduction to Library Cells 
### FLOORPLANNING 
#### 1. Defining width and height of Core and Die
First step in the floorplan is to define the dimensions of core and die, which in turn contraints the  dimensions of the SoC and the IPs contained in it. We define two terms in this regard - _Utilization Factor_ and _Aspect Ratio_.

__Utilization Factor__-
Utilization factor gives represents the percentage of the core area occupied by the netlist(with cells abutting each other and excluding the wires). So it is defined as the ratio of Area ocupied by the netlist and Total area of the core. 
                                         
__Aspect Ratio__-
Aspect ratio is the ratio of Height and Width of the core and tells if the core is rectungular or square.

#### 2. Defining locations of pre-placed cells
In the netlist, there will be some portions which repeat many times at different locations. So, we divide the entire netlist into certain blocks so that the repeating blocks can be duplicated easily as and when required. These blocks are placed on the floor before runnning the autommated PnR, and hence the name _pre-placed cells_.Automated tools cannot re locate these pre-placed cells. 

#### 3. Surronding pre-placed cells with de-coupling capacitors


### LAB
#### Few useful flags and commands
- To create runs folder with custom name
```shell
prep -design picorv32 -tag trial_run1
```
  This creates a new runs folder with the name _trial_run1_
- To overwrite default configurations in config.tcl
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
#### Running floorplan
```
run_floorplan
```
<img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/Images/image.png" width="75%">

After running the above command, a new file named _piorv32a.floorplan.def_ will be created in the directory _runs/trial_run1/results/floorplan/_ which looks like this,

<img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/Images/Screenshot%202021-01-23%20150009.png" width="75%">

The DIEAREA variable contains the (x1 y1)(x2 y2) co ordinates where x1,y1 is the lower left vertex and x2,y2 is the upper right vertex of the die. This information can be used to calculate the area of the die. 

#### Opening floorplan in MAGIC
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

#### Running floorplan
The following command places all the standard cells pertaning to the netlist, on the floorplan created from the previous step. 
```
run_placement
```
All the checks should be passed as follows,

![](/Images/Screenshot%202021-01-23%20192527.png)

#### Opening floorplan in MAGIC
Now open the just created _piorv32a.placement.def_ in magic using the command similar to the one from previous step.
```
magic -T /home/mayurta/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```
![](/Images/Screenshot%202021-01-23%20192939.png)

![](/Images/Screenshot%202021-01-23%20193030.png)

## DAY 3 : Designing library cell using MAGIC layout and ngspice charactereization

### LAB
OBJECTIVE : To perform simulatation and characterization of an inverter and plug it into the _picorv32_.
#### Setting up the inverter files
Instead of designing the inverter from scratch, we git clone the folder containing a pre-designed inverter and work with it. The link to be cloned from was already given in the workshop. We first go to the openLANE_flow(openlane) directory and clone the inverter there as follows, 
```
git clone https://github.com/nickson-jose/vsdstdcelldesign.git
```
This command creates a new folder named _vsdstdcelldesign_ inside our _openLANE_flow_ folder.
![](/D3_images/Screenshot2021-01-24123134.png)

Now, we copy the tech file into the _vsdstdcelldesign_ directory and open the inverter design with magic. For copying, go to the directory where tech file is present i.e pdks/sky130/libs.tech/magic and use the command `cp sky130A.tech ABSOLUTE_PATH_TO_VSDSTDCELLDESIGN` as follows, 

![](/D3_images/Screenshot2021-01-24123728.png)

#### Opening the inverter in MAGIC
Now, we can open the inverter in magic by typing,

```
magic -T sky130.tech sky130_inv.mag
```

![](/D3_images/Screenshot2021-01-24124655.png)

To simulate the inverter, we need a _.spice_ file corresponding to the _.mag_ file. We first extract the _.mag_ file, whcih creates a _.spice_ file in the same directory.

![](/D3_images/Screenshot2021-01-24171831.png)

Then we convert the _.ext_ into _.spice_ including all the parasitics.

![](/D3_images/Screenshot2021-01-24172043.png)

![](/D3_images/Screenshot2021-01-24172213.png)

Then we edit the _.spice_ file to include model files, define power supply nodes and analysis type.

![](/D3_images/Screenshot2021-01-24194614.png)

#### Runing the simulations in Ngspice
Next, we run the simlulation by typing, 
```
ngspice sky130_inv.spice
```
![](/D3_images/Screenshot2021-01-24194710.png)

To plot the simulation results, 
```
plot y vs time a
```
This plots output(node y) vs time and also the input(node a)..

![](/D3_images/Screenshot2021-01-24_194923.png)
Timing characterization of the cell can be performed in ngspice by calculating delays and transition times. 

## DAY 4 : Pre-layout timing analysis and importance of good clock tree
### LAB
MAGIC contains all the detailed information about a cell. For PnR, such detailed information is not necessary. So, we use a different file format __LEF__ for placement and routing stage. LEF( Library Exchange Format) contains only the abstract information about the cell and hence is also used for protecting the IPs. So, before plugging our Inverter into the layout of _picorv32_, we need to convert the _.mag_ file of inverter into _.lef_. 

For routing, certain guidelines are to be strictly followed. Two of such guidelines relevant in our case are,
1) The input and output ports must lie on the intersection of horizontal and vertical tracks
2) Width of the standard cell must be odd multiples of track pitch and height must be odd multiples of vertical track pitch

#### Verifying the gudidelines and coverting to lef file
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

#### Plugging the inverter lef file into picorv32a

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
 
 
 #### Timing analysis in OpenSTA
 Next we try to improve the timing still more by using OpenSTA. Before that we need to set it up first. We need two files with format _.sdc_ and _.conf_, in our case, _my_base.sdc_ and _sta.conf_. These files were already available with us in the _exatras_ directory of the cloned _vsdstdcelldesign_ folder. We copy the _.sdc_ file into _src_ directory of _picorv32a_. Then we modify the contents in _.conf_ as follows, specifying the paths to respective _.lib_ files and _.sdc_ file. 
 
  <img src="https://github.com/MayurTA/VSD-IAT_workshop/blob/main/D4_images/Screenshot_2021-01-26_105216.png"  width = "60%">
 
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
  
  
 
