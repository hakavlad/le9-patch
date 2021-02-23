# mem2log

Memory metrics (from `/proc/meminfo`) monitor and logger.

## Usage

Just run the script.
```
$ mem2log -h
usage: mem2log [-h] [-i INTERVAL] [-l LOG] [-m MODE]

optional arguments:
  -h, --help            show this help message and exit
  -i INTERVAL, --interval INTERVAL
                        interval in sec
  -l LOG, --log LOG     path to log file
  -m MODE, --mode MODE  mode (1 or 2)
```

Output examples:
```
$ ./mem2log
Starting mem2log with interval 2s, mode: 1
Process memory locked with MCL_CURRENT | MCL_FUTURE | MCL_ONFAULT
All values are in mebibytes
MemTotal: 9788.1, SwapTotal: 97880.7
--
MA is MemAvailable, A is Anon, F is File
AF is Active(file), IF is Inactive(file), SF is SwapFree
--
MA=5081 (51.9%), A=3638, F=444 (AF=281, IF=162), SF=97880 (100.0%)
MA=5080 (51.9%), A=3638, F=444 (AF=281, IF=162), SF=97880 (100.0%)
MA=5080 (51.9%), A=3638, F=444 (AF=281, IF=162), SF=97880 (100.0%)
MA=5080 (51.9%), A=3639, F=444 (AF=281, IF=162), SF=97880 (100.0%)
MA=5080 (51.9%), A=3639, F=444 (AF=281, IF=162), SF=97880 (100.0%)
MA=5080 (51.9%), A=3639, F=444 (AF=281, IF=162), SF=97880 (100.0%)
^C--
Got the SIGINT signal
Peak values:
  MA: min 5079.6, max 5081.3
  A:  min 3637.8, max 3639.0
  F:  min 443.7, max 443.8
  AF: min 281.4, max 281.4
  IF: min 162.3, max 162.4
  SF: min 97880.4, max 97880.4
Exit.
```

```
$ ./mem2log -m2
Starting mem2log with interval 2s, mode: 2
Process memory locked with MCL_CURRENT | MCL_FUTURE | MCL_ONFAULT
All values are in mebibytes
MemTotal: 9788.1, SwapTotal: 97880.7
--
MA is MemAvailable, BU is Buffers, CA is Cached
AA is Active(anon), IA is Inactive(anon), AF is Active(file), IF is Inactive(file)
SF is SwapFree, SU is `SwapTotal - SwapFree`, SH is Shmem, SR is SReclaimable
--
MA 4639.3, BU 60.3, CA 1102.3, AA 253.3, IA 3909.3, AF 292.0, IF 205.1, SF 97880.2, SU 0.5, SH 660.1, SR 46.1
MA 4637.7, BU 60.3, CA 1103.6, AA 253.3, IA 3909.2, AF 291.8, IF 205.1, SF 97880.2, SU 0.5, SH 661.4, SR 46.1
MA 4638.1, BU 60.3, CA 1102.4, AA 253.3, IA 3909.2, AF 291.8, IF 205.1, SF 97880.2, SU 0.5, SH 660.2, SR 46.1
MA 4626.7, BU 60.8, CA 1107.7, AA 253.3, IA 3919.6, AF 294.1, IF 208.1, SF 97880.2, SU 0.5, SH 660.6, SR 46.1
MA 4608.4, BU 62.3, CA 1123.3, AA 253.3, IA 3936.6, AF 302.5, IF 213.2, SF 97880.2, SU 0.5, SH 664.1, SR 46.2
MA 4597.9, BU 62.6, CA 1125.8, AA 253.4, IA 3951.8, AF 305.8, IF 220.7, SF 97880.2, SU 0.5, SH 656.2, SR 46.6
MA 4595.3, BU 63.9, CA 1131.9, AA 253.4, IA 3954.9, AF 307.9, IF 225.7, SF 97880.2, SU 0.5, SH 656.2, SR 46.7
^C--
Got the SIGINT signal
Peak values:
  MA: min 4595.3, max 4639.3
  BU: min 60.3, max 63.9
  CA: min 1102.3, max 1131.9
  AA: min 253.3, max 253.4
  IA: min 3909.2, max 3954.9
  AF: min 291.8, max 307.9
  IF: min 205.1, max 225.7
  SF: min 97880.2, max 97880.2
  SU: min 0.5, max 0.5
  SH: min 656.2, max 664.1
  SR: min 46.1, max 46.7
Exit.
```

Log file example (started with cmd `mem2log -l /tmp/mem.log`):
```
2021-02-23 16:40:34,534: Starting mem2log with interval 2s, mode: 1
2021-02-23 16:40:34,535: Log file: /tmp/mem.log
2021-02-23 16:40:34,538: Process memory locked with MCL_CURRENT | MCL_FUTURE | MCL_ONFAULT
2021-02-23 16:40:34,538: All values are in mebibytes
2021-02-23 16:40:34,538: MemTotal: 9788.1, SwapTotal: 97880.7
2021-02-23 16:40:34,538: --
2021-02-23 16:40:34,538: MA is MemAvailable, A is Anon, F is File
2021-02-23 16:40:34,538: AF is Active(file), IF is Inactive(file), SF is SwapFree
2021-02-23 16:40:34,538: --
2021-02-23 16:40:34,539: MA=4622 (47.2%), A=4185, F=572 (AF=371, IF=201), SF=97880 (100.0%)
2021-02-23 16:40:36,542: MA=4621 (47.2%), A=4185, F=572 (AF=371, IF=201), SF=97880 (100.0%)
2021-02-23 16:40:38,545: MA=4622 (47.2%), A=4185, F=572 (AF=371, IF=201), SF=97880 (100.0%)
2021-02-23 16:40:40,547: MA=4621 (47.2%), A=4185, F=572 (AF=371, IF=201), SF=97880 (100.0%)
2021-02-23 16:40:42,550: MA=4512 (46.1%), A=4259, F=576 (AF=377, IF=199), SF=97880 (100.0%)
2021-02-23 16:40:44,553: MA=4604 (47.0%), A=4187, F=580 (AF=403, IF=177), SF=97880 (100.0%)
2021-02-23 16:40:46,555: MA=4617 (47.2%), A=4187, F=580 (AF=403, IF=177), SF=97880 (100.0%)
2021-02-23 16:40:48,558: MA=4614 (47.1%), A=4187, F=580 (AF=403, IF=177), SF=97880 (100.0%)
2021-02-23 16:40:49,165: --
2021-02-23 16:40:49,165: Got the SIGINT signal
2021-02-23 16:40:49,165: Peak values:
2021-02-23 16:40:49,165:   MA: min 4511.7, max 4621.9
2021-02-23 16:40:49,166:   A:  min 4184.6, max 4259.2
2021-02-23 16:40:49,166:   F:  min 571.9, max 580.1
2021-02-23 16:40:49,166:   AF: min 370.9, max 402.8
2021-02-23 16:40:49,166:   IF: min 177.4, max 201.1
2021-02-23 16:40:49,166:   SF: min 97880.2, max 97880.2
2021-02-23 16:40:49,167: Exit.
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
