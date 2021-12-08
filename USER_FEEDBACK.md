
# User feedback

> Memory manager working poorly under heavy memory pressure is one of the problems that gives the linux kernel bad reputation.
> Looking into recent talks over OOM livelocks, I'm pretty much persuaded that OOM-killer coming to save us from thrashing (to death) is nothing but a myth.
> By default overcommit settings, even unprivileged users could easily make thrashing happen, and even hang up the whole system by abusing memory allocation.

> Protect clean file pages under memory pressure
> https://github.com/hakavlad/le9-patch

> With this feature enabled, the OOM-killer should kick in very smoothly under critical memory pressure situations (as it originally should).
> Find the video links showing its great resilience, in the project page.
> The behavior can be enabled/disabled and configured by sysctl tunables, meaning that there seems to be no compatibility cost to adopt the patch.

-- firelzrd, https://forum.xanmod.org/thread-4102-post-7529.html#pid7529


> As far as I've been testing it out, the result is brilliant.

> - You can explicitly control the minimum amount of file cache to leave untouched on RAM.
> - When you run out of memory, the OOM reaper/killer always works very smoothly without slowing down or freezing the system forever.
> - Settings can be dynamically changed and the effect can be observed, or reverted to stock kernel's behavior by just setting sysctl knobs.

> With this patch I now set my vm.swappiness = 1 to keep as much working set as possible on memory, and it works just fine.

-- firelzrd, https://forum.xanmod.org/thread-4102-post-7531.html#pid7531


> In my humble opinion, le9 series isn't about helping server administrators coordinate their resource plans, but meant to address the problem that is making the operating system wrongfully breakable.

> This infamous thrashing and OOM-livelock problem is like "what Linux virtual memory manager was designed to do something, should be doing it, but isn't doing it" at critical memory pressure situations.
> Yes. It's originally something Linux should be doing.

> More than CGROUPs or similar kinds of fine-tuned configuration effort, OOM killer, once it's there, when needed, it must just work and kill what should be killed, without special settings.

> And I believe that le9's modification to the memory manager's behavior just does it perfectly.

-- firelzrd, https://forum.xanmod.org/thread-4102-post-7603.html#pid7603


> I find it especially egregious that so much work has to go into userspace daemons to nudge the kernel OOM killer to do its job; it just seems so wrong to dedicate something as fundamental as memory management to a userspace process. I firmly believe that no condition should ever lead to a frozen system (even if the kernel is still technically running behind the scenes), and blaming the user for overloading it doesn't solve the fundamental issue of the kernel's bad handling of non-optimal conditions.

-- sb56637, https://www.phoronix.com/forums/forum/software/general-linux-open-source/1267300-le9-strives-to-make-linux-very-usable-on-systems-with-small-amounts-of-ram/page2#post1267331


> This doesn't look like a hack to me. It seems to be a fairly simple and effective change that actually eliminates the root cause of these stalls. I hope it makes mainline.

-- brent, https://www.phoronix.com/forums/forum/software/general-linux-open-source/1267300-le9-strives-to-make-linux-very-usable-on-systems-with-small-amounts-of-ram/page4#post1267387


> Either way, killing some processes is obviously preferable to locking up the whole system in endless thrashing (which is equivalent to killing all processes).

-- intelfx, https://www.phoronix.com/forums/forum/software/general-linux-open-source/1267300-le9-strives-to-make-linux-very-usable-on-systems-with-small-amounts-of-ram/page5#post1267436


> I tried the patch by installing the Linux Mint ISO that is on ValdikSS's website on a netbook-class laptop with 2 Gb of RAM (and a painfully slow hard drive) and couldn't believe my eyes. What was previously an unusable system when running a browser (in my case Firefox) is now apparently perfectly fine. I didn't open 37 tabs, but enough of them for casual, every day browsing and it kept going.

-- tedesign, https://www.phoronix.com/forums/forum/software/general-linux-open-source/1267300-le9-strives-to-make-linux-very-usable-on-systems-with-small-amounts-of-ram/page7#post1267789


> I am a near total Linux noob who has been trying to resurrect an old ASUS W3J laptop purchased in 2006 that has a 32 bit intel 945PM chipset that limits the addressable ram. Although there are 2 x 2 GB DDR2 ram modules installed, the system can’t fully use all 4 GB. The RV530 mobility X1600 also allocates some of this ram so that the system has something like 2.8 GB total ram. It has a core2duo T7200 64 bit processor and an IDE 320 GB WD hard drive, so no SSD is possible. I’ve been working on trying out various Linux distributions over the last 3 weeks to replace XP, and they all have had major deficiencies. 32 bit distros don’t allow for the use of modern Firefox, and 64 bit distros use most of the ram very quickly, such that multitasking is not really possible. I happened across ValdikSS’s post and tried out the Mint XFCE 20.2 ISO.

> Oh my purple penguins! It’s like a whole new computer! I can open multiple tabs in Firefox, run the system monitor, open LibreOffice, and have media playing without the system grinding to a crashing halt! With Firefox, the most tabs I have opened is around 8, but the system is smooooth. It seems to run like it did with XP ages ago. The only issue with the iso from ValdikSS I have is with the suspend not working when I close the lid, but I think it is an XFCE issue, not an issue with the kernel. Still researching that one.

> In short, XanMod with the le9 patch has transformed a machine from 2006 that was usable but unsafe with XP into a comfortably usable and safe computing system with Mint XFCE 20.2. I can’t thank you enough for this. Seriously, this has saved me from having to buy a new laptop, and has given me so much joy in being able to use something that would otherwise go into a landfill. It’s a win for me and a win for the environment. 

-- KansaKilla, https://www.phoronix.com/forums/forum/software/general-linux-open-source/1267300-le9-strives-to-make-linux-very-usable-on-systems-with-small-amounts-of-ram/page8#post1268100


