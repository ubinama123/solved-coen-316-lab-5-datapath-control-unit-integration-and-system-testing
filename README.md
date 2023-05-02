Download Link: https://assignmentchef.com/product/solved-coen-316-lab-5-datapath-control-unit-integration-and-system-testing
<br>
<h1>Introduction</h1>

The datapath designed in the previous lab contains 10 control signals. The function of the control unit, which is the focus of this lab, is to produce the correct values for all the control signals at the proper point in time.  Tables 1 and 2 lists the 10 control signals and summarizes their operation.

<strong>Table 1: Single bit control signals.</strong>

<table width="0">

 <tbody>

  <tr>

   <td width="208">Control signal</td>

   <td width="208">value = 0</td>

   <td width="208">value = 1</td>

  </tr>

  <tr>

   <td width="208">reg_write</td>

   <td width="208">do not write into register file</td>

   <td width="208">write into register file</td>

  </tr>

  <tr>

   <td width="208">reg_dst</td>

   <td width="208">rt is the destination register</td>

   <td width="208">rd is the destination register</td>

  </tr>

  <tr>

   <td width="208">reg_in_src</td>

   <td width="208">d_out of data_cache is the d_in to the register file</td>

   <td width="208">ALU output is the d_in to theregister file</td>

  </tr>

  <tr>

   <td width="208">alu_src</td>

   <td width="208">out_b of register file (rt) is the y input of the ALU</td>

   <td width="208">sign extended immediate is the y input of the ALU</td>

  </tr>

  <tr>

   <td width="208">add_sub</td>

   <td width="208">ALU operation = addition</td>

   <td width="208">ALU operation = subtraction</td>

  </tr>

  <tr>

   <td width="208">data_write</td>

   <td width="208">do not write into data cache</td>

   <td width="208">write into data cache</td>

  </tr>

 </tbody>

</table>

<strong>Table 2: Two bit control signals.</strong>

<table width="0">

 <tbody>

  <tr>

   <td width="125">Control signal</td>

   <td width="125">value = 00</td>

   <td width="125">value = 01</td>

   <td width="125">value =10</td>

   <td width="125">value = 11</td>

  </tr>

  <tr>

   <td width="125">logic_func</td>

   <td width="125">AND</td>

   <td width="125">OR</td>

   <td width="125">XOR</td>

   <td width="125">NOR</td>

  </tr>

  <tr>

   <td width="125">func</td>

   <td width="125">load upper immediate</td>

   <td width="125">set less</td>

   <td width="125">arithmetic</td>

   <td width="125">logic</td>

  </tr>

  <tr>

   <td width="125">branch_type</td>

   <td width="125">no branch</td>

   <td width="125">beq</td>

   <td width="125">bne</td>

   <td width="125">bltz</td>

  </tr>

  <tr>

   <td width="125">pc_sel</td>

   <td width="125">no jump (PC+1, or PC+target addressifbranch condition is true)</td>

   <td width="125">jump (PC = target address)</td>

   <td width="125">jump register (PC = rs)</td>

   <td width="125">not used</td>

  </tr>

 </tbody>

</table>

Table 3 lists the 20 instructions implemented by the CPU together with the values of the 6 bit opcode field and the 6 bit func field (contained within the instruction as per Figure 1 of Lab 4) together with the 10 control signals.  Table 3 is <strong>partially</strong> completed, you are to <strong>complete</strong> the table by deriving the values of the 10 control signals based upon Tables 1 and 2 and knowledge of which control signals need to be activated during a particular instruction in order to achieve correct execution of the instruction. Refer to your datapath of Lab 4 to assist in completing the table.

<strong>Table 3: 20 instructions with opcode and function fields and control signals. [1]</strong>

