__Ursäkta, denna sida är på engelska!__

No warranty provided. Here you will find some results from our meetings.

To be clear, these guides are a few years old (2016-2019), and there is for instance a new libreboot version since then <https://libreboot.org/news/libreboot20210522.html>. Not a big deal, but something to investigate.

# Coreboot and Libreboot introduction

For more information about Libreboot see <https://libreboot.org>, here we only quickly describe some hints and script we used during flashing.

Note, it is not possible to run Windows on Libreboot/Coreboot but it is possible to run free operating systems like Linux and BSD.

For flashing see this [(X200 Libreboot) guide](https://libreboot.org/docs/install/x200_external.html) for a general idea, however each system has their own chip configuration which differs a little.

When installing an OS on a Libreboot (/Coreboot) system, use these [guides](https://libreboot.org/docs/gnulinux/) (old link?).

When installing Debian I used [this guide](https://libreboot.org/docs/gnulinux/encrypted_debian.html) in particular. The guide uses LUKS->LVM->Partitions, thus everything is encrypted. You don't need to install GRUB since it is already on the libreboot ROM (if you're using the GRUB payload), however, using a local GRUB makes it possible to [change GRUB config](https://libreboot.org/docs/gnulinux/grub_cbfs.html) without reflashing ROM. But reflashing ROM can be done in the OS (boot with kernel flag iomem=relaxed).
If you have an unencrypted /boot the default GRUB-scripts works better, i e it boots automatically (using Libreboot2016). Otherwise, it can be fixed manually with a better GRUB config ... well basically it should work by just adding _cryptomount -a_ before mounting the volumes? 

__Note:__ So for security it is wise to encrypt /boot/, which is then decrypted before booting to OS, using GRUB config on flash chip. Just one warning, _older_ versions of Coreboot and Libreboot did not support LUKS v2 so use LUKS v1. Then you were required to have /boot/ as LUKSv1 so that you can then decrypt the main drive once kernel has been loaded, or of course, exclusively use LUKSv1.
But since [Libreboot 20210522](https://libreboot.org/news/libreboot20210522.html) LUKSv2 is supported.



# General flash guides 

## Our external flashing setup

BeagleBone Black (BBB), powered by USB. 5A 2A

Tip: Don't power anything from a regular (computer) ATX power supply, it will work but is unreliable and a time waster.

Current setup is to connect to the BBB via Ethernet to a router and thus SSH. Can also connect directly with the SPI interface, see Libreboot flashing guide.

Pomona chip holders

10cm wires



## Flashing Internally

To internally flash your ROM (on Coreboot or Libreboot) use the kernel parameter "iomem=relaxed". Thus, if you boot manually you would enter e g "linux=vmlinuz.. iomem=relaxed".

Get a newer version of flashrom than in Debian repo. In my case the older version did not detect the exact chip on x200, which was fixed by getting most recent version.

Note these tools are built and ready [at](https://www.mirrorservice.org/sites/libreboot.org/release/stable/20160907/libreboot_r20160907_util.tar.xz) or similar.

    #backup current rom
    sudo ./flashrom  -p internal -r my.rom

    #Optional: remove old GRUB (test) config
    ./cbfstool my.rom remove -n grubtest.cfg
    #Optional: get the new GRUB (test) config into your ROM
    ./cbfstool my.rom add -n grubtest.cfg -f grubtest.cfg -t raw
    
    #Optional: change background in GRUB menu (dest must be called background.png /jpg)
    #./cbfstool my.rom remove background.png -n background.png
    #./cbfstool my.rom add -f background.png -n background.png -t raw

    # Required.. If you use new ROM or haven't set MAC address before, this needs to be done!
    # These instructions I took from Libreboot flash instructions
    # (used ich9gen from Libreboot util archive)
    #mv my.rom resources/utilities/ich9deblob/my.rom
    #cd resources/utilities/ich9deblob/
    #./ich9gen --macaddress "00:DE:AD:BE:EF:00" # SET A UNIQUE MAC ADDRESS HERE!!
    #dd if=ich9fdgbe_8m.bin of=my.rom bs=1 count=12K conv=notrunc
    #cd - && mv resources/utilities/ich9deblob/my.rom .
 
    sudo ./flashrom -p internal -w my.rom #updated image


## Building Libreboot and preparing ROM

This script worked 2019-04 for __Debian 10__ and stable __Libreboot 2016__.
The procedure should be the same for all Libreboot boards, here X200.

Unlike Coreboot, this build process is fairly automated and does not present any customization choices by default

(It is possible to use latest from Git (this is propably not recommended..), however we noted that the cryptomount program had less features in the version on Git (around 2019), compared to the 2016 stable release which for us was a downside.)


    #!/bin/bash -e
    cd libreboot
    ./download flashrom
    cd flashrom
    sudo apt-get install libusb-1.0-0-dev libpci-dev linux-image-$(uname -r) -y
    sudo apt install make gcc pkg-config libssl1.0-dev zlib1g-dev pciutils-dev libftdi-dev libusb-dev build-essential -y
    make install #does this need sudo? 
    make
    cd ..
    #backup current rom
    sudo ./flashrom  -p internal -r ~/this_is_my0.rom


    #build libreboot, for the ROM

    #dependencies for Debian
    sudo ./oldbuild dependencies trisquel7

    #download and build necessary
    ./download grub coreboot crossgcc seabios

    ./oldbuild module crossgcc
    ./oldbuild module grub
    ./oldbuild module coreboot
    ./oldbuild module seabios


    cd resources/utilities/ich9deblob
    make
    cd ../../../

    cd ./crossgcc/util/cbfstool/
    make
    cd ../../../
    
    #BUILD COMPLETE, now fix the image
    #finally build the ROM we want
    ./oldbuild roms withgrub x200_8mb

    cp bin/grub/x200_8mb/x200_8mb_usqwerty_vesafb.rom resources/utilities/ich9deblob/x200.rom
    cd resources/utilities/ich9deblob/
    ./ich9gen --macaddress "00:DE:AD:BE:EF:00" # SET A UNIQUE MAC ADDRESS HERE!!
    dd if=ich9fdgbe_8m.bin of=x200.rom bs=1 count=12K conv=notrunc
    mv x200.rom ../../../
    cd ../../../

    #FINALLY add or change the files in the image for customization
    #./cbfstool x200.rom extract -n grub.cfg -f grubtest.cfg
    #./crossgcc/util/cbfstool/cbfstool x200_other_mac.rom remove -n grubtest.cfg
    #./crossgcc/util/cbfstool/cbfstool x200_other_mac.rom add -n grubtest.cfg -f grubtest.cfg -t raw

    ## LASTLY FLASH TO CHIP (here internally)
    #sudo ./flashrom/flashrom -p internal -w x200.rom



# Specific flash guides

BEFORE flashing laptops make sure EC version is up to date (yes it's proprietary). (Link for fun: <https://notabug.org/libreboot/libreboot/issues/731>.. __Sorry for broken link!__)

## T400 without blobs (Coreboot)
Here's our config for coreboot, copy it here to this path [coreboot/.config](t400_coreboot/).
( Below in the text we refer to it as ~/coreboot_config_t400_8mb )

    #!/bin/bash -e
    #Building Coreboot without blobs for T400
    #Assumed 8MB rom (can use ./flashrom -p internal to see this on the proprietary T400)
    #If not 8MB just change size in menuconfig and take other .bin from ich9gen, no problem
    
    
    # See also https://www.coreboot.org/Build_HOWTO#debian
    apt update && apt upgrade -y
    apt-get install git build-essential gnat flex bison libncurses5-dev wget zlib1g-dev -y
    
    # CLONE 
    git clone https://review.coreboot.org/coreboot
    cd coreboot
    git submodule update --init --checkout
    
    #You can copy our config and use it if you wish
    #cp coreboot_config_t400_8mb coreboot/.config
    
    # CONFIGURE
    # In this menu config set wanted settings (see our config, or the guide for T400 by zerocat http://www.zerocat.org/projects/coreboot-machines/doc/generated-documentation/html/md_doc_build-coreboot-t400.html)
    # Our final conf is in ~/coreboot_config_t400_8mb
    # NOTE: we add descriptor for gbit ethernet later (NO need for "add Intel descriptor.bin file")
    make menuconfig #or make nconfig
    
    # BUILD toolchain
    make crossgcc-x64 CPUS=2
    #or make crossgcc CPUS=2 #for all architectures
    
    # BUILD rom
    make
    cp build/coreboot.rom ~/t400_8mb_coreboot_notReady.rom
    
    
    # DESCRIPTOR
    # We add the gbit ethernet descriptor with the tool provided by libreboot
    git clone https://notabug.org/libreboot/libreboot
    cd libreboot
    ./oldbuild module ich9deblob
    cd resources/utilities/ich9deblob/
    
    #NOTE: SET YOUROWN MAC HERE! Should be unique, just take the one which is under the T400
    ./ich9gen --mac-address 00:24:7E:AA:AA:AA
    
    cp ~/t400_8mb_coreboot_notReady.rom ~/t400_8mb_coreboot_flashable.rom
    dd if=ich9fdgbe_8m.bin of=t400_8mb_coreboot_flashable.rom bs=12k count=1 conv=notrunc
    
    exit 
    
    # ROM IS READY! 
    # NEXT STEPS ARE DONE ON EXTERNAL DEVICE (we used BeagleBoneBlack)
    # See libreboot for external flash, just disassemble laptops (can take a few hours) -> https://libreboot.org/docs/install/t400_external.html
    # Flash with for example BBB: https://libreboot.org/docs/install/bbb_setup.html
    # (Probably you want to power the BBB with 5V mini-usb or power adapter and connect everything to it. However, when we flashed we connected BBB (connector called SYS_5V) to 5V on regular ATX PSU, and the chip gets 3.3V directly from the PSU, and ground from BBB)

    ./flashrom -p linux_spi:dev=/dev/spidev1.0,spispeed=512 -r factory1.rom
    ./flashrom -p linux_spi:dev=/dev/spidev1.0,spispeed=1024 -r factory2.rom
    ./flashrom -p linux_spi:dev=/dev/spidev1.0,spispeed=1024 -r factory3.rom
    ./flashrom -p linux_spi:dev=/dev/spidev1.0,spispeed=256 -r factory4.rom
    ./flashrom -p linux_spi:dev=/dev/spidev1.0,spispeed=2048 -r factory5.rom
    sha256sum factory*.rom #a majority should match, or at least 2 should have the same hash 
    
    ./flashrom -p linux_spi:dev=/dev/spidev1.0,spispeed=512 -w ~/t400_8mb_coreboot_flashable.rom
    #Should say "Verified". If you're paranoid you could read it again and check hash.
    
## X230 (Coreboot)

Make sure bios has latest EC version. We had EC 1.14 (G2HT35WW), this should be the latest (citation needed).

### Internally

Due to security weakness in older versions of the proprietary BIOS, it is now possible to flash internally.
This is the recommended procedure for X230.

See: <https://doc.coreboot.org/mainboard/lenovo/ivb_internal_flashing.html?highlight=x230>

### Externally (not recommended)

#### Warning

As we flashed externally, we were not careful enough, and accidentally lost a resistor. This is certainly a risk when flashing by this method, the same issue as described here
<https://www.reddit.com/r/coreboot/comments/dhwdss/did_i_just_brick_my_x230/fmytpm0/?utm_source=reddit&utm_medium=web2x&context=3> (supposedly a R1378, SMD 33.2Ω resistor). With open circuit here computer cannot boot, however by adding it back or by shorting it it will be able to boot (after booting, the computer can run with an open circuit here ... seemingly).

#### Instructions

After asking on IRC the recommendation was to, on the signal lines, add a resistor e.g. 47 Ohm... presumably for both data lines and clock?

See (POSSIBLY OUTDATED LINKS):


<https://doc.coreboot.org/mainboard/lenovo/Ivy_Bridge_series.html> (OLD: http://www.coreboot.org/Board:lenovo/x230#Flashing)
(additionally: <https://www.chucknemeth.com/laptop/lenovo-x230/flash-lenovo-x230-coreboot> and <https://github.com/0xbb/coreboot-x230>)

Recommendation: instead of supplying 3.3V externally, connect power and ethernet to computer to get WakeOnLan (might need to enable in BIOS). Thereby the chip will get powered with reduced risk. 

Warning: Be careful not to break off the resistors close to the chips as these are very brittle! (as mentioned before)

Connect to Pomona holder (8pin) like below if BeagleBone Black is used: (see links above for Raspberry Pi 3)

    1 DI (IO0)    -> 18
    2 CLK         -> 22 SPIO_SCLK
    3 NC
    4 VCC         -> 3 or 4 (3.3V)

    8 GND         -> 1 or 2 (GND)
    7 NC
    6 DO  (IO1)   -> 21 SPIO_DO
    5 CS          -> 17 SPIO_CSO

Both chips should have the same layout. Read from both chips SPI2 (4MB) and SPI1 (8MB), save these images a number of times and make sure they seem to be correctly read from chip (i.e. verify hashsum). In my case the below command was used according to the id on the second row of the chips, together with the recommendation flashrom gives when no -c flag is used. Possible to try different speeds, 2048 worked well. For this ethernet was not connect or any other power to the laptop, from our efforts 3.3V external power needed to be provided to the chip on pin 4 instead.

    #Top chip
    ./flashrom -p linux_spi:dev=/dev/spidev1.0,spispeed=2048 -r x230_spi2_0.rom -c "MX25L3206E/MX25L3208E"
     
    #Bottom chip
    ./flashrom -p linux_spi:dev=/dev/spidev1.0,spispeed=2048 -r x230_spi1_0.rom -c "MX25L6406E/MX25L6408E"


To be continued ...


## KGPE-D16 (Libreboot)

### Summary, using this free computer as a workstation

Once you have flashed and found working memory for the system. It works rather well, no need to remove CMOS battery for instance (if no picture/boot, just disconnect PSU for a sec and restart).
Small annoyance is that the computer won't start with USB _storage_ attached (so instead, you should disconnect these and reconnect when grub is shown).
It is a good machine for every day use (free firmware & 32 cores and 64GB DDR3 ECC RAM), and possible to find used starting at 5k = 3k+1k+1k (motherboard+cpu&cooler+ram), remember that external GPU and sound cards will be needed.
The single-thread performance is rubbish and a major bottleneck for certain applications (no you can't play CS:GO or other modern video games, and only slight performance gain can be achieved by forcing all threads to highest clock in Linux with cpupower). The other downside is that the dual CPU system draws ~200 watts IN IDLE (and 400w at full load).

### General

Also Raptor Engineering was working on new free features (OpenBMC, IPMI, and Thermal management drivers) for this board, [here](https://www.raptorengineering.com/coreboot/kgpe-d16-bmc-port-status.php).

 Note these bugs and fixes were tested 2017 with the stable release _Libreboot-20160907_.

  * Do NOT use cheap Chinese 12v connectors, they will melt!
  * RAM is sensitive, less is more likely to work. See [coreboot wiki](https://www.coreboot.org/Board:asus/kgpe-d16) for working configurations
  * RAM order is important, see motherboard manual
  * Use 62xx-series CPUs, because Libreboot does not update (proprietary) microcode. However, this is up to you as you may then use 63xx for better performance (we have not tried updating though). (See [Libreboot](https://libreboot.org/docs/hardware/kgpe-d16.html))
  * Internal GPU works (for text mode only), note the hardware jumper (see motherboard manual)
  * North bridge (?) gets hot, use good ventilation
  * A system with 2x 6276 CPUs draws between 200 and 400 watt, depending on the workload (this is a lot in idle!.
  * There are two ethernet ports and one IPMI (not used)
  * Hardware jumpers for GPU, ethernet etc.
  * Note: It is necessary to have a dedicated sound card and graphics card, _if_ those features are desired.


### Using
  * Both GRUB and SeaBIOS work. But SeaBIOS has worked better when booting from CD-ROM or USB.
  * If problems booting, disconnect power and remove CMOS battery for a couple of seconds.
  * The default script in GRUB does not work with encrypted boot-partition (can manually boot).
  * When booting from GRUB, you might want to use "load config from external device" instead of "boot USB". Recommended when booting LiveCDs etc., if you don't have any particular configuration locally.

### Quirks SeaBIOS stable
  * With the stable release and SeaBIOS, most is working well but there is still one issue with USB devices at boot.
**Example:** A USB stick is connected at the back of the moderboard when the computer boots.
Suddenly, USB ports does not work, such as the chassi's USB hub which is connected on the moderboard.
These are completely dead and the peripheral devices does not light up. Only rebooting does not help.
**Solution**, turn off computer and disconnect power (we did not need to clear CMOS), and disconnect the problematic USB stick on the back. Turn on power. The USB stick can now be plugged in when the hardware has been initialized and GRUB is shown.

### Quirks with earlier GRUB version
  * Sometimes we get an error when booting, this is resolved by disconnecting USD devices such as keyboard and mouse until the hardware is initiated.
  * In GRUB the USB keyboard is sometimes unusably slow. Fix: always have a PS/2 keyboard connected, but use the USB keyboard.
  * In some configurations the fans will always be low and the system fail to boot, which would also overheat the already stuck system (!), thus you might want to power the fans with PSU instead of board.

### Debugging
  * If you can't boot: reset CMOS (battery and power), make sure your graphic output correctly set (jumper on board). Otherwise, it is most likely a memory, CPU or ROM-image problem. Try to boot with as little RAM as possible or other memory brand, make sure the CPU is properly seated, verify ROM image on chip (e.g. check hashsum) - we have had all these problems. Finally, you can use a serial cable to debug and get an idea about the issue, and ask in Libreboot IRC.

### Our Tested Systems

#### A

Debian Buster

ASUS KGPE-D16 (1.03G)

2x 6276 CPU (CPU Cooler: 2x Cooler Master Evo 212, __not__ v2. This cooler does not really fit. But with two motherboard spacers to reach motherboard holes to attach, and by only using one of the metal bars which the cooler uses to connect to the motherboard - it actually works rather well. Even if the CPU is not covered completely by the cooler. Regardless, good temperatures and stability)


4x 16GB 1600Mhz Reg-ECC *MT36JSF2G72PZ-1G6E1LG (HP: 672612-081)* [max 32GB/CPU!](https://www.coreboot.org/Board:asus/kgpe-d16/known_bad_configs)

Modern AMD graphics card (not fully free!)

Sound card (not fully free!)


#### B

Debian Stretch

ASUS KGPE-D16 (1.03G)

1x 6276 CPU (CPU Cooler: 1x Dynatron A13 G34 Socket LGA 1974 2U Active Cooler, quite loud)

4x 4GB 1333Mhz Reg-ECC HMT151R7BFR4C-H9 


### Pics: The systems (old)

[Div pics a](resources/div_pics/d16_a.jpg), [Div pics b](resources/div_pics/d16_b.jpg), [Div pics c](resources/div_pics/d16_c.jpg), [Div pics d](resources/div_pics/d16_d.jpg), [Div pics e](resources/div_pics/d16_e.jpg),

#### Pics: Flashing (Don't use ATX PSU and do not solder like this, just for reference!)

[PLEASE JUST POMONA HOLDER INSTEAD but should be correctly connected 1](resources/fail_flash_1.png)

[PLEASE JUST POMONA HOLDER INSTEAD but should be correctly connected 2](resources/fail_flash_2.jpg)

[PLEASE JUST POMONA HOLDER INSTEAD but should be correctly connected 3](resources/div_pics/d16_g.jpg)

[pic 4](resources/div_pics/d16_f.jpg)


### Manuals for flashing

[D16-chip](resources/d16_chip.png)

[Beagleboard Black P9 connector](resources/BBB_P9.png)

### Flashing Instructions

Buy a couple of flash chips in case of you breaking them, and to test different payloads. Furthermore, one could buy a proprietary chip just to debug the hardware.  

We used SPI-speed set to 2048 (spi=2048), but this probably depends a lot depending on your flash setup. Find the most stable speed by reading the image from chip and verifying that it read correctly.

Use a holder for the ROM, do not solder. Use a female-male cable when connecting to BBB (or other board). According to Libreboot IRC, shorter cables are NOT necessarily better. We found no cable length to be optimal (~5/10/20 cm), final configuration used ~10 cm cables. Do not power ROM with regular ATX PSU as it is unstable, better to use power from the board or other external PSU.

When searching through my notes I found the following from Libreboot IRC;
_"Try different cables max 30 cm long, add 50 ohm resistors in series and keep trying. While 1: flashrom -c 'your chip' #Should be constant"_
 
ROM-holder (eg.):  3M IC test socket, DIP 18, 218-3341-00-0602J

ROM-chip (eg.): Winbond W25Q16DV (if ROM too small can extend, see: *Extend 2MB ROM to 8MB*)


    1 CS          -> 17 SPIO_CSO
    2 DO  (IO1)   -> 21 SPIO_DO
    3 /WP (IO2)   -> 3.3V
    4 GND         -> GND

    8 VCC         -> 3.3V
    7 /HOLD (IO3) -> 3.3V
    6 CLK         -> 22 SPIO_SCLK
    5 DI (IO0)    -> 18


    #remember to backup current image if you'd like
    #can read current contents a nr of times and they should be the same
    ./flashrom -p linux_spi:dev=/dev/spidev1.0,spispeed=512 -r factory.rom
    ./flashrom -p linux_spi:dev=/dev/spidev1.0,spispeed=512 -r factory1.rom
    ./flashrom -p linux_spi:dev=/dev/spidev1.0,spispeed=512 -r factory2.rom
    ./flashrom -p linux_spi:dev=/dev/spidev1.0,spispeed=1024 -r factory3.rom
    ./flashrom -p linux_spi:dev=/dev/spidev1.0,spispeed=1024 -r factory4.rom
    ./flashrom -p linux_spi:dev=/dev/spidev1.0,spispeed=1024 -r factory5.rom
    ./flashrom -p linux_spi:dev=/dev/spidev1.0,spispeed=256 -r factory6.rom
    ./flashrom -p linux_spi:dev=/dev/spidev1.0,spispeed=256 -r factory7.rom
    ./flashrom -p linux_spi:dev=/dev/spidev1.0,spispeed=256 -r factory8.rom
    ./flashrom -p linux_spi:dev=/dev/spidev1.0,spispeed=2048 -r factory9.rom
    ./flashrom -p linux_spi:dev=/dev/spidev1.0,spispeed=2048 -r factory10.rom
    ./flashrom -p linux_spi:dev=/dev/spidev1.0,spispeed=2048 -r factory11.rom
    sha512sum factory*.rom #they _should_ be the same

    #Flash ROM (can try different speeds), choose an appropriate ROM (keyboard layout, GRUB/Seabios)
    ./flashrom -p linux_spi:dev=/dev/spidev1.0,spispeed=512 -w ~/libreboot_r20160907_grub_kgpe-d16/kgpe-d16_svenska_txtmode.rom

    ## NOTE ##
    # In my case (KGPE-D16) it was somewhat difficult - seemingly at random - to achieve a configuration such as read and write had good reliability.
    # Furthermore, writing had good success rate while reading was more error prone.
    # Thus, it is possible to write to the ROM and read (e.g.) 10 times and compare the resulting `sha512sum` to that of the ROM downloaded from Libreboot.
    # If the sum EXACTLY matches atleast once, it is reasonable to assume that the write indeed succeeded (we believe).

# Extras for Libreboot and Coreboot

## GRUB

For some setups the default GRUB script does not work. Either try updating the grub.cfg to boot automatically or  boot manually each time.

### Boot manually

    #Open commandline in GRUB
    cryptomount -a        #decrypt all HDDs (can also specify disk manually)
    root=lvm/matrix-boot  #default boot partition, or use `ls` to list partitions
    linux=/vmlinuz        #if (the symlink) not present, choose latest file, press TAB to list
    initrd=/initrd        #if (the symlink) not present, choose latest file, press TAB to list
    boot

### Boot automatically
Add the below to the preferred boot option, in grub.cfg, for automatic boot. It is recommended to test any configuration with grubtest.cfg, before changing the main config grub.cfg. This can then be flashed internally as described in earlier section.

    menuentry 'Boot to regular encrypted OS  [x]' --hotkey='x' {
    #Simple setup to boot from encrypted boot partition
    #LUKS -> LVM -> logical volumes
    #LVM name matrix and boot volume rootvol or boot
    #Might get weird if multiple lvm devices connected

        cryptomount -a

        #try boot first since rootvol might symlink to boot and break
        set root=(lvm/matrix-boot)
        if [ -f "/vmlinuz" ]; then
          if [ -f "/initrd.img" ]; then
            linux /vmlinuz
            initrd /initrd.img
          fi

       else
          echo "Trying to boot lvm/matrix-boot.."
          echo "Couldn't find kernel"
          echo "Trying to boot lvm/matrix-rootvol.."
          set root=(lvm/matrix-rootvol)
          if [ -f "/vmlinuz" ]; then
            if [ -f "/initrd.img" ]; then
              linux /vmlinuz
              initrd /initrd.img
            fi
          fi

        fi
    }


## OpenBSD guide

It _is_ possible to use OpenBSD with Coreboot (we haven't tried) and Libreboot (as we verified below), but they do not support hard drive encryption!

When installing OpenBSD on the x200, the OS was first installed on the HDD then flashed. Then, add a "/grub" directory that allows grub to automatically boot into openbsd instead of going into command line.

#### The manual way is to press "c" when the grub menu appears:
```
grub> kopenbsd (usb0,openbsd1)/6.1/amd64/bsd.rd
grub> boot

```
#### The more nicer way (as root do):
```
mkdir /grub && cd grub
echo '''
default=0
timeout=3

menuentry "OpenBSD"{
      kopenbsd (usb0,openbsd1)/6.1/amd64/bsd.rd
}

''' > libreboot_grub.cfg

#cat libreboot_grub.cfg
default=0
timeout=3

menuentry "OpenBSD"{
      kopenbsd (usb0,openbsd1)/6.2/amd64/bsd.rd
}


#reboot
```
This works very good with openbsd6.0(and 6.2), read more [here](https://libreboot.org/docs/bsd/openbsd.html)(use amd64, X is not working with i386)

[update] I have run openbsd 6.2 with libreboot on thinkpad x200 for about two months now without any errors, still no update on the harddisk crypto

## Extend 2MB ROM to 8MB

### Here is the script we use to flash 8mb chip

```
root@beaglebone:~# cat flashme_8mb.sh 
echo 'Creating a 8mb Libreboot Rom'
echo 'creating the file /home/flashing_d16/libreSept16/8mb_free.rom'
echo 'dd ing'
touch /home/flashing_d16/libreSept16/8mb_free.rom
echo ' ' > /home/flashing_d16/libreSept16/8mb_free.rom
echo 'Zeros then image'
dd if=/dev/zero bs=6144k count=1 of=/home/flashing_d16/libreSept16/8mb_free.rom
echo 'Writing image'
dd if=/home/flashing_d16/libreSept16/libreboot_r20160907_grub_kgpe-d16/kgpe-d16_svenska_txtmode.rom >> /home/flashing_d16/libreSept16/8mb_free.rom 
ls -lah /home/flashing_d16/libreSept16/8mb_free.rom  
echo 'Its flashing time'
/home/flashing_d16/libreSept16/libreboot_r20160907_util/flashrom/armv7l/flashrom -p linux_spi:dev=/dev/spidev1.0,spispeed=2048 -w /home/flashing_d16/libreSept16/8mb_free.rom
echo 'everything is done'
echo 'plz verify'
echo '
cd directory-with-size
/home/flashing_d16/libreSept16/libreboot_r20160907_util/flashrom/armv7l/flashrom -p linux_spi:dev=/dev/spidev1.0,spispeed=512 -r factory.rom
/home/flashing_d16/libreSept16/libreboot_r20160907_util/flashrom/armv7l/flashrom -p linux_spi:dev=/dev/spidev1.0,spispeed=512 -r factory1.rom
/home/flashing_d16/libreSept16/libreboot_r20160907_util/flashrom/armv7l/flashrom -p linux_spi:dev=/dev/spidev1.0,spispeed=512 -r factory2.rom
/home/flashing_d16/libreSept16/libreboot_r20160907_util/flashrom/armv7l/flashrom -p linux_spi:dev=/dev/spidev1.0,spispeed=1024 -r factory3.rom
/home/flashing_d16/libreSept16/libreboot_r20160907_util/flashrom/armv7l/flashrom -p linux_spi:dev=/dev/spidev1.0,spispeed=1024 -r factory4.rom
/home/flashing_d16/libreSept16/libreboot_r20160907_util/flashrom/armv7l/flashrom -p linux_spi:dev=/dev/spidev1.0,spispeed=1024 -r factory5.rom
/home/flashing_d16/libreSept16/libreboot_r20160907_util/flashrom/armv7l/flashrom -p linux_spi:dev=/dev/spidev1.0,spispeed=256 -r factory6.rom
/home/flashing_d16/libreSept16/libreboot_r20160907_util/flashrom/armv7l/flashrom -p linux_spi:dev=/dev/spidev1.0,spispeed=256 -r factory7.rom
/home/flashing_d16/libreSept16/libreboot_r20160907_util/flashrom/armv7l/flashrom -p linux_spi:dev=/dev/spidev1.0,spispeed=256 -r factory8.rom
/home/flashing_d16/libreSept16/libreboot_r20160907_util/flashrom/armv7l/flashrom -p linux_spi:dev=/dev/spidev1.0,spispeed=2048 -r factory9.rom
/home/flashing_d16/libreSept16/libreboot_r20160907_util/flashrom/armv7l/flashrom -p linux_spi:dev=/dev/spidev1.0,spispeed=2048 -r factory10.rom
/home/flashing_d16/libreSept16/libreboot_r20160907_util/flashrom/armv7l/flashrom -p linux_spi:dev=/dev/spidev1.0,spispeed=2048 -r factory11.rom
sha512sum factory*.rom
'

```


# More

## Free Wifi

Note that many systems come with proprietary wifi, e g the X200 laptop or the S3 phone. In these cases another wifi chip is needed, either external (micro/)USB or internal.

For hardware recommendations see [[rekommenderat.md]].

## Free phones

PLEASE DON'T HAVE GOOGLE SERVICES ON YOUR PRIMARY PHONE, REGARDLESS OF THE FIRMWARE SITUATION. At most have a seperate phone for this.

That being said, all phones are to some degree proprietary devices that track you (base-band processor..). Still it is quite a difference to run free software and firmware, as compared to giving Google root access to your device!

Nevertheless, [Replicant](https://www.replicant.us/) is good but slow (how outdated is Android 6, does it lack critical security patches? We hope they will release Android 10 Replicant some time) .
New somewhat libre phones: [Librem 5](https://puri.sm/shop/librem-5/), [Pinephone](https://www.pine64.org/pinephone/), maybe Fairphone 3.
For more hardware recommendations see [[rekommenderat]].

We have some experience with Replicant, guides coming (TODO!).
