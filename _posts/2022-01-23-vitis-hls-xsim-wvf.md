---
layout: post
title:  "Export Co-sim Waveform for Vitis HLS"
date:   2022-01-23 18:10:00 +0800
categories: research fpga
---

In a Vitis High-level Synthesis (HLS) project, the tool provides a
Co-simulation feature for the user to test the correctness of the
generated RTL design. This is done by running a simulation with the
stimuli from C testbench input, capturing the output data from the
simulation waveform and check them against the results produced by
the original C source.

While it comes very handy for the Vitis HLS to automate everything
with a single TCL command `cosim_design` as long as the testbench
C source is specified, it is often wished to view the waveform
to understand the I/O behaviour of the design. There are two ways
to do it.

This post applies to Vitis/Vivado 2020.2.

## Co-sim Option in Vitis HLS (Recommended)

Use `cosim_design` with following options in lieu of using it alone:

```tcl
cosim_design -trace_level <all|port|port_hier> [-wave_debug]
```

The option of `-trace_level` specifies what signals to log. Usually
`port` is enough as it logs all the top-level port signals. The optional
`-wave_debug` starts a Vivado GUI for the simulator for interactive
debugging; otherwise, the logged wave goes into the `<design_name>.[wdb|wcfg]`
files pair, which can be viewed by [this method](#open-xsim-wave-files).

Documentation for Vitis HLS `cosim_design`:
[link](https://docs.xilinx.com/r/en-US/ug1399-vitis-hls/cosim_design)

## Manually Hacking the Simulation Script

Inside the directory of the `<prj_dir>/<sln_dir>/sim/verilog` created
by Vitis HLS (after using `cosim_design`) there are the files below:

> NOTE: Running Vitis HLS again will overwrites modifications to
> the files below, hence this only serve as a temporary workaround,
> especially in the case that you want the VHD file directly.

```
run_xsim.sh
<design_name>.tcl
```

Add `-debug all` option to the `xelab` line to the `run_xsim.sh` (should
be the 1st line) to enable trace logging. Then modify the `<design_name>.tcl`
similarly to the example below if you want to log in XSim default format:

```tcl
log_wave -r /
run all
quit
```

Or if you want to dump in VCD format which can be viewed by other tools
e.g. GTKWave, change it to this:

```tcl
open_vcd
log_vcd [get_object /*]
run all
close_vcd
quit
```

Finally, run the simulation again by executing `sim.sh` in the directory.
Either `<design_name>.wdb` `<design_name_*>.wcfg` or `dump.vcd` will be
created in the directory after the simulation is done.

## Open XSim Wave Files

The `wdb` and `wcfg` files pair can be loaded using the Vivado TCL commands
from `vivado -mode batch` as below:

```tcl
# Replace with actual filenames
open_wave_database <name>.wdb
open_wave_config <name>.wcfg
current_fileset
start_gui
```
