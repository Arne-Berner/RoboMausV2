# Goal
- build a micromouse (from here on out called "rat") from scratch

# Motivation
## The fear of the unknown
I have been working with electronics for a while now, but I never really got into PCB design. I always thought it was too complicated and I would never be able to do it. But I also always wanted to build a micromouse. So I thought, why not combine the two and learn something new in the process.

That being said I also wanted to use my 3D printer more and get rid of that fear that accompanies anything new. Doing this project I feel like I will be able to create electronic devices much more easily in the future. Not only because I learned some tricks on my way, but because I simply have done it before.

## Beginner to Intermediate Rust Project
Let's be honest, coding for embedded systems sound hard. Rust also sounds hard. But weirdly enough, those two things combined are much easier than I thought! I want to provide all the code I used for this project for anyone interested. If you feel like creating electronic devices could be fun, give it a try! Especially the esp32 and rust go really well together. You can [try out Rust on the ESP32 on Wokwi](https://wokwi.com/).

# Parts with their Voltage and Reason I used them

## Placed
### ESP32-s3-mini: 3.3V 
The reason I chose the s3-mini was because it wasn't much more expensive and I was not sure how much processing power will be needed for the project. If I had more time I probably would have chosen an esp32 from the c series. You don't need to use an esp32 at all for a micromouse, but I will need to send the map created by my SLAM-algorithm to my computer via wifi. The STM32 is the MCU that is used for most micromouse.


### USB-C: 5V output
Micro USB tend to break of more easily from PCBs and USB-C has the possibility of using more Volt in later designs, without changing too much.


### AMS1117-3.3: LDO that converts to 3.3V
This seems to be a very common LDO and even though the esp32 is somewhat capable of handling higher voltage than described in the datasheet, I didn't want my project to go up in flames at a bad time.

### L293D (better known as an H-Bridge)
This will let me use my 3.3V from the esp and my 8V from the LiPos to drive my motors. It usually takes one source that ranges from 5V to 36V and another source that will only take 5V. I will see how it works in the project. I could've used an smt h-bridge to minimize the size on the pcb, but running the h-bridge in the wrong manner can destroy it. If that happens (and it will happen) I would like to be able to replace it fast.

### TCS34725 (Color sensor): 2.7V - 3.6V / nom: 3V
I will need to detect the colors of certain places in the labyrinth as an extra task. This seems to be a common and cheap color sensor. It also has a rust library. 

### GY-521 or MPU-6050 (gyroscope sensor): 2.375V - 3.46V
This will help me position orientate my rat. I'm not sure yet, how I will combine this with the input I get from the encoder and what will be used primarily if there is a difference between the two. The mpu-6050 seems to be cheaper but has better specs.

## Not Placed
### SK6812MINI-B-012 (NeoPixel): 5V
This could be used for debugging purposes (and it looks cool), but it needs 5V and that would've drained the LiPos faster than I wanted them to. There is a 3V alternative to the 5V neopixel, but I could not find it on lcsc.

### AMS1117-5.0: LDO that converts to 5V
This would have been necessary if I used the Neopixel above or if the ams1117-3.3 would not be able to handle the lipo voltage.

# Learnings
## How to read a Datasheet instead of "read the datasheet"
When you start in electronics you hear "read the datasheet" a lot. It sounds like bad advice at first, because you probably are asking experienced people about very specific device and datasheets can be hundreds of pages long. Where to start reading up on it? What if there is a feature that you didn't know the name of? Most people rather stop than to "read the datasheet" without knowing where to look for the most important info. 

Later on it feels like "read the datasheet" really was the solution to most problems. But only seemingly so. Experts don't read the full datasheet, even if it is only a few pages long. They won't look at all the data. But they know how to skim the datasheet for the most important info and see if anything is readworthy. But I am no expert and I still need to find answers to my questions. So how to read a datasheet?

There are some basic informations that you most likely want to take a look at:
- volt and ampere (often found as VCC or Vin/Vout info)
- the pinout
- provided example schematics
- (footprints)
With those infos you will have most infos so that your ICs don't burst into flames. It also has the benefit, that most often when looking for those informations you will find other important or odd info that will help you with your project. Be aware that datasheets sometimes contain the data for many variations of IC.

