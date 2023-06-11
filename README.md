
![pic](https://i.imgur.com/trdDj7q.png)

# Add sysctl knobs for protecting the working set

Protection of clean file pages (page cache) may be used to prevent thrashing, reducing I/O under memory pressure, avoid high latency and prevent livelock in near-OOM conditions. The current le9 patches are based on patches that were originally created by Mandeep Singh Baines (2010) and Marcus Linsner (2018-2019). Let's give the floor to the original founders:

> On ChromiumOS, we do not use swap. When memory is low, the only way to free memory is to reclaim pages from the file list. This results in a lot of thrashing under low memory conditions. We see the system become unresponsive for minutes before it eventually OOMs. We also see very slow browser tab switching under low memory. Instead of an unresponsive system, we'd really like the kernel to OOM as soon as it starts to thrash. If it can't keep the working set in memory, then OOM. Losing one of many tabs is a better behaviour for the user than an unresponsive system.

> This patch create a new sysctl, min_filelist_kbytes, which disables reclaim of file-backed pages when when there are less than min_filelist_bytes worth of such pages in the cache. This tunable is handy for low memory systems using solid-state storage where interactive response is more important than not OOMing.

> With this patch and min_filelist_kbytes set to 50000, I see very little block layer activity during low memory. The system stays responsive under low memory and browser tab switching is fast. Eventually, a process a gets killed by OOM. Without this patch, the system gets wedged for minutes before it eventually OOMs.

— https://lore.kernel.org/lkml/20101028191523.GA14972@google.com/

> The attached kernel patch (applied on top of 4.18.5) that I've tried, almost completely eliminates the disk thrashing (the constant reading of executable (and .so) files on every context switch) associated with freezing the OS and so, with this patch, the OOM-killer is triggered within a maximum of 1 second when it is needed, rather than, without this patch, freeze the OS for minutes (or just a long time, it may even auto reboot depending on your kernel .config options set to panic (reboot) on hang after xx seconds) with constant disk reading well before OOM-killer gets triggered.

— https://bugs.launchpad.net/ubuntu/+source/linux/+bug/159356/comments/89

Original le9 patches (by Marcus Linsner) protected active file pages. Current versions (le9ec) allow to protect the specified amount of clean file pages and anonymous pages.

# le9ec patch

The kernel does not provide a way to protect the [working set](https://en.wikipedia.org/wiki/Working_set) under memory pressure. A certain amount of anonymous and clean file pages is required by the userspace for normal operation. First of all, the userspace needs a cache of shared libraries and executable binaries. If the amount of the clean file pages falls below a certain level, then [thrashing](https://en.wikipedia.org/wiki/Thrashing_(computer_science)) and even [livelock](https://en.wikipedia.org/wiki/Deadlock#Livelock) can take place.

The patch provides sysctl knobs for protecting the working set (anonymous and clean file pages) under memory pressure.

The `vm.anon_min_kbytes` sysctl knob provides *hard* protection of anonymous pages. The anonymous pages on the current node won't be reclaimed under any conditions when their amount is below `vm.anon_min_kbytes`. This knob may be used to prevent excessive swap thrashing when anonymous memory is low (for example, when memory is going to be overfilled by compressed data of zram module).

The `vm.clean_low_kbytes` sysctl knob provides *best-effort* protection of clean file pages. The file pages on the current node won't be reclaimed under memory pressure when the amount of clean file pages is below `vm.clean_low_kbytes` *unless* we threaten to OOM. Protection of clean file pages using this knob may be used when swapping is still possible to
- prevent disk I/O thrashing under memory pressure;
- improve performance in disk cache-bound tasks under memory pressure.

The `vm.clean_min_kbytes` sysctl knob provides *hard* protection of clean file pages. The file pages on the current node won't be reclaimed under memory pressure when the amount of clean file pages is below `vm.clean_min_kbytes`. Hard protection of clean file pages using this knob may be used to
- prevent disk I/O thrashing under memory pressure even with no free swap space;
- improve performance in disk cache-bound tasks under memory pressure;
- avoid high latency and prevent livelock in near-OOM conditions.

`le9ec` patches provide three sysctl knobs (`vm.anon_min_kbytes`, `vm.clean_low_kbytes`, `vm.clean_min_kbytes`) with zero values and does not protect the working set by default (`CONFIG_ANON_MIN_KBYTES=0`, `CONFIG_CLEAN_LOW_KBYTES=0`, `CONFIG_CLEAN_MIN_KBYTES=0`). You can specify other values during kernel build, or change the knob values on the fly.

- `le9ec-4.9.patch` may be correctly applied to vanilla Linux 4.9;
- `le9ec-4.14.patch` may be correctly applied to vanilla Linux 4.14;
- `le9ec-4.19.patch` may be correctly applied to vanilla Linux 4.19;
- `le9ec-5.4.patch` may be correctly applied to vanilla Linux 5.4;
- `le9ec-5.10.patch` may be correctly applied to vanilla Linux 5.10—5.13;
- `le9ec-5.13-rc2-MGLRU.patch` may be correctly applied to Linux 5.13 with [mgLRU patchset v3](https://lore.kernel.org/lkml/20210520065355.2736558-1-yuzhao@google.com/) applied;
- `le9ec-5.14-rc6-MGLRU.patch` may be correctly applied to Linux 5.14 with [mgLRU patchset v4](https://lore.kernel.org/lkml/20210818063107.2696454-1-yuzhao@google.com/) applied;
- `le9ec-5.14.patch` may be correctly applied to vanilla Linux 5.14;
- `le9ec-5.15.patch` may be correctly applied to vanilla Linux 5.15—5.19;
- `le9ec-5.15-MGLRU.patch` may be correctly applied to Linux 5.15 with [mgLRU patchset v5](https://lore.kernel.org/lkml/20211111041510.402534-1-yuzhao@google.com/) applied and may be correctly applied to Linux 5.16-rc8 with [mgLRU patchset v6](https://lore.kernel.org/lkml/20220104202227.2903605-1-yuzhao@google.com/) applied.

## Effects

- Improving system responsiveness under low-memory conditions;
- Improving performance in I/O bound tasks under memory pressure;
- OOM killer comes faster (with hard protection);
- Fast system reclaiming after OOM (with hard protection).

Note that the effects depend on the values of the sysctl tunables.

## Testing

These tools may be used to monitor memory and PSI metrics during stress tests:
- [mem2log](https://github.com/hakavlad/mem2log) may be used to log memory metrics from `/proc/meminfo`;
- [psi2log](https://github.com/hakavlad/nohang/blob/master/docs/psi2log.manpage.md) from [nohang](https://github.com/hakavlad/nohang) package may be used to log [PSI](https://facebookmicrosites.github.io/psi/docs/overview) metrics during stress tests.

Please report your results [here](https://github.com/hakavlad/le9-patch/issues/4).

## Demo

- https://youtu.be/iU3ikgNgp3M - The Linux (with le9 patch) kernel's ability to gracefully handle memory pressure. Boot with `mem=4G`, no swap space, opening chromium tabs, no hangs. The killer comes without delay. This is how le9 patch fixes the problem described [here](https://lore.kernel.org/lkml/d9802b6a-949b-b327-c4a6-3dbca485ec20@gmx.com/).
- https://youtu.be/c5bAOJkX_uc - Linux 5.9 + `le9i-5.9.patch`, playing supertuxkart with 7 threads `while true; do tail /dev/zero; done` in background. `vm.unevictable_activefile_kbytes=1000000`, `vm.unevictable_inactivefile_kbytes=0`.
- https://youtu.be/d4Sc80TMEtA - webkit2gtk3 compilation with zram-fraction=1, max-zram-size=8192. No hangs, no heavily freezes, system was responsive for all time during webkit2gtk3 compilation.
- https://youtu.be/ZrLqUWRodh4 - Debian 11 on VM, Linux 5.14 with `le9ec` patch, no swap space, playing `SuperTux` while 1000 `tail /dev/zero` started simultaneously:
    - No freezes with `vm.clean_min_kbytes=300000`, I/O pressure was closed to zero, memory pressure was moderate (70-80 `some`, 12-17 `full`), all `tail` processes has been killed in 2 minutes (0:06 - 2:14), it's about 8 processes reaped by `oom_reaper` per second;
    - Complete UI freeze without the working set protection (since 3:40).
- https://youtu.be/tsnA6Mx-MpQ - 5.14.3.le9fd, `for i in {1..100}; do (tail /dev/zero &); done`, swap on zram, `MemTotal` = `SwapTotal` = 9.6 GiB.
- https://youtu.be/1ZwzjxCHFyc - 5.14.2.le9fa, playing SuperTuxKart, 8 terminal emulator windows with `while true; do tail /dev/zero; done`, no swap space, `vm.clean_min_kbytes=260000`, low memory and I/O pressure, no UI freeze. The userspace daemon ([nohang](https://github.com/hakavlad/nohang)) running in the background just checks kmsg for OOM events and sends GUI notifications.

## Warning

- These patches were written by an amateur. Use at your own risk.

## Review at LKML

- v1 (le9ec): https://lore.kernel.org/lkml/20211130201652.2218636d@mail.inbox.lv/

## What about non-x86?

No data. Testing is encouraged. Please report your results [here](https://github.com/hakavlad/le9-patch/issues/4).

## le9 and Multigenerational LRU Framework

- le9 modifies `get_scan_count()` to protect the working set.
- Multigenerational LRU doesn't use `get_scan_count()`.
- Enabling [multi-generational LRU](https://lwn.net/Articles/856931/) disables le9 effects.
- `vm.anon_min_kbytes`, `vm.clean_low_kbytes` and `vm.clean_min_kbytes` have no effect when mg-LRU is enabled.
- [mg-lru-helper](https://github.com/hakavlad/mg-lru-helper) can be used to easily manage mg-LRU (enable, disable, get status).

## User feedback

See [USER_FEEDBACK.md](USER_FEEDBACK.md).

## Resources

See [RESOURCES.md](RESOURCES.md).

