# readline

Infos für emacs-Mode (=Standard). Ich verzichte auf "set -o vi", da dies auf den meisten Servern eben nicht aktiv ist :(

**Movement**

,===
Ctrl-a, zum Zeilenstart
Ctrl-e, zum Zeilenende
Alt-f, vor zum Ende des nächsten Worts
Alt-b, zurück zum Anfang des nächsten Worts
,===

**History**

,===
Ctrl-p, letzten Command aus History holen
Ctrl-n, nächsten Command aus History holen
Ctrl-r, reverse Search durch History
Ctrl-s, forward Search durch History
Alt-p, History nach String durchsuchen (reverse)
Alt-n, History nach String durchsuchen (forward)
Alt-., letztes Argument des vorigen Commands einfügen
Alt-n Alt-., Argument n des vorigen Commands einfügen
,===

**Cut and Paste**

,===
Ctrl-k, cut (kill) bis zum Zeilenende
Ctrl-u, cut bis zum Zeilenanfang
Alt-d, cut bis zum Ende des akt. Wortes
Alt-Delete, cut bis zum Anfang des akt. Wortes
Ctrl-y, paste (yank)
Alt-y, "kill ring" rotieren und neues Top einfügen. Geht nur nach vorherigem Paste
,===

**Sonstiges**

,===
Alt-&lt;n>, nächsten Command n Mal wiederholen (z.B. Alt-5 Alt-f = 5 Wörter vorwärts)
Ctrl-_, incrementelles undo
Alt-r, alle Änderungen an akt. Zeile rückgängig machen
Ctrl-], vorwärts nach Zeichen suchen + jump
Ctrl-Alt-], rückwärts nach Zeichen suchen + jump
Ctrl-#, akt. Zeile auskommentieren und neue öffnen
,===
