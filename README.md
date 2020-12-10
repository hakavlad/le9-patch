# le9*.patch

> The attached kernel patch (applied on top of 4.18.5) that I've tried, almost completely eliminates the disk thrashing(the constant reading of executable(and .so) files on every context switch) associated with freezing the OS and so, with this patch, the OOM-killer is triggered within a maxium of 1 second when it is needed, rather than, without this patch, freeze the OS for minutes(or just a long time, it may even auto reboot depending on your kernel .config options set to panic(reboot) on hang after xx seconds) with constant disk reading well before OOM-killer gets triggered.

— https://bugs.launchpad.net/ubuntu/+source/linux/+bug/159356/comments/89

> Tested on kernel 4.18.5 under Qubes OS, in both dom0 and VMs. It gets
> rid of the disk thrashing that would otherwise seemingly-permanently
> freeze a qube (VM) with continous disk reading (seen from dom0 via
> sudo iotop). With the above, it only freezes for at most 1 second
> before OOM-killer triggers and restores the RAM by killing some
> process.

> If anyone has a better idea, please let me know. I am hoping someone
> knowledgeable can step in :)

> I tried to find a way to also keep Inactive file pages in RAM, just
> for tests(!) but couldn't figure out how (I'm not a programmer).
> So, keeping just the Active file pages, seem good enough for now, even
> though I can clearly see (via vm.block_dump=1) that there are still
> some pages that are being re-read during high memory pressure, but
> they for some reason don't cause any(or much) disk thrashing.

— https://lkml.org/lkml/2018/9/10/296

## Why don't you try sending it to linux-mm?

multiple reasons
* this patch is just a proof of concept really, and does not meet the quality I'd accept of myself for sending it upstream (have you read that help text? lol)
* sending patches to ML requires having read and knowing all the rules for submitting patches - <s>yuck </s>(ie. me lazy)
* they require real name and I don't want/care to provide one(did it in the past tho)
* they will want changes to the patch that I won't like to do while still keeping my name attached to the patch (as a example from my prev. time: moving a define whose place was clearly inside a .h near its siblings(CPU stuff), into the .c right above and in the <s>middle</s>(actually top) of the function of the code using it, just because it was the only place this define was used)
* lazy
* kernel is so bugged that I learned to not care anymore

But hey if anyone else wants to send it, be my guest, but use your own name (it's ok, you can pretend that you wrote it, you've my permission, or you can even modify it)
I don't care, I consider the patch in the public domain(and/or all other licenses, for ease of use).

<s>/me out</s>(actually I've decided to resume using this account(since today 03sept2019) - maybe because I'm too lazy to create yet another one everywhere, or I simply want to synergize on this one) - EDIT: nevermind, deleted everything at the end of oct. 2019, but my gists r still available on archive org tho.

-- https://www.phoronix.com/forums/forum/phoronix/general-discussion/1118164-yes-linux-does-bad-in-low-ram-memory-pressure-situations-on-the-desktop?p=1120024#post1120024

## Info

`le9h.patch` was tested on Debian 9 with Linux 5.3.18 and works well. Need to rebase for Linux 5.4+.

## Effects

The effects are the same as with [prelockd](https://github.com/hakavlad/prelockd) but can be achieved by reserving less memory size:
- OOM killer comes faster (especially with noswap).
- Fast system reclaiming after OOM.
- Improved system responsiveness under low-memory conditions.

## Demo

https://youtu.be/eJ7kRaJT4Ts - Linux 5.9.12 with le9g.patch

## Known problems

- `MemAvailable` may be calculated incorrectly (reserved `vm.unevictable_activefile_kbytes` value cannot be reclaimed);
- Setting too high `vm.unevictable_activefile_kbytes` can lead to unwanted and too aggressive swapping out. Don't set too high `vm.unevictable_activefile_kbytes` value.
![pic](https://i.ibb.co/8cNsJXT/Virtual-Box-deb9-2-09-12-2020-23-31-54.png)
![pic](https://i.ibb.co/9p9q698/Virtual-Box-deb9-2-09-12-2020-23-33-42.png)

## See also

- Main thread https://web.archive.org/web/20191018023405/https://gist.github.com/constantoverride/84eba764f487049ed642eb2111a20830
- https://bugs.freedesktop.org/show_bug.cgi?id=111601
- OOM killer doesn't work properly, leads to a frozen OS https://unix.stackexchange.com/questions/373312/oom-killer-doesnt-work-properly-leads-to-a-frozen-os/458855#458855
- RFC: vmscan: add min_filelist_kbytes sysctl for protecting the working set (2010) https://lore.kernel.org/patchwork/patch/222042/
- https://forum.rosalinux.ru/viewtopic.php?p=101829&sid=10e68a368377f6031a55b1ed1a1802ec#p101829
- https://abf.io/import/kernel-desktop-4.15/blob/rosa2016.1/le9-rosa.patch
- https://www.kernel.org/doc/html/latest/vm/unevictable-lru.html
- When DMA is disabled system freeze on high memory usage (since 2007) https://bugs.launchpad.net/ubuntu/+source/linux/+bug/159356
- Let's talk about the elephant in the room - the Linux kernel's inability to gracefully handle low memory pressure
    - https://lore.kernel.org/lkml/d9802b6a-949b-b327-c4a6-3dbca485ec20@gmx.com/
    - https://www.phoronix.com/forums/forum/phoronix/general-discussion/1118164-yes-linux-does-bad-in-low-ram-memory-pressure-situations-on-the-desktop
    - https://news.ycombinator.com/item?id=20620545
    - https://www.reddit.com/r/linux/comments/cmg48b/lets_talk_about_the_elephant_in_the_room_the/
- https://stackoverflow.com/questions/52067753/how-to-keep-executable-code-in-memory-even-under-memory-pressure-in-linux
- Howto prevent kernel from evicting code pages ever? (to avoid disk thrashing when about to run out of RAM)
    - https://stackoverflow.com/questions/51927528/how-to-prevent-linux-kernel-from-evicting-file-backed-executable-pages-when-abou
    - https://lkml.org/lkml/2018/8/22/176
    - https://lkml.org/lkml/2018/9/10/296
- `le9b.patch` https://launchpadlibrarian.net/386196358/le9b.patch
- `le9d.patch` https://launchpadlibrarian.net/389887258/le9d.patch
- `le9e.patch` https://web.archive.org/web/20191018064208/https://github.com/howaboutsynergy/q1q/blob/865a6500231aec266bc9d646dfd230908b8676e5/OSes/archlinux/home/user/build/1packages/4used/kernel/linuxgit/le9e.patch
- `le9g.patch`
    - https://bugzilla.kernel.org/show_bug.cgi?id=196729#c49
    - https://www.phoronix.com/forums/forum/phoronix/general-discussion/1118164-yes-linux-does-bad-in-low-ram-memory-pressure-situations-on-the-desktop?p=1119440#post1119440
- `le9h.patch`
    - https://web.archive.org/web/20191018023217/https://gist.github.com/howaboutsynergy/04fd9be927835b055ac15b8b64658e85
    - https://www.phoronix.com/forums/forum/phoronix/general-discussion/1118164-yes-linux-does-bad-in-low-ram-memory-pressure-situations-on-the-desktop?p=1119792#post1119792
- `le9i.patch` https://web.archive.org/web/20191018023434/https://gist.github.com/howaboutsynergy/cbfa3cc5e8093c26c29f5d411c16e6b1
