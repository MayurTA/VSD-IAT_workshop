# VSD-IAT Workshop on Advanced Physical Design using OPENLANE/SKY130

## DAY 1 : Inception of Opensource EDA, OpenLANE and SKY130 PDK 


## DAY 2 : Floorplan and introduction to Library Cells
### Defining width and height of Core and Die
First step in the floorplan is to define the dimensions of core and die, which in turn contrains the  dimensions of the SoC and the IPs contained in it. We define two terms in this regard - _Utilization Factor_ and _Aspect Ratio_.

![core](Images/Screenshot%20(127).png/240/240)

Consider the whole netlist that must be contained in the Core. Now, strip off the wires and arrange the cells in the remaining netlist such that they abut each other. Utilization factor is defined with respcet to this modified netlist.
