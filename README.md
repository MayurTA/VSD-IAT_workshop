# VSD-IAT Workshop on Advanced Physical Design using OPENLANE/SKY130

## DAY 1 : Inception of Opensource EDA, OpenLANE and SKY130 PDK 


## DAY 2 : Floorplan and introduction to Library Cells
### Defining width and height of Core and Die
First step in the floorplan is to define the dimensions of core and die, which in turn contraints the  dimensions of the SoC and the IPs contained in it. We define two terms in this regard - _Utilization Factor_ and _Aspect Ratio_.

#### Utilization Factor
Utilization factor gives represents the percentage of the core area occupied by the netlist(with cells abutting each other and excluding the wires).

                                        __Utilization Factor = Area occupied by the netlist / Total area of the core__
#### Aspect Ratio
Aspect ratio tells if the core is rectungular or square. 

                                                             __Aspect ratio = Height / Width__
