
# Protect clean file pages under memory pressure

Protection of clean file pages (page cache) may be used to prevent thrashing, reducing I/O under memory pressure, avoid high latency and prevent livelock in near-OOM conditions. The current le9 patches provide two sysctl knobs for soft and hard protection of clean file pages. The current le9 patches are based on patches that were originally created by Mandeep Singh Baines (2010) and Marcus Linsner (2018-2019). Let's give the floor to the original founders:

> On ChromiumOS, we do not use swap. When memory is low, the only way to free memory is to reclaim pages from the file list. This results in a lot of thrashing under low memory conditions. We see the system become unresponsive for minutes before it eventually OOMs. We also see very slow browser tab switching under low memory. Instead of an unresponsive system, we'd really like the kernel to OOM as soon as it starts to thrash. If it can't keep the working set in memory, then OOM. Losing one of many tabs is a better behaviour for the user than an unresponsive system.

> This patch create a new sysctl, min_filelist_kbytes, which disables reclaim of file-backed pages when when there are less than min_filelist_bytes worth of such pages in the cache. This tunable is handy for low memory systems using solid-state storage where interactive response is more important than not OOMing.

> With this patch and min_filelist_kbytes set to 50000, I see very little block layer activity during low memory. The system stays responsive under low memory and browser tab switching is fast. Eventually, a process a gets killed by OOM. Without this patch, the system gets wedged for minutes before it eventually OOMs.

— https://lore.kernel.org/patchwork/patch/222042/

> The attached kernel patch (applied on top of 4.18.5) that I've tried, almost completely eliminates the disk thrashing(the constant reading of executable(and .so) files on every context switch) associated with freezing the OS and so, with this patch, the OOM-killer is triggered within a maxium of 1 second when it is needed, rather than, without this patch, freeze the OS for minutes(or just a long time, it may even auto reboot depending on your kernel .config options set to panic(reboot) on hang after xx seconds) with constant disk reading well before OOM-killer gets triggered.

— https://bugs.launchpad.net/ubuntu/+source/linux/+bug/159356/comments/89

Original le9 patches protected active file pages. Current versions (le9cb) protect clean file pages (`Active(file)` + `Inactive(file)` - `Dirty`).

## le9cb patches

`le9cb*-5.10` patches may be correctly applied to Linux 5.10—5.12-rc4.

`le9cb*-5.4` patches may be correctly applied to Linux 5.4.

`le9cb*-4.19` patches may be correctly applied to Linux 4.19.

The `vm.clean_file_low_kbytes` sysctl knob provides *best-effort* protection of clean file pages. The clean file pages on the current node won't be reclaimed uder memory pressure when their volume is below `vm.clean_file_low_kbytes` *unless* we threaten to OOM or have no swap space or vm.swappiness=0.

The `vm.clean_file_min_kbytes` sysctl knob provides *hard* protection of clean file pages. The clean file pages on the current node won't be reclaimed under memory pressure when their volume is below `vm.clean_file_min_kbytes`.

The `le9cb0`, `le9cb1`, `le9cb2` patches differ only in the default values.

`le9cb0` just provides two sysctl knobs with 0 values and does not protect clean file pages by default.

`le9cb1` provides only soft protection (`vm.clean_file_low_kbytes=150000`, `vm.clean_file_min_kbytes=0`). This patch may be safly used by default.

`le9cb2` provides hard protection of clean file pages (`vm.clean_file_low_kbytes=250000`, `vm.clean_file_min_kbytes=200000`).

## Effects

- Improving system responsiveness under low-memory conditions;
- Improving performans in I/O bound tasks under memory pressure;
- OOM killer comes faster (with hard protection);
- Fast system reclaiming after OOM.

## Testing

