# Remapping modifier keys on a Mac, for Linux
I have an old Macbook Air from about ten years ago, which was doing nothing but collecting dust. I decided it might be nice to try installing Linux on it, and, a few months later, this laptop has become my main personal machine.

Linux works flawlessly on this older model, and the hardware is--as you might expect--fantastic. I love the build quality of Macs, but prefer Linux over Mac OS any day.

The only trouble with this machine is that they keyboard doesn't have the right number of modifier keys in the right places for my Emacs usage. I currently use Emacs as a window manager, so this gets very annoying very quickly.

The bottom row of the Mac keyboard looks a bit like this:

`fn` `ctrl` `alt` `cmd` `space` `cmd` `alt`

What I want is:

`fn` `super` `alt` `ctrl` `space` `ctrl` `alt`

The `cmd`/`super` keys are the same, but I want to move one of them to `ctrl` and replace both of the `cmd` keys with `ctrl`.

Lastly, although there are two `alt` keys, the right one is interpreted as `alt gr`, which isn't useful for me, because it doesn't act as a standard Meta key in Emacs.

Oh, and another thing! The arrow keys are tiny on this keyboard! I'm very used to using `h``j``k``l` as arrow keys inside Emacs, and I want to be able to use them as arrow keys outside of Emacs too (e.g. in a web browser), with the use of a modifier key.

So, here are the three things I want to achieve:

1. Mapping both `cmd` keys to `ctrl`, and `ctrl` to `cmd`.
2. Making the right `alt` behave like the left one.
3. Make `h``j``k``l` behave like arrow keys when some modifier key is held down.

The remainder of this post is an explanation of how I did it. All of the remapping was achieved with `xmodmap`. I find `xmodmap` a bit arcane, but, in retrospect, everything I needed to know was in the manpage. I just didn't read it enough times! If you're not familiar with `xmodmap`, I suggest reading the manpage before continuing with this post, because it provides a better summary than I could offer.

## Step 0: my keycodes
Before I begin, here are the keycodes for my various modifier keys for reference:

| Key         | Keycode |
|-------------+---------|
| left ~cmd~  |     133 |
| right ~cmd~ |     134 |
| ~ctrl~      |      37 |
| left ~alt~  |      64 |
| right ~alt~ |     108 |

I discovered these by running `xev`, pressing keys, and watching the `stdout`.

## Step 1: swapping `cmd` and `ctrl`
In order to swap `ctrl` with `cmd` we need to:
1. Clear the `control` and `mod4` modifiers.
2. Re-assign the keycodes for the `ctrl` and right/left `cmd` keys to the relevant keysym (e.g. `Control_L`).
3. Re-add the modifiers to the same keysyms, which now have different keycodes assigned to them.

This is how it's done in `xmodmap` expressions:

```sh
xmodmap -e 'clear control'
xmodmap -e 'clear mod4'
xmodmap -e 'keycode 133 = Control_L NoSymbol Control_L'
xmodmap -e 'keycode 134 = Control_R NoSymbol Control_R'
xmodmap -e 'keycode 37 = Super_L NoSymbol Super_L'
xmodmap -e 'add control = Control_L' 
xmodmap -e 'add control = Control_R' 
xmodmap -e 'add mod4 = Super_L' 
```

> You can find the keycodes of all your keys with `xmodmap -pke`, and a list of the modifiers with `xmodmap -pm`.

## Step 2: make right `alt` behave like the left one
In order to remap right `alt` to also be left `alt`, we just need to:

1. Clear `mod5`, which the right `alt` was previously a keysym for.
2. Remap the keycode of right `alt` to be `Alt_L`.

We don't need to re-add it to a modifier, because `Alt_L` is already mapped to `mod1`.

This is how it's done:

```sh
xmodmap -e 'clear mod5'
xmodmap -e 'keycode 108 = Alt_L NoSymbol Alt_L'
```

## Step 3: make `h``j``k``l` into arrow keys using caps lock
The following will make `h``j``k``l` behave like arrow keys if caps lock is held down:

```sh
#                  code = no modifier
#                         | with shift
#                         | | mode_switch
#                         | | |    mode_switch and shift
#                         | | |    |
#                         v v v    v
xmodmap -e 'keycode  43 = h H Left h hstroke Hstroke hstroke'
xmodmap -e 'keycode  44 = j J Down j dead_hook dead_horn dead_hook'
xmodmap -e 'keycode  45 = k K Up k kra ampersand kra'
xmodmap -e 'keycode  46 = l L Right l lstroke Lstroke lstroke'
xmodmap -e 'keycode  66 = Mode_switch NoSymbol Caps_Lock'
```

The result is that holding caps lock and any of `h``j``k``l` will result in arrow key presses. Caps lock will no longer result in capitalised characters, but shift can still be used for this.

## Step 4: profit!
I hope this post helps you if you're looking to do a similar thing. It took me a bit of Stack Overflow surfing, and man page reading (not to mention lots of bricking my keyboard layout!) to work out how to do this. Enjoy Linux on Mac!
