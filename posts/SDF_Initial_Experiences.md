[[!tag tech servers sdf netbsd shell]]
So, I now have a prevalidated (for now) account over at the [Super Dimension Fortress (SDF)](https://sdf.org/). 
I'll most likely validate it later, but not right now. 
In this post, I'm going to go over my initial experiences and my thoughts about them. 

# Initial Signup Process
Anyone can start using SDF by making a free account, and there's multiple ways of doing so. 
There's a simple web form on the SDF website to do it. 
Other methods include connecting to new@sdf.org using either SSH or Telnet (either works)

I used the SSH method - though my initial choice of when to try proved to be wrong, as the one time it didn't time out it disconnected me very early in the process.
Anyway, when SSHing to the system you'll get a series of prompts that ask you questions about your new account. 
There's the obvious ones about username/password, but it also asks you for some details (name, email, and zip code) and isn't clear if this is required or expected (I don't think it is)
or how the information entered will be used and displayed. As such, I only entered my name but am happy to enter an email if needed (I do not have a ZIP code, though. Not American, you see?)

If you exit this user account creation process (two methods are saying N to the final 'are you sure' about it or trying to use an already-taken name) you get dumped into a basic command-line menu.
The menu's prompt is 'FEP Command' and while this seems inconsequential, it is in fact a reference to a completely different type of computer than the UNIX and Windows-based systems that are currently dominant.
In the 1980s and early 1990s, there were companies that made Lisp Machines, which were and still are very different to modern systems running a UNIX-like or Windows. 
On these systems, there was a Lisp processor, but in many systems the Lisp CPU was unable to boot the system standalone, so there was an additional processor (or for the later ones, something specially integrated with the main processor) to bootstrap the Lisp one with the rest of the system.
Symbolics systems, in particular, had a Front End Processor that perfomed this task (as well as other things like handling full system crashes).
SDF is not a Lisp Machine, it has no actual FEP. However, this restricted initial commandline serves similar purposes.
  * It acts as a minimal 'frontend' to the actual SDF boxen. 
  * It's used to create new accounts to the system, 'bootstrapping' your access :)
It's a nice reference or play on words, isn't it?
# On the System
As a prevalidated user, you don't get access to a actual shell, instead you get their custom restricted shell, called 'psh'. 
It has various limitations which will quickly become apparent, especially if you use a very nicely customised zsh environment. 
A minor detail I noticed is that both 'emacs' and 'nano' end up presenting the same editor: UW Pico 4.10. 
However, even though the shell is limited you still have the Gopher client and limited usage of lynx.
There's also the bulletin board and chat systems, both of which I believe are unique to SDF and sdf-eu. 
While the FEP prompt had a 'software' command, there's also one in psh and surprisingly the results are very different to the ones you get from the same FEP command (much newer, for example). 
At the time of writing I haven't heavily used either Gopher, the BBS or the chat system, but hopefully that will change (and this post will be on my new SDF gopherhole too).
