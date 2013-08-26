WOOT '13 Trip Report
====================

I recently attended WOOT '13 (7th USENIX Workshop on Offensive
Technologies) which was held on 13-August-2013 in Washington, DC.

WOOT focusses on offensive technologies and is more "academic" (read
rigorous, less showmanship) than industrial conferences like Black Hat
and DEFCON.

Overall, I loved WOOT and I think that it is among the best "hardcore"
technical conferences I have attended. Also, Washington, D.C. is very
nice :-). This trip was funded by Solar Designer.

I presented my "Looking inside the (Drop) box" paper which was well
received and even generated some media buzz. See my "dedrop" project,
https://github.com/kholia/dedrop for more information on my paper.

Unfortunately, I was unable to attend USENIX Security '13 talks as the
fees was quite high ($1045).

Highlights
==========

Here are the highlights of my trip,

* Talked with Ron Rivest (in person!) about the "cryptocalypse" and he said,

  -  It is ALWAYS better to be prepared.

  -  Unexpected developments (e.g. factoring improvements) can ALWAYS
     happen.

  I mentioned that Red Hat is enabling ECC stuff and he was (visibly)
  happy to hear about it. He also went into algorithmic complexity of
  primality testing algorithms and I pretended that I understood
  algorithms to be nice ;)

* Ran into Felix "FX" Lindner. I mentioned to him that I have been
  reading his articles since high school. To which he replied "that only
  implies and proves that I am OLD" ;)

* Talked with Sergey Bratus about how x86 MMU fault handling mechanism
  is Turing complete. Sergey's paper might be the most "hardcore" paper which I
  have read (and didn't understand). Sergey thanked Solar Designer and pipacs
  for their help. He even asked me how I was associated with the Openwall
  organization.

* Had a general chat with J. Alex Halderman (the guy behind the Cold Boot
  Attacks paper) about their IPMI security work (which was great!).

Paper Summaries
===============

See https://www.usenix.org/conference/woot13/tech-schedule/workshop-program

* Truncating TLS Connections to Violate Beliefs in Web Applications

  - server beliefs != client beliefs (when some messages can be silently
    dropped).

  - Easy to read and understand paper.

  - The demo showing M$ Live account hijacking was cool.

* FireDrill: Interactive DNS Rebinding

  - Enable DNS rebinding attacks by overflowing the browser's DNS cache which
    is used for DNS pinning.

  - The live demo was super cool and showed how the presenter (the attacker)
    was able to gain access to his girlfriend's internal network.

  - Easy to read and understand paper.

* Subverting BIND's SRTT Algorithm Derandomizing NS Selection

  - DNS cache poisoning attack with too much maths in it.

* Bluetooth: With Low Energy Comes Low Security

  - Awesome paper on Bluetooth Smart / BTLE which showed that the key exchange
    is totally broken!

  - Encryption on any BTLE link can be broken trivially (and the original
    designers knew about this!).

* Breaking Cell Phone Authentication: Vulnerabilities in AKA, IMS, and Android

  - Attacks on 3GPP AKA (Authentication and Key Agreement Protocol).

  - Presented high-impact MiTM attack on T-Mobile's Wi-Fi calling service.

* Cloning Credit Cards: A Combined Pre-play and Downgrade Attack on EMV Contactless

  - Cloning EMV contactless cards to steal money. What can possibly go wrong?
    ;)

* Leveraging Honest Users: Stealth Command-and-Control of Botnets

  - Creating resilient botnet(s) by eliminating internal command-and-control
    infrastructure.

* From an IP Address to a Street Address: Using Wireless Signals to Locate a Target

  - A practical paper showing how an attacker can locate a target (physically!)
    by using Wi-Fi.

* Looking Inside the (Drop) box

  - The paper with the shortest title!

  - Reverse Dropbox, bypass SSL and 2FA

* Illuminating the Security Issues Surrounding Lights-Out Server Management

  - Very cool work showing that IPMI stuff is totally broken.

  - Lots of IPMI implementations have significant flaws (like buffer overflow
    in login handler).

  - This work should be read along with Dan Farmer's articles on IPMI.

* "Weird Machines" in ELF: A Spotlight on the Underappreciated Metadata

  - Describes how the ELF metadata itself can be used for doing computation!

* Introducing Die Datenkrake: Programmable Logic for Hardware Security Analysis

  - Open-source programmable and extensible hardware platform for doing
    "Hardware Security Analysis".

  - Maybe this box will get popular in the future and will replace application
    specific hardware devices.

* The Page-Fault Weird Machine: Lessons in Instruction-less Computation

  - x86 MMU fault handling mechanism is Turing complete!

  - If you have sleeping problems, give this paper a read ;)

 
Also see http://www.guusbosman.nl/2013/08/14/leet-and-woot-13

Thoughts
========

I only regret being "shy" and not talking to as many people as I should
have. Also, I should have tried sneaking into the USENIX Security '13 talks!

