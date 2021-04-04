# FiSTer-JVS-IO 
#### A project to enable JVS IO support for MiSTer FPGA *family*

<img height="200" src="https://github.com/ArcadeHustle/FiSTer-JVS-IO/blob/main/fist.jpg"><br>

Now that the community is addressing JAMMA input, [MiSTer FPGA](https://github.com/MiSTer-devel/Main_MiSTer/wiki) is in need of proper arcade support via JVS. 

### RS485 hardware options for JVS Support integration
FiSTer-JVS-IO will need to be compatible with the standard [MiSTer IO-Board](https://github.com/MiSTer-devel/Main_MiSTer/wiki/IO-Board) in order to make use of the 3.5mm audio, and VGA outputs. This could change in the future as the project becomes less dependent on the existing landscape. 

Current MiSTer DB9 support is done via standard RS232 signaling, which triggers the need for an adapter for 5 volt logic. The [Accessory converter](https://github.com/blue212/SNAC) by Blue212 serves this purpose. JVS is based on RS485 so it will require a similar level shift from 3v to 5v if we make use of the User IO port.<br>
<img src="https://github.com/ArcadeHustle/FiSTer-JVS-IO/blob/main/rs232_rs485.jpg">

We are familiar with using either a Maxim MAX485, or a MaxLinear SP485 for the task of converting RS485 to RS232 signaling. In this form MiSTer can hopefully handle JVS via SNAC with minor code changes.  
https://datasheets.maximintegrated.com/en/ds/MAX1487-MAX491.pdf<br>
https://www.maxlinear.com/ds/sp483-sp485.pdf

### Serial Native Accessory Converter via User Port (Serial IO)
[SNAC](https://github.com/MiSTer-devel/Main_MiSTer/wiki/User-Port-(Serial-IO)) seems completely up to the task, however odd lines in the development sandbox prevent SNAC from having full access to MiSTer's menuing system. 
One approach would be to make use of a patched user_io subsystem as mentioned in the Atari-Forum thread ["How to use the menu with SNAC controllers and other USER_IO controller solutions"](https://www.atari-forum.com/viewtopic.php?t=38453).

This approach *would likely* require yet another fork to be maintained due to staunch development guidelines for "official" cores. Full support of each core would require internal MiSTer integration of RS485 routines. Antonio Villena's [MiSTer_DB9](https://github.com/antoniovillena/MiSTer_DB9) has already addressed these same sort of integration issues in it's [environment](https://github.com/MiSTer-DB9/Forks_MiSTer/blob/35b7b7f3831a526fb9c18ab31eb29a61545fb18b/fork_ci_template/README%20DB9%20Support.md). Viewing the [initial import](https://github.com/MiSTer-DB9/Main_MiSTer/pull/1/files) of Antonio Villena's DB9 work gives a better idea of what is required to modify the core of MiSTer to potentially support native JVS over SNAC.

Although it may be instinctive to ensure that all "arcade" cores support JVS via SNAC, the task will be monumental. To start we would be more than happy to simply have the existing SNAC [supported cores](https://github.com/MiSTer-devel/Main_MiSTer/wiki/Frequently-Asked-Questions#what-are-the-methods-for-connecting-controllers-to-the-serial-port-of-the-io-add-on-board) to be functional. 
The wording "Supporting cores (SNES, Genesis, NES, and TG16) allow to directly connect original controllers", could be applied to JVS support, and in essence make any JVS capable arcade platform an "original controller". The Sega Net City, and Naomi Universal line are perfect examples of JVS cabinets. Taito Viewlix, and Chewlix clones would be another example of potential candidate "controllers".

<img width="200" height="400" src="https://github.com/ArcadeHustle/FiSTer-JVS-IO/blob/main/netcity.jpg"><img width="200" height="400" src="https://github.com/ArcadeHustle/FiSTer-JVS-IO/blob/main/viewlix.jpg">

####

In examining Antonio's IO-2-DB9 example we can see all the elements we need to make an RS485 add on board.  
https://github.com/antoniovillena/MiSTer_DB9/blob/master/Hardware/io2db9.brd
https://github.com/antoniovillena/MiSTer_DB9/blob/master/Hardware/io2db9.sch

You can clearly see that Serial 0, and Serial 1 are represented by pins AH9+AG11, and AF13+AG13
<img src="https://github.com/ArcadeHustle/FiSTer-JVS-IO/blob/main/io2db9.png"><br>
<img src="https://github.com/ArcadeHustle/FiSTer-JVS-IO/blob/main/userio.png"><br>

These pin names in turn correspond with an Arduino pin map. For example Arduino_IO14 is PIN_AH9, aka USER_IO[1], or D- on the SNAC USB board!
<img height="400" src="https://github.com/ArcadeHustle/FiSTer-JVS-IO/blob/main/pinout.png"><img height="400" src="https://github.com/ArcadeHustle/FiSTer-JVS-IO/blob/main/snacusb.png"><br>

[Examples:](https://misterfpga.org/viewtopic.php?t=1528)
```
Arduino_IO15 AG11  USER_IO[0]
Arduino_IO14 AH9   USER_IO[1]
Arduino_IO13 AH12  USER_IO[2]
...
Arduino_IO8 AF17   USER_IO[6]
```

### RS485 to HID
Converting directly to HID keyboard output is a potential option. At the very least [Input.cpp](https://github.com/MiSTer-devel/Main_MiSTer/blob/master/input.cpp) from Main_MiSTer would need to be patched to parse JVS frames using their start/stop bits. Building a new parser may not be trivial, but is certainly possible. 
The WCH CH9328 is a suitable hardware choice for getting input to MiSTer, in this case from RS485, to HID.<br>
http://www.wch.cn/product/ch9328.html<br>
http://www.wch-ic.com/products/CH9328.html<br>
http://www.wch.cn/downloads/file/224.html

### RS485 to UART
Perhaps the easiest option is to make use of an RS485 to RS232 board with a built in USB bridge, as folks have done for years with bootleg Taito X2, and X3 machines. 
[![Poor Mans JVS](http://img.youtube.com/vi/kqXEYtvGzno/0.jpg)](https://www.youtube.com/watch?v=kqXEYtvGzno)<br>

## What do I buy to help out with development!? 

Three commercial products give us a chance to play with this theory of JVS operations alongside MiSTer.<br>

SNAC to USB:<br>
https://github.com/blue212/SNAC/blob/master/snac/snac-usb_partlist.txt<br>
https://github.com/blue212/SNAC/tree/master/snac/gerbers/snac_usb3<br>
https://misterfpga.co.uk/product/snac-serial-native-accessory-converter-usb3-version-bare-pcb/<br>

RS485 to HID:<br>
https://www.amazon.com/RS485-Serial-Keyboard-Protocol-CH9328/dp/B083XR42KT<br>

RS485 to RS232:<br>
https://www.amazon.com/gp/product/B078X5H8H7/<br>

## Verified OpenJVS Support
[![OpenJVS Discord](https://github.com/ArcadeHustle/FiSTer-JVS-IO/blob/main/openjvs.jpg)](https://discord.com/invite/aJAR9N2)<br>

Either way JVS will need to be parsed per the spec, and converted to input suitable for MiSTer:<br>
[JAMMA Video Standard (JVS) Third Edition](http://daifukkat.su/files/jvs_wip.pdf)<br>
[JAMMA VIDEO規格(第3版)](http://superusr.free.fr/arcade/JVS/JVST_VER3.pdf)

Our theory was that Bobby Dilly's [JVSCore](https://github.com/bobbydilley/JVSCore/blob/master/src/input.c#L40) could be used to present MiSTer with a compatible joystick via [/dev/uinput](https://github.com/MiSTer-devel/Main_MiSTer/blob/master/input.cpp#L1307), and that minimal system modification would be required. The OpenJVS /dev/uinput implementation should simply mate right up with MiSTer's Input.cpp.<br>
<img img width="400" height="323" src="https://github.com/ArcadeHustle/FiSTer-JVS-IO/blob/main/mating.jpg">
 
We were right, and it did, it pretty well worked out the box our first try! We need to nail down some input mapping issues, and it will be ready for mainstream. We have confirmed to be able to support a standard Sega control panel with 2 players and 6 buttons each side.<br>
<img src="https://github.com/ArcadeHustle/FiSTer-JVS-IO/blob/main/jvscore.jpg">

# What's next? 
How do we mature the MiSTer landscape further for arcade use? What do we need? 

## Service core 
Nearly every arcade PCB in existance has a "test" / "service" menu in which various settings can be changed, and I/O can be checked. 

## IO test core
The Sega Naomi JVS IO test menu needs to be recreated as a basic core used to spot check for improperly connected, or stuck buttons or sticks on arcade control panels being used as input devices for MiSTer. 

<img src="https://github.com/ArcadeHustle/FiSTer-JVS-IO/blob/main/iotest.jpg">

## Coin settings
Similarly in the service menu most arcade PCBs have a coin setting that allows for free-play. It would be great to mimic this setting and flip the necessary bits to place arcade cores into free-play when they are eventually able to access the JVS protocol via SNAC.

<img src="https://github.com/ArcadeHustle/FiSTer-JVS-IO/blob/main/coin.jpg">

