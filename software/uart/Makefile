RISCV_TOOLS_PREFIX = C:\Dev\VSCode_RISCV_hex\data\xpack-riscv-none-elf-gcc-11.3.0-1\bin\riscv-none-elf-

#QUARTUS_DIR = C:\Dev\Quartus\quartus\bin64\
QUARTUS_DIR = D:\striped_quartus
CXX = $(RISCV_TOOLS_PREFIX)g++ -march=rv32im
CC = $(RISCV_TOOLS_PREFIX)gcc -march=rv32im -O1 -fpack-struct
AS = $(RISCV_TOOLS_PREFIX)gcc -march=rv32im
#CXXFLAGS = -MD -O1 -Wall -std=c++11
#CCFLAGS = -MD -O1 -Wall
#LDFLAGS = -Wl,--gc-sections,--no-relax
LDFLAGS = -Wl
LDLIBS =
MAIN = main_irq

#test: testbench.vvp $(MAIN)32.hex
#	# vvp -l testbench.log -N testbench.vvp

#testbench.vvp: testbench.v ../../picorv32.v $(MAIN)_dbg.v
#	#iverilog -o testbench.vvp testbench.v ../../picorv32.v
#	#chmod -x testbench.vvp

quartus_$(MAIN).hex: $(MAIN)32.hex
	python ../hex8tointel.py $(MAIN).tmp > quartus_$(MAIN).hex
#del /F /Q *.o *.d *.tmp *.elf *.lss *.map $(MAIN)32.hex

$(MAIN)32.hex: $(MAIN).elf ../hex8tohex32.py
	$(RISCV_TOOLS_PREFIX)objcopy -O verilog $(MAIN).elf $(MAIN).tmp
	$(RISCV_TOOLS_PREFIX)objdump -h -S $(MAIN).elf > "$(MAIN).lss"
	python ../hex8tohex32.py $(MAIN).tmp > $(MAIN)32.hex

start.o: ../_core/start.S
	$(CC) -c -nostdlib ../_core/start.S $(LDLIBS)

$(MAIN).elf: $(MAIN).o start.o ../_core/syscalls.o ../_core/utils.o uart.o ../gpio/gpio.o ../irq/interrupt.o
	$(CC) $(LDFLAGS),-Map=$(MAIN).map -o $@ $^ -T ../_core/sections.ld $(LDLIBS)

sint:
	$(QUARTUS_DIR)quartus_sh --flow compile ../peripherals/gpio/sint/de10_lite/de10_lite.cdf

fpga:
	$(QUARTUS_DIR)quartus_pgm -z -m JTAG -o "p;../peripherals/gpio/sint/de10_lite/output_files/de10_lite.sof"

flash:
	$(QUARTUS_DIR)quartus_stp_tcl -t "flash.tcl"

clean:
#	rm -f ../*/*.o *.o *.d *.tmp start.elf *.lss *.map
	Remove-Item -Path *.o, *.d, *.tmp, *.elf, *.lss, *.map -Force
#	rm -f $(MAIN).elf $(MAIN).hex $(MAIN)32.hex quartus_$(MAIN).hex	
	Remove-Item -Path $(MAIN).elf, $(MAIN).hex, $(MAIN)32.hex, quartus_$(MAIN).hex -Force


-include *.d
.PHONY: test clean







