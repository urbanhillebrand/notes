# turnserver

**Credentials aus Secret generieren**

```
secret=$(awk -F "=" '/static-auth-secret/ {print $2}' /etc/turnserver.conf) && time=$(date +%s) && expiry=86400 && username=$(( $time + $expiry )) && echo username: $username && echo password : $(echo -n $username | openssl dgst -binary -sha1 -hmac $secret | openssl base64)
```
