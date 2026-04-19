# Linux Performance Troubleshooting

## waiting I/O

**Ein wenig "I/O wait" ist ganz normal; Anhaltspunkte ([Quelle](https://www.atatus.com/blog/iowait-and-linux-performance/)):**

* 0-4% keine nennenswerte Last
* 5-10% normal
* 11-20% spürbar, träges Verhalten
* >21% gravierende Performanceeinbußen

`sysstat` installieren!

`waiting`-I/O deutet auf Prozesse hin, bei denen die CPU auf Disk- oder Netzwerk-I/O warten muss.

-> mit `top` schon sichtbar, auch ggf. pro CPU.

**Anzeige pro Harddisk**

```
iostat -x 2
iostat -p /dev/sdb -hm 2 # in MB/sec, nur explizit ang. device
```

**Mögliche Alternative ohne Zusatztools:**

```
while true; do ps -eo state,pid,cmd | grep "^D"; echo "----"; sleep 2; done
```

**📌 NOTE**\
`ps` zeigt als State `D`, wenn ein Prozess auf I/O wartet.

* **Anzeige I/O pro Prozess**\
`iotop`, oder `iotop -P -o -t -a -d 10 -b` (alle 10s kumuliert)
* **Anzeige offene Files für Prozess**\
`lsof -p <pid>`

## disk speed

### sequential read/write

write speed: `sync; dd if=/dev/zero of=tempfile bs=1M count=1024; sync` (mehrmals laufen lassen, mitteln)

**read speed**

```
dd if=tempfile of=/dev/null bs=1M count=1024

# Cache löschen:
sudo sysctl -w vm.drop_caches=3

# wiederholen
dd if=tempfile of=/dev/null bs=1M count=1024
```

**hdparm**

```
hdparm -Tt /dev/sdb

/dev/sdb:
 Timing cached reads:   18514 MB in  1.99 seconds = 9285.91 MB/sec
 Timing buffered disk reads: 1092 MB in  3.00 seconds = 363.78 MB/sec
```

### random read/write

**fio**

```
fio --randrepeat=1 --ioengine=libaio --direct=1 --gtod_reduce=1 --name=test --filename=random_read_write.fio --bs=4k --iodepth=64 --size=250M --readwrite=randrw --rwmixread=80
```

### IOPS messen

**🔥 CAUTION**\
LÖSCHT DEVICE!

```
Schreibtest:

fio --rw=randwrite --name=IOPS-write --bs=4k --direct=1 --filename=/dev/TESTDEVICE --numjobs=4 --ioengine=libaio --iodepth=32 --refill_buffers --group_reporting --runtime=60 --time_based

Lesetest:

fio --rw=randread --name=IOPS-read --bs=4k --direct=1 --filename=/dev/TESTDEVICE --numjobs=4 --ioengine=libaio --iodepth=32 --refill_buffers --group_reporting --runtime=60 --time_based
```
