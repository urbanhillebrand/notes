# ssh

* **Passwort-Authentifizierung erzwingen**\
 `ssh -o PreferredAuthentications=password -o PubkeyAuthentication=no <hostname>`
* **SSH-Key aus `known_hosts` löschen**\
 `ssh-keygen -R &lt;hostname>
* **ED25519-Keypair erstellen**\
 `ssh-keygen -t ed25519 -C "uh20230329-ed25519"`
* **Neue Keys openssh-Server erstellen**

  ```
  sudo rm /etc/ssh/ssh_host_*
  sudo dpkg-reconfigure openssh-server
  sudo systemctl restart ssh
  ```
