
# le9eb patch

The kernel does not provide a way to protect the [working set](https://en.wikipedia.org/wiki/Working_set) under memory pressure. A certain amount of anonymous and clean file pages is required by the userspace for normal operation. First of all, the userspace needs a cache of shared libraries and executable binaries. If the amount of the clean file pages falls below a certain level, then [thrashing](https://en.wikipedia.org/wiki/Thrashing_(computer_science)) and even [livelock](https://en.wikipedia.org/wiki/Deadlock#Livelock) can take place.

The patch provides sysctl knobs for protecting the working set (anonymous and clean file pages) under memory pressure.

The `vm.anon_min_kbytes` sysctl knob provides *hard* protection of anonymous pages. The anonymous pages on the on the current node won't be reclaimed under any conditions when their amount is below `vm.anon_min_kbytes`. This knob may be used to prevent excessive swap thrashing when anonymous memory is low (for example, when memory is going to be overfilled by compressed data of zram module).

The `vm.clean_low_kbytes` sysctl knob provides *best-effort* protection of clean file pages. The clean file pages on the current node won't be reclaimed under memory pressure when their amount is below `vm.clean_low_kbytes` *unless* we threaten to OOM or have no free swap space or vm.swappiness=0. Protection of clean file pages using this knob may be used when swapping is still possible to
- prevent disk I/O thrashing under memory pressure;
- improve performance in disk cache-bound tasks under memory pressure.

The `vm.clean_min_kbytes` sysctl knob provides *hard* protection of clean file pages. The clean file pages on the current node won't be reclaimed under memory pressure when their amount is below `vm.clean_min_kbytes`. Hard protection of clean file pages using this knob may be used to
- prevent disk I/O thrashing under memory pressure even with no swap space;
- improve performance in disk cache-bound tasks under memory pressure;
- avoid high latency and prevent livelock in near-OOM conditions.

`le9eb` patches provide three sysctl knobs with 0 values and does not protect the working set by default (`CONFIG_ANON_MIN_KBYTES=0`, `CONFIG_CLEAN_LOW_KBYTES=0`, `CONFIG_CLEAN_MIN_KBYTES=0`).

- `le9eb-4.14.patch` may be correctly applied to vanilla Linux 4.14;
- `le9eb-4.19.patch` may be correctly applied to vanilla Linux 4.19;
- `le9eb-5.4.patch` may be correctly applied to vanilla Linux 5.4;
- `le9eb-5.10.patch` may be correctly applied to vanilla Linux 5.10—5.13;
- `le9eb-5.13-rc2-MGLRU.patch` may be correctly applied to Linux 5.13 with [mgLRU patchset](https://lore.kernel.org/lkml/20210520065355.2736558-1-yuzhao@google.com/) applied (note that enabling mgLRU disables le9 effects);
- `le9eb-5.14-rc1.patch` may be correctly applied to vanilla Linux 5.14-rc1.

