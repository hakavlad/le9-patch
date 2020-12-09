# le9*.patch

> The attached kernel patch (applied on top of 4.18.5) that I've tried, almost completely eliminates the disk thrashing(the constant reading of executable(and .so) files on every context switch) associated with freezing the OS and so, with this patch, the OOM-killer is triggered within a maxium of 1 second when it is needed, rather than, without this patch, freeze the OS for minutes(or just a long time, it may even auto reboot depending on your kernel .config options set to panic(reboot) on hang after xx seconds) with constant disk reading well before OOM-killer gets triggered.

-- https://bugs.launchpad.net/ubuntu/+source/linux/+bug/159356/comments/89

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

-- https://lkml.org/lkml/2018/9/10/296

### Why don't you try sending it to linux-mm?

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
