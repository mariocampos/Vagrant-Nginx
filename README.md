# Vagrant + Nginx
### En este laboratorio se creará una máquina virtual con líneas de comando con *Vagrant* y luego dentro de ella, se instalará *Nginx*
#### Las herramientas que se usará serán las siguientes:
- [Vagrant](https://www.vagrantup.com/downloads)
- [Virtualbox](https://www.virtualbox.org/wiki/Downloads)
---
### Crearemos la máquina virtual con Vagrant.
1. Crear carpeta:
~~~
$ mkdir vagrant_v1
~~~
2. Dentro de la carpeta:
~~~
$ cd vagrant_v1
~~~
3. Ejecutamos el siguiente comando:
~~~
$ vagrant init
A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.
~~~
4. Automáticamente se creará el archivo Vagrantfile, lo cual editaremos con las siguientes líneas:
~~~
$ vi Vagrantfile

# -*- mode: ruby -*-
# vi: set ft=ruby :
Vagrant.configure("2") do |config|
  config.vm.define "vm_lab" do |node|
    node.vm.box = "centos/7"
    node.vm.hostname = "nginx"
    node.vm.network "private_network", ip: "10.1.1.10"
      node.vm.provider "virtualbox" do |v|
        v.cpus = 1
        v.memory = 256
    end
  end
end
~~~
5. Luego se creará la máquina virtual con el siguiente comando:
~~~
$ vagrant up
Bringing machine 'vm_lab' up with 'virtualbox' provider...
==> vm_lab: Importing base box 'centos/7'...
==> vm_lab: Matching MAC address for NAT networking...
==> vm_lab: Checking if box 'centos/7' version '2004.01' is up to date...
==> vm_lab: Setting the name of the VM: vagrant_v3_vm_lab_1626364370016_98675
==> vm_lab: Clearing any previously set network interfaces...
==> vm_lab: Preparing network interfaces based on configuration...
    vm_lab: Adapter 1: nat
    vm_lab: Adapter 2: hostonly
==> vm_lab: Forwarding ports...
    vm_lab: 22 (guest) => 2222 (host) (adapter 1)
==> vm_lab: Running 'pre-boot' VM customizations...
==> vm_lab: Booting VM...
==> vm_lab: Waiting for machine to boot. This may take a few minutes...
    vm_lab: SSH address: 127.0.0.1:2222
    vm_lab: SSH username: vagrant
    vm_lab: SSH auth method: private key
    vm_lab:
    vm_lab: Vagrant insecure key detected. Vagrant will automatically replace
    vm_lab: this with a newly generated keypair for better security.
    vm_lab:
    vm_lab: Inserting generated public key within guest...
    vm_lab: Removing insecure key from the guest if it's present...
    vm_lab: Key inserted! Disconnecting and reconnecting using new SSH key...
==> vm_lab: Machine booted and ready!
==> vm_lab: Checking for guest additions in VM...
    vm_lab: No guest additions were detected on the base box for this VM! Guest
    vm_lab: additions are required for forwarded ports, shared folders, host only
    vm_lab: networking, and more. If SSH fails on this machine, please install
    vm_lab: the guest additions and repackage the box to continue.
    vm_lab:
    vm_lab: This is not an error message; everything may continue to work properly,
    vm_lab: in which case you may ignore this message.
==> vm_lab: Setting hostname...
==> vm_lab: Configuring and enabling network interfaces...
==> vm_lab: Rsyncing folder: /cygdrive/c/Users/MCAMPOBE/Documents/Vagrant/vagrant_v3/ => /vagrant
~~~
6. Validamos que la máquina virtual esté creada.
~~~
$ vagrant status
Current machine states:

vm_lab                    running (virtualbox)

The VM is running. To stop this VM, you can run `vagrant halt` to
shut it down forcefully, or you can run `vagrant suspend` to simply
suspend the virtual machine. In either case, to restart it again,
simply run `vagrant up`.
~~~
7. Ingresamos a la máquina virtual.
~~~
$ vagrant ssh vm_lab
Last login: Thu Jul 15 16:14:50 2021 from 10.0.2.2
[vagrant@nginx ~]$ hostname
nginx
[vagrant@nginx ~]$
~~~
---
### Instalación de Nginx en máquina virtual Centos 7.
>En caso tenga otra ditro de Linux. Hacer [click aquí](https://nginx.org/en/linux_packages.html#Debian "Nginx with Debian") para realizar la configuración e instalación.
8. Dentro de la máquina virtual cambiamos de usuario a *root* para instalar sin problemas.
~~~
[vagrant@nginx ~]$ sudo su -
[root@nginx ~]#
~~~
9. Antes de la instalación de *Nginx*, actualizamos los paquetes de Centos:
~~~
[root@nginx ~]# yum -y update
~~~
10. Instalamos el paquete de utilidades de yum.
~~~
[root@nginx ~]# yum install yum-utils
~~~
11. Creamos el siguiente archivo:
~~~
[root@nginx ~]# vi /etc/yum.repos.d/nginx.repo
~~~
12. Agregamos el siguiente contenido y guardamos con el comando *:wq*:
~~~
[nginx-stable]
name=nginx stable repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=1
enabled=1
gpgkey=https://nginx.org/keys/nginx_signing.key
module_hotfixes=true

[nginx-mainline]
name=nginx mainline repo
baseurl=http://nginx.org/packages/mainline/centos/$releasever/$basearch/
gpgcheck=1
enabled=0
gpgkey=https://nginx.org/keys/nginx_signing.key
module_hotfixes=true
~
~
~
~
:wq
~~~
13. Configuramos el administrador de paquetes *yum* para habilitar *Nginx* en primera línea.
~~~
[root@nginx ~]# yum-config-manager --enable nginx-mainline
~~~
14. Instalamos *Nginx*.
~~~
[root@nginx ~]# yum install nginx
~~~
15. Abrimos los puertos que usa *Nginx*.
~~~
[root@nginx ~]# firewall-cmd --permanent --add-port={80/tcp,443/tcp}
[root@nginx ~]# firewall-cmd --reload
~~~
16. Iniciamos y habilitamos *Nginx*
~~~
[root@nginx ~]# systemctl enable nginx
[root@nginx ~]# systemctl start nginx
~~~
17. Validamos el estado.
~~~
[root@nginx ~]# systemctl status nginx
● nginx.service - nginx - high performance web server
   Loaded: loaded (/usr/lib/systemd/system/nginx.service; disabled; vendor preset: disabled)
   Active: active (running) since Thu 2021-07-15 19:58:42 UTC; 2s ago
     Docs: http://nginx.org/en/docs/
  Process: 32580 ExecStart=/usr/sbin/nginx -c /etc/nginx/nginx.conf (code=exited, status=0/SUCCESS)
 Main PID: 32581 (nginx)
   CGroup: /system.slice/nginx.service
           ├─32581 nginx: master process /usr/sbin/nginx -c /etc/nginx/nginx.conf
           └─32582 nginx: worker process

Jul 15 19:58:42 nginx systemd[1]: Starting nginx - high performance web server...
Jul 15 19:58:42 nginx systemd[1]: Can't open PID file /var/run/nginx.pid (yet?) after start: No such file or...ectory
Jul 15 19:58:42 nginx systemd[1]: Started nginx - high performance web server.
Hint: Some lines were ellipsized, use -l to show in full.
~~~
18. Validamos ingresando a un navegador y ponemos la IP:
