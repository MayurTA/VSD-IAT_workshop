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
![](/Images/image.png)

After running the above command, a new file named _piorv32a.floorplan.def_ will be created in the directory _runs/trial_run1/results/floorplan/_ which looks like this,

![](/Images/Screenshot%202021-01-23%20150009.png)

The DIEAREA variable contains the (x1 y1)(x2 y2) co ordinates where x1,y1 is the lower left vertex and x2,y2 is the upper right vertex of the die. This information can be used to calculate the area of the die. 

#### Opening floorplan in MAGIC
```
magic -T /home/mayurta/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &
```
The above commmand first reads the tech file which is _sky130A.tech_, reads lef file which is _merged.lef_ and def file which is _picorv32a.floorplan.def_.

![](/Images/Screenshot%202021-01-23%20161903.png)

In the layout, many i/o pins can be seen at the border of the layout, which are equidistant from each other by default(which can be changed in the _/home/mayurta/Desktop/work/tools/openlane_working_dir/openLANE_flow/configuration/README.md_ file). 

![](/Images/Screenshot%202021-01-23%20162058.png)

And many tap cells can be seen all over the layout, whcih connect n-well to Vdd and substrate to ground to prevent _latch-up_. These tap cells are diagonllay equidistant from each other.

![](/Images/Screenshot%202021-01-23%20162218.png)

A few standard cells can also been at the lower left corner of the layout. 

![](/Images/Screenshot%202021-01-23%20162346.png)
