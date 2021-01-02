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

Log file example:
```
2021-01-03 03:19:55,981: Starting mem2log with interval 1.0s
2021-01-03 03:19:55,982: Log file: 0007
2021-01-03 03:19:55,983: Process memory locked with MCL_CURRENT | MCL_FUTURE | MCL_ONFAULT
2021-01-03 03:19:55,983: All values are in mebibytes
2021-01-03 03:19:55,983: MemTotal: 9788.1, SwapTotal: 48940.3
2021-01-03 03:19:55,983: --
2021-01-03 03:19:55,983: MA is MemAvailable, BU is Buffers, CA is Cached
2021-01-03 03:19:55,983: AA is Active(anon), IA is Inactive(anon), AF is Active(file), IF is Inactive(file)
2021-01-03 03:19:55,983: SF is SwapFree, SU is `SwapTotal - SwapFree`, SH is Shmem, SR is SReclaimable
2021-01-03 03:19:55,983: --
2021-01-03 03:19:55,983: MA 7865.9, BU 39.4, CA 399.2, AA 235.1, IA 484.4, AF 103.3, IF 155.3, SF 47734.1, SU 1206.2, SH 173.8, SR 52.5
2021-01-03 03:19:56,985: MA 7864.6, BU 39.5, CA 400.9, AA 235.1, IA 484.5, AF 103.4, IF 155.7, SF 47734.1, SU 1206.2, SH 175.2, SR 52.5
2021-01-03 03:19:57,987: MA 7865.1, BU 39.5, CA 400.4, AA 235.1, IA 484.7, AF 103.4, IF 155.7, SF 47734.1, SU 1206.2, SH 174.7, SR 52.5
2021-01-03 03:19:58,990: MA 7865.6, BU 39.5, CA 399.5, AA 235.1, IA 484.5, AF 103.4, IF 155.7, SF 47734.1, SU 1206.2, SH 173.8, SR 52.5
2021-01-03 03:19:59,738: --
2021-01-03 03:19:59,739: Got the SIGINT signal
2021-01-03 03:19:59,739: Peak values:
2021-01-03 03:19:59,739:   MA: min 7864.6, max 7865.9
2021-01-03 03:19:59,739:   BU: min 39.4, max 39.5
2021-01-03 03:19:59,739:   CA: min 399.2, max 400.9
2021-01-03 03:19:59,739:   AA: min 235.1, max 235.1
2021-01-03 03:19:59,740:   IA: min 484.4, max 484.7
2021-01-03 03:19:59,740:   AF: min 103.3, max 103.4
2021-01-03 03:19:59,740:   IF: min 155.3, max 155.7
2021-01-03 03:19:59,740:   SF: min 47734.1, max 47734.1
2021-01-03 03:19:59,740:   SU: min 1206.2, max 1206.2
2021-01-03 03:19:59,740:   SH: min 173.8, max 175.2
2021-01-03 03:19:59,741:   SR: min 52.5, max 52.5
2021-01-03 03:19:59,741: Exit.
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
