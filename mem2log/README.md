# mem2log

Memory metrics (from `/proc/meminfo`) monitor and logger.

mem2log has two modes: 1 (default) and 2 (logs more metrics). 

Set the `--log LOG` option to log into the file. 

Set the `-i INTERVAL` option to specify the interval.


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
MemTotal: 9823.8, SwapTotal: 49119.2
--
MA is MemAvailable, MF is MemFree, A is Anon, F is File
AF is Active(file), IF is Inactive(file), SF is SwapFree
--
MA=7557 (77%), MF=159, A=1362, F=7384 (AF=5076, IF=2308), SF=48921 (100%)
MA=7558 (77%), MF=136, A=1362, F=7409 (AF=5027, IF=2382), SF=48921 (100%)
MA=7557 (77%), MF=139, A=1362, F=7407 (AF=4988, IF=2419), SF=48921 (100%)
MA=7555 (77%), MF=137, A=1362, F=7408 (AF=4959, IF=2449), SF=48921 (100%)
MA=7556 (77%), MF=141, A=1362, F=7407 (AF=4971, IF=2437), SF=48921 (100%)
^C--
Got the SIGINT signal
Peak values:
  MA: min 7555.4, max 7557.7
  MF: min 136.0, max 158.5
  A:  min 1361.7, max 1362.0
  F:  min 7384.4, max 7409.0
  AF: min 4958.9, max 5076.0
  IF: min 2308.4, max 2449.3
  SF: min 48920.8, max 48920.8
Exit.
```

```
$ ./mem2log -m2
Starting mem2log with interval 2s, mode: 2
Process memory locked with MCL_CURRENT | MCL_FUTURE | MCL_ONFAULT
All values are in mebibytes
MemTotal: 9823.8, SwapTotal: 49119.2
--
MA is MemAvailable, MF is MemFree, BU is Buffers, CA is Cached
AA is Active(anon), IA is Inactive(anon), AF is Active(file), IF is Inactive(file)
SF is SwapFree, SU is `SwapTotal - SwapFree`, SH is Shmem, SR is SReclaimable
--
MA 7555.3, MF 231.4, BU 90.4, CA 7543.9, AA 711.3, IA 652.1, AF 4773.8, IF 2547.3, SF 48920.8, SU 198.4, SH 192.6, SR 311.3
MA 7554.7, MF 132.4, BU 90.5, CA 7642.3, AA 711.3, IA 652.1, AF 4773.7, IF 2645.9, SF 48920.8, SU 198.4, SH 192.5, SR 311.2
MA 7555.5, MF 132.6, BU 90.5, CA 7643.5, AA 711.4, IA 652.1, AF 4742.1, IF 2678.6, SF 48920.8, SU 198.4, SH 192.5, SR 310.7
MA 7555.8, MF 144.5, BU 90.6, CA 7632.6, AA 711.4, IA 652.1, AF 4687.7, IF 2722.2, SF 48920.8, SU 198.4, SH 192.6, SR 310.0
MA 7554.7, MF 127.3, BU 90.7, CA 7649.1, AA 711.8, IA 652.1, AF 4649.7, IF 2776.7, SF 48920.8, SU 198.4, SH 192.6, SR 309.5
^C--
Got the SIGINT signal
Peak values:
  MA: min 7554.7, max 7555.8
  MF: min 127.3, max 231.4
  BU: min 90.4, max 90.7
  CA: min 7543.9, max 7649.1
  AA: min 711.3, max 711.8
  IA: min 652.1, max 652.1
  AF: min 4649.7, max 4773.8
  IF: min 2547.3, max 2776.7
  SF: min 48920.8, max 48920.8
  SU: min 198.4, max 198.4
  SH: min 192.5, max 192.6
  SR: min 309.5, max 311.3
Exit.
```

Log file example (started with cmd `mem2log -l /tmp/mem.log`):
```
2021-03-08 01:33:13,376: Starting mem2log with interval 2s, mode: 1
2021-03-08 01:33:13,377: Log file: /tmp/mem.log
2021-03-08 01:33:13,379: Process memory locked with MCL_CURRENT | MCL_FUTURE | MCL_ONFAULT
2021-03-08 01:33:13,379: All values are in mebibytes
2021-03-08 01:33:13,379: MemTotal: 9823.8, SwapTotal: 49119.2
2021-03-08 01:33:13,379: --
2021-03-08 01:33:13,379: MA is MemAvailable, MF is MemFree, A is Anon, F is File
2021-03-08 01:33:13,379: AF is Active(file), IF is Inactive(file), SF is SwapFree
2021-03-08 01:33:13,380: --
2021-03-08 01:33:13,380: MA=7542 (77%), MF=136, A=1375, F=7410 (AF=4388, IF=3022), SF=48921 (100%)
2021-03-08 01:33:15,381: MA=7544 (77%), MF=141, A=1375, F=7407 (AF=4334, IF=3074), SF=48921 (100%)
2021-03-08 01:33:17,384: MA=7544 (77%), MF=134, A=1375, F=7415 (AF=4302, IF=3113), SF=48921 (100%)
2021-03-08 01:33:19,385: MA=7543 (77%), MF=186, A=1375, F=7363 (AF=4300, IF=3063), SF=48921 (100%)
2021-03-08 01:33:21,387: MA=7542 (77%), MF=146, A=1376, F=7402 (AF=4296, IF=3106), SF=48921 (100%)
2021-03-08 01:33:21,877: --
2021-03-08 01:33:21,877: Got the SIGINT signal
2021-03-08 01:33:21,878: Peak values:
2021-03-08 01:33:21,878:   MA: min 7541.9, max 7544.0
2021-03-08 01:33:21,878:   MF: min 134.1, max 186.2
2021-03-08 01:33:21,878:   A:  min 1374.6, max 1375.5
2021-03-08 01:33:21,878:   F:  min 7362.9, max 7415.3
2021-03-08 01:33:21,878:   AF: min 4296.3, max 4388.0
2021-03-08 01:33:21,878:   IF: min 3022.3, max 3112.9
2021-03-08 01:33:21,878:   SF: min 48920.8, max 48920.8
2021-03-08 01:33:21,878: Exit.
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
