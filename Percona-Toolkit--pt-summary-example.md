## Percona Toolkit - pt-summary example
Whenever we create a ticket with Percona, this information can be helpful in attaching to that ticket for investigation.

This report provides server info, like uptime, is it on-prem or on-cloud, o/s version, etc details, CPU architecture, processor details, memory details, mounted filesystems, disk info, LVMs, network interfaces, Transparent Huge Pages, 

If DB server is under heavy load, running pt-summary and checking the report can help in identifying the bottleneck.

```sh
root@ip:~# pt-summary
# Percona Toolkit System Summary Report ######################
        Date | 2025-02-16 17:41:57 UTC (local TZ: UTC +0000)
    Hostname | ip-<your-ip-here>
      Uptime |  2:11,  1 user,  load average: 0.00, 0.00, 0.00
      System | Xen; HVM domU; v4.11.amazon (Other)
 Service Tag | ec2f0039-7f94-2e05-a89d-26c87e0c4e48
    Platform | Linux
     Release | Ubuntu 24.04.1 LTS (noble)
      Kernel | 6.8.0-1021-aws
Architecture | CPU = 64-bit, OS = 64-bit
   Threading | NPTL 2.39
     SELinux | No SELinux detected
 Virtualized | Xen
# Processor ##################################################
  Processors | physical = 1, cores = 1, virtual = 1, hyperthreading = no
      Speeds | 1x2299.998
      Models | 1xIntel(R) Xeon(R) CPU E5-2686 v4 @ 2.30GHz
      Caches | 1x46080 KB
# Memory #####################################################
       Total | 957.4M
        Free | 76.1M
        Used | physical = 694.3M, swap allocated = 0.0, swap used = 0.0, virtual = 694.3M
      Shared | 900.0k
     Buffers | 342.5M
      Caches | 263.1M
       Dirty | 80 kB
     UsedRSS | 641.6M
  Swappiness | 60
 DirtyPolicy | 20, 10
 DirtyStatus | 0, 0
  Locator   Size     Speed             Form Factor   Type          Type Detail
  ========= ======== ================= ============= ============= ===========
  DIMM 0    1 GB     Unknown           DIMM          RAM           None
# Mounted Filesystems ########################################
  Filesystem   Size Used Type  Opts                                                                                                 Mountpoint
  /dev/xvda15  105M   6% vfat  rw,relatime,fmask=0077,dmask=0077,codepage=437,iocharset=iso8859-1,shortname=mixed,errors=remount-ro /boot/efi
  /dev/xvda16  881M  10% ext4  rw,relatime                                                                                          /boot
  tmpfs         96M   1% tmpfs rw,nosuid,nodev,inode64                                                                              /run/user/1000
  tmpfs         96M   1% tmpfs rw,nosuid,nodev,size=196080k,nr_inodes=819200,mode=755,inode64                                       /run/user/1000
  tmpfs         96M   1% tmpfs rw,nosuid,nodev,noexec,relatime,size=5120k,inode64                                                   /run/user/1000
  tmpfs         96M   1% tmpfs rw,nosuid,nodev,relatime,size=98036k,nr_inodes=24509,mode=700,uid=1000,gid=1000,inode64              /run/user/1000
  tmpfs        192M   1% tmpfs rw,nosuid,nodev,inode64                                                                              /run
  tmpfs        192M   1% tmpfs rw,nosuid,nodev,size=196080k,nr_inodes=819200,mode=755,inode64                                       /run
  tmpfs        192M   1% tmpfs rw,nosuid,nodev,noexec,relatime,size=5120k,inode64                                                   /run
  tmpfs        192M   1% tmpfs rw,nosuid,nodev,relatime,size=98036k,nr_inodes=24509,mode=700,uid=1000,gid=1000,inode64              /run
  tmpfs        479M   0% tmpfs rw,nosuid,nodev,inode64                                                                              /dev/shm
  tmpfs        479M   0% tmpfs rw,nosuid,nodev,size=196080k,nr_inodes=819200,mode=755,inode64                                       /dev/shm
  tmpfs        479M   0% tmpfs rw,nosuid,nodev,noexec,relatime,size=5120k,inode64                                                   /dev/shm
  tmpfs        479M   0% tmpfs rw,nosuid,nodev,relatime,size=98036k,nr_inodes=24509,mode=700,uid=1000,gid=1000,inode64              /dev/shm
  tmpfs        5.0M   0% tmpfs rw,nosuid,nodev,inode64                                                                              /run/lock
  tmpfs        5.0M   0% tmpfs rw,nosuid,nodev,size=196080k,nr_inodes=819200,mode=755,inode64                                       /run/lock
  tmpfs        5.0M   0% tmpfs rw,nosuid,nodev,noexec,relatime,size=5120k,inode64                                                   /run/lock
  tmpfs        5.0M   0% tmpfs rw,nosuid,nodev,relatime,size=98036k,nr_inodes=24509,mode=700,uid=1000,gid=1000,inode64              /run/lock
# Disk Schedulers And Queue Size #############################
        xvda | [mq-deadline] 64
# Disk Partioning ############################################
Device       Type      Start        End               Size
============ ==== ========== ========== ==================
/dev/xvda    Disk                               8589934592
/dev/xvda1   Part    2099200   16777182                  0
/dev/xvda14  Part       2048      10239                  0
/dev/xvda15  Part      10240     227327                  0
/dev/xvda16  Part     227328    2097152                  0
# Kernel Inode State #########################################
dentry-state | 17971    12081   45      0       2677    0
     file-nr | 1216     0       9223372036854775807
    inode-nr | 17852    1594
# LVM Volumes ################################################
Unable to collect information
# LVM Volume Groups ##########################################
Unable to collect information
# RAID Controller ############################################
  Controller | No RAID controller detected
# Network Config #############################################
 FIN Timeout | 60
  Port Range | 60999
# Interface Statistics #######################################
  interface  rx_bytes rx_packets  rx_errors   tx_bytes tx_packets  tx_errors
  ========= ========= ========== ========== ========== ========== ==========
  lo            35000        300          0      35000        300          0
  enX0       17500000      15000          0     700000       6000          0
# Network Devices ############################################
  Device    Speed     Duplex
  ========= ========= =========
  enX0
# Top Processes ##############################################
    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
      1 root      20   0   22232  11356   7516 S   0.0   1.2   0:01.45 systemd
      2 root      20   0       0      0      0 S   0.0   0.0   0:00.00 kthreadd
      3 root      20   0       0      0      0 S   0.0   0.0   0:00.00 pool_wo+
      4 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker+
      5 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker+
      6 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker+
      7 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker+
     10 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker+
     12 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker+
# Notable Processes ##########################################
  PID    OOM    COMMAND
    ?      ?    sshd doesn't appear to be running
# Memory mamagement ##########################################
Transparent huge pages are enabled.
# The End ####################################################
```