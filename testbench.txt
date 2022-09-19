library ieee;
use ieee.std_logic_1164.all;
 
entity testbench is
end testbench;

architecture tb of testbench is

    component T_FlipFlop is
	port(
      Clk: in std_logic;
      reset: in std_logic;
      T: in std_logic;
      Q: in std_logic;
      Qp: out std_logic);
    end component;
    
    component Circuit is
    port(
        Clk: in std_logic;
        reset: in std_logic;
        X: in std_logic; 
        Qi: in std_logic_vector(3 downto 0);
        Z: out std_logic_vector(3 downto 0));
    end component;
	
    constant ClockFrequency : integer := 100e6;
    constant ClockPeriod    : time    := 1000 ms / ClockFrequency;

    signal Clk_in, X_in: std_logic:='0';
    signal finished: std_logic:='0';
    signal reset_in: std_logic:='0';
    signal Q_init: std_logic_vector(3 downto 0):="0000";
    signal Z_out: std_logic_vector(3 downto 0);
  
begin

    DUT: Circuit port map(Clk_in, reset_in, X_in, Q_init, Z_out);

    Clk_in <= not Clk_in after ClockPeriod / 2 when finished /= '1' else '0';
    
    process is

    begin
    		X_in <= '0';
            wait for 173 ns;

			X_in <= '1';
			wait for 76 ns;
            
            X_in <= '0';
            wait for 41 ns;

			X_in <= '1';
			wait for 98 ns;
            
            X_in <= '0';
			wait for 126 ns;
            
            X_in <= '1';
            wait for 80 ns;
            
         finished <= '1';
        
        wait;
    end process;
 
end tb;