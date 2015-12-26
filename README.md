### USE

* First you need to [Install Vagrant](http://www.vagrantup.com/download)

#### Install Vagrant plugins
```
vagrant plugin install vagrant-parallels vagrant-hostmanager
```

#### Install from Atlas
```
vagrant init magehost/trusty-apache-php5
vagrant login

vagrant up
```

#### Install from our web server:
```
wget https://magentohosting.pro/vagrant/Vagrantfile
vagrant up
```

### Install from local - Parallels
```
vagrant destroy -f
rm -rf ~/.vagrant.d/boxes/magehost-VAGRANTSLASH-trusty-apache-php5
cp ../magehostdev/Vagrantfile_local Vagrantfile
vagrant up --provider parallels
```

### Install from local - VirtualBox
```
vagrant destroy -f
rm -rf ~/.vagrant.d/boxes/magehost-VAGRANTSLASH-trusty-apache-php5
cp ../magehostdev/Vagrantfile_local Vagrantfile
vagrant up --provider virtualbox
```

### PACKAGE

#### Test insecure SSH
```
open box.pvm
ssh -i vagrant-insecure.key vagrant@[IP]
```

#### Package
```
VERSION=9
####  Parallels
rm -rf parallels/box.pvm/*.log parallels/box.pvm/*~ parallels/box.pvm/*.backup parallels/box.pvm/harddisk1.hdd/*.Backup parallels/box.pvm/*.app
prl_disk_tool compact --hdd parallels/box.pvm/harddisk1.hdd
tar -cvzf push/trusty-apache-php5_prl_v${VERSION}.box -C parallels box.pvm Vagrantfile metadata.json
####  VirtualBox
rm -f trusty-apache-php5_vb_v${VERSION}.box
vagrant package --base magehostdev.pro --output push/trusty-apache-php5_vb_v${VERSION}.box --vagrantfile parallels/Vagrantfile
```

#### Increase version + set checksum
In each JSON file you need to update the version and the filename. In `catalog.json` also update the md5 sum.
```
md5 push/trusty-apache-php5_prl_v${VERSION}.box
md5 push/trusty-apache-php5_vb_v${VERSION}.box
joe push/catalog.json catalog_local.json
```

#### Upload
```
cd push; vagrant push ftp; cd ..
```
