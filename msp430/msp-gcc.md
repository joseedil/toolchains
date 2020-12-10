MSP430-GCC-TI Toolchain
=======================

This file cover the setup of the Texas Instruments GCC toolchain on OSX Catalina.

Installing the MSP430-GCC-TI toolchain
--------------------------------------

* Download the installer including support files from the TI website <https://www.ti.com/tool/MSP430-GCC-OPENSOURCE>.
I'm using the msp430-gcc-full-osx-installer-9.2.0.0.app.zip file as reference.

* Unzip the file and run the installer. It will ask for the root password to be able to install in the /opt folder.

* Install it at the `/opt/ti/msp430/gcc` folder. Use other folder if you have good reason to.

* `mspdebug is not installed by default and should be installed manually. It depends on the Boost and libusb libraries.
I use Macports to install such dependencies.

  ~~~
  $ sudo port install boost libusb libusb-compat hidapi
  ~~~

* Clone the <https://github.com/dlbeer/mspdebug> repo somewhere:

  ~~~
  $ git clone https://github.com/dlbeer/mspdebug
  $ cd mspdebug
  ~~~

- Compile and install the tool:

  ~~~
  $ make BOOST_DIR=/opt/local
  $ sudo make install
  $ sudo cp /opt/ti/msp430/gcc/bin/libmsp430.dylib /usr/local/lib/
  ~~~

The last command is to enable the `tilib` driver globally.

* Add the toolchain to your PATH by appending the following to your `~/.bashrc`:

  ~~~
  export PATH=$PATH:/opt/ti/msp430/gcc/bin/
  export MSP430_GCC_INCLUDE_DIR=/opt/ti/msp430/gcc/include
  ~~~

Flashing and debugging the Lanchpad F5529
-----------------------------------------

* Write a blick example somewhere and save as `main.cpp`:

  ~~~
  #include <msp430.h>

  #define LED1 BIT0

  void delay(unsigned int millis) {
    volatile unsigned int i, j;
    for(j = 0; j < millis; j++) {
      for(i=100; i>0; i--) {}
    }
  }

  int main(void) {
    WDTCTL = WDTPW + WDTHOLD;

    P1DIR = LED1;
    P1OUT = LED1;

    while(1) {
      P1OUT ^= LED1;
      delay(1000);
    }

    return 0;
  }
  ~~~

* Compile with

  ~~~
  $ msp430-elf-gcc -mmcu=msp430f5529 -o main.elf main.cpp
  ~~~

* Flash the device directly with `mspdebug`:

  ~~~
  $ mspdebug tilib
  (mspdebug) prog blink.elf
  (mspdebug) run
  ~~~

Use the `exit` command to quit `mspdebug`.

* Using `gdb`: on another terminal open `mspdebug` and start the gdb server.

  ~~~
  $ mspdebug tilib
  (mspdebug) gdb
  ~~~

* Open `gdb` and connect with the server:

  ~~~
  $ msp430-elf-gdb main.elf
  (gdb) target remote :2000
  (gdb) monitor erase
  (gdb) load
  (gdb) continue
  ~~~

Useful resources
----------------

installing the tools: <https://github.com/chintal/toolchains/blob/master/msp430-gcc-ti.md>

mspdebug: `<http://pm.bu.edu/~tt/mspdebug+launchpad.pdf>
