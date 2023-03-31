* Field-programmable gate array
:PROPERTIES:
:heading: 1
:END:
* EDA (Electronic Design Automation)
:PROPERTIES:
:heading: 2
:END:
** Xilinx
*** [[Vivado]]
* Hardware Description Language
:PROPERTIES:
:heading: 2
:END:
** [[VHDL]]
** Verilog
** Abstraction Level
*** RTL (Register-Transfer Level)
**** The flow of data between
***** Registers
***** Operations
*** Behavioral level
**** Without implementation of logic gates and interconnections
*** Gate level
**** Logic gates and interconnections
* Avoid Latches
:PROPERTIES:
:heading: 2
:END:
** Created by incomplete assignment in combinational logic
** #+BEGIN_SRC VHDL
process (i_data, i_enable)
begin
  if i_enable = '1' then
    o_latch <= i_data;
  end if;
end process;
#+END_SRC
** ~i_enable = '0'~ is not specified, and thus, the value does not change (Which implies a latch)