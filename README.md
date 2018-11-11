# issi-programmer
Program ISSI QSPI flash for use with Arria 10, Cyclone 10 GX and Cyclone V devices


Requirements
------------

Quartus 18.1 Prime Lite or Prime Standard

Validation
----------

Tested on the Arrow SoCKit board (Cyclone V) and Intel PSG Arria 10 SoC Development kit (Arria 10) using IS25WP512 devices.


Modify the programmer reference design
--------------------------------------

For any targets other than the Arrow SoCKit board do the following

1. open Quartus 18.1
2. change the device to match the target board
3. modify the design to match the target board clk input
	a. identify the clk input pin, io standard and frequency
	b. modify the assignments for clk input pin and io standard
	c. modify the sdc file "create_clock" parameter to match the clock period
4. open platform designer with the qsys_top.qsys file
	a. edit the ext_clk_50m component "clock frequency"
	b. if target is Arria 10 then replace the Altera PLL with the IOPLL component
	   reconnect all clock to the IOPLL component.
	c. generate the system
	d. in Quartus under Project --> Add/Remove files in project  remove qsys_top/qsys_top.qip and replace with the newly generated one
	e. compile the design.

	
Create Payload using the Convert Programming Files Utility
-----------------------------------------------------------

1. open the utility - Files --> Convert Programming Files
	a. Programming file type : jic
		deselect Create Memory Map
		select Create config data RPD
	b. output_files/output_file.jic
	b. Configuration device: 
		1. Cyclone: Select QSPI128 or QSPI512 (for 3 byte or 4 byte addressing respectively)
		2. Arria 10: Select EPCQL512
	c. Mode: Active Serial x4
	d. Options/Boot info: Select Big Endian
	e. Advanced:
		1. Select Disable EPCS/EPCQ ID check (must see a check mark in the box)
		2. QSPI Flash single IO mode dummy clock
			i. Cyclone: 8
			ii. Arria 10: Unchangeable
		3. QSPI Flash quad IO mode dummy clock
			i. Cyclone: 6
			ii. Arria 10: Unchangeable
	f. Input files to convert
		1. Flash Loader: Add Device
		2. SOF Data: Add File - select the payload sof file
		3. Optional: turn on compression - click on sof file - click Properties - select Compression
	g. Generate
	h. rename outfile_file_auto.rpd to payload.rpd in output_files folder
	
	
Flash QSPI Non Voltile settings
-------------------------------

This only needs to be done once for a device.

1. in the Nios II Command Shell cd to the scripts directory
2. For Cyclone V: ./setup_issi_cv.sh
3. For Arria 10: ./setup_issi_a10.sh


Program Payload into ISSI flash device
--------------------------------------
	
1. connect the usb cable to the target and power on the target board
2. in the Nios II Command Shell cd to the scripts directory
3. ./program.sh
4. ./verify.sh
5. power cycle the board
6. see if the config done led is illuminated - this indicates the fpga is configured

