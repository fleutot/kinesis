From https://unix.stackexchange.com/a/274266/38106

---8<---

# Make your own xkb configuration file

The idea is to "read" the current keyboard config (do not call xmodmap), and write your own symbols file based on it. First:

	xkbcomp -xkb $DISPLAY

This creates server-0_0.xkb. In this file, take the following block:

	xkb_symbols "pc+inet(evdev)+compose(menu)+whatever(option)" {
		key <ESC> { [ Escape ] };
		...
	};

change the first line into:

	default xkb_symbols "my_symbols" {
		include "pc+inet(evdev)"
		include "compose(menu)+whatever(option)"

(I think you can break options into as many "include" lines as you like). Change the keys you want to modify and prepend them with override:

	override key <AE10>  { [ 0, parenright, degree ]};
	
Remove all unchanged keys.

# System-wide installation

Put all this into /usr/share/X11/xkb/symbols/my_terrific_kb. Now users may load it with setxkbmap my_terrific_kb (in .xinitrc or .xsessionrc). Probably you can put Option "XkbLayout" "my_terrific_kb" in xorg.conf for a system-wide change.

# Single-user installation

Put all this into ~/anywhere/my_terrific_kb. Find the XInput id of your keyboard with xinput list. Then run xkbcomp -i <XInput_id> ~/anywhere/my_terrific_kb $DISPLAY.

--->8---

# What I have done

Followed the instructions above, swapped the Swedish symbols (`Group2`) for å and ', removed the other symbols (which don't need to be overridden).

	sudo apt install xinput list
	xinput list

The output was:

    ⎡ Virtual core pointer                    	id=2	[master pointer  (3)]
    ⎜   ↳ Virtual core XTEST pointer              	id=4	[slave  pointer  (2)]
    ⎜   ↳ DLL075B:01 06CB:76AF Touchpad           	id=11	[slave  pointer  (2)]
    ⎜   ↳ SynPS/2 Synaptics TouchPad              	id=18	[slave  pointer  (2)]
    ⎜   ↳ USB Optical Mouse                       	id=14	[slave  pointer  (2)]
    ⎣ Virtual core keyboard                   	id=3	[master keyboard (2)]
        ↳ Virtual core XTEST keyboard             	id=5	[slave  keyboard (3)]
        ↳ Power Button                            	id=6	[slave  keyboard (3)]
        ↳ Video Bus                               	id=7	[slave  keyboard (3)]
        ↳ Power Button                            	id=8	[slave  keyboard (3)]
        ↳ Sleep Button                            	id=9	[slave  keyboard (3)]
        ↳ Integrated_Webcam_HD                    	id=10	[slave  keyboard (3)]
        ↳ Intel Virtual Button driver             	id=15	[slave  keyboard (3)]
        ↳ Intel HID events                        	id=16	[slave  keyboard (3)]
        ↳ AT Translated Set 2 keyboard            	id=17	[slave  keyboard (3)]
        ↳ Dell WMI hotkeys                        	id=19	[slave  keyboard (3)]
        ↳ HID 05f3:0007                           	id=12	[slave  keyboard (3)]
        ↳ HID 05f3:0007                           	id=13	[slave  keyboard (3)]

and the ID to use was `3`:

	xkbcomp -i 3 ~/kinesis/kinesis_swe_us.conf $DISPLAY

# Future work

## AltGr

Let CTRL+ALT act as a AltGr (since I have no dedicated AltGr on my keyboard, and that makes typing {} [] hard in Swedish layout.

This is how the output of `xkbcomp -xkb $DISPLAY` looks like for brackets:

    key <AE08> {
        type[group2]= "FOUR_LEVEL",
        symbols[Group1]= [               8,        asterisk ],
        symbols[Group2]= [               8,       parenleft,     bracketleft,   guillemotleft ]
    };
    key <AE09> {
        type[group2]= "FOUR_LEVEL",
        symbols[Group1]= [               9,       parenleft ],
        symbols[Group2]= [               9,      parenright,    bracketright,  guillemotright ]
    };

where `Group1` is US layout and `Group2` is swedish.

## Create an actual Swedish layout

This is modifying the layout by overriding what already exists in the Swedish layout. Better would be to create a stand-alone swedish layout.

Even worse: it's not actually a swedish layout, it uses groups within a single layout.

One issue now is that I cannot display which layout is active in my status bar: the name displaying there is the string in the section `deault xkb_symbols "this_string"` of my `~/kinesis/kinesis_swe_us.conf`.
This might help: https://wiki.archlinux.org/index.php/X_KeyBoard_extension
