# todo.sh

```
t a "Aufgabe"
t a "Aufgabe +Projekt"
t a "Aufgabe @WerOderWo"

t addm "Aufgabe1
Aufgabe2" # mehrere Aufgaben hinzuf.

# Auflisten
t
t ls
t ls @WerOderWo
t ls +Projekt
t ls Keyword # Testsuche
t lsp # nur Tasks mit einer gesetzten Priorität

# Prioritäten
t pri <tasknummer> A # setzt Priorität "A" für Task
t depri <tasknummer> # entfernt Priorität von Task

# Sonstiges
t do <tasknummer> # setzt Task auf erledigt
t del <tasknummer>
t replace <tasknummer> "Neuer Aufgabentext"
t app <tasknummer> "Text to add" # Text anhängen
```
