# java

## thread dump erstellen

(Z.B. f. Alfresco)

zuerst mit `top -H` die Thread-ID des schuldigen Prozesses herausfinden

PID von java ermitteln: `ps auxw|grep java`

oder eleganter: `top -p $(pidof java) -H`

Java-Threaddump erzeugen: `kill -3 $(pidof java)`

Bei Alfresco steht der Threaddump in catalina.out. Die Thread-ID muss von dezimal (top) nach hexadezimal (java) umgerechnet werden (Anzeige in catalina.out: nid=0x...)

_Anm.:_ Java läuft dabei normal weiter!
