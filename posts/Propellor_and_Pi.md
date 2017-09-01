[[!meta title="Propellor and a Raspberry Pi 3" author="samis"]]
[[!tag tech linux]]
I've given a number of configuration management tools a whirl - Puppet, Chef, Salt, Ansible.
Chef and Puppet started nice, but seemed complex to use given my small needs.
Salt and Ansible had potential and features unique to either, but I've never liked using YAML syntax, and this showed.

Recently I've looked into [Propellor](https://propellor.branchable.com/), a configuration management system written in Haskell with the interface being a DSL in the same language.
I've never been a Haskeller, indeed I'm an unrepentant fan of Smalltalk's beautiful OO simplicity - so why?

1. The provided DSL looked interesting and readable. More readable than YAML at any rate (and likely the DSLs of the other two)
2. Despite being written in and using Haskell, it claimed that knowledge of it was not a prerequisite.
3. The 'getting started' process wasn't complex. The most difficult part was waiting for GHC and dependencies to be compiled (Gentoo.)
4. Unlike with Chef or Puppet, there was no fancy layered complexity of the tool itself. No fancy 'structures' like Librarian, R10K and Berkshelf for Chef. No large servers requiring gigabytes of RAM all the time.

I got started, there were and remain some niggles and quirks on the road - for example my VPS needed special bootstrapping attention (interestingly the problem was fixed upstream the same day I worked around it)
Soon enough two of three boxen of interest had a no-op configuration applied, indicating that the program was working correctly for future usage. But what happened to that third one? It's a long story.

This third box was my [Raspberry Pi 3](https://cdn.shopify.com/s/files/1/0174/1800/products/Rainbow_1_of_3_47e94e82-ba3a-4804-a280-3140109cd304_1024x1024.jpg?v=1456669057) running [Arch Linux ARM](https://archlinuxarm.org/).

# My attempts

First I tried just installing GHC. Didn't work, linking errors (later I found there were missing static libraries)
Then I tried to get a known-good compiler via Stack...before realizing it didn't have ARM binaries. It also didn't work well with the previously mentioned compiler.
I then tried installing an additional package that contained the static libraries. That almost worked...but I later found it to be incomplete Oh well.

# Getting weirder and crazier...

My next plan was very weird: Taking the Debian packaged binaries and running them on my system. This worked when in my home directory....but magically stopped working when installed system-wide (??)
The one that actually succeeded (the most) was simpler: Grab a generic binary release tarball and extract it into /usr/local. This actually worked, no linking errors woo!

# Close, but no cigar..yet
This meant the compiler was actually working, but when it came to install the software, the newer compiler meant I had to use a newer snapshot of packages.
This brought dependency problems, which could be solved by compiling and installing another program.
I did what was needed, and the program was compiling. The speed was slow, much slower than the my 'hello world' tests.

And then, in the middle, it hung. It still answered ping requests, but I couldn't type or SSH in.
I tried rebooting, with the intent of trying again either the same method or differently. Unfortunately, the system didn't come up on wifi afterwards.
Nor did it come up when connected via Ethernet.

What was wrong? Plugging in a HDMI cable told me that, and it was 'fsck: UNEXPECTED INCONSISTENCY. Run fsck manually.'
I couldn't properly handle this because the receiver for my USB keyboard was godknowswhere.
I need to handle this offline, which means digging out my laptop and using it's sdcard slot. I think I'll take the opportunity to downgrade the compiler while I'm at it. Maybe pre-compile some software if the emulation isn't too bad.
