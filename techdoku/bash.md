# bash

**Emacs Keybindings - movement**

|     |     |
| --- | --- |
| Ctrl-B | move 1 char backwards |
| Alt-B | 1 word backwards |
| Ctrl-F | move 1 char forwards |
| Alt-F | 1 word forwards |
| Ctrl-A | move to line beginning |
| Ctrl-E | move to line end |

**Emacs Keybindings - deletion**

|     |     |
| --- | --- |
| Ctrl-W | delete word backwards |
| Alt-D | delete word forwards |
| Ctrl-k | delete to end of line |
| Ctrl-u | delete to beginning of line |

**Emacs Keybindings - other**

|     |     |
| --- | --- |
| Ctrl-_ | undo |
| Ctrl-y | yank (nach delete) |

**Tippfehler korrigieren**

```
cd /temp
^temp^tmp
```

**History**

```
cd !$       # letzter Parameter des vorangehenden Commands
echo !^     # erster Parameter des vorangehenden Commands
echo !:5    # fünfter Parameter des vorangehenden Commands
!ls         # letzten ls-Command wiederholen
!?test.txt  # letzten Command der test.txt beinhaltet wiederholen
```

**Pipelines**

```
cmd |& tee filename     # shorthand für cmd 2>&1 | tee filename
```

**Character ranges**

```
ls -ld [a-cx-z]*
ls -ld [!a-c]*
ls -ld [^a-c]* # gleich wie mit "!"
ls -ld [-a-c]  # will man "-" matchen, ganz am Anfang oder Ende des Ausdrucks stellen
```

**Character classes**

```
ls -ld [[:digit:]]*
```

**POSIX character classes**

* alnum
* alpha
* ascii
* blank
* cntrl
* digit
* graph
* lower
* print
* punct
* space
* upper
* word
* xdigit

## Snippets

**Diverses**

```
mv filename{,.bak}
mv filename.{old,new}

while IFS= read -r line; do
  printf "%s\n" "$line"
done < "$file"

ip a | sort -t . -k 3,3n -k 4,4n # Sort nach IP-Adresse
```

**Funktion statt Alias**

Aliase können nicht mit Parametern umgehen, deswegen z.B.

`foo() { /path/to/command "$@" ;}`


**tcp-Socket ohne Tools aufmachen**
```
exec 3<>/dev/tcp/google.com/80
echo -e "GET / HTTP/1.1\r\nhost: google.com\r\nConnection: close\r\n\r\n" >&3
cat <&3
exec 3>&-

# ungetestet
(timeout 1 bash -c 'cat < /dev/null > /dev/tcp/google.com/443') && echo "Port offen" || echo "Port geschlossen"
```

**Datei ohne Comments/Leerzeilen ausgeben**

```
grep -vE '^#|^$|^[[:space:]]+' filename
# oder wo was hier:
grep -Ev '^[[:space:]]*$|^[[:space:]]*#' 
```

**Files zeilenweise mergen**

```
paste user_names.txt user_ips.txt | awk '{printf "%s\t\t\t%s\n", $1, $2}' > users
```

**Case insensitive match**

```
orig_nocasematch=$(shopt -p nocasematch; true)
shopt -s nocasematch

# [...]

$orig_nocasematch
```

## Best practices

**Quoting**

```
cp $file $destination           # WRONG
cp -- "$file" "$destination"    # right!


for i in "$@"; do
    echo "$i"
done
```

**Options**

* `set -o pipefail` verwenden 
* `set -o nounset` verwenden (Abbruch bei leerer Variable)
* `set -o errexit` ggf. verwenden # fail on every non-zero return code
* `set -o xtrace` zum Debugging

**Deprecated style**

* `myfunc() { ... }` statt `function myfunc { ...}`
* `[[` statt `[` oder `test`
* `$( ... )` statt backticks

**Traps**

```
rollback() {
   del_from_passwd $user
   if [ -e /home/$user ]; then
      rm -rf /home/$user
   fi
   exit
}

trap rollback INT TERM EXIT
add_to_passwd $user
cp -a /etc/skel /home/$user
chown $user /home/$user -R
trap - INT TERM EXIT
```

## Snippets

**Redirects**

```
./cmd 2>&1 # sterr auf stdout uml.
./cmd &>/dev/null # stdout und sterr umleiten
```

**xargs multiple cmds**

```
find * -maxdepth 0 -type d -print0 | xargs -0 -I{} sh -c 'cd "{}"; sudo -u www-data php occ app:disable duplicatefinder;'
```

**split space separated strings**

```
line="eins zwei drei"
set -- $(echo $line)
echo "1: $1"
echo "2: $2"
echo "3: $3"
```

**Find external ip**

```
dig +short myip.opendns.com @resolver1.opendns.com
host myip.opendns.com resolver1.opendns.com
wget -qO- http://ipecho.net/plain | xargs echo
wget -qO - icanhazip.com
curl ifconfig.me
curl icanhazip.com
```

**Locking**

