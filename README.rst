===================================================================
RISC-V Soft CPU Security Contest by Thales and Microchip Technology
===================================================================

Thanks
******

We would like to thank Microchip Technology and the RISC-V Foundation for their participation in this contest. 
Additionally, thanks to John Merrill and John Wilander for their work on the RIPE tool.

You can contact us through the Github or at softcpu-contest@riscv.org 

Problem Overview
****************

The boom of the IoT provides, thanks to the massive number of deployed objects, a very efficient attack vector for DDoS.
These IoT devices are also increasing the attack surface of back-end systems (see `Thales Data Threat Report`_).
The `top attacks`_ should be addressed by properly managing credentials. 
Especially coding flaws leading to Buffer Overflows are a significant type of attacks that should be prevented.  
Obviously better coding practices should be encouraged but the execution platform should be also improved to mitigate such attacks.

In this contest, we are proposing to consider some very classical attacks:

* Corrupting a function pointer on the heap
* Buffer overflow on the stack to corrupt longjump buffer
* Buffer overflow on the stack to change the return address
* Corrupting a function pointer on the stack
* Corrupting a C structure holding a function pointer

An introduction to `Buffer overflow`_,  `Return Oriented Programming`_, and many others scan be easily found on the Internet. 

The testing code is "`RIPE`_, the Runtime Intrusion Prevention Evaluator".

Without changing the RIPE testing code, the contestants must develop protection strategies at the RISC-V microprocessor level (only the compiler may be modified to help the microprocessor). 
This cyber-improved RISC-V processor will have to either suppress or mitigate the success of these attacks.

