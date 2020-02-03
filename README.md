# DZ1
Документ описывает создание кастомизированного образа linux.

исходные данные: компьютер с установленной операционной системой Ubuntu.

Шаги:

1. установка ПО.
1.1 Vagrant
sudo apt install curl
curl -O https://releases.hashicorp.com/vagrant/2.2.6/vagrant_2.2.6_x86_64.deb && sudo dpkg -i vagrant_2.2.6_x86_64.deb
1.2 Packer
curl https://releases.hashicorp.com/packer/1.4.4/packer_1.4.4_linux_amd64.zip -o packer_1.4.4_linux_amd64.zip
sudo gunzip packer_1.4.4_linux_amd64.zip
sudo cp packer /usr/local/bin/
cd /usr/local/bin/
sudo chmod +x packer


2. создать аккаунт на https://github.com/ и https://app.vagrantup.com

3. сбор собственного образа.
3.1 скачиваем manual_kernel_update из https://github.com/dmitry-lyutenko/manual_kernel_update
3.2 переходим в паку с centos.json. в моем случае отредактировал 2 параметра:

 "provisioners": [
      "start_retry_timeout": "3m",
      "pause_before": "60s",
3.3 Для создания образа системы достаточно перейти в директорию packer и в ней выполнить команду:
packer build centos.json


результат будет примерно такой:

 centos-7.7: Installing:
    centos-7.7:  kernel-ml      x86_64      5.5.1-1.el7.elrepo         elrepo-kernel       49 M
    centos-7.7:
 
 
 
==> Builds finished. The artifacts of successful builds are:
--> centos-7.7: 'virtualbox' provider box: centos-7.7.1908-kernel-5-x86_64-Minimal.box

packer$ ls -l
итого 736324
-rw-r--r-- 1 rustam3 rustam3 753973124 фев  3 12:21 centos-7.7.1908-kernel-5-x86_64-Minimal.box


в данном примерно обноляется ядро.

3.4 проверка результата 
vagrant box add --name centos-7-5 centos-7.7.1908-kernel-5-x86_64-Minimal.box
vagrant box list
vagrant up --provider=virtualbox
vagrant ssh

аутпут должен быть:
vagrant ssh
Last login: Mon Feb  3 07:20:33 2020 from 10.0.2.2
[vagrant@localhost ~]$ uname -r
5.5.1-1.el7.elrepo.x86_64


4. добавление образа в Vagrant cloud:
vagrant cloud publish --release user-name/centos-7-5 1.0 virtualbox centos-7.7.1908-kernel-5-x86_64-Minimal.box

в результате на серевер должен сохранится файл образ с нужными настройками, который могут испльзовать другие
