# jq

## Beispiele

**`test.json`**

```
[
    {
        "name": "blah-gw01",
        "url": "https://blah-gw01:1234",
        "key": "blahkey",
        "secret": "blahsecret"
    },
    {
        "name": "blah-gw02",
        "url": "https://blah-gw02:9876",
        "key": "blahkey2",
        "secret": "blahsecret2"
    }
]
```

**Snippets**

```
jq '.' test.json

jq '.[] | { fw: .name, url: .url}' test.json # einzelne Felder mit Beschriftung

jq '.[] | select(.name=="blah-gw02")' test.json # SELECT

jq --arg fw blah-gw02 '.[] | select(.name==$fw)' test.json # dasselbe, aber parametrisiert
```

**In Loop verarbeiten**

```
#!/bin/bash

JSON="test2.json"

jq -c -r '.[]' $JSON |
    while read item; do
        key=$(jq -r '.key' <<<"$item")
        url=$(jq -r '.url' <<<"$item")
        echo "gef.: $key / $url"
    done
```
