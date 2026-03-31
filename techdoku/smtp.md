# smtp

## TLS-Session

    openssl s_client -connect mail.example.com:25 -starttls smtp

oder (Exchange?)

    openssl s_client -connect mail.example.com:25 -starttls smtp -crlf

## AUTH PLAIN testen
```perl
perl -MMIME::Base64 -e 'print encode_base64("\000jms1\@jms1.net\000not.my.real.password")'
```

**⚠️ WARNING**\
perl-spezifische Sonderzeichen wie das "@" escapen

oder

`printf “\0username\0password” | openssl enc -a`

**SMTP-Session**

```
AUTH PLAIN <base64outputvonoben>
```

## AUTH LOGIN testen
```
echo -n 'benutzername' | openssl enc -a
echo -n 'password' | openssl enc -a
```

**SMTP-Session**

```
ehlo urban.test
AUTH LOGIN
YmVudXR6ZXJuYW1l ①
cGFzc3dvcmQ= ②
```
1. b64-codierter Benutzername von oben
2. b64-codiertes PW von oben
