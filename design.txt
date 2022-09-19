library ieee;
library work;
use ieee.std_logic_1164.all;
use ieee.numeric_std.ALL; 

entity T_FlipFlop is
port(
    Clk: in std_logic;
    reset: in std_logic;
    T: in std_logic;
    Q: in std_logic;
    Qp: out std_logic:=Q);
end T_FlipFlop;

architecture func of T_FlipFlop is

begin

    process(Clk, T, Q) is
    begin
      if (reset = '1') then
      	Qp <= '0';
      elsif (Clk'event and Clk = '1') then        
        if (T = '1') then
        	Qp <= not Q;
        end if;
      end if;
    end process;
 
end architecture;

library ieee;
use ieee.std_logic_1164.all;
use ieee.numeric_std.all;


entity Circuit is
port(
    Clk: in std_logic;
    reset: in std_logic;
    X: in std_logic;
    Qi: in std_logic_vector(3 downto 0); 
	Z: out std_logic_vector(3 downto 0));
end Circuit;

architecture main of Circuit is

signal Qp: std_logic_vector(3 downto 0):=Qi;
signal trueVal: std_logic_vector(3 downto 0):=Qi;

begin

	TF3: entity work.T_FlipFlop(func)
    port map(
	  Clk => Clk,
      reset => reset,
      T => ((not x) and Qp(2) and Qp(1) and Qp(0)) or ((not x) and Qp(3) and Qp(2)) or (x and (not Qp(2)) and (not Qp(1)) and (not Qp(0))) or (not x and Qp(3) and (Qp(1) or Qp(0))),
      Q => Qp(3),
      Qp => Qp(3)
    );
    
    TF2: entity work.T_FlipFlop(func)
    port map(
	  Clk => Clk,
      reset => reset,
      T => ((not x) and Qp(1) and Qp(0) and (not Qp(3))) or (x and (Qp(3) or Qp(2)) and (not Qp(1)) and (not Qp(0))) or (Qp(3) and Qp(2)),
      Q => Qp(2),
      Qp => Qp(2)
    );
    
    TF1: entity work.T_FlipFlop(func)
    port map(
	  Clk => Clk,
      reset => reset,
      T => ((not x) and (not Qp(3)) and Qp(0)) or (x and (Qp(1) or (Qp(3) xor Qp(2))) and (not Qp(0))) or (Qp(1) and Qp(3)),
      Q => Qp(1),
      Qp => Qp(1)
    );
    
    TF0: entity work.T_FlipFlop(func)
    port map(
	  Clk => Clk,
      reset => reset,
      T => ((not Qp(3)) or ((not Qp(2)) and (not Qp(1))) or (x xor Qp(0))),
      Q => Qp(0),
      Qp => Qp(0)
    );
    
    process(Z, Qp) is
        
	variable decimal: integer := to_integer(unsigned(trueVal)) - 1;
    variable first: integer := 1;
    
    begin
    
   	 if (Z'event) then
     	if (reset = '1') then
        	decimal := 0;
     	elsif (to_integer(unsigned(trueVal)) > 9 or first = 1) then
        	if (first = 1) then
              decimal := to_integer(unsigned(trueVal));
              first := 0; 
        	elsif (X = '0') then
              decimal := 0;
            elsif (X = '1') then
              decimal := 9;
            end if;
        elsif (X = '0') then
          decimal := (decimal + 1) mod 10;
        elsif (X = '1') then
          decimal := (decimal - 1) mod 10;
        end if;
        
        trueVal <= std_logic_vector(to_unsigned(decimal, trueVal'length));

    	report "X value: " & to_string(X) & ", Circuit output: " & to_string(Z) & ", True value: " & to_string(to_integer(unsigned(Z)));

    	assert(to_integer(unsigned(Z)) = decimal)
        report "------- Test failed" & ", X value: " & to_string(X) & ", Circuit output: " & to_string(Z) & ", True value: " & to_string(to_integer(unsigned(Z))) severity Error;
    
     end if;

     Z <= Qp;

   end process;
         
end architecture;