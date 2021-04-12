
# le9db patches

`le9db*-5.10` patches may be correctly applied to Linux 5.10—5.12-rc7.

The `vm.clean_low_kbytes` sysctl knob provides *best-effort* protection of clean file pages. The clean file pages on the current node won't be reclaimed under memory pressure when their amount is below `vm.clean_low_kbytes` *unless* we threaten to OOM or have no free swap space or vm.swappiness=0.

The `vm.clean_min_kbytes` sysctl knob provides *hard* protection of clean file pages. The clean file pages on the current node won't be reclaimed under memory pressure when their amount is below `vm.clean_min_kbytes`.

The `le9db0`, `le9db1`, `le9db2` patches differ only in the default values.

`le9db0` just provides two sysctl knobs with 0 values and does not protect clean file pages by default.

`le9db1` provides only soft protection by default (`vm.clean_low_kbytes=150000`, `vm.clean_min_kbytes=0`). This patch may be safly used by default.

`le9db2` provides hard protection of clean file pages by default (`vm.clean_low_kbytes=250000`, `vm.clean_min_kbytes=200000`).

