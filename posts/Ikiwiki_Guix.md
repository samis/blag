[[!meta title="Guix-based ikiwiki" author="samis"]]
[[!tag tech linux guix ikiwiki]]
This site very obviously uses [ikiwiki](https://ikiwiki.info) both as a static wiki compiler but also for the few dynamic elements present. It's written in perl, and has a large number of Perl module dependencies. Nearly all of these do not care about the versions of much else, so they'll just work after you install them. Unfortunately, I discovered the Ikiwiki's image functionality uses the PerlMagick module, which does indeed depend on ImageMagick - in fact, it depended on ImageMagick 6.x. It wasn't in Alpine's package repositories for my version (they only had 7.x ) and installing it by hand did not work, failing with an error. I even tried replacing the distro's ImageMagick with my own that included perl support, but to no avail. 

# Enter Nix (and Guix)
At this time I had previously encountered Nix and Guix, both of which offer a substantially different approach to package management compared with conventional systems (such as regular distro package managers but others as well). One of their offered features is isolation: You can use a package (or a version of a package) without that extending to other packages or the system as a whole. There's other useful features that drop out of their model but this is the key one in this context. 

## Nix
[Nix](https://nixos.org/nix/) is the older, more well-known and expansive one so I tried that first. It's not perfect - I'm not really much a fan about the Nix derivation language which has been made from whole cloth solely for the project's purposes. This was not a dealbreaker, given that as a user I would hopefully not need to touch this at all. It passed the first test of actually having an ikiwiki package, but it failed the second as it would not install and on closer inspection it was marked as 'broken' due to test failures. There was no immmediate fix in sight. I decided to look my other options, starting with Guix.

## Guix
The main (and maybe only?) good alternative to Nix is [GNU's Guix](https://www.gnu.org/software/guix/). The primary difference is that instead of Nix's external DSL, Guix uses an internal DSL in GNU's own Guile language. I personally prefer this as it's nice to read and comes with the bonus of having a regular programming language at your fingertips when you need it. Additionally, Guix's NixOS-equivalent results in the system, init system and package manager having the same, unified configuration language - Guile. The system's defined in an .scm file, packages are defined by Guix's .scm files, services are defined by .scm files provided by Guix and read by Shepherd. 
Conincidentally, right at the moment I was looking into Guix a patch containing an ikiwiki package had been posted on their mailing list. I decided to wait for it to get merged and then try it out. Unlike the Nix version, it actually installed! It even almost ran (although there were some missing dependencies. More on that shortly!) and generated my wiki successfully. No imagemagick problems because it linked against it's own copy, stored under a long name in /gnu/store. 

It was not perfect however, as I discovered that the more dynamic portions of the site (such as ikiwiki.cgi) complaining about not being able to find perl modules that should've been installed as dependencies. I tried to add packages and tweak the configuration to point it at different locations, but I could not make it find what I needed. I sought help from the #guix channel on Freenode, but it took a while and luckily after I explained the problem and pointed the person helping (they even happened to be the original submitter), they had made a patch to fix the problem. A few tweaks later and all the dynamic parts were working again, even though I had to add some packages to Guix via their nice command-line importer and some editing in Emacs.

Shortly after everything was working, I decided to move my modified ikiwiki and related packages out of modified Guix source tree, into their own proper namespace. After doing this, I setup a mirror of this namespace and repository to a public git server instance. Whatever packages I'm using but haven't finished or tried to upstream yet can be found [here](https://ahti-saarelainen.zgrep.org/git/samis/guix), on Ahti's Gogs. 

# Onward?
This sequence of events has lead me to reconsider using the blob of Perl with many dependencies that is ikiwiki. I've looked for alternatives but didn't find any that combine the concepts of blogs and wiki in such nice ways. The closest is DWiki, but that has substantially less features vs ikiwiki so I decided not to try it out. (Although it's still a nice piece of Python software). Due to this lack of replacements, I've been wanting to replace ikiwiki with something not written in Perl, and likely written by me. Conincidentally, I've wanted to learn more about the Common Lisp programming language (and the Lisp family in general), and this provides a very nice opportunity to learn the language and then use it to solve a desire that I have.