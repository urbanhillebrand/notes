# postfix

## mailq

```bash
mailq | tail -n +2 | awk  'BEGIN { RS = "" } /noreply@eca\.at/ { print $1 }' | tr -d '*!' | postsuper -d -

# oder etwas weniger explizit/robust:
mailq| grep -i noreply@eca.at | cut -c1-12 | postsuper -d -
```
