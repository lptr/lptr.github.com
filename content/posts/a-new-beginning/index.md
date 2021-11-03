---
title: "A new beginning"
date: 2021-11-01T16:18:59+01:00
draft: false
---

Looks like every ten years I attempt to write a blog. This time around it seems that I actually have something to say, so let's see what happens. :)

{{< figure src="flow-alert-photo.jpg" title="Flow alert device" align="center" >}}

Above is a flow sensor I am currently building for our family farm. It's the second IoT device to be installed there, and its role will be to warn us whenever the irrigation system develops another leak. The device uses a ton of technologies I haven't even heard of 18 months ago when all of this started. Many things worked out really well since then, and it took help from many people. Below is a recounting of the trip so far, to serve as introduction for the next chapters.

## The printer

As so many things recently, this all started with Covid. When the virus hit, I wanted to help. I saw an article on the web about people 3D printing "ear savers" for people doctors wearing masks all day, and even holders for plexiglass shields, when there weren't enough surgical masks around in the beginning. This sounded like a great idea, and I wanted to join.

After consulting with a colleague at work who had prior experience, I got myself a [Prusa MK3S](https://shop.prusa3d.com/en/3d-printers/180-original-prusa-i3-mk3s-kit.html). And because of this colleague's advice, I did not get the pre-assembled printer, but ordered a kit to be put together by myself. While I never managed to help doctors with my prints, this turned out to be the beginning of a ton of adventure in both technology and self-discovery.

You see, back then I was as far from considering myself a maker, or even a tinkerer, as you could possibly imagine. To demonstrate: I recently got a grill for the garden; the one with the little wheels to move it around. The wheels were secured to the axle with a _cotter pin,_ and I messed up one of them during assembly. I later spent _hours_ in home improvement stores online and offline to find the exact same pin, to no avail. I was convinced that the grill would only function properly with the stock pin. Of course, today I know that any piece of wire would have done the trick, but that realization came after a lot of learning. (That's not to say I ever actually fixed the wheel, of course.)

So when the box with the printer finally arrived last April, I was extremely excited, but also terrified. Here I was, a software developer by trade, who is also proficient at putting together Lego (even Technic!), but I couldn't imagine myself successfully assembling _grown up stuff_ in the _real world._ But the Prusa manuals were great, and with a little help from my wife I finished building the printer by 4 AM, and had the first print (a [Benchy](https://www.prusaprinters.org/prints/3161-3d-benchy)) ready by 7 AM. I was extremely proud and surprised. And even though the print came out partially broken due to issues with layer adhesion, it was marvelous.

This was a turning point in my life: I could imagine, for the first time, that my work can have an immediate effect on the physical reality around me. That I can build stuff. And boy was this just the beginning...

{{< figure src="speaker-mount.jpg" title="Speaker mount, one of my first functional designs" align="center" >}}

Both of my parents are mechanical engineers, and I always looked up to them for their ability to design actual physical objects. My father was an early adopter and an avid fan of AutoCad and Inventor, and I always wanted to design my own stuff like he did. While those tools I could never afford for my hobby, AutoDesk allows just the people like me to use some of their most advanced tools for free as part of the [Fusion 360 personal use package](https://www.autodesk.com/products/fusion-360/personal). And there are an amazing variety of [tutorials to get someone started](https://www.youtube.com/watch?v=VbSkwvZyU_0). So I dove in!

## A light comes on

The next piece of the puzzle came as another kit: after printing some parts for his scooter, a friend paid me with an ESP8266 MCU, a breadboard, a blue LED and a single resistor. With that he also kicked me way out of my comfort zone of Java development, and put me on a path of learning and facing a number of my fears around technology.

The first of these was electronics: I hated the subject in high school and then at university. It took months to muster up the courage, but eventually I connected the pieces, and uploaded my first self-written firmware. During my career I participated in several big releases, but few could have lived up to the excitement I felt at that single blue light blinking for the first time!

{{< figure src="esp8266.jpg" title="The first ESP8266" align="center" >}}

The next major step was to learn C++: a language I haven't used since university 20 years ago. As someone who jumped on the managed languages bandwagon in 2000 when .NET wasn't even a beta, and has been working mostly with Java ever since, C++ represented danger and chaos. As one of the lucky coincidences, in [my work I also had to do some C++](https://docs.gradle.org/6.7.1/release-notes.html#file-system-watching-is-ready-for-production-use). It was a pleasant surprise to find that C++11 changed the game fundamentally, and for the better. But still, it was an entirely new world with its strange mechanics (what's a move constructor, again?) and lack of safety rails.

[Visual Studio Code](https://code.visualstudio.com/) was another revelation. Having used Eclipse and then IntelliJ most of my life I kind of accepted that IDEs are huge, unwieldy beasts by necessity. VSCode proved that wrong, and convinced me that there is a better, lightweight approach to software development. [Platform IO](https://platformio.org/) made it alost too easy to get started and then become proficient in embedded development.

## Lives on the line

The first major project where all of this (and more) came together was my chicken door. We have a small family farm a few kilometers away from our home where we started raising chickens (did I mention my wife is also on a learning spree?), and letting them in and out of the coop every day was a chore we decided to do without. But it had to be really-really safe, as we knew from experience that foxes and ferrets were frequently paying visits to our chicken coop.

{{< figure src="chickens.jpg" title="Some of our first chickens, named after Hungarian royalty" align="center" >}}

If you saw the title of this blog, you know I couldn't just design a _simple_ chicken coop door. It had to be properly overengineered! After several iterations I ended up with using a Bosch aluminum profile for the frame, fishing line and a large aluminum plate for the door itself, an [ESP32 board](https://github.com/Xinyuan-LilyGO/LilyGo-T-Call-SIM800) I got from another friend for brains, and several 3D printed parts all designed in Fusion to put the thing together.

{{< figure src="chicken-door-drawing.png" title="The brains of the chicken door (MK3) sans wiring" align="center" >}}

One of the things I learned was that hardware design was just as iterative a process as software development was. I first tried my prototype out inside the house, then outside, and finally in the chicken coop itself. But before it could be installed in its production environment, there was a lot of going back to the drawing board, redesigning the 3D parts, soldering and re-soldering connectors, ordering replacement parts for the electronics I messed up with my clumsy soldering, starting yet another new drawing in Fusion titled "MK3" etc.

## The software backend

And the software was no easier! For one, there were completely new challenges, like dealing with the limited resources available on the ESP32 platform. Take for example a seemingly trivial thing, like checking if a given time has passed. This code won't work:

```cpp
if (millis() > previousTimestamp + timeoutInMillis) {
    // This will not always do what you think it should do
}
```

Or more precisely: it would only work for the first fifty-ish days (or the first 2^32 milliseconds). After that, `previousTimestamp + timeoutInMillis` can roll over, and the condition will trigger immediately. Imagine debugging that in production!

The solution? You have to shuffle things around a bit:

```cpp
if (millis() - previousTimestamp > timeoutInMillis) {
    // Do your thing that should happen after the timeout
}
```

A subtle difference, but the code works correctly now indefinitely! This is a problem you need to deal with on a 64-bit system only if you are working with time spans across hundreds of millennia, even if your timestamps maintain nanosecond precision.

Much of the rest that I learned during my career was also non-applicable in this new environment, or wasn't as readily available. Take automated testing. On the JVM it is very typical to test the entirety of the codebase. With an application designed to be tested via dependency injection, proper abstractions etc. it is easy to test things, too. Native development, at least the parts I've interacted with, seems to be on a very different opinion. It is also much harder to pry your code away from the Arduino framework to keep it testable on its own. But eventually I managed to get the core of the logic testes with [GoogleTest](https://github.com/google/googletest). I can't imagine how else I could sleep well, knowing the complexity of the [state machine code](https://github.com/kivancsikert/chicken-coop-door/blob/7700468dc6b997e306b75f8abc70d9919f7bb3b7/lib/door/DoorLogic.cpp#L42-L126) that is keeping my chickens safe during the night!

Mentioning that state machine code reminds me that about half of it has been written by Visual Studio Code's [Copilot feature](https://copilot.github.com/). It is truly astonishing how _relevant_ the suggestions for the AI aer based on so little context. It truly felt like having a second pair of eyes on the code all the time. Meeting copilot was my "[AlphaGo Zero](https://en.wikipedia.org/wiki/AlphaGo_Zero) moment," where I got the first taste of what AIs could mean in the future, how immediate that effect will be, and how very soon.

But enough of SkyNet for now, let's go back to chickens!

{{< figure src="chicken-door.jpg" title="Successful user testing" align="center" >}}

This thing is now operational for a few months without any major problems. The final setup uses a local Raspberry Pi that relays MQTT messages back-and-forth to a server in the cloud. (Did I mention I was always afraid to install my own servers? Well, that's of the past now, too.) There are graphs tracking motor position and light levels throughout the year using [Grafana](https://grafana.com/) and [InfluxDB](https://www.influxdata.com/products/influxdb/). There's also a dashboard created using [NodeRED](https://nodered.org/) that allows us to control the door from afar. And all this is running nice and isolated using [Docker Compose](https://docs.docker.com/compose/) (yes, even on the Pi).

{{< figure src="node-red-dashboard.jpg" title="NodeRED dashboard on phone" width="50%" align="center" >}}

So this is where we are today. I went from a non-maker Java developer to learn stuff about CAD, C++, Arduino, 3D printing, electronics, infrastructure as a service and a ton of other stuff. I am not planning to slow down either, and would like to continue exploring things like subtractive manufacturing (I'd love to build one of those home CNC mills), solar power, robots, image processing and machine learning just to name a few. Check back if you are interested in these continued adventures! :)
