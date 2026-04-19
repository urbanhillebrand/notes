# Backup Urban

## lap

### rsync-Backup auf nas1

#### Vorbereitungen

**Auf nas1 in `sshd_config`:**

```
PubkeyAuthentication yes
AuthorizedKeysfile .ssh/authorized_keys
```

SSH-Key erstellen: `sshkeygen -f laprsync -t ed25519`

-> auf nas1 in `/var/services/homes/laprsync/.ssh/authorized_keys` eintragen, und den User auf die nötigen Shares berechtigen.

**Test (Bsp.)**

`rsync -av -e 'ssh -i /home/urban/ssh/laprsync' . laprsync@10.2.0.101:/volume1/testbak`
