Digital VLSI SoC Design and Planning — RTL to GDSII

A 2-week hands-on workshop on complete RTL-to-GDSII flow for digital VLSI SoC design,
organised by VSD (VLSI System Design) in collaboration with NASSCOM.
This repository documents my learning, lab outputs, and key takeaways from each day.


Day 1 — Inception of Open-Source EDA, OpenLANE & Sky130 PDK
Theory

Understood the difference between a chip package and the actual silicon die
Learned about core, pads, and how signals travel through wire bonding
Understood the role of Foundry IPs vs Macros in chip design
Traced the journey from a C program → RISC-V assembly → binary → RTL → GDSII
Learned why the Sky130 PDK (released by Google + SkyWater in 2020) was a landmark moment for open-source chip design

OpenLANE Flow Overview
StageTool(s) UsedSynthesisYosys, ABCFloorplan & PDNOpenROADPlacementOpenROADCTSTritonCTSRoutingFastRoute, TritonRouteTiming AnalysisOpenSTADRC & LVSMagic, Netgen
Lab — Running Synthesis for picorv32a
bashcd /home/vscode/Desktop/OpenLane
make mount
./flow.tcl -interactive
package require openlane 1.0.2
prep -design picorv32a
run_synthesis
After synthesis, calculated the flop ratio:
Flop Ratio = D Flip-Flops / Total Cells = 1613 / 15762 ≈ 10.23%

Day 2 — Floorplanning and Introduction to Library Cells
Theory

Utilisation Factor = Area of Netlist / Total Core Area (typical: 0.5–0.6)
Aspect Ratio = Height / Width of core
Learned about pre-placed cells, decoupling capacitors, and power mesh design
Understood pin placement strategy and logical cell blockage

Lab — Floorplan and Placement
tclrun_floorplan
run_placement
Viewed the floorplan and placement output in Magic VLSI layout editor.

Day 3 — Design and Characterisation of Library Cells using Magic & ngspice
Theory

Wrote a SPICE deck for a CMOS inverter and extracted key parameters:

Rise time — 20% to 80% of output rising edge
Fall time — 80% to 20% of output falling edge
Propagation delay — 50% input to 50% output


Got an overview of the 16-mask CMOS fabrication process

Lab — Custom Inverter Cell Characterisation
bashgit clone https://github.com/nickson-jose/vsdstdcelldesign.git
magic -T sky130A.tech sky130_inv.mag &
Inside Magic tkcon:
tclextract all
ext2spice cthresh 0 rthresh 0
ext2spice
Ran ngspice simulation and measured rise time, fall time, and propagation delay from the waveform:
bashngspice sky130_inv.spice
plot y vs time a
Also identified and fixed a poly.9 DRC rule violation in the Sky130 tech file.

Day 4 — Pre-Layout Timing Analysis and Clock Tree Synthesis (Partial)
Theory

Learned LEF file requirements — ports must lie on routing track intersections
Cell width must be an odd multiple of track pitch
Setup slack = Data Required Time − Data Arrival Time (must be ≥ 0)
Understood OCV, clock uncertainty, and CRPR concepts in STA

Lab — Custom Cell Integration (Completed up to config.tcl editing)
Set grid values in Magic based on tracks.info:
tclgrid 0.46um 0.34um 0.23um 0.17um
Generated LEF file from the custom inverter layout, copied LEF and lib files to picorv32a/src/ directory, and edited config.tcl to include the custom cell:
tclset ::env(LIB_SYNTH)   "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"
set ::env(EXTRA_LEFS)  [glob $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/src/*.lef]

⚠️ Day 4 lab partially completed — STA and CTS steps in progress.


Tools & Environment
ToolPurposeOpenLANERTL-to-GDSII automation flowYosysRTL synthesisOpenROADFloorplan, Placement, CTS, RoutingMagicLayout editor, DRC, LVSOpenSTAStatic Timing AnalysisngspiceSPICE simulationTritonRouteDetailed routingSky130 PDKSkyWater 130nm open-source PDK

Key Learnings

Understood how a chip moves from RTL to a manufacturable GDSII using a fully open-source toolchain
Got hands-on with synthesis, floorplanning, placement, and custom cell integration for the picorv32a RISC-V core
Learned to characterise custom standard cells using Magic and ngspice
Gained practical exposure to STA concepts — setup/hold slack, OCV, CRPR


Acknowledgements

Kunal Ghosh — Co-founder, VSD (VLSI System Design)
Nickson Jose — for the vsdstdcelldesign repository used in Day 3 labs
NASSCOM — for facilitating this workshop program


References

VSD SoC Design Workshop
OpenLANE GitHub
SkyWater Sky130 PDK
vsdstdcelldesign


Documented by NEHA C T | BMSCE | VLSI & Physical Design Enthusiast
