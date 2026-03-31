# dpkg

## Snippets

**clone**

```
dpkg --get-selections | tee packages_list
# scp to dst.
sudo dpkg --set-selections < packages_list
sudo apt-get -u dselect-upgrade
```
