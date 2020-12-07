MSP430-GCC-TI Toolchain
=======================

This file cover the setup of the Texas Instruments GCC toolchain on OSX Catalina.

Installing the MSP430-GCC-TI toolchain
--------------------------------------

* Download the installer including support files from the TI website <https://www.ti.com/tool/MSP430-GCC-OPENSOURCE>.
I'm using the msp430-gcc-full-osx-installer-9.2.0.0.app.zip file as reference.

* Unzip the file and run the installer. It will ask for the root password to be able to install in the /opt folder.

* Install it at the `/opt/ti/msp430/gcc` folder. Use other folder if you have good reason to.

* `mspdebug is not installed by default and should be installed manually. It depends on the Boost library.
I use Macports to install such dependencies.

  ---
  $ sudo port install boost
  ---

* Clone the <https://github.com/dlbeer/mspdebug> repo somewhere:

  ---
  $ sudo git clone https://github.com/dlbeer/mspdebug
  $ cd mspdebug
  ---

- Compile and install the tool:

  ---
  $ make BOOST_DIR=/opt/local
  $ sudo make install
  $ sudo cp /opt/ti/msp430/gcc/bin/libmsp430.dylib /usr/local/lib/
  ---

The last command is to enable the `tilib` driver globally.
