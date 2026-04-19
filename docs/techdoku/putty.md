# PuTTY

**ProxyJump-Equivalent**

* Jumphost als session definieren (z.B. `eca-gw90.c.cibex.net`)
* Zielsession definieren
  * unter "Connection - Proxy - Proxy type" "Local" auswählen
  * unter "Connection - Proxy - Telnet command, or local proxy command" `plink.exe -load "eca-gw90.c.cibex.net" -nc %host:%port` eingeben
