# bitwarden

## CLI

### Self signed CA für Zertifikat verwenden

```
cp /pfad/zu/ca.crt ~/.config/Bitwarden\ CLI/ca.crt
export NODE_EXTRA_CA_CERTS=~/.config/Bitwarden\ CLI/ca.crt # -> in .bashrc setzen!
bw login
```

**Passworteingabe in laufender Sitzung verhindern**

```
bw unlock
export BW_SESSION="[....]"

# oder 
alias bwlogin='eval $(bw unlock | grep "export" | cut -c 3-)'
```

**Passwortgenerierung**

```
bw generate --length 14
bw generage --length 14 -ulns # uppercase, lowercase, numbers, special chars
```

**Snippets**

```
bw sync
bw list items --search clustercbx
bw list items --search clustercbx | jq '.[] | .name,.username,.login'
bw get item <itemid>
bw get password <itemid>
bw get password https://some.url
bw delete item <itemid>
bw list items --trash
```

**Tipps**

* Suchen sind **nicht** case sensitive
* Output zu `jq` pipen
