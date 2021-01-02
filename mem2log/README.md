# mem2log

Memory metrics (from `/proc/meminfo`) monitor and logger.

## Usage

Just run the script.
```
$ mem2log -h
usage: mem2log [-h] [-i INTERVAL] [-l LOG]

optional arguments:
  -h, --help            show this help message and exit
  -i INTERVAL, --interval INTERVAL
                        interval in sec
  -l LOG, --log LOG     path to log file
```

Output like follow:
```
$ mem2log
Starting mem2log with interval 2s
Process memory locked with MCL_CURRENT | MCL_FUTURE | MCL_ONFAULT
All values are in mebibytes
MemTotal: 9788.1, SwapTotal: 48940.3
--
MA is MemAvailable, BU is Buffers, CA is Cached
AA is Active(anon), IA is Inactive(anon), AF is Active(file), IF is Inactive(file)
SF is SwapFree, SU is `SwapTotal - SwapFree`, SH is Shmem, SR is SReclaimable
--
MA 7435.4, BU 68.7, CA 622.0, AA 200.2, IA 1021.4, AF 293.8, IF 159.3, SF 48193.5, SU 746.8, SH 228.6, SR 61.0
MA 7434.1, BU 68.7, CA 622.9, AA 200.2, IA 1021.7, AF 293.6, IF 159.3, SF 48193.5, SU 746.8, SH 229.5, SR 61.0
MA 7436.4, BU 68.7, CA 622.0, AA 200.2, IA 1019.6, AF 293.7, IF 159.2, SF 48193.5, SU 746.8, SH 228.6, SR 61.0
MA 7435.9, BU 68.7, CA 622.0, AA 200.2, IA 1019.6, AF 293.7, IF 159.2, SF 48193.5, SU 746.8, SH 228.6, SR 60.9
^C--
Got the SIGINT signal; exit.
```

## Requirements

- Python >= 3.3

## Install
```
$ git clone https://github.com/hakavlad/le9-patch.git
$ cd le9-patch/mem2log
$ sudo make install
```

## Uninstall
```
$ sudo make uninstall
```
