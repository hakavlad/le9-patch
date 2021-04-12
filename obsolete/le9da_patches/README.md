
# le9da patches

`le9da*-5.10` patches may be correctly applied to Linux 5.10—5.12-rc6.

The `vm.clean_low_kbytes` sysctl knob provides *best-effort* protection of clean file pages. The clean file pages on the current node won't be reclaimed under memory pressure when their amount is below `vm.clean_low_kbytes` *unless* we threaten to OOM or have no free swap space or vm.swappiness=0.

The `vm.clean_min_kbytes` sysctl knob provides *hard* protection of clean file pages. The clean file pages on the current node won't be reclaimed under memory pressure when their amount is below `vm.clean_min_kbytes`.

The `le9da0`, `le9da1`, `le9da2` patches differ only in the default values.

`le9da0` just provides two sysctl knobs with 0 values and does not protect clean file pages by default.

`le9da1` provides only soft protection by default (`vm.clean_low_kbytes=150000`, `vm.clean_min_kbytes=0`). This patch may be safly used by default.

`le9da2` provides hard protection of clean file pages by default (`vm.clean_low_kbytes=250000`, `vm.clean_min_kbytes=200000`).

