# tmux

**Wichtige Commands**

```
tmux ls # Session listen

tmux attach # oder "a"; an letzte Sitzung anhängen
tmux a -t mysession # an mysession anhängen
tmux a -d -t mysession # an mysession anhängen, andere Sessions detachen

tmux # neue Session; innerhalb von tmux mit ":new"
tmux new-session # oder "new-s"; innerhalb von tmux mit ":new"
tmux new-session -s mysession # benannte Sitzung erstellen; innerhalb von tmux mit ":new -s mysession"
tmux new-s -As blah # Neue Session oder an ggf. vorhandene attachen

tmux kill-session -t mysession # oder "kill-ses"; Sitzung killen
tmux kill-session -a # alle Sessions außer der eigenen killen
tmux kill-session -a -t mysession # alle Sessions außer mysession killen
```

**📌 NOTE**\
`L` = "leader" (Standard: Ctrl-B)

**Wichtige Shortcuts zu Sessions**

* `L d`: detach von Session
* `L s`: Sessions listen
* `L w`: Sessions und Windows listen
* `L $`: Session umbenennen
* `L :kill-session`: Session killen

**Wichtige Shortcuts zu Windows**

* `L c`: Window erst.
* `L n`, `L p`: next/prev Window
* `L [0-9]`: Zu Window n springen
* `L ,`: Window umbenennen
* `L :setw synchronize-panes`: Eingaben in allen Windows syncen

**Wichtige Shortcuts zu Panes**

* `L x`: close current pane
* `L z`: toggle pane zoom
* `L %`: split horizontal
* `L "`: split vertikal
* `L ;`: zur zuletzt gewählten Pane springen
* `L {`: move pane left
* `L }`: move pane right
* `L q`: zeigt Pane-Nummern an; (schnelle) Eingabe einer Nummer springt dorthin
* `L spacebar`: toggle between pane layouts
* `L !`: move current pane to new window

**Copy mode (mit `set -g mode-keys vi`)**

* `L [`: enter copy mode
* `q`: quit copy mode
* `Enter`: copy selection
* `Esc`: clear selection
* `L :save-buffer /path/to/file`: save buffer to file

**📌 NOTE**\
Auswahl entweder im visual mode, oder mit `Spacebar` starten