## Copilot and co.
Reading the datasheet might be hard if it is in chinese or if it is hundreds of pages long. Using any LLM to get the gist of it or some pointers where to look for can be very helpfull. But be warned: Especially in electronics halucinations seem to occur very often. They should only be used, if you check the data afterwards. The pinout is very often wrong, the current and voltage vary from the real parts and you can't use it for example schematics. But it will give you words to look for in the datasheet that might be important. 

"What should I be aware of with part XY" or "What could go wrong with wiring X to Y" are the most helpfull prompts.

## Kicad
### Schematics
I like to start with everything at once and see everything come together at the end. But who would have thought that this is neither good in programming, nor in electronics? 

The next project I would definitely draw the schematics for only one or two parts and then check if everything is alright. Do some adjustments, check if everything is alright, put it in the pcb. Rinse and repeat. That way I wouldn't have had to redo big portions of the project and I would have been way faster. Not only that, but I would have some starting point when placing the footprints on the pcb.

Another trick I learned from a friend of mine named "MAG" is to take really good care of a global library and push it to github or any cloud provider. That way you can use it on any machine available to you and using github you could help other people by taking care of that library.


### Libraries and imports
Since I knew I wanted to get the pcb on jlcpcb I mainly used [this website](https://dougy83.github.io/jlcparts/#/) to find the right part. This is a fork of [this repository](https://github.com/yaqwsx/jlcparts) and should be part of that repository soon. It often makes sense to focus on one shop for electronic parts, since shipping and handling fees will stack up fast with different shops. I used [Easyeda cli Plugin](https://github.com/uPesy/easyeda2kicad.py) to get the libraries from EasyEda, since I figured they would fit the parts used by jlc the best.
If I were to start again I would probably use [this Import Plugin](https://github.com/Steffen-W/Import-LIB-KiCad-Plugin#use-of-the-application) instead or go the hard route and learn to draw my own footprints and symbols. Most people that have been doing this for a while seem to prefer using their own footprints. That way there will be no suprises when buying the pcb.

It's also worth mentioning, that adding test points to any power io seems like a good idea. Using test points in general for critical conditions can help tremendously. But what are critical areas? It depends on your project, but when something does not work, the first thing you check is probably the power supplies.


### Symbols
- Symbols are not the parts themselves, but a representation for them
- Symbols most often don't use the right pin attributes, but "unspecified", which results in a lot of errors when using easyeda2kicad
- GND and VCC are all "Power Input"
- Signals that could go in both ways are bidirectional
- Signals that go only in one way are input or output (which you won't find often and could make your life harder)
- a lot of warnings can be mitigated by using "passive" for pins, unless you know they are generating/receiving signals

### Footprints
- Footprints are the actual parts you would probably find. This abstraction between symbols and footprints confused me at first
- drawing them yourself can be a good practice, but the online libraries are much easier to use
- there is always a section about that on every datasheet

### PCB
- One layer for horizontal signals and one layer for vertical signals
- Use more space first and then take away as much space as you need
- Draw a trace to see all the connected parts highlighted (this is good, when trying to find connected parts)
- there is a button to delete the whole trace (delete full track)
- there is a button to place a via on the current trace and change layer (place through via)
- put GND vias as close to the pad as possible and with a big trace to lower inductance
- you can use space to get some relative measurements. The origin of one of you coordinate systems in the bottom corner will change


### Planning and Assembly
Using an interactive BOM can be very usefull for any step, but especially the assembly can be structured that way. Checking if your parts are the way they should be, can be done with an interactive html. You can load it on your phone, you can export it for your other projects and you don't need to rewrite anything you don't need. I use the interactive BOM plugin for kicad.

For the actual assembly service on jlcpcb I use a plugin called "Fabrication Toolkit" which you can find in the plugin and content manager in kicad.

### Using Stencils
It can be usefull to place NPTH - holes that do not have any plating around them - to place the stencil properly. You can then use little pegs through both pcb and stencil to place the stencil very fast and accurately.


### Annoying things you want to know before they happen
- if you edit your symbols via schematics, they will only be updated for that one symbol in the schematics. There is no way to save that edit to your actual symbol in the library.
- if you have another window (e.g. the plugin window in the main menu) you cannot click anything in any other kicad window
- interactive bom gets cached, so you need to use ikognito mode
- interactive bom uses the generated bom fields for its fields. Those are only shown, if you updated the schematics editor and imported the changes into the pcb editor

Lastly I would advice you not to use the update pcb with relink too much, if you just used "annotate schematics". This will swap around parts and it's not fun.

## be a copycat
- you don't know what parts you need at the beginning. Just write "gyroscope pcb" on your prefered search or youtube to get a good idea, what might be usefull.

## PCB-Design
### 2 layer PCB vs 4 layer PCB
Truth being told, I have seen very different opinions on this. Some say 4 layers can get much more expensive. This is not necessarily the case when you only use through hole vias, but they are undeniably more expensive. But four layers are also a lot easier to design and depending on who you ask will make the signals less prone to EMI. 

As a rule of thumb I would say starting with a 2 layer pcb is the way to go and then go up to 4 layers, if you need to. For example when you have different voltages on your board that cannot easily be separate. But there is not much lost when you go for 4 layers from the beginning. Depending on how you click on what option in jlcpcb the price for this 4 layer board was 5$ more instead of the same size with 2 layers.

### Energie takes the path of least impedance, not resistance
Which makes a big difference in an AC Environment. But most hobby projects are DC, so why bother? Since any pulsed DC signal counts as a AC signal. This means for you, that you will have to keep the distance between the signal, ground and vcc very close to each other. This can be done via a good PCB layer stackup and thoughtfull routing.

### Signals will influence each other
Any Signal will be able to influence any other Signal next to it. Even though signals might look more tidies up, when you use the minimum distance possible by your PCB manufacturer, you should try to widen the gap between them. There is a tradeoff here, where some signals need to be coupled and some signals need to travel the shortest path possible.

### Important rules (that can be broken, if you know what you are doing) ... which I don't
- keep signals spaced as wide as possible
- short distance between forward and back pass
- keep GND and VCC via close to each other
- antennas work best with nothing in the way (place it at the edge)
- 3.3V signals should only reference 3.3V power in the current or next layer
- separate analog and digital signals spatially
- keep the distance between analog and digital 20x as wide as between ground and signal plane (no need to split the ground plane)
- different voltages get different areas on the board (the planes can be split if no other signals are crossing those areas)
- different frequencies get different areas on the board
- use ground vias to get signals from one layer to another
- don't put power and ground on layer 2 and 3 on a 4 layer board, because of the bigger distance
- on a 6 layer board don't put them on 2 and 5
- power layers should be near high-speed ICs
- use test pads
- PCB layers are stacked in pairs (distance wise)


# Resources
## Micromouse
There are some places to look at, but the best resource by far that I could find was [Micromouse Project from UCLA](https://projects.ieeebruins.com/micromouse/).

## PCB and KiCad
A video about proper grounding on pcb can be found [here](https://www.youtube.com/watch?v=ySuUZEjARPY&t=7540s&pp=ygURcGNiIGdyb3VuZCBwbGFuZSA%3D). [Robert Feranec](https://www.youtube.com/@RobertFeranec) also has very nice tutorials on different topics, even though they can be quite wordy. When it comes to nice and concise material I like [Phil's Lab](https://www.youtube.com/@PhilsLab). The audio is good, there are some good examples and he only concentrates on the most important points. Also the [getting started docs from KiCad](https://docs.kicad.org/8.0/en/getting_started_in_kicad/getting_started_in_kicad.html) are easy to read. Please let me know if you know any "non-youtube" sources that are well written!

## Rust
I have to admit, that after working through some nice [beginner std tutorials](https://docs.esp-rs.org/std-training/) (there is also [a no-std version](https://docs.esp-rs.org/no_std-training/)) which you can use with [Wokwi - an online esp32 simulator](https://wokwi.com/), I mostly look at the [esp-rs repos](https://github.com/esp-rs) and their respective examples. That is not the case because I am a hardcore "read the docs N00b" kinda person, but because the examples there and their docs are very well written and always up-to-date! 

But let's be serious, there always is a point, where you run into a wall with the docs, because you just don't know some technique that exists. The best place to find help then is [the esp matrix room](https://matrix.to/#/#esp-rs:matrix.org). There are a ton of helpfull people that will even answer the most basic questions in a respectful way. 

I think there should be an honorable mention for the [embedded rustaceans blog](https://blog.theembeddedrustacean.com/) which can help you at the beginning a lot and [awesome esp rust](https://github.com/esp-rs/awesome-esp-rust) which can be an inspiration for cool projects or topics you didn't know about.


# Next Versions
## learnings for V1
- [x] the holes for the case should be 3mm and those for the motors the small size
- [x] add pinheader for jtag
- [ ] add markings for + and - in the relevant places
- [x] gyroscope needs more space, to be able to be used in the middle
- [x] use jst-ph instead of xh
- [x] go up one size for smd. There is no benefit in 0403 when I solder it myself
- [x] use a stencil next time, when working with usb-c and Quad Flat No-Lead chips
- [ ] build cases for all the sensors/pcbs used
- [x] I don't need to care too much about interference from the motors for the usb since they will be turned off while flashing.
- [x] I want the testpoints near the peripheral they are for, because it's easier to test and see what is going on
- [ ] better naming and silkscreen helps a lot

## For Version 2
- [ ] make the LiPos chargeable
- [ ] use STM32 for the IR Sensors/ADC because they are less noisy
- [x] add proper silkscreen
I did think about adding lipo chargers, but:
1. It takes up some space to do that
2. It's hard to charge Lipos from one source in parallel
3. There are some nice TC4056 breakout boards which I can use, while running tests on the mouse

I did not use the STM32 for IR Sensors/ADC yet, because the quality of the IR sensor was somewhat sufficient and I needed to move fast, after Version 1 did not work at all.

## For Version 3
- [ ] use STM32 for the IR Sensors/ADC because they are less noisy
- [ ] get NPTH points for better alignment with the stencil
- [ ] go down to 2 layers so that it is cheaper to replicate


## What went wrong
I misunderstood "different voltages get different areas on the board (the planes can be split if no other signals are crossing those areas)". It means that you should split the power planes, if you have different voltages on your board. I thought it meant that you should split the ground planes beneath the power planes. There is no need for that, since in high frequency areas the signal will travel above the corresponding ground anyway. If it was closer to DC voltage, then it would just travel the shortes path through ground.

I definitely shouldn't have used a trackpad. My wrists hurt really bad and it was much slower than it needed to be.

I assumed that the adc multiplexing works across _all_ pins, but it only works for the first 14 or so. Testing every pin as I would like to use them on the board would have helped a lot!

## What went right
I could achieve a very small mouse with 4 layers. I'm not sure if I was able to do so with less layers.

I got over the fear of doing something for the first time and I learned a lot about electronics.


# Thanks
Hannes and Mohamad have helped me tremendously. Robert gave me some ideas for the next version and what I could have done better this time. Thank you!

# JLCPCB pricing
This has been really confusing to me, so that is why I am adding this section here. 
If you want to get your pcb from jlc pcb there are some weird things you have to look out for. For example, if you add a pcb with 4 layers from the beginning, it will have a lower price, than if you had added a 2 layer pcb and then changed it to a 4 layer pcb. The same goes for the size of the pcb. If you add a pcb with 100x100mm and then change it to 100x150mm it will be more expensive than if you had added a 100x150mm pcb from the beginning. 

Not only that, it gets really niche if you look at the assembly service. There are "basic", and "extended" parts. At least it looks like that. There are also "preferred extended" parts, which act like basic parts, if you use the "economic assembly". But first things first:
The assembly costs depending on what parts you choose. If you choose only basic parts and no special pcb, then this will get categorized as "economic assembly". That is the cheapest option you can get.

If you add "preferred extended" parts to this, it should stay in the "economic assembly" category. But if you add "extended" parts to this, it will get categorized as "standard assembly". This not only is the most expensive option, but you also have to pay 3$ for every _different_ extended part. So if you have some special transistor a thousand times on your board, that is 3$ extra for you. But if you have 10 different extended parts on your board, that is 30$ extra for you. This extra price comes from the fact, that they have to place those extended parts per hand into the machines, because they don't have the big reels for the most used parts.

Lastly there is a shipping option called "global direct standard line" which is much cheaper than the other options. 

## Some inaccurate information about the prices
- 2 layer 100x100mm: around 5$
- 4 layer 100x100mm: around 10$
- 4 layer bigger sizes: around 35 dollar$
- assembly economic: 7-10 dollar
- assembly standard: 20-30 dollar
- any extended part: 3 dollar
- global direct standard line: 2-5 dollar
- stencil: extra 2-20 dollar (mostly because of the shipping costs)

Overall I would say that small projects that don't need more than 2 layers should be done as such. Although 4 layers can give you a lot better signals and smaller boards, so I would always go for a 4 layer board that's smaller than 100x100 over a bigger 2 layer board.
The assembly is quite cheap, if you don't like soldering and would pay yourself for that time. The only problem is, that they don't have all the parts you might need and as soon as you use extended parts, it'll get really expensive really fast.


