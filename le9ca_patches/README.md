
# le9cb patches

`le9ca*-5.10` patches may be correctly applied to Linux 5.10—5.12-rc4.

The `vm.clean_file_low_kbytes` sysctl knob provides *best-effort* protection of clean file pages. The clean file pages on the current node won't be reclaimed uder memory pressure when their volume is below `vm.clean_file_low_kbytes` *unless* we threaten to OOM or have no swap space or vm.swappiness=0.

The `vm.clean_file_min_kbytes` sysctl knob provides *hard* protection of clean file pages. The clean file pages on the current node won't be reclaimed under memory pressure when their volume is below `vm.clean_file_min_kbytes`.

`le9ca0` just provides two sysctl knobs with 0 values and does not protect clean file pages by default.

`le9ca1` provides only soft protection (`vm.clean_file_low_kbytes=150000`, `vm.clean_file_min_kbytes=0`). This patch may be safly used by default.

`le9ca2` provides hard protection of clean file pages (`vm.clean_file_low_kbytes=250000`, `vm.clean_file_min_kbytes=200000`).