These tools may be used to monitor memory and PSI metrics during stress tests:
- [mem2log](https://github.com/hakavlad/le9-patch/tree/main/mem2log) may be used to log memory metrics from `/proc/meminfo`;
- [psi2log](https://github.com/hakavlad/nohang/blob/master/docs/psi2log.manpage.md) from [nohang](https://github.com/hakavlad/nohang) package may be used to log [PSI](https://facebookmicrosites.github.io/psi/docs/overview) metrics during stress tests.

## Demo

- https://youtu.be/c5bAOJkX_uc - Linux 5.9 + `le9i-5.9.patch`, playing supertuxkart with 7 threads `while true; do tail /dev/zero; done` in background. `vm.unevictable_activefile_kbytes=1000000`, `vm.unevictable_inactivefile_kbytes=0`.
- https://youtu.be/d4Sc80TMEtA - webkit2gtk3 compilation with zram-fraction=1, max-zram-size=8192. No hangs, no heavily freezes, system was responsive for all time during webkit2gtk3 compilation.
- https://youtu.be/iU3ikgNgp3M - The Linux (with le9 patch) kernel's ability to gracefully handle memory pressure. Boot with `mem=4G`, no swap space, opening chromium tabs, no hangs. The killer comes without delay. This is how le9 patch fixes the problem described [here](https://lore.kernel.org/lkml/d9802b6a-949b-b327-c4a6-3dbca485ec20@gmx.com/).

## Warnings

- These patches were written by an amateur. Use at your own risk;
- `MemAvailable` may be calculated incorrectly (protected `vm.clean_file_min_kbytes` value cannot be reclaimed);
- Hard protection of file pages may invoke `Fatal IO error 11` [#5](https://github.com/hakavlad/le9-patch/issues/5) and even `kernel: Oops` [#6](https://github.com/hakavlad/le9-patch/issues/6) with DRM/i915 driver. [Disabling](https://github.com/hakavlad/disable-i915-gem-shrinker) DRM/i915 GEM shrinker can prevent this.

## Need to review

These patches need to be reviewed by linux-mm peoples.

## How to get it

- The kernel build with the patch is available for Fedora 33: https://copr.fedorainfracloud.org/coprs/atim/kernel-futex/;
- [pf-kernel](https://gitlab.com/post-factum/pf-kernel/-/wikis/README) provides the file pages protection (with own le9 implementation) by default since [v5.10-pf2](https://gitlab.com/post-factum/pf-kernel/-/tags/v5.10-pf2).

## Resources

- RFC: vmscan: add min_filelist_kbytes sysctl for protecting the working set (2010)
    - https://lore.kernel.org/patchwork/patch/222042/
    - https://lkml.org/lkml/2010/10/28/289/
- CHROMIUM: vmscan: add min_filelist_kbytes sysctl for protecting the working set (2020) https://chromium.googlesource.com/chromiumos/third_party/kernel-next/+/545e2917dbd863760a51379de8c26631e667c563%5E%21/
- le9 main thread https://web.archive.org/web/20191018023405/https://gist.github.com/constantoverride/84eba764f487049ed642eb2111a20830
- When DMA is disabled system freeze on high memory usage (since 2007) https://bugs.launchpad.net/ubuntu/+source/linux/+bug/159356
- Let's talk about the elephant in the room - the Linux kernel's inability to gracefully handle low memory pressure
    - https://lore.kernel.org/lkml/d9802b6a-949b-b327-c4a6-3dbca485ec20@gmx.com/
    - https://www.phoronix.com/forums/forum/phoronix/general-discussion/1118164-yes-linux-does-bad-in-low-ram-memory-pressure-situations-on-the-desktop
    - https://news.ycombinator.com/item?id=20620545
    - https://www.reddit.com/r/linux/comments/cmg48b/lets_talk_about_the_elephant_in_the_room_the/
- How to keep executable code in memory even under memory pressure ? in Linux https://stackoverflow.com/questions/52067753/how-to-keep-executable-code-in-memory-even-under-memory-pressure-in-linux
- Howto prevent kernel from evicting code pages ever? (to avoid disk thrashing when about to run out of RAM)
    - https://stackoverflow.com/questions/51927528/how-to-prevent-linux-kernel-from-evicting-file-backed-executable-pages-when-abou
    - https://lkml.org/lkml/2018/8/22/176
    - https://lkml.org/lkml/2018/9/10/296
- OOM killer doesn't work properly, leads to a frozen OS https://unix.stackexchange.com/questions/373312/oom-killer-doesnt-work-properly-leads-to-a-frozen-os/458855#458855
- `le9b.patch` https://launchpadlibrarian.net/386196358/le9b.patch
- `le9d.patch` https://launchpadlibrarian.net/389887258/le9d.patch, https://lkml.org/lkml/diff/2018/9/10/296/1
- `le9e.patch` https://web.archive.org/web/20191018064208/https://github.com/howaboutsynergy/q1q/blob/865a6500231aec266bc9d646dfd230908b8676e5/OSes/archlinux/home/user/build/1packages/4used/kernel/linuxgit/le9e.patch
- `le9g.patch`
    - https://bugzilla.kernel.org/show_bug.cgi?id=196729#c49
    - https://www.phoronix.com/forums/forum/phoronix/general-discussion/1118164-yes-linux-does-bad-in-low-ram-memory-pressure-situations-on-the-desktop?p=1119440#post1119440
- `le9h.patch`
    - https://web.archive.org/web/20191018023217/https://gist.github.com/howaboutsynergy/04fd9be927835b055ac15b8b64658e85
    - https://www.phoronix.com/forums/forum/phoronix/general-discussion/1118164-yes-linux-does-bad-in-low-ram-memory-pressure-situations-on-the-desktop?p=1119792#post1119792
- `le9i.patch` https://web.archive.org/web/20191018023434/https://gist.github.com/howaboutsynergy/cbfa3cc5e8093c26c29f5d411c16e6b1
- Bug 111601 - regression: deadlock-freeze due to kernel commit aa56a292ce623734ddd30f52d73f527d1f3529b5 + `memfreeze`, `le9i.patch`, `le9h.patch` https://bugs.freedesktop.org/show_bug.cgi?id=111601
- Merge origin/chromeos-4.19-lowmem https://gitlab.freedesktop.org/seanpaul/dpu-staging/commit/0b992f2dbb044896c3584e10bd5b97cf41e2ec6d
- https://abf.io/mikhailnov/kernel-desktop-4.15/blob/master/Chromium-OS-low-memory-patchset.patch
- Discussing protection of file pages with post-factum and mikhailnov - https://www.linux.org.ru/news/kernel/16052362?cid=16055197 and further.
- LKML: Marcus Linsner: Howto prevent kernel from evicting code pages ever? (to avoid disk thrashing when about to run out of RAM) https://lkml.org/lkml/2018/8/22/176
- How to prevent Linux kernel from evicting file-backed executable pages when about to run out of RAM? (which would otherwise cause disk-thrashing) https://stackoverflow.com/questions/51927528/how-to-prevent-linux-kernel-from-evicting-file-backed-executable-pages-when-abou
- mm/concepts https://www.kernel.org/doc/html/latest/admin-guide/mm/concepts.html
- M. Gorman - Understanding the Linux Virtual Memory Manager https://www.kernel.org/doc/gorman/
- Unevictable LRU Infrastructure https://www.kernel.org/doc/html/latest/vm/unevictable-lru.html
- Why is kswapd0 running on a computer with no swap? https://askubuntu.com/questions/432809/why-is-kswapd0-running-on-a-computer-with-no-swap/432827
- This patch looks like it could be merged with mainline. Why don't you try sending it to linux-mm? https://www.phoronix.com/forums/forum/phoronix/general-discussion/1118164-yes-linux-does-bad-in-low-ram-memory-pressure-situations-on-the-desktop/page17#post1120024
- Ubuntu freeze when low memory https://askubuntu.com/questions/1017884/ubuntu-freeze-when-low-memory
- How to avoid high latency near OOM situation? https://unix.stackexchange.com/questions/423261/how-to-avoid-high-latency-near-oom-situation

