===================================================================
RISC-V Soft CPU Security contest by Thales and Microchip Technology
===================================================================

Thanks
******

We would like to thanks Microchip Technology and the RISC-V Foundation for their participation on this contest. We also thank John Merrill and John Wilander for their work on the RIPE tool.

Rules of the Hackathon
**********************

The Security Hackathon objective is to propose hardware security counter measures to thwart classical software attacks. Entries should be composed of a RISC-V RV32IMC soft cpu in Verilog that can run the proposed attack software. The only software modifications authorised are on the compiler and the zephyr configuration.

Microchip Creative Board
------------------------

The contests' entries will be tested on the Microsemi `Creative Board`_ . Licenses for this board are free and can be retrieve from the licensing Microchip webpage.

Zephyr RTOS
-----------

We are using the 1.14 version of zephyr for this hackathon. You can download and set it up through the github link or directly with the west tool.
We advise using `Zephyr Getting Started`_.

Attack Software
---------------

The attack software is a modification for zephyr of the `hope-RIPE`_  itself a RISC-V port of the runtime intrusion prevention evaluator `RIPE`_ from Wilander and Nikiforakis. You can change the ATTACK_NR symbol from 1 to 5 to select a different attack configuration.

Scoring
*******

TBD

Setting up the Demonstration
****************************

In order to help contestants get their hands on the Creative Board, Libero and the Zephyr framework, we set up a project that trigs attacks on the MiV cpu.

Hardware
--------

For the demonstration We used the FPGA design from the IGL2_MiV_FreeRTOS_Demo of the `Future Electronics Design Center github repository`_. You can regenerate it through Libero or flash it directly to your board with the FlashPro tool.

Zephyr RTOS
-----------

Our FPGA design requires some modifications from the igloo2 board configuration already present in the Zephyr baseline. You first nee to apply the zephyr modification with ::

    cp file.patch <your zephyrproject location>/zephyr
    cd <your zephyrproject location>/zephyr
    git apply file.patch

before launching the ripe build.::

    cd ripe/
    west build -b m2gl025_miv .

or with the cmake and make tools:: 

    mkdir my_build
    cd my_build
    cmake -DBOARD=m2gl025_miv ripe
    make


.. _Zephyr Getting Started: https://docs.zephyrproject.org/latest/getting_started/index.html
.. _Creative Board: https://www.futureelectronics.com/fr/p/development-tools--development-tool-hardware/futurem2gl-evb-future-electronics-dev-tools-7091559
.. _hope-RIPE: https://github.com/draperlaboratory/hope-RIPE
.. _RIPE: https://github.com/johnwilander/RIPE
.. _Future Electronics Design Center github repository: https://github.com/Future-Electronics-Design-Center/Creative-Eval-Board
