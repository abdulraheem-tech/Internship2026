# Internship2026

README

This workshop contains collaterals for RTL Design and Synthesis Workshop using Sky130 PDK's


### System Specifications

| Parameter | Specification |
|-----------|--------------|
| **Design** | VSDBabySoC (Mixed-Signal System-on-Chip) |
| **Technology Node** | SkyWater 130nm (sky130hd PDK) |
| **Clock Frequency** | Target: 91 MHz (11 ns period) |
| **Standard Cells** | sky130_fd_sc_hd library |
| **EDA Tools** | OpenROAD, Yosys, OpenSTA, Magic, ngspice |


### Tool Installation and Setup

**Objective:** Establish complete open-source EDA toolchain for RTL-to-GDSII flow.

#### Tools Installed

| Tool | Version | Purpose |
|------|---------|---------|
| **Yosys** | 0.46 | RTL synthesis |
| **Icarus Verilog** | 12.0 | Verilog simulation |
| **GTKWave** | 3.3.117 | Waveform viewer |
| **OpenROAD** | v2.0-26087 | Physical design implementation |
| **Magic** | 8.3.489 | Layout viewer, DRC, extraction |
| **ngspice** | 42 | SPICE circuit simulation |
| **OpenLANE** | v2.0 | Complete RTL-to-GDSII flow |
| **Sky130 PDK** | Latest | Process Design Kit |

#### Installation Process

```bash
# System Update
sudo apt update && sudo apt upgrade -y

# Install Dependencies
sudo apt-get install -y build-essential clang bison flex \
  libreadline-dev gawk tcl-dev libffi-dev git \
  graphviz xdot pkg-config python3 python3-pip \
  libboost-system-dev libboost-python-dev \
  libboost-filesystem-dev zlib1g-dev

# Clone and Build Yosys
git clone https://github.com/YosysHQ/yosys.git
cd yosys
make config-gcc
make -j$(nproc)
sudo make install

# Install Icarus Verilog
sudo apt-get install iverilog

# Install GTKWave
sudo apt-get install gtkwave

# Clone and Build OpenROAD
git clone --recursive https://github.com/The-OpenROAD-Project/OpenROAD-flow-scripts
cd OpenROAD-flow-scripts
./build_openroad.sh --local
source ./env.sh
```

---


---

### RTL Design and Synthesis

**Objective:** Master Verilog RTL design, synthesis with Yosys, and timing library analysis.

#### Key Learnings

**1. RTL Design Fundamentals**
- Module structure and hierarchy
- Blocking vs non-blocking assignments
- Sequential vs combinational logic
- Flip-flop coding styles (D, T, JK)
- FSM design patterns

**2. Logic Synthesis**
- RTL to gate-level netlist conversion
- Technology mapping to sky130_fd_sc_hd library
- Area and timing optimization
- Hierarchical vs flat synthesis

**3. Standard Cell Libraries**

The `sky130_fd_sc_hd__tt_025C_1v80.lib` timing library contains:
- **210+ standard cells** (gates, buffers, flip-flops, latches)
- Timing models: setup, hold, propagation delays
- Power models: leakage, dynamic power
- Area footprints
- Drive strength variants (e.g., `and2_1`, `and2_2`, `and2_4`)

#### Example: Simple Counter Synthesis

```verilog
module counter(
    input clk,
    input reset,
    output reg [3:0] count
);
    always @(posedge clk or posedge reset) begin
        if (reset)
            count <= 4'b0000;
        else
            count <= count + 1'b1;
    end
endmodule
```

**Synthesis with Yosys:**

```tcl
yosys
read_verilog counter.v
synth -top counter
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
write_verilog -noattr counter_synth.v
stat
show
```

#### Key Metrics from Week 1 Labs

| Design | DFF Count | Total Cells | Flop Ratio |
|--------|-----------|-------------|------------|
| good_mux | 0 | 3 | 0% |
| bad_mux (latch) | 1 | 4 | 25% |
| counter | 4 | 12 | 33.3% |

---