<table width="0">

 <tbody>

  <tr>

   <td width="48">Inst.</td>

   <td width="52">op</td>

   <td width="52">func</td>

   <td width="48">reg_wr ite</td>

   <td width="50">reg_dst</td>

   <td width="45">reg_in_src</td>

   <td width="51">alu_src</td>

   <td width="50">add_su b</td>

   <td width="49">data_w rite</td>

   <td width="48">logic_func</td>

   <td width="39">func</td>

   <td width="49">branch_type</td>

   <td width="46">pc_sel</td>

  </tr>

  <tr>

   <td width="48">lui</td>

   <td width="52">001111</td>

   <td width="52"> </td>

   <td width="48">1</td>

   <td width="50">0</td>

   <td width="45">1</td>

   <td width="51">1</td>

   <td width="50">0(don’t care)</td>

   <td width="49">0</td>

   <td width="48">00(don’t care)</td>

   <td width="39">00</td>

   <td width="49">00</td>

   <td width="46">00</td>

  </tr>

  <tr>

   <td width="48">add</td>

   <td width="52">000000</td>

   <td width="52">100000</td>

   <td width="48">1</td>

   <td width="50">1</td>

   <td width="45">1</td>

   <td width="51">0</td>

   <td width="50">0</td>

   <td width="49">0</td>

   <td width="48">00</td>

   <td width="39">10</td>

   <td width="49">00</td>

   <td width="46">00</td>

  </tr>

  <tr>

   <td width="48">sub</td>

   <td width="52">000000</td>

   <td width="52">100010</td>

   <td width="48"> </td>

   <td width="50"> </td>

   <td width="45"> </td>

   <td width="51"> </td>

   <td width="50"> </td>

   <td width="49"> </td>

   <td width="48"> </td>

   <td width="39"> </td>

   <td width="49"> </td>

   <td width="46"> </td>

  </tr>

  <tr>

   <td width="48">slt</td>

   <td width="52">000000</td>

   <td width="52">101010</td>

   <td width="48"> </td>

   <td width="50"> </td>

   <td width="45"> </td>

   <td width="51"> </td>

   <td width="50"> </td>

   <td width="49"> </td>

   <td width="48"> </td>

   <td width="39"> </td>

   <td width="49"> </td>

   <td width="46"> </td>

  </tr>

  <tr>

   <td width="48">addi</td>

   <td width="52">001000</td>

   <td width="52"> </td>

   <td width="48">1</td>

   <td width="50">0</td>

   <td width="45">1</td>

   <td width="51">1</td>

   <td width="50">0</td>

   <td width="49">0</td>

   <td width="48">00</td>

   <td width="39">10</td>

   <td width="49">00</td>

   <td width="46">00</td>

  </tr>

  <tr>

   <td width="48">slti</td>

   <td width="52">001010</td>

   <td width="52"> </td>

   <td width="48"> </td>

   <td width="50"> </td>

   <td width="45"> </td>

   <td width="51"> </td>

   <td width="50"> </td>

   <td width="49"> </td>

   <td width="48"> </td>

   <td width="39"> </td>

   <td width="49"> </td>

   <td width="46"> </td>

  </tr>

  <tr>

   <td width="48">and</td>

   <td width="52">000000</td>

   <td width="52">100100</td>

   <td width="48">1</td>

   <td width="50">1</td>

   <td width="45">1</td>

   <td width="51">0</td>

   <td width="50">1</td>

   <td width="49">0</td>

   <td width="48">00</td>

   <td width="39">11</td>

   <td width="49">00</td>

   <td width="46">00</td>

  </tr>

  <tr>

   <td width="48">or</td>

   <td width="52">000000</td>

   <td width="52">100101</td>

   <td width="48"> </td>

   <td width="50"> </td>

   <td width="45"> </td>

   <td width="51"> </td>

   <td width="50"> </td>

   <td width="49"> </td>

   <td width="48"> </td>

   <td width="39"> </td>

   <td width="49"> </td>

   <td width="46"> </td>

  </tr>

  <tr>

   <td width="48">xor</td>

   <td width="52">000000</td>

   <td width="52">100110</td>

   <td width="48"> </td>

   <td width="50"> </td>

   <td width="45"> </td>

   <td width="51"> </td>

   <td width="50"> </td>

   <td width="49"> </td>

   <td width="48"> </td>

   <td width="39"> </td>

   <td width="49"> </td>

   <td width="46"> </td>

  </tr>

  <tr>

   <td width="48">nor</td>

   <td width="52">000000</td>

   <td width="52">100111</td>

   <td width="48"> </td>

   <td width="50"> </td>

   <td width="45"> </td>

   <td width="51"> </td>

   <td width="50"> </td>

   <td width="49"> </td>

   <td width="48"> </td>

   <td width="39"> </td>

   <td width="49"> </td>

   <td width="46"> </td>

  </tr>

  <tr>

   <td width="48">andi</td>

   <td width="52">001100</td>

   <td width="52"> </td>

   <td width="48"> </td>

   <td width="50"> </td>

   <td width="45"> </td>

   <td width="51"> </td>

   <td width="50"> </td>

   <td width="49"> </td>

   <td width="48"> </td>

   <td width="39"> </td>

   <td width="49"> </td>

   <td width="46"> </td>

  </tr>

  <tr>

   <td width="48">ori</td>

   <td width="52">001101</td>

   <td width="52"> </td>

   <td width="48"> </td>

   <td width="50"> </td>

   <td width="45"> </td>

   <td width="51"> </td>

   <td width="50"> </td>

   <td width="49"> </td>

   <td width="48"> </td>

   <td width="39"> </td>

   <td width="49"> </td>

   <td width="46"> </td>

  </tr>

  <tr>

   <td width="48">xori</td>

   <td width="52">001110</td>

   <td width="52"> </td>

   <td width="48"> </td>

   <td width="50"> </td>

   <td width="45"> </td>

   <td width="51"> </td>

   <td width="50"> </td>

   <td width="49"> </td>

   <td width="48"> </td>

   <td width="39"> </td>

   <td width="49"> </td>

   <td width="46"> </td>

  </tr>

  <tr>

   <td width="48">lw</td>

   <td width="52">100011</td>

   <td width="52"> </td>

   <td width="48">1</td>

   <td width="50">0</td>

   <td width="45">0</td>

   <td width="51">1</td>

   <td width="50">0</td>

   <td width="49">0</td>

   <td width="48">10(don’t care)</td>

   <td width="39">10</td>

   <td width="49">00</td>

   <td width="46">00</td>

  </tr>

  <tr>

   <td width="48">sw</td>

   <td width="52">101011</td>

   <td width="52"> </td>

   <td width="48"> </td>

   <td width="50"> </td>

   <td width="45"> </td>

   <td width="51"> </td>

   <td width="50"> </td>

   <td width="49"> </td>

   <td width="48"> </td>

   <td width="39"> </td>

   <td width="49"> </td>

   <td width="46"> </td>

  </tr>

  <tr>

   <td width="48">j</td>

   <td width="52">000010</td>

   <td width="52"> </td>

   <td width="48"> </td>

   <td width="50"> </td>

   <td width="45"> </td>

   <td width="51"> </td>

   <td width="50"> </td>

   <td width="49"> </td>

   <td width="48"> </td>

   <td width="39"> </td>

   <td width="49"> </td>

   <td width="46"> </td>

  </tr>

  <tr>

   <td width="48">jr</td>

   <td width="52">000000</td>

   <td width="52">001000</td>

   <td width="48"> </td>

   <td width="50"> </td>

   <td width="45"> </td>

   <td width="51"> </td>

   <td width="50"> </td>

   <td width="49"> </td>

   <td width="48"> </td>

   <td width="39"> </td>

   <td width="49"> </td>

   <td width="46"> </td>

  </tr>

  <tr>

   <td width="48">bltz</td>

   <td width="52">000001</td>

   <td width="52"> </td>

   <td width="48"> </td>

   <td width="50"> </td>

   <td width="45"> </td>

   <td width="51"> </td>

   <td width="50"> </td>

   <td width="49"> </td>

   <td width="48"> </td>

   <td width="39"> </td>

   <td width="49"> </td>

   <td width="46"> </td>

  </tr>

  <tr>

   <td width="48">beq</td>

   <td width="52">000100</td>

   <td width="52"> </td>

   <td width="48">0</td>

   <td width="50">0</td>

   <td width="45">0</td>

   <td width="51">0</td>

   <td width="50">0</td>

   <td width="49">0</td>

   <td width="48">00</td>

   <td width="39">00</td>

   <td width="49">01</td>

   <td width="46">00</td>

  </tr>

  <tr>

   <td width="48">bne</td>

   <td width="52">000101</td>

   <td width="52"> </td>

   <td width="48"> </td>

   <td width="50"> </td>

   <td width="45"> </td>

   <td width="51"> </td>

   <td width="50"> </td>

   <td width="49"> </td>

   <td width="48"> </td>

   <td width="39"> </td>

   <td width="49"> </td>

   <td width="46"> </td>

  </tr>

 </tbody>

