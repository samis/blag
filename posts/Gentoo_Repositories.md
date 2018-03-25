[[!meta title="Using eselect-repository with Gentoo" author="samis"]]
[[!tag tech linux gentoo portage]]
# Background
Some time after installing Gentoo, I moved away from syncing the portage tree via Git,  because having history of the repository is nice and as a bonus, the repository can be synchronized from GitHub over HTTPS while rsync uses plain text. 
Commits in the repository are also GPG-signed, but the current version of portage does not use this (edit: by default, I rechecked the dates and 2.1.24 *should* have it) to validate the repository [(yet)](https://github.com/gentoo/portage/commit/7a507942b669b6a157612e8f7ce3fe362c39b38f)

However, the official Gentoo package repository is not enough, and even though I use [Bedrock Linux](https://bedrocklinux.org/), it's nice to have packages designed for Gentoo so I use a number of unofficial repositories or overlays to add more packages and the conventional tool to use is [Layman](https://wiki.gentoo.org/wiki/Layman) which has a decent-enough interface and support for a wide variety of methods to sync an overlay - all of the commonly-known OSS version control systems and some others. 
These overlays are updated at the same time as the official repository via portage (which calls down to layman to do the actual sync).

When I switched to syncing the tree via Git, I used a repository that had pre-generated all the additional information not directly contained in the actual 'gentoo' repository like metadata and other files. 
This has the advantage of being simpler to use, but as a consequence the repository's history and disk-usage is bloated because it has to track the entire history of not only the ebuilds and related files, but also the easily-reproducible metadata and files taken from other repos - and recently I found a package that resolves the problem with ease-of-use so I decided to try it out (I had other reasons, but that's for later)

At the same time, I thought that since most of my used overlays were using git, wouldn't it make sense to sync them the same way, rather than going through layman? 
It'd certainly be simpler, a quick look showed that there was a package that provided a good interface for this, [eselect-repository](https://github.com/mgorny/eselect-repository) that also was much simpler than layman, being under 500 total lines in a file compared to layman's roughly 10kloc (estimated by cloc after removing autoconf and doc). 
Any non-git overlays would stay managed by layman, though. 

# Doing the move
(Note: Some parts left out or tweaked for the purposes of this post.)

1. I added the mv overlay to add the package with the scripts, and then installed both it and `eselect-repository` with emerge.
2. Ensure `/etc/portage/repos.conf/gentoo.conf`looks like this (change the clone-depth if you don't want a full clone):

        [DEFAULT]
        main-repo = gentoo
             
        [gentoo]
        location = /usr/portage
        sync-type = git
        sync-uri = https://github.com/gentoo/gentoo.git
        auto-sync = yes
        clone-depth = 0

3. Tweak `/etc/eselect/repository.conf` so it uses the original sources, rather than the ones with metadata in the history. 
4. Make a note of your layman-managed git overlays somewhere to re-add them shortly afterwards. 
5. Remove (or disable) all the git overlays using `layman` (`-d` or `-D`)
6. Re-add the overlays using `eselect repository enable` for overlays on the main list, and `add` for those not
7. Run your first sync command. Ignore the messages about non-existent directories, they'll go away as each overlay is created when synced.

# Unrelated Benefits
While this section isn't related to either eselect-repository or layman, it is related to the changes and why I did them. Essentially, after finding out about a certain overlay which had a large number of packages, I noticed that eix-sync was taking a good amount of time to update the database for this large overlay - looking at another, I noticed that I never generated the metadata cache for any of my overlays. While googling about this, I found the complete set of shell scripts to update this for all overlays and repositories, along with other things (and also found a post pointing out the wastefulness of storing all the metadata in git for the Gentoo repository). The end-result was this post, and the generated metadata cache, while taking up-front CPU time, makes the eix updates faster overall and hopefully will also provide a nice speed boost for portage in general. 

# Next?
While there are not many next steps, the most obvious one is to add support in portage for the sync methods that Layman provides but Portage itself does not. Even though I'm not an expert in Python, this shouldn't be difficult to implement.

(Addendum: Someone in fact already wrote a module for Mercurial syncing, which is one of the two non-git overlay types I use. You can find it on Gentoo's bugzilla and apply it as a user patch, it works perfectly fine in my experience.)