Already existing strategies are heavily documented in the literature, but not so commonly deployed (https://en.wikipedia.org/wiki/Shadow_stack , https://en.wikipedia.org/wiki/Control-flow_integrity , https://www.usenix.org/node/190961 , etc.).

We encourage contestants to develop a cyber-improved version of RISC-V processors, and thus contributing to the Cyber-Security of the IoT.

Rules of the Contest
**********************

The Security Contest's objective is to propose hardware security counter measures to thwart classical software attacks. 
Entries should be composed of a RISC-V RV32IMC soft CPU and all libero files necessary to reproduce the bitstream (e.g. project, HDL source files, constraints, etc.) that can run the proposed attack software. 
The only software modifications authorised are on the compiler and the Zephyr configuration.

Microchip Creative Board
------------------------

The contests' entries will be tested on the Microchip `Creative Development Board`_. Licenses for this board are free and can be retrieved from the `Microchip Licensing`_ webpage.

Zephyr RTOS
-----------

We are using the 1.14 version of Zephyr for this contest. You can download and set it up through the GitHub link or directly with the west tool.
We advise using the `Zephyr Getting Started`_ guide. and retrieving the version below with west::

     west init zephyrproject --mr v1.14.1-rc1

Attack Software
---------------

The attack software is a modification for Zephyr of the `hope-RIPE`_  which is a RISC-V port of the runtime intrusion prevention evaluator `RIPE`_ from Wilander and Nikiforakis. 
You can change the ATTACK_NR symbol from 1 to 5 to select a different attack configuration.

Scoring and Prizes
******************

Scores will be attributed as follows:

- How many of the five security attacks are thwarted: 40%
- The total resources used, including logic elements, math blocks and internal RAM: 15%
- The Fmax of the implementation: 15%
- The expected power calculator consumption: 15% 
- Amount of changes to the compiler: 15%

Prizes will be attributed as follows:

* First place prize : 5000€ + `HiFive Unleashed`_ + `HiFive Unleashed Expansion Board`_
* Second place prize : 2000€ + `HiFive Unleashed`_
* Third place prize : 1000€ + `HiFive Unleashed`_

Setting up the Demonstration
****************************

In order to help contestants get their hands on the Creative Board, Libero and the Zephyr framework, we set up a project that triggers attacks on the MiV cpu.

Hardware
--------

For the demonstration we used the FPGA design from the IGL2_MiV_FreeRTOS_Demo of the `Future Electronics Design Center GitHub repository`_. You can regenerate it through Libero or flash it directly to your board with the FlashPro tool.

Zephyr RTOS
-----------

This FPGA design from Future Electronics requires some modifications from the IGLOO2 board configuration already present in the Zephyr baseline. You first need to apply the zephyr modification with::

    patch <your zephyrproject location>/zephyr/soc/riscv32/riscv-privilege/miv/dts_fixup.h file.patch

And then launch the ripe build with west::

    cd ripe/
    west build -b m2gl025_miv .

or with the cmake and make tools:: 

    mkdir my_build
    cd my_build
    cmake -DBOARD=m2gl025_miv ripe
    make

Running the code 
----------------

The Zephyr ELF can be loaded on the target using the Microsemi, a subsidiary of Microchip, provided OpenOCD. OpenOCD is part of the SoftConsole IDE.
SoftConsole 6.0 is available at the `Softconsole Download`_ page.

Once installed and your board connected to your development system, you can start OpenOCD::

    /usr/local/Microsemi_SoftConsole_v6.0/eclipse//../openocd/bin/openocd --file board/microsemi-riscv.cfg

Attach your debugger and load the executable, erase the FreeRTOS default demo, and run the executable ::

        thales@Linux:~/devel/hackhathon/RISC-V-IoT-Hackathon/ripe$ /home/thales/devel/hackhathon/toolchain/riscv32-zephyr-elf/bin/riscv32-zephyr-elf-gdb build/zephyr/zephyr.elf 
        GNU gdb (crosstool-NG 1.24.0-rc2-dirty) 8.2.1
        Copyright (C) 2018 Free Software Foundation, Inc.
        License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
        This is free software: you are free to change and redistribute it.
        There is NO WARRANTY, to the extent permitted by law.
        Type "show copying" and "show warranty" for details.
        This GDB was configured as "--host=x86_64-build_pc-linux-gnu --target=riscv32-zephyr-elf".
        Type "show configuration" for configuration details.
        For bug reporting instructions, please see:
        <http://www.gnu.org/software/gdb/bugs/>.
        Find the GDB manual and other documentation resources online at:
        <http://www.gnu.org/software/gdb/documentation/>.
        
        For help, type "help".
        Type "apropos word" to search for commands related to "word"...
        Reading symbols from build/zephyr/zephyr.elf...done.
        (gdb) target remote localhost:3333
        Remote debugging using localhost:3333
        warning: Target-supplied registers are not supported by the current architecture
        0x60002798 in ?? ()
        (gdb) load
        Loading section vector, size 0x10 lma 0x80000000
        Loading section exceptions, size 0x268 lma 0x80000010
        Loading section text, size 0x6290 lma 0x80000278
        Loading section sw_isr_table, size 0x150 lma 0x80006508
        Loading section devconfig, size 0x3c lma 0x80006658
                Loading section rodata, size 0x1344 lma 0x80006694
        Loading section datas, size 0x84c lma 0x800079d8
        Loading section initlevel, size 0x3c lma 0x80008224
        Loading section _k_mutex_area, size 0x14 lma 0x80008260
        Start address 0x80000000, load size 33396
        Transfer rate: 7 KB/sec, 3339 bytes/write.
        (gdb) c
        Continuing.

        Program received signal SIGTRAP, Trace/breakpoint trap.
        0x60000658 in ?? ()
        (gdb) load
        Loading section vector, size 0x10 lma 0x80000000
        Loading section exceptions, size 0x268 lma 0x80000010
        Loading section text, size 0x6290 lma 0x80000278
        Loading section sw_isr_table, size 0x150 lma 0x80006508
        Loading section devconfig, size 0x3c lma 0x80006658
        Loading section rodata, size 0x1344 lma 0x80006694
        Loading section datas, size 0x84c lma 0x800079d8
        Loading section initlevel, size 0x3c lma 0x80008224
        Loading section _k_mutex_area, size 0x14 lma 0x80008260
        Start address 0x80000000, load size 33396
        Transfer rate: 7 KB/sec, 3339 bytes/write.
        (gdb) c
        Continuing.

On the UART console, the result of the attack is displayed (Here for the attack #2) ::

        ***** Booting Zephyr OS v1.14.1-rc1 *****
        [z_sched_lock]  scheduler locked (0x80040cf8:255)
        [k_sched_unlock]  scheduler unlocked (0x80040cf8:0)
        RIPE is alive! m2gl025_miv
        -t direct -i shellcode -c longjmpstackparam -l stack -f homebrew----------------
        Shellcode instructions:
        lui t1,  0x80002               80002337
        addi t1, t1, 0x30c                 30c30313
        jalr t1000300e7
        ----------------
        target_addr == 0x80041ae0
        buffer == 0x800416b0
        payload size == 1077
        bytes to pad: 1060

        overflow_ptr: 0x800416b0
        payload: 7#

        Executing attack... success.
        Code injection function reached.
        exit


.. _Zephyr Getting Started: https://docs.zephyrproject.org/latest/getting_started/index.html
.. _Creative Development Board: https://www.futureelectronics.com/fr/resources/videos/future-electronics-microsemi-creative-development-board
.. _hope-RIPE: https://github.com/draperlaboratory/hope-RIPE
.. _RIPE: https://github.com/johnwilander/RIPE
.. _Future Electronics Design Center github repository: https://github.com/Future-Electronics-Design-Center/Creative-Eval-Board
.. _HiFive Unleashed: https://www.crowdsupply.com/sifive/hifive-unleashed
.. _HiFive Unleashed Expansion Board: https://www.crowdsupply.com/microsemi/hifive-unleashed-expansion-board
.. _Microchip Licensing: https://www.microsemi.com/product-directory/design-resources/1711-licensing
.. _Softconsole Download: https://www.microsemi.com/product-directory/design-tools/4879-softconsole#downloads
.. _Thales Data Threat Report: https://www.thalesesecurity.com/2019/data-threat-report
.. _top attacks: https://securelist.com/new-trends-in-the-world-of-iot-threats/87991/
.. _Buffer overflow: https://www.owasp.org/index.php/Buffer_overflow_attack
.. _Return Oriented Programming: https://en.wikipedia.org/wiki/Return-oriented_programming
.. _RIPE: https://github.com/johnwilander/RIPE