</table>

Note that Table 3 has some entries which are “don’t care” values which have arbitrarily assigned with values. <strong>Procedure</strong>

Complete Table 3 by deriving all the remaining values of the control signals. Design the control unit using VHDL. The control unit in this implementation is a combinational logic circuit whose inputs are the opcode and function fields of the instruction and the outputs are the 10 control signals.  Test your control unit (through simulation) to ensure that it generates the correct values for the 10 control signal for each of the 20 instructions. You may either use a VHDL process for the control unit which will be added to your datapath designed in Lab 4, or you may design the control unit as a separate entity and use it as an additional component to be added with a port map statement to your existing datapath. Alternatively, the datapath of Lab 4 may be added as a component together with an instance of your control unit to create a new top level entity (with name cpu).

<strong>Use the following VHDL entity specification for the final CPU design</strong>:

library IEEE; use IEEE.std_logic_1164.all; use IEEE.std_logic_signed.all;

entity  cpu  is port(reset : in std_logic;      clk   : in std_logic;      rs_out, rt_out : out std_logic_vector(3 downto 0);     — output ports from register file

pc_out : out std_logic_vector(3 downto 0); — pc reg      overflow, zero : out std_logic); — will not be constrained                                       —  in Xilinx since                                       — not enough LEDs end cpu;

You will note that in the cpu entity, the top-level ports consist of the out_a ( rs ) and out_b (rt) ports of the register file and the PC register (with negated outputs to account for the active-low LEDs on the XUPV2Pro FPGA board.   There is an asynchronous reset (which will be constrained to a

switch input) and a clock input (constrained to the debounced clock input switch on the expansion

IO board of the FPGA board ). <strong>It is important that you use the above entity specification, as it is the one which will be used during the lab test</strong>.

Test your complete CPU by writing different test programs into the I-cache and <strong>verify correct operation of your CPU on the XUPV2Pro FPGA development board</strong>.


