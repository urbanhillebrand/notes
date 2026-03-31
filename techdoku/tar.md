# tar

**Restore von inkrementellem tar in ein separates Verzeichnis**

for bkpfile in alfresco-*.tgz; do tar --listed-incremental=/dev/null -xpzf $bkpfile -C /opt/restore; done

**Verschachtelt**

```
tar xzf mox_laa-gw01_2021-11-30-165243.tar.gz -O mnt/mox/config.tar.gz | tar xzf - -O etc/firewall/firewall.specs
```

**Komplexeres Beispielscript für moxbak**

```
#!/bin/bash

SRCDIR="/var/rsyncd/moxbak"

# Loop durch alle Sub-/Subdirectories
find $SRCDIR -maxdepth 2 -mindepth 2 -type d -print0 | while read -d $'\0' mydir
do
        #echo "dir: $mydir"
        newest=''
        newest=$(ls -rt $mydir/mox*.tar.gz 2>/dev/null | head -n1)
        if [ ! -z $newest ]; then
                #echo "gefunden: $newest"
                myspecs="mnt/config.tar.gz"
                if ! $(tar tzvf $newest | grep -q "$myspecs"); then
                        # mox8
                        myspecs="mnt/mox/config.tar.gz"
                fi
                if $(tar xzf $newest -O $myspecs | tar xzf - -O etc/firewall/firewall.specs | egrep -qi 'tms|timm|schneider'); then
                        echo "match in $mydir/$newest"
                fi
        fi
done
```
