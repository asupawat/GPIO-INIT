In January 2015 the first version of the Raspbian operating system with built
in support for the device tree was released. The device tree is a data
structure for describing hardware. Many aspects of the hardware can be
described in this data structure rather than being hard coded into the
operating system.

Device tree overlays can be used to apply changes to the kernel's internal
device tree representation. For example, a device tree overlay can be used to
enable the pullup or pulldown resistor for a GPIO.

Lets say that the pullup and pulldown resistors for GPIO #7, #8, and #9 need
to be configured as shown in the following table:

GPIO # | Header Pin # | Pull Type
:---: | :---: | :---:
7 | 26 | pulldown
8 | 24 | pulldown
9 | 21 | pullup

The device tree source file `mygpio-overlay.dts` for the device tree overlay
to acheive this is as follows:

```
/dts-v1/;
/plugin/;

/ {
    compatible = "brcm,bcm2708";

    fragment@0 {
        target = <&gpio>;
        __overlay__ {
            pinctrl-names = "default";
            pinctrl-0 = <&my_pins>;

            my_pins: my_pins {
                brcm,pins = <7 8 9>;     /* gpio no. */
                brcm,function = <0 0 0>; /* 0:in, 1:out */
                brcm,pull = <1 1 2>;     /* 2:up 1:down 0:none */
            };
        };
    };
};
```

The device tree compiler compiles the source into a binary form. The compiler
itself is installed with the following command:

```
sudo apt-get install device-tree-compiler
```

And the overlay is compiled with the following command:

```
dtc -@ -I dts -O dtb -o mygpio-overlay.dtb mygpio-overlay.dts
```

The device tree blob `mygpio-overlay.dtb` produced by the compiler is the
binary and should be copied to directory `/boot/overlays`.

The last piece of the puzzle is adding the following line at the end of
`/boot/config.txt` so that the overlay gets loaded at boot time:

```
device_tree_overlay=overlays/mygpio-overlay.dtb
```

For additional information about the device tree see
[Device Trees, Overlays and Parameters](http://www.raspberrypi.org/documentation/configuration/device-tree.md)
