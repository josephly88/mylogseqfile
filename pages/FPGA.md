- # Field-Programmable Gate Array
- ## EDA (Electronic Design Automation)
	- Xilinx
		- [[Vivado]]
- ## HDL (Hardware Description Language)
	- [[VHDL]]
	- Verilog
	- SystemVerilog
- ## Debug
	- Avoid Latches
		- Created by incomplete assignment in combinational logic
		- ``` VHDL
		  process (i_data, i_enable)
		  begin
		    if i_enable = '1' then
		      o_latch <= i_data;
		    end if;
		  end process;
		  ```
			- Problem: `i_enable = '0'` is not specified
				- the value does not change (Which implies a latch)
- ## Testing and Verification
	- Simulation
	- Formal Verification
	- Testing with test vectors
-
- ### Unsorted
	- Abstraction Level
		- RTL (Register-Transfer Level)
			- The flow of data between
				- Registers
				- Operations
		- Behavioral level
			- Without implementation of logic gates and interconnections
		- Gate level
			- Logic gates and interconnections