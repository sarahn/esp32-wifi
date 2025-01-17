# ESP32-C6 on Ubuntu 22.04

## Install compilation environment
Prerequisites: Native running rust toolchain

    rustup toolchain install stable --component rust-src
    rustup target add riscv32imac-unknown-none-elf
    sudo apt install git wget flex bison gperf python3 python3-pip python3-venv cmake ninja-build ccache libffi-dev libssl-dev dfu-util libusb-1.0-0
    cargo install esp-generate
    # This adds the espflash command to cargo and is not needed
    # cargo install cargo-espflash 
    cargo install espflash
    sudo usermod -aG dialout ${USER}
    
## Install debugger

    git clone https://github.com/espressif/openocd-esp32.git
    cd openocd-esp32
    # Examine rules first if desired
    sudo cp ./contrib/60-openocd.rules /etc/udev/rules.d/60-openocd.rules
    sudo apt install pkg-config libudev-dev libusb-1.0-0-dev libtool gdb-multiarch ddd
    sudo -k
    # Follow openocd-esp32 README
    ./bootstrap
    ./configure
    ./make

## Prepare terminal    
		# If you have not logged back in and out
		newgrp dialout
		# if not specified elsewhere
		export PATH=$PATH:${HOME}/.cargo/bin
		# If you want to log your terminal
		script session-$(date --rfc-3339=seconds | tr ' :' -).log
 
    
 ## New project (not needed here)
    esp-generate --chip=esp32c6 esp-hello-world

## Running

    cd esp32-wifi
    # Maybe add ESP_LOG=DEBUG or --release
    SSID=thessid PASSWORD="" cargo run

## Debugging

### Start server

    # Plug cable into right USB port when ports are facing you
    cd openocd-esp32/tcl
    ../src/openocd -f board/esp32c6-builtin.cfg
    # Quit out when the board needs to be reprogrammed
    # TODO how to reprogram using openocd
 
### Using client

	    # In one terminal
	    gdb-multiarch target/riscv32imac-unknown-none-elf/release/dhcp
	    # or
	    ddd --debugger gdb-multiarch target/riscv32imac-unknown-none-elf/release/dhcp
	    # or figure out vscode integration
	    # At gdb prompt
	    target extended-remote localhost:3333
	    # set breakpoints etc as desired
	    
	    # In another terminal
	    telnet localhost 4444
	    init # (?)
	    reset # (?) to start the program over
	    
	    # In debugger, if using gdb prompt
	    c # or continue
