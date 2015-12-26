### USE

* First you need to [Install Vagrant](http://www.vagrantup.com/download)

#### Install from Atlas
```
vagrant plugin install vagrant-parallels vagrant-hostmanager
vagrant init magehost/trusty-apache-php5
vagrant login

vagrant up
```

#### Install from our web server:
```
vagrant plugin install vagrant-parallels vagrant-hostmanager
wget https://magentohosting.pro/vagrant/Vagrantfile
vagrant up
```

### Destruct previous test
```
vagrant destroy -f
rm -rf ~/.vagrant.d/boxes/magehost-VAGRANTSLASH-trusty-apache-php5
rm -rf .bundle .vagrant httpdocs Vagrantfile
```

### Install from local test
```
vagrant plugin install vagrant-parallels vagrant-hostmanager
cp ../magehostdev/Vagrantfile Vagrantfile
gsed -i 's/http:\/\/magentohosting\.pro\/vagrant\/catalog\.json/file:\/\/\/Users\/jeroen\/vagrant\/magehostdev\/catalog_local\.json/g' Vagrantfile
vagrant up --provider virtualbox
vagrant up --provider parallels
```

### PACKAGE

#### Test insecure SSH
```
open box.pvm
ssh -i vagrant-insecure.key vagrant@[IP]
```

#### Package - Parallels
```
VERSION=9
#
rm -rf parallels/box.pvm/*.log parallels/box.pvm/*~ parallels/box.pvm/*.backup parallels/box.pvm/harddisk1.hdd/*.Backup parallels/box.pvm/*.app
prl_disk_tool compact --hdd parallels/box.pvm/harddisk1.hdd
tar -cvzf trusty-apache-php5_prl_v${VERSION}.box -C parallels box.pvm Vagrantfile metadata.json
#
rm -f trusty-apache-php5_vb_v${VERSION}.box
vagrant package --base magehostdev.pro --output trusty-apache-php5_vb_v${VERSION}.box --vagrantfile virtualbox/Vagrantfile
```

#### Increase version + set checksum
In each JSON file you need to update the version and the filename. In `catalog.json` also update the md5 sum.
```
md5 trusty-apache-php5_prl_v${VERSION}.box
md5 trusty-apache-php5_vb_v${VERSION}.box
joe catalog.json catalog_local.json
```

#### Upload
```
scp -P2222 trusty-apache-php5_*_v${VERSION}.box catalog.json Vagrantfile maghopro@sun:httpdocs/vagrant/Vagrantfile
```
