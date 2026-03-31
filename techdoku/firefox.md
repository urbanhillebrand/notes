# Firefox

## mass edit history

Zuerst FF beenden, dann z.B.:

```
cd /home/urban/.mozilla/firefox/7q9fcu1c.default-release
cp places.sqlite{,.bak}
sqlite3 places.sqlite "select url from moz_places where url glob 'https://???-gw0?.c.cibex.net*';"
# ggf. mit delete wiederholen
```
