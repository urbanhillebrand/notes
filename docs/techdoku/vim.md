# vim

## Tipps

**Basics**

```
r                   # einzelnes Zeichen ersetzen
R                   # ersetzen bis man ESC drückt
ZZ                  # speichern & exit
ZQ                  # exit ohne speichern
```

**g**

|     |     |
| --- | --- |
| gj bzw. gu | angezeigte Zeile runter/rauf |
| g0 bzw. g$ | Anfang/Ende der angezeigten Zeile |
| gu &lt;motion> bzw. gU &lt;motion> | lower- bzw. Uppercase |

**Completion**

* Ctrl-n # nächster Match
* Ctrl-p # vorheriger Match

**Autocompletion**

```
Ctrl-D              # zeigt alle möglichen Autocompletions (auch z.B. bei :set oder :help)
TAB                 # verwende aktuell gewählte Autocompletion
```

**Sprünge**

|     |     |
| --- | --- |
| w | zum nächsten Wort (`W` ignoriert Satzzeichen) |
| b | zum vorherigen Wort (`B` ignoriert Satzzeichen) |
| e | zum Wortende (`E` ignoriert Satzzeichen) |
| ) | Satz vor |
| ( | Satz zurück |
| } | Absatz vor |
| { | Absatz zurück |
| gg | zum Start |
| G | zum Ende |
| % | findet matching parenthesis |
| mx | setzt Marker "x" ("x" = beliebiger Buchstabe; klein=filespez.; groß=inkl. Filename!) |
| 'x | spring zu Marker "x" (Backtick geht auch) |
| H | move to top of screen |
| M | move to middle of screen |
| L | move to bottom of screen |
| ^U | move up half a screen |
| ^D | move down half a screen |
| ^F | page down |
| ^B | page up |
| &lt;Zahl>% | springe zu &lt;zahl> Prozent der Datei |

**Nach Sprüngen (z.B. Suche) sind die letzten Positionen gespeichert**

* Ctrl-o # zurück
* Ctrl-i # vorwärts

**Bereiche bei Suchen/Ersetzen**

```
:5,15s/old/new/g  # zw. Zeilen 5 und 15 ersetzen
:'a,'bs/foo/bar/g # nur zw. Markierungen a und b
:s/old/new        # nur akt. Zeile
:%s/old/new/g     # im ganzen file
:%s/old/new/gc    # mit Bestätigung
```

**❗ IMPORTANT**\
Verb - Modifier - Noun!

**Verbs**

|     |     |
| --- | --- |
| d | delete |
| c | change |
| y | yank (copy) |
| v | visually select |
| V | visually select (line) |
| C | change line from where you are at |
| S | change entire line |

**Modifiers**

|     |     |
| --- | --- |
| i | inside |
| a | around |
| &lt;number> | number |
| t | search for something and stop before it |
| f | search for something and land on it |
| / | find string (literal or regex) |

**Nouns**

|     |     |
| --- | --- |
| w | word |
| s | sentence |
| ) | sentence |
| p | paragraph |
| } | paragraph |
| t | tag |
| b | block |

**registers**

Commands mit einem doppelten Anführungszeichen und dem Registernamen beginnen. Registernamen in Kleinbuchstaben ersetzen den Inhalt des Registers, mit Großbuchstaben wird der Inhalt ergänzt:

```
"kyy # kopiert aktuelle Zeile in Register k
"kp  # fügt Inhalt von Register k ein
"Lyy # hängt aktuelle Zeile an Register l an
:reg # Zeigt Inhalt aller Register an
```

Trick um ein Register zu löschen: `qaq` (Startet Makroaufzeichnung in Register a, und beendet diese sogleich).

**📌 NOTE**\
vim hat noch viele andere Register (`:h registers`). Notabene: Register 0-9 haben die letzten 10 yank-Inhalte!

Komplexeres Beispiel: `qaq:g/pattern/y A`

--> Leert Register a, und kopiert alle Zeilen die "pattern" matchen in das Register a

**norm**

Mit `norm` kann man "normal mode" commands auf eine, aber auch auf viele Zeilen anwenden. Nachfolgendes Beispiel ergänzt z.B. Text am Ende einer Zeile:

`:norm Ablahtextende`

Kombination mit `:g` (oder `v` für nicht-Matches) möglich:

`:g/pattern/:norm IHier kommt das Pattern: `

**Copy&Paste**

* "xy # kopiert in Puffer x (erl. Zeichen: Kleinbuchstaben, Ziffern 0-9)
* "xp # einfügen von Puffer x

**Visual mode**

* :w FILENAME speichert nur die Markierung

**External commands**

* :! COMMAND

**Dateiteile**

```
v motion :w FILENAME        # nur Inhalt der vis. selection abspeichern
:r FILENAME                 # File einlesen
:r !ls                      # Output von Shellcommand einlesen
```

**Case**

```
~           # switch case akt. Zeichen
g~{motion}  # switch case mit motion, z.B. "g~3w" oder "g~G"
g~~         # switch case akt. Zeile
{visual}~   # mit visueller Auswahl
{visual}U   # visuelle Auswahl, switch to uppercase
{visual}u   # visuelle Auswahl, switch to lowercase
gU{motion}  # switch to uppercase mit motion
gUU         # switch to uppercase, akt. Zeile
[...]
```

**Speichern eines nicht als root geöffneten Files**

* :w !sudo tee %

**Buffers**

* :bn # next
* :bp # previous
* :bd # delete
* :ls # list
* :b &lt;part> # switch to (partial match)

**Scroll**

* Ctrl-u/-d half page up/down
* Ctrl-F/-B page down/up
* Ctrl-E/-Y scroll down/up, in Zeile bleiben
* zz center current line
* zt make current line top
* zb make current line bottom

**Shell commands**

|     |     |
| --- | --- |
| :!&lt;command> | führe &lt;command> as |
| :shell | führe shell aus (Ende mit `exit`) |
| :read !&lt;command> | fügt Output von &lt;command> unterhalb der aktuellen Zeile ein |
| &lt;ZN>read !&lt;command> | fügt Output von &lt;command> unterhalb von Zeilennummer &lt;ZN> ein |

**Tabs to spaces**

```
:set tabstop=4 shiftwidth=4 expandtab
:retab
```

**Spaces to tabs**

```
:set noexpandtab
:retab!
```

## vimdiff (oder vim -d)

* `zo` open fold
* `zc` close fold
* `]c` zur nächsten Änderung
* `[c` zur vorherigen Änderung
* `Ctrl-w w` zwischen Fenstern springen
* `:diffput` aktuelle Änderung von links nach rechts
* `:diffget` aktuelle Änderung von rechts nach links
* `u` undo

**🔥 CAUTION**\
Nach undo stimmt Highlighting evt. nicht mehr -> `:diffupdate`

**📌 NOTE**\
Mit `:qa` kann man beide Files gleichzeitig beenden - ansonsten ist 2 x `:q` oder `ZZ` usw. nötig

## Nützliche Options

```
set ic              # ignore case
set is              # incsearch
set hls             # highlight search
```

## vundle
```
:PluginInstall
:bdelete (buffer close)
:PluginUpdate
:PluginList
:PluginSearch! <nameofplugin>
```

* Nach Installation muss das Plugin in .vimrc hinzugefügt werden ("Plugin '&lt;nameofplugin>'")
* Auch bei Deinstallation in Vundle & vimrc entfernen (oder: .vimrc entfernen && :PluginClean)
