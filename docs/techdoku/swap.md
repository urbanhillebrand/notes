# swap

## swapfile nachrüsten

```
sudo dd if=/dev/zero of=/swapfile bs=1G count=2
sudo chown root:root /swapfile
sudo chmod 0600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# fstab
echo "/swapfile none swap sw 0 0" | sudo tee /etc/fstab

# verify
free -m
```
