# While doing a new kinesis conf
Some stuff broke with my xkb after updating to ubuntu 23.04.

xkbcomp $DISPLAY new_kinesis.xkb
to create a default config

Recompiling the result right away gave this:
```
$ xkbcomp -i 9 new_kinesis.xkb $DISPLAY 
No symbols defined for <AB11> (keycode 97)
No symbols defined for <JPCM> (keycode 103)
No symbols defined for <I120> (keycode 120)
No symbols defined for <AE13> (keycode 132)
No symbols defined for <I149> (keycode 149)
No symbols defined for <I154> (keycode 154)
No symbols defined for <I168> (keycode 168)
No symbols defined for <I178> (keycode 178)
No symbols defined for <I183> (keycode 183)
No symbols defined for <I184> (keycode 184)
No symbols defined for <FK19> (keycode 197)
No symbols defined for <FK24> (keycode 202)
No symbols defined for <I217> (keycode 217)
No symbols defined for <I219> (keycode 219)
No symbols defined for <I222> (keycode 222)
No symbols defined for <I230> (keycode 230)
No symbols defined for <I248> (keycode 248)
```

I commented these out from the config, but got:
```
$ xkbcomp -i 9 new_kinesis.xkb $DISPLAY 
Warning:          Attempt to alias <I132> to non-existent key <AE13>
                  Ignored
Warning:          Attempt to alias <I197> to non-existent key <FK19>
                  Ignored
Warning:          Attempt to alias <I202> to non-existent key <FK24>
                  Ignored
```

Maybe instead of commenting out the "No symbols defined", I should find out what these are, and define a symbol for them?

Instead, I commented out the aliases, hoping I won't be misisng them.

Then, I swapped these symbols for Group2: (apostrophe, asterisk) with (aring, Aring). (key AD11 with BKSL, but for Group2 only.)
