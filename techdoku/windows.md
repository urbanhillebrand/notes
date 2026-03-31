# windows

**Diverses**

```
runas /user:thp\administrator cmd 
shutdown.exe /r /t 0 /f      # sofortiger Reboot
shutdown.exe /r /t 3600 /f   # Reboot in 1 Stunde (3600 Sekunden)
shutdown.exe /a              # Abbruch eines angeforderten Shutdowns
shutdown.exe /s /f /t 0      # sofortiges Herunterfahren

nslookup -type=srv _autodiscover._tcp.iq-tec.at

dsa.msc # AD User & Gruppen
gpupdate /force
gpresult /h:c:\temp\result.html
cscript cmd_lastlogon.vbs
psexec \\pcname cmd
w32tm /stripchart /computer:192.168.1.1 /dataonly /samples:5 # ntp testen
```

**Zertifikat installieren**

```
mmc ausf.
Datei - SnapIn hinz. - Zertifikatsmanager
ggf. auch root-Zert. hinzufügen!
```

**Angemeldeten User herausfinden**

```
query user
query user /server:swrdsp01
tasklist /s swrdsp01 /fi "imagename eq explorer.exe" /v # alternativ
```

**DN von AD-Komponente rausfinden**

```
adsiedit.msc
```

**Auf Terminal Server installieren**

```
change user /install
# ... installieren ...
change user /execute
```

**Remote shell auf Client ausführen**

am DC: cmd als admin, `powershell` ausführen

`enter-pssessession <computername>`

prüfen: `$env:computername`
oder `hostname`

Alternativ:
invoke-command &lt;computername> {&lt;command>}

**Inst-Image auf DC kopieren**

am DC: Ausführen - `sysvol`
im Ordner cibex Installationspaket ablegen

Powershell als Admin ausführen!

```
Import-Module servermanager
Add-WindowsFeature telnet-client
```

**Programmnamen Verwaltungstools**

| Appletname | Filename |
| --- | --- |
| AD Users and Computers | dsa.msc |
| Computer Management | compmgmt.msc |
| DHCP Management | dhcpmgmt.msc |
| DNS Management | dnsmgmt.msc |
| Event Viewer | eventvwr.msc |
| Local Group Policy Editor | gpedit.msc |
| Local Security Settings Manager | secpol.msc |
| Local Users and Groups Manager | lusrmgr.msc |
| Performance Monitor | perfmon.msc |
| Services Management | services.msc |

**Remote-Ausführung von Scripts**

```
runas /user:ci\sbsadmin cmd
psexec \\l0002 cmd
```

**Last reboot**

```
systeminfo
systeminfo | find /i "Boot Time"
```

* akt. Version 0.3 - sonst aus git.cibex.net:scripts/windows/WindowsServer holen
* Powershell als Admin starten

```
cd $env:ProgramData
mkdir cibex
mkdir cibex\bin
copy Pfad\zu\RebootAllServers.ps1 .
./RebootallServers.ps1 -Date "27.11.2018 06:00"
```

* In Aufgabenplanung aktivieren, dass Script unabhängig von Benutzeranmeldung
  ausgeführt werden soll
* Task speichern, und Admin-Passwort angeben
* ggf. noch Admin-User inkl. Domänennamen neu auswählen (bei entsprechender Fehlermeldung)
* soll der Reboot untertags erfolgen, Parameter `-SkipDateChk` angeben

**Port testen (Bsp. f. WSUS)**

```
tnc mgmt.cibex.net -p 8531

test-netconnection mgmt.cibex.net -port 8531 https://mgmt.cibex.net:8531/selfupdate/wuident.cab
```

**OpenVPN TAP-Adapter verschwindet**

```
C:\Program Files\TAP-Windows\bin\addtap.bat # als Admin ausführen
```

**Scheduled Reboot hinzufügen**

```
Task hinzufügen
Benutzer = Domainadmin
"run with highest privileges" aktivieren
auch ohne Benutzeranmeldung ausführen
Command: shutdown.exe, Parameter: /r /f /t 0
```

**Zeit syncen**

```
w32tm /resync
```

## Outlook

Achtung: https://www.lifewire.com/view-complete-message-source-outlook-1173713

Ohne Addins starten: `outlook.exe /safe`

Bei Problemen die nicht von Plugins kommen kann man die Reparatur über control center - software - repair versuchen

**MIME-Source einer Nachricht erhalten**

```
perl-modul Email::Outlook::Message installieren (Arch: perl-email-outlook-message)
msgconvert message.msg
oder
msgconvert --mbox some-mbox-file *.msg
```

**Powershell auf V5 upgraden**

* Test: In Powershell `host` eintippen -> Version wird in 2. Zeile angezeigt
* von `/home/urban/mnt/ci/nas01-download/PS1` die richtige Version auf Server kopieren & ausführen
* ACHTUNG - Reboot danach notwendig!

**PC für Fernzugriff vorbereiten**

* Windows - Pause (oder Start - Einstellungen - System)
* Remotedesktop - Remotedesktop aktivieren
* oder Erweiterte Systemeinstellungen - bei Remotedesktopbenutzern User hinzufügen
* einstellungen - Energie -> Bildschirm + Energiesparmodus anpassen

**zusätzlich für WOL:**

* ggf. im Gerätemanager bei der NIC WOL aktivieren (auch unter "Erweitert" Settings prüfen; Energieverwaltung, Magic Packet...)
* Tip: mmc als Admin starten, und Computerverwaltung oder Gerätemanager hinzufügen

**PC von User herausfinden**

* Rechtsklick auf Start - Computer Management
* System Tools - Sessions
