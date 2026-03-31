# vagrant

```
mkdir myvm && cd myvm
vagrant init
vi Vagrantfile
```

-> config.vm.box = "ubuntu/trusty64"

```
vagrant up
vagrant ssh
vagrant destroy
```

* **Boxes**\
https://atlas.hashicorp.com/boxes/search

dann z.B. `vagrant box add ubuntu/trusty64`

Box Konvertieren:
```
vagrant mutate ubuntu/trusty64 libvirt
```

Box Update:
```
vagrant box update
```

Alle Versionen anzeigen:
```
vagrant box list
```

Alte Version löschen:
```
vagrant box remove ubuntu/trusty64 --provider=virtualbox --box-version=20160127.0.0
```

## vagrant-libvirt installieren

https://github.com/vagrant-libvirt/vagrant-libvirt/issues/541
https://gist.github.com/j883376/d90933620c7ed14daa4e0963e005377f#gistcomment-2115266

**Probleme mit Abhängigkeiten:**

```
VAGRANT_DISABLE_STRICT_DEPENDENCY_ENFORCEMENT=1 vagrant plugin install vagrant-libvirt
```
