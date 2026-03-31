**Snippets**
```
# nur geänderte Files anzeigen
diff -rq dir1 dir2

# oder mit Änderungen (unified Diff)
diff -ru dir1 dir2

# Muster ausschließen
diff -rq -x "*.log" -x "*.bak" dir1 dir2
```

**Optionen**
- `-b` ignoriert unterschiedl. Anzahl an Leerzeichen
- `-w` ignoriert sämtliche Leerzeichen/Tabs
- `-B` ignoriert Änderungen, die nur aus leeren Zeilen bestehen
- 