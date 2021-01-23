# VSD-IAT Workshop on Advanced Physical Design using OPENLANE/SKY130

## DAY 1 : Inception of Opensource EDA, OpenLANE and SKY130 PDK 


## DAY 2 : Floorplan and introduction to Library Cells
### Theory 2.1
#### FLOORPLANNING 
##### Defining width and height of Core and Die
First step in the floorplan is to define the dimensions of core and die, which in turn contraints the  dimensions of the SoC and the IPs contained in it. We define two terms in this regard - _Utilization Factor_ and _Aspect Ratio_.

__Utilization Factor__
Utilization factor gives represents the percentage of the core area occupied by the netlist(with cells abutting each other and excluding the wires). So it is defined as the ratio of Area ocupied by the netlist and Total area of the core. 
                                         
__Aspect Ratio__
Aspect ratio is the ratio of Height and Width of the core and tells if the core is rectungular or square.