```
#!/usr/bin/bash

lockfile="/tmp/$(basename $0).lock"

exec 100>"$lockfile" || { echo "Fehler beim Herstellen des Locks ($lockfile) - Abbruch" 1>&2; exit 1; }
flock -n 100 || { echo "Fehler beim Locking" 1>&2; exit 1; }
trap "rm -f $lockfile" EXIT

# Start des Programms...
```

**Heredocs**

```
    [...]
    cat <<EOF
blah
EOF

# oder mit <<- um die Einrückung zu fixen:
    [...]
    cat <<- EOF
    blah
    blah
    EOF

# oder mit Delimiter in Quotes -> keine variable expansion:
    cat <<- "EOF"
    echo $var
    EOF
```

**Rumpfscript Beispiel mit `getopts`**

```
#!/usr/bin/env bash

VERSION="0.0.99-dev"

appname=$(basename $0)
debug="false"


##### SUBS

log() { echo "$@"; systemd-cat -t $appname -p info echo "$@"; }

log_err() { echo "$@" 1>&2; systemd-cat -t "$appname" -p warning echo "$@"; }

log_deb() { [[ "$debug" != "true" ]] && return; echo "$@"; systemd-cat -t $appname -p debug echo "$@";}

show_usage() {
    cat <<EOT

$appname $VERSION

[...]

SYNTAX: $appname [OPTIONS]

OPTIONS

EOT
} # show_usage


##### MAIN

while getopts ":DhVt:" opt; do
    case "$opt" in
        t) test_param="${OPTARG}" ;;
        D) debug="true"; log_deb "Debugging aktiviert" ;;
        h) show_usage; exit 0 ;;
        V) log "$appname $VERSION"; exit 0 ;;
        :) log_err "Option -${OPTARG} benötigt ein Argument - Abbruch."; exit 1 ;;
        ?) log_err "Ungültige Option -${OPTARG}"; exit 1 ;;
    esac
done
shift "$(($OPTIND -1))"

log "Starte $appname"

# sind wir root?
[[ $EUID -ne 0 ]] && { log_err "Script muss als root ausgeführt werden - Abbruch."; exit 1; }

# genügend Argumente?
[[ "$#" -gt 0 ]] || { log_err "Keine zu sichernden Suffixe erhalten - Abbruch."; exit 1; }

[...]

log "Ende $appname"
```

**Daemon**

```
#!/bin/bash
DEBUG=false

# This part is for fun, if you consider shell scripts fun- and I do.
trap process_USR1 SIGUSR1

process_USR1() {
    echo 'Got signal USR1'
    echo 'Did you notice that the signal was acted upon only after the sleep was done'
    echo 'in the while loop? Interesting, yes? Yes.'
    exit 0
}
# End of fun. Now on to the business end of things.

print_debug() {
    whatiam="$1"; tty="$2"
    [[ "$tty" != "not a tty" ]] && {
        echo "" >$tty
        echo "$whatiam, PID $$" >$tty
        ps -o pid,sess,pgid -p $$ >$tty
        tty >$tty
    }
}

me_DIR="$( cd "$( dirname "${BASH_SOURCE[0]}" )" && pwd )"
me_FILE=$(basename $0)
cd /

#### CHILD HERE --------------------------------------------------------------------->
if [ "$1" = "child" ] ; then   # 2. We are the child. We need to fork again.
    shift; tty="$1"; shift
    $DEBUG && print_debug "*** CHILD, NEW SESSION, NEW PGID" "$tty"
    umask 0
    $me_DIR/$me_FILE XXrefork_daemonXX "$tty" "$@" </dev/null >/dev/null 2>/dev/null &
    $DEBUG && [[ "$tty" != "not a tty" ]] && echo "CHILD OUT" >$tty
    exit 0
fi

##### ENTRY POINT HERE -------------------------------------------------------------->
if [ "$1" != "XXrefork_daemonXX" ] ; then # 1. This is where the original call starts.
    tty=$(tty)
    $DEBUG && print_debug "*** PARENT" "$tty"
    setsid $me_DIR/$me_FILE child "$tty" "$@" &
    $DEBUG && [[ "$tty" != "not a tty" ]] && echo "PARENT OUT" >$tty
    exit 0
fi

##### RUNS AFTER CHILD FORKS (actually, on Linux, clone()s. See strace -------------->
                               # 3. We have been reforked. Go to work.
exec >/tmp/outfile
exec 2>/tmp/errfile
exec 0</dev/null

shift; tty="$1"; shift

$DEBUG && print_debug "*** DAEMON" "$tty"
                               # The real stuff goes here. To exit, see fun (above)
$DEBUG && [[ "$tty" != "not a tty" ]]  && echo NOT A REAL DAEMON. NOT RUNNING WHILE LOOP. >$tty

$DEBUG || {
while true; do
    echo "Change this loop, so this silly no-op goes away." >/dev/null
    echo "Do something useful with your life, young padawan." >/dev/null
    sleep 10
done
}

$DEBUG && [[ "$tty" != "not a tty" ]] && sleep 3 && echo "DAEMON OUT" >$tty

exit # This may never run. Why is it here then? It's pretty.
```
