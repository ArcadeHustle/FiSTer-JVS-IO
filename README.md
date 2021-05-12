# FiSTer-JVS-IO 

<img height="200" src="https://github.com/ArcadeHustle/FiSTer-JVS-IO/blob/main/fist.jpg"><br>

- [FiSTer-JVS-IO](#fister-jvs-io)
  * [A project to enable JVS IO support for MiSTer FPGA *family*](#a-project-to-enable-jvs-io-support-for-mister-fpga-family)
  * [RS485 hardware for JVS Support integration](#rs485-hardware-for-jvs-support-integration)
  * [Verified OpenJVS Support](#verified-openjvs-support)
- [What's next?](#whats-next)
  * [Service core](#service-core)
  * [IO test core](#io-test-core)
  * [Coin settings](#coin-settings)
- [Why FiSTer?](#why-fister)

## A project to enable JVS IO support for MiSTer FPGA *family*
Now that the community is addressing JAMMA input, [MiSTer FPGA](https://github.com/MiSTer-devel/Main_MiSTer/wiki) is in need of proper arcade support via [JVS](https://wiki.arcadeotaku.com/w/JVS). If you are here searcing for [MiSTercade](https://misteraddons.com/products/mistercade) or JAMMA info, you are in the wrong place. Please see [MiSTercade review by MAVProxyUser](https://github.com/MAVProxyUser/MiSTercade-Review), a candid review of MiSTercade hardware in MiSTer landscape.<br>

The Sega Net City, and Naomi Universal line are perfect examples of JVS cabinets. Taito Viewlix, and Chewlix clones would be another example of potential candidate JVS "controllers".<br>

<img width="200" height="400" src="https://github.com/ArcadeHustle/FiSTer-JVS-IO/blob/main/netcity.jpg"><img width="200" height="400" src="https://github.com/ArcadeHustle/FiSTer-JVS-IO/blob/main/viewlix.jpg">

### RS485 hardware for JVS Support integration
The FiSTer-JVS-IO concept is now in the standalone prototype phase, it will no longer require any other IO boards. Our original intent was to make use of [SNAC](https://github.com/MiSTer-devel/Main_MiSTer/wiki/User-Port-(Serial-IO)) as a means to bring the most accurate JVS experience to MiSTerFPGA possible. Due to the required 5v logic for RS485 we considered simply making a variant of the [Accessory converter](https://github.com/blue212/SNAC) by Blue212. JVS is based on serial logic so it would require a level shift from 3v to 5v if we truly wanted to make use of the User IO port.<br>

<img src="https://github.com/ArcadeHustle/FiSTer-JVS-IO/blob/main/rs232_rs485.jpg">

Although we've not completely abandoned this path, we will be backburnering it for now as we focus on a USB based solution that fits more into the MiSTer landscape. It may be instinctive to ensure that all "arcade" cores support JVS via SNAC, the task would however be monumental. Because of that our current approach will make use of JVS emulation over serial, subsequently converted to USB events.<br>

Historically we would use either a Maxim MAX485, MAX3485, MaxLinear SP485, SP3485, or LT1785 for the task of converting RS485 to proper RS232 signaling. By using a CP2102 USB to serial SoC, alongside the RS485 transciever we can enable MiSTer to handle JVS without the need for SNAC. Best of all we can do so with only minor code changes.<br>
https://datasheets.maximintegrated.com/en/ds/MAX1487-MAX491.pdf<br>
https://www.maxlinear.com/ds/sp483-sp485.pdf<br>
https://www.analog.com/media/en/technical-documentation/data-sheets/LT1785-1785A-1791-1791A.pdf<br>
https://www.silabs.com/documents/public/data-sheets/CP2102-9.pdf<br>

### Verified OpenJVS Support
With the help of both [Bobby Dilly](https://twitter.com/bobbydilleyuk), and [Booger](https://twitter.com/marcusjordan) we've been able to get a functional example of JVS working in a recent fork of MiSTer. These changes will be backported to the current MiSTer tree, and submitted as a Pull Request in the coming weeks.<br>
[![OpenJVS Discord](https://github.com/ArcadeHustle/FiSTer-JVS-IO/blob/main/openjvs.jpg)](https://discord.com/invite/aJAR9N2)<br>

Our main requirement was that JVS needed to be parsed per the spec, and converted to input suitable for MiSTer:<br>
[JAMMA Video Standard (JVS) Third Edition](http://daifukkat.su/files/jvs_wip.pdf)<br>
[JAMMA VIDEO規格(第3版)](http://superusr.free.fr/arcade/JVS/JVST_VER3.pdf)

Our theory was that Bobby Dilly's [JVSCore](https://github.com/bobbydilley/JVSCore/blob/master/src/input.c#L40) could be used to present MiSTer with a compatible joystick via [/dev/uinput](https://github.com/MiSTer-devel/Main_MiSTer/blob/master/input.cpp#L1307), and that minimal system modification would be required. The OpenJVS /dev/uinput implementation should simply mate right up with MiSTer's Input.cpp.<br>

<img img width="400" height="323" src="https://github.com/ArcadeHustle/FiSTer-JVS-IO/blob/main/mating.jpg">
 
We were right, and it did, it pretty well worked out the box our first try! We only needed to nail down some input mapping issues. It is technically ready for mainstream, but we need to wait for some other things to cook first. We have confirmed to be able to support a standard Sega control panel with 2 players and 6 buttons each side.<br>

<img src="https://github.com/ArcadeHustle/FiSTer-JVS-IO/blob/main/jvscore.jpg">

# What's next? 
How do we mature the MiSTer landscape further for arcade use? What do we need? Well we obviously couldn't stop with the progress we made above. We needed something better than the generic USB RS485 adapter that we were currently using. Many of the theoretical design discussions have been tamped down, and in turn solidified into a final design [concept](https://github.com/ArcadeHustle/FiSTer-JVS-IO/blob/main/DoubleFiSTer.txt).

## Double Fister
Enter the DoubleFister, a single IO board with the intent of supporting two JVS arcade cabinets for simultaneous play. It will feature dual VGA, 3.5mm, and RCA outputs, dual JVS input, and a scriptable JVS injection button. (used to manually coin up, for now!) <br>

<img width="400" src="https://github.com/ArcadeHustle/FiSTer-JVS-IO/blob/main/Teasers/Two.png"><br>
<img width="400" src="https://github.com/ArcadeHustle/FiSTer-JVS-IO/blob/main/Teasers/One.png"><br>
<img width="400" src="https://github.com/ArcadeHustle/FiSTer-JVS-IO/blob/main/Teasers/Three.png"><br>

Once we complete the hardware testing phases, we will push for some software additions as well. If you would like to be a beta tester, please contact [@d0tslash](https://twitter.com/d0tslash), [@fsckewe](https://twitter.com/fsckewe), or [@JaviRodasG](https://twitter.com/JaviRodasG) via Twitter. 

## Single Fist
[SNAC](https://github.com/MiSTer-devel/Main_MiSTer/wiki/User-Port-(Serial-IO)) seems completely up to the task, however odd lines in the development sandbox prevent SNAC from having full access to MiSTer's menuing system. 
One approach would be to make use of a patched user_io subsystem as mentioned in the Atari-Forum thread ["How to use the menu with SNAC controllers and other USER_IO controller solutions"](https://www.atari-forum.com/viewtopic.php?t=38453).<br>

This approach *would likely* require yet another fork to be maintained due to staunch development guidelines for "official" cores. Full support of each core would require internal MiSTer integration of RS485 routines. Antonio Villena's [MiSTer_DB9](https://github.com/antoniovillena/MiSTer_DB9) has already addressed these same sort of integration issues in it's [environment](https://github.com/MiSTer-DB9/Forks_MiSTer/blob/35b7b7f3831a526fb9c18ab31eb29a61545fb18b/fork_ci_template/README%20DB9%20Support.md). Viewing the [initial import](https://github.com/MiSTer-DB9/Main_MiSTer/pull/1/files) of Antonio Villena's DB9 work gives a better idea of what is required to modify the core of MiSTer to potentially support native JVS over SNAC.<br>

In examining Antonio's IO-2-DB9 example we can see all the elements we need to make an RS485 add on board.<br>
https://github.com/antoniovillena/MiSTer_DB9/blob/master/Hardware/io2db9.brd<br>
https://github.com/antoniovillena/MiSTer_DB9/blob/master/Hardware/io2db9.sch<br>

The connection flow for a direct JVS connection to the USER IO appears to be as follows (confirmation needed):<br>
```
[MiSTer]                  [RS485 IC]              [USB3 TypeA]      [Sega 837-13551 JVS IO]
AG11 USERIO[0] (3.3v) <-> RO (UART_RX, JVS A) <-> Data+ (Pin 3) <-> A+  (Green)
AH9  USERIO[1] (3.3v) <-> DI (UART_TX, JVS B) <-> Data- (Pin 2) <-> B-  (White)
GND		      <-> GND                 <-> GND   (Pin 7  <-> GND (Black)
```

The Blue212 SNAC Adapter could easily be modified to handle this task. Adding an RS485 transciever will allow for existing serial / DB9 code to be reused for core control support. 
https://github.com/blue212/SNAC/blob/master/snac/snac-usb_partlist.txt<br>
https://github.com/blue212/SNAC/tree/master/snac/gerbers/snac_usb3<br>

Although it may be instinctive to ensure that all "arcade" cores support JVS via SNAC, the task will be monumental. To start we would be more than happy to simply have the existing SNAC [supported cores](https://github.com/MiSTer-devel/Main_MiSTer/wiki/Frequently-Asked-Questions#what-are-the-methods-for-connecting-controllers-to-the-serial-port-of-the-io-add-on-board) to be functional (SNES, Genesis, NES, and TG16). 

## Service core 
Nearly every arcade PCB in existance has a "test" / "service" menu in which various settings can be changed, and I/O can be checked. 

## IO test core
The Sega Naomi JVS IO test menu needs to be recreated as a basic core used to spot check for improperly connected, or stuck buttons or sticks on arcade control panels being used as input devices for MiSTer. 

<img src="https://github.com/ArcadeHustle/FiSTer-JVS-IO/blob/main/iotest.jpg">

## Coin settings
Similarly in the service menu most arcade PCBs have a coin setting that allows for free-play. It would be great to mimic this setting and flip the necessary bits to place arcade cores into free-play when they are eventually able to access the JVS protocol.

<img src="https://github.com/ArcadeHustle/FiSTer-JVS-IO/blob/main/coin.jpg">

# Why FiSTer?

Forever instigating shit that enrages randos!<br>
Frankly, I see through everything, Rysha!<br>
Fuck, I sure think emulation rocks!<br>
Finally, I see the evasive racism...<br>

