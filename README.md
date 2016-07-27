[Source](http://if2a.free.fr/documentation/FAQWTO.html "Permalink to if2a-0.94.4")

# if2a-0.94.4

In all the following examples, the option `-d` will be used. It tells to if2a not to write anything. If you want to write, don't use this option.

* * *

### 2.1. My cart is not or badly detected !

It seems that recent ultra carts need a recent multiboot program (the one loaded onto the GBA for I/O operations on carts). If nothing works (for instance **Unknown cart** displayed on GBA), an alternate multiboot program maybe tried. Inside the binaries version of if2a, two of them are available:

    **$ ./if2a -B list
    if2a - http://if2a.free.fr
    Available internal files for F2A-usb-linker-multiboot:
    	0 = binware/multiboot-f2a-usb-v2.4b.mb (22528 bytes)
    	1 = binware/multiboot-f2a-usb-v2.6bU.mb (23552 bytes)
    	or  name = any external file name**

The first one (0=) is the latest version which works properly with pro carts. These are not correctly detected with the latest multiboot program (1=) (got from PowerWriter 2.61). Recent ultra carts (any size, from 256Mbits to 2Gbits) need this last version:

    if2a -B 1 other options...

Old pro cart users may try the latest multiboot program with if2a since if2a tries and corrects the cart detection. Use this feature at your own risk (but it works at least for my two 256Mbits pro carts :-).

If you need to change the default behaviour of if2a (to use -B 1 by default), you need to get the sources, copy Makefile.defaultconf to Makefile.conf, edit it and recompile.

* * *

### 2.2. Write and delete roms

You might need a loader. Several loaders are internally available in if2a. You can see them with the following command. You need either to plug your GBA in so to automatically detect the flashcart type or to specify your flashcart type with the `-t` option.

    **if2a -L list**

From if2a-0.94, a minimal version of PogoShell is included in the loaders, with almost nothing in the filesystem, so to have a small and very powerfull loader which perfectly works with f2apro carts. This loader can also be used with f2a ultra carts (option `-L 1`). If you want to use your own Pogoshell build, just specify it with the `-L` option (see the Pogoshell paragraph).

* * *

#### 2.2.1. Without cart map (and without loader)

Let's suppose you have rom1.gba, rom2.gba and rom3.gba. The following command will put them into your cart (the loader will be automatically included).

    **if2a -d -W rom1.gba rom2.gba rom3.gba**

If you need to add rom4.gba later, you need the three other files:

    **if2a -d -W rom1.gba rom2.gba rom3.gba rom4.gba**

if2a will not rewrite rom1.gba through rom3.gba. Update: the if2a-0.94.2 built the cartmap with this `-W` option. This is no longer the case since if2a-0.94.3. Read about the cartmap in the next paragraph.

For the loader not to be automatically inserted in the beginning of the cart, (ex: a single flashme.gba or a nds roms to be burned), use the option `-n`:

    **if2a -d -W -n rom1.gba rom2.gba rom3.gba rom4.gba**

* * *

#### 2.2.2. With cart map (also called "if2a's easyrom" feature)

A flexible way to manage roms has appeared in if2a-0.94. Roms can be added with option `-A` and removed with option `-X`. For each operation, a rom map is maintained in the cart just after the loader. To be able to use them if no cartmap is present on your cart, you need to tell if2a to build one. The option `-Y` will do this:

    **if2a -d -Y -A mynewrom.gba -A "my other new rom.gba" -X myoldbuggyhomebrewprogram -A "my new corrected program.gba,showed name in loader"**

The `-Y` option will forget any older cartmap from the cart. It will rebuild a new one according to the command line. Then you can add / remove roms:

    **if2a -d -A anotherrom.gba -X "showed name in loader"**

As you can see, this is flexible. if2a will tell you whether what you want to do is possible. If names contain spaces, you need to double quote. You can change the rom name specifying it after the comma like in the example.

Note: one cannot build a map from scratch according to what's already existing in cart. If you already have roms in cart, they cannot be taken into account in the "easyrom" cartmap unless they are reburned using the `-Y` / `-A` options.

* * *

#### 2.2.3. Header correction

By default, headers are gba-ly automatically corrected. If this is unwanted, use the option `-H`:

    **if2a -d -H -W -n rom1.nds.gba rom2...**

* * *

#### 2.2.4. Pogoshell

Pogoshell is usable in conjunction with the easyrom feature. By default (for Pro carts), a minimal version of pogoshell is used as the default loader. This can be changed by the `-L` option. The first time you can do (with optional `-A` / `-X` options, see above):

    **if2a -d -Y -L flashme.gba**

Then you can add or remove roms using if2a and without the need to rebuild a new pogoshell everytime (see above). Your pogoshell will see them in the cartrom/ directory. Note: you cannot remove a rom which is inside pogoshell using if2a.

In case you prefer not using the if2a easyrom feature, you still can do:

    **if2a -d -W -n flashme.gba**

* * *

### 2.3. Saves and SRAM management

#### 2.3.1. How to backup all cart sram ?

The command is:

    **if2a -d -b all -r all-sram.bin**

According to your cart type, it will create 256KB (f2apro) or 128KB (f2au) file. The option `-r` tells if2a that the SRAM is to be read.

* * *

#### 2.3.2. How to backup a rom save when using Flash2Advance Pro loader (gba-loader 3.2 or 3.3) ?

First check which sram bank is used by your rom. By default, the first four banks use 64KB each which are designed as bank 1, 2, 3 or 4 and they refer respectively to the rom number 1, 2, 3 or 4. If you play with Select, B, R or L in the loader, you can change the sram bank used for the rom. The bank used takes names like '`1a`' or '`2b3`'. You must use these names with the `-b` option in if2a:

    **if2a -d -b 2b3 -r myromsave.bin**

You can restore it back to GBA with:

    **if2a -d -b 2b3 -w myromsave.bin**

* * *

#### 2.3.3. How to backup a rom save when using PogoShell ?

There is no automatic way to backup a particular save rom when using PogoShell yet. What can be done is to save everything, or if you need to backup a particular rom's sram, you can start this rom with PogoShell (using its save) then power-off the GBA. PogoShell will have make an expanded copy of the rom save in the first 64Ko bank of the sram cart. You can then back it up with the command:

    **if2a -d -b 1 -r mysamerom.bin**

To restore it back to the cart, you should start again the rom from PogoShell, then power-off the GBA, then write the sram data with:

    **if2a -d -b 1 -w mysaverom.bin**

Then power-on PogoShell back, it will ask if the save rom has to be saved, answer yes.

IMPORTANT NOTE: before doing this, you should make a whole backup of your sram.

* * *

### 2.4. *NIX

#### 2.4.1. Using sudo

if2a needs to be run as "root" so that it can access the usb devices. You might be interested in staying who you really are when using if2a. In order to do that, you can use the **sudo** program that temporarily gives the root power to a normal user for a specific command. You need to have this program (often included in a packaged called sudo) installed on your machine and if2a copied in /usr/local/bin (where it should usually be). Then you need to edit the file /etc/sudoers, and to add the following line:

    myself	ALL=NOPASSWD:/usr/local/bin/if2a

(where "myself" is your account login). Then you will be able to do, beeing yourself:

    sudo if2a [command and options]

* * *

#### 2.4.2. I get the annoying message 'Device or resource busy' !

This happens under Linux with some distributions that include the `usbtest` module in the kernel configuration. This module is automatically loaded by hotplug when the linker renumerates, and prevents if2a from using the cable.

The error message looks like this:

    usb_set_configuration: could not set config 1: Device or resource busy

To check if it is really `usbtest` that messes things up, try the command **dmesg|tail**, here it says:

    usb 1-2.4: usbfs: interface 0 claimed by usbtest while 'if2a' sets config #1

This module can be unloaded (as root) with the command **rmmod usbest** then if2a can be restarted. For future use, you can add the word `usbtest` at the end of the file **/etc/hotplug/blacklist**, this will prevent hotplug from loading this module.  
Night Mode
