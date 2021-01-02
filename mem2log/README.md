# mem2log

Python script that logs memory metrics from `/proc/meminfo`.

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
MemTotal: 9788.1M, SwapTotal: 48940.3
--
MA is MemAvailable, BU is Buffers, CA is Cached, 
AA is Active(anon), IA is Inactive(anon), AF is Active(file), IF is Inactive(file), 
SF is SwapFree, SU is `SwapTotal - SwapFree`, SH is Shmem, SR is SReclaimable
--
MA 7524.7, BU 62.5, CA 595.6, AA 216.5, IA 971.4, AF 256.6, IF 173.0, SF 48174.0, SU 766.3, SH 219.5, SR 58.7
MA 7467.6, BU 62.5, CA 596.8, AA 216.5, IA 1027.1, AF 256.5, IF 173.0, SF 48174.0, SU 766.3, SH 220.8, SR 58.8
MA 7469.7, BU 62.6, CA 595.6, AA 216.5, IA 1025.7, AF 256.5, IF 173.0, SF 48174.0, SU 766.3, SH 219.6, SR 58.8
^C--
Got the SIGINT signal; exit.
```

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
