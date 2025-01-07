# USB
- does not have any problems while flashing, because everything is turned off while flashing. That way, there will be no interference.

# TP points
I want them to be near the peripherals I want to test. I also mainly want them for outputs. Inputs pins are secondary, but can be interesting too.

# JTAG and UART
I don't have a debugger, but maybe there is one in my university I can borrow. That way I can actually debug the mouse. There is a built-in debugger in the usb now, but it might have problems because of the spinning wheels.

# placing parts vertically vs horizontally
It depends on how you route the tracks. If the top layer tracks go vertically, the party should be placed horizontally

# solder paste tip:
Hey GreatScott, I used to be a surface mount assembly tech in the UK, just wanted to give some advice on solder paste application using a stencil. Lay the paste down the shortest edge of stencil and using a thin but fairly stiff piece of metal drag the paste "sausage" across the stencil in one go(using firm but steady pressure), the paste should appear to roll as its going. Repeatedly wiping across the same spot as you do in the video is causing excess paste to go through the stencil, which can cause messy joints and bridges.

## maybe?
https://www.printables.com/model/741515-smt-solder-paste-stencil-printer-table-v2/files#preview.DfxV9

## für die zukunft:
npth (löcher) ins pcb placen, die dann auch im stencil vorkommen. dann einfach ein jig 3d printen, wo die Löcher durch einen "pin" übereinander gelegt werden und die resultate sollten perfekt sein.

# Value in Kicad
It seems that I should just use the part as the value, so that the html bom does not get really weird. It doesn't change the text of things anyway. That would be changed by "reference"
