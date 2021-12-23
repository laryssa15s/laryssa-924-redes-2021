# Implementação DNS Master

## Objetivo: 

    * Instalação e configuração do servidor DNS master com o Bind9
    
## Informações importantes

   * Antes de tudo é preciso definir duas tabelas: uma com as definições de ips usados e outra com os domínios a serem utilizados também.

```
Tabela 1: Definições da rede interna
--------------------------------
|  DESCRICAO  |  IP            |
--------------------------------
| rede        | 10.9.24.112    |
| máscara     | 255.255.255.0  |
| Gateway     | 10.9.24.1      |
| Broadcast   | 10.9.24.255    |
| Samba-SRV   | 10.9.24.112    |
| NameServer1 | 10.9.24.112    |
| NameServer2 | 10.9.24.106    |
--------------------------------
```      

OBS: na tabela acima estão os nameservers 1 e 2, DNS Master e o Slave, respectivamente. Como a instalação será do DNS Master, apenas será utilizado o nameserver 1 e seu ip correspondente. 

```
Tabela 2: Definições do domínio:
|      Apelido      |               NOME                                   |
|:------------------|:-----------------------------------------------------|
| gateway (gw)      | gw.emanuellylaryssa924.labredes.ifalarapiraca.local  |
| nameserver1 (ns1) | ns1.emanuellylaryssa924.labredes.ifalarapiraca.local |
| nameserver2 (ns2) | ns2.emanuellylaryssa924.labredes.ifalarapiraca.local |
| dupla (vm2)       | vm2.emanuellylaryssa924.labredes.ifalarapiraca.local |
```

OBS: o domínio não pode conter letras maiúsculas, espaços e/ou algum tipo de caracter especial, como "-" e "_".

 * Definir um nome para a máquina virtual como "ns1.nomedaequipeturma.labredes.ifalarapiraca.local"

```bash 
$ sudo hostnamectl set-hostname samba.emanuellylaryssa924.labredes.ifalarapiraca.local
$ sudo reboot
```

## Instalação do DNS
    
* Instalar o bind9 via apt-get  

```bash
$ sudo apt-get install bind9 dnsutils bind9-doc 
```

```Reading package lists... Done
Building dependency tree
Reading state information... Done
Suggested packages:
  bind-doc resolvconf
The following NEW packages will be installed:
  bind9 bind9-doc dnsutils
0 upgraded, 3 newly installed, 0 to remove and 0 not upgraded.
Need to get 0 B/3353 kB of archives.
After this operation, 6817 kB of additional disk space will be used.
Selecting previously unselected package bind9.
(Reading database ... 113120 files and directories currently installed.)
Preparing to unpack .../bind9_1%3a9.16.6-3ubuntu1.2_amd64.deb ...
Unpacking bind9 (1:9.16.6-3ubuntu1.2) ...
Selecting previously unselected package bind9-doc.
Preparing to unpack .../bind9-doc_1%3a9.16.6-3ubuntu1.2_all.deb ...
Unpacking bind9-doc (1:9.16.6-3ubuntu1.2) ...
Selecting previously unselected package dnsutils.
Preparing to unpack .../dnsutils_1%3a9.16.6-3ubuntu1.2_all.deb ...
Unpacking dnsutils (1:9.16.6-3ubuntu1.2) ...
Setting up bind9-doc (1:9.16.6-3ubuntu1.2) ...
Setting up dnsutils (1:9.16.6-3ubuntu1.2) ...
Setting up bind9 (1:9.16.6-3ubuntu1.2) ...
Adding group `bind' (GID 119) ...
Done.
Adding system user `bind' (UID 113) ...
Adding new user `bind' (UID 113) with group `bind' ...
Not creating home directory `/var/cache/bind'.
wrote key file "/etc/bind/rndc.key"
named-resolvconf.service is a disabled or a static unit, not starting it.
Created symlink /etc/systemd/system/bind9.service → /lib/systemd/system/na                                                                                              med.service.
Created symlink /etc/systemd/system/multi-user.target.wants/named.service                                                                                               → /lib/systemd/system/named.service.
Processing triggers for systemd (246.6-1ubuntu1.7) ...
Processing triggers for man-db (2.9.3-2) ...
Processing triggers for ufw (0.36-7) ...

```

* Verificar o status do serviço para saber se o servidor DNS está rodando como deve.

```bash
$ sudo systemctl status bind9
```

```bash
 named.service - BIND Domain Name Server
     Loaded: loaded (/lib/systemd/system/named.service; enabled; vendor preset: enabled)
     Active: `active` (running) since Tue 2021-12-21 00:02:24 UTC; 21h ago
       Docs: man:named(8)
   Main PID: 40990 (named)
      Tasks: 5 (limit: 1061)
     Memory: 22.7M
     CGroup: /system.slice/named.service
             └─40990 /usr/sbin/named -f -4 -u bind

Dec 21 21:10:06 ns1.emanuellylaryssa924.labredes.ifalarapiraca.local named[40990]: client @0x7fa2a0014580 10.0.24.3#65174 (wpad.meuintelbras.local): query (cache) 'wpad.>
Dec 21 21:10:08 ns1.emanuellylaryssa924.labredes.ifalarapiraca.local named[40990]: client @0x7fa2a0014580 10.0.24.3#61737 (signaler-pa.clients6.google.com): query (cache>
Dec 21 21:10:09 ns1.emanuellylaryssa924.labredes.ifalarapiraca.local named[40990]: client @0x7fa2a0014580 10.0.24.3#53810 (www.youtube.com): query (cache) 'www.youtube.c>
Dec 21 21:10:14 ns1.emanuellylaryssa924.labredes.ifalarapiraca.local named[40990]: client @0x7fa2a0014580 10.0.24.3#59974 (wpad.meuintelbras.local): query (cache) 'wpad.>
Dec 21 21:10:22 ns1.emanuellylaryssa924.labredes.ifalarapiraca.local named[40990]: client @0x7fa2a0014580 10.0.24.3#58316 (sadownload.mcafee.com): query (cache) 'sadownl>
Dec 21 21:10:24 ns1.emanuellylaryssa924.labredes.ifalarapiraca.local named[40990]: client @0x7fa2a0014580 10.0.24.3#60603 (download.mcafee.com): query (cache) 'download.>
Dec 21 21:10:26 ns1.emanuellylaryssa924.labredes.ifalarapiraca.local named[40990]: client @0x7fa2a0014580 10.0.24.3#65048 (ssl.gstatic.com): query (cache) 'ssl.gstatic.c>
Dec 21 21:10:49 ns1.emanuellylaryssa924.labredes.ifalarapiraca.local named[40990]: client @0x7fa2a0014580 10.0.24.3#52838 (api.twitter.com): query (cache) 'api.twitter.c>
Dec 21 21:10:52 ns1.emanuellylaryssa924.labredes.ifalarapiraca.local named[40990]: client @0x7fa2a0014580 10.0.24.3#60026 (play.google.com): query (cache) 'play.google.c>
Dec 21 21:10:54 ns1.emanuellylaryssa924.labredes.ifalarapiraca.local named[40990]: client @0x7fa2a0014580 10.0.24.3#50514 (static.meliuz.com.br): query (cache) 'static.m>
```
OBS: é possível perceber que o DNS está rodando ao verificar a palavra "active" em destaque


* Em caso de não rodar é preciso ativar o DNS.

```bash
$ sudo systemctl `enable` bind9
```

## A pasta bind

* Será preciso adicionar alguns arquivos à pasta bind para realizar a configuração do DNS Master. 

```
Realizar a checagem dos arquivos e permissões da pasta bind
```

```bash
`$ ls /etc/bind`

bind.keys  db.255    named.conf                named.conf.options
db.0       db.empty  named.conf.default-zones  rndc.key
db.127     db.local  named.conf.local          zones.rfc1918
```

```bash
`$ ls -la /etc/bind`

total 56
drwxr-sr-x  2 root bind 4096 Dec 19 18:42 .
drwxr-xr-x 98 root root 4096 Dec 19 18:42 ..
-rw-r--r--  1 root root 1991 Apr 27  2021 bind.keys
-rw-r--r--  1 root root  237 Sep 15  2020 db.0
-rw-r--r--  1 root root  271 Sep 15  2020 db.127
-rw-r--r--  1 root root  237 Sep 15  2020 db.255
-rw-r--r--  1 root root  353 Sep 15  2020 db.empty
-rw-r--r--  1 root root  270 Sep 15  2020 db.local
-rw-r--r--  1 root bind  463 Sep 15  2020 named.conf
-rw-r--r--  1 root bind  498 Sep 15  2020 named.conf.default-zones
-rw-r--r--  1 root bind  165 Sep 15  2020 named.conf.local
-rw-r--r--  1 root bind  846 Sep 15  2020 named.conf.options
-rw-r-----  1 bind bind  100 Dec 19 18:42 rndc.key
-rw-r--r--  1 root root 1317 Sep 15  2020 zones.rfc1918
```

### Zonas

* As zonas são extremamente importantes para o funcionamento do servidor DNS e são especificadas em arquivos db. Além disso, elas se dividem em "zona direta" e "zona reversa". Portanto serão criados dois arquivos db, uma para a zona direta e outro para a zona reversa e, assim, ser possível adicionar informações, como o domínio da rede e os ips.


* Os arquivos db são bancos de dados usados quando não se tem conhecimento do ip mas tem o nome da máquina. Cada zona tem o seu próprio arquivo db e, por esse motivo, a zona direta terá um e a reversa outra. 

```Criar um diretório para armazenar os arquivos de zonas
```

```bash
$ sudo mkdir /etc/bind/zones
```

```bash
`$ cd /etc/bind/
$ ls -la`
total 60
drwxr-sr-x  3 root bind 4096 Dec 19 18:48 .
drwxr-xr-x 98 root root 4096 Dec 19 18:42 ..
-rw-r--r--  1 root root 1991 Apr 27  2021 bind.keys
-rw-r--r--  1 root root  237 Sep 15  2020 db.0
-rw-r--r--  1 root root  271 Sep 15  2020 db.127
-rw-r--r--  1 root root  237 Sep 15  2020 db.255
-rw-r--r--  1 root root  353 Sep 15  2020 db.empty
-rw-r--r--  1 root root  270 Sep 15  2020 db.local
-rw-r--r--  1 root bind  463 Sep 15  2020 named.conf
-rw-r--r--  1 root bind  498 Sep 15  2020 named.conf.default-zones
-rw-r--r--  1 root bind  165 Sep 15  2020 named.conf.local
-rw-r--r--  1 root bind  846 Sep 15  2020 named.conf.options
-rw-r-----  1 bind bind  100 Dec 19 18:42 rndc.key
drwxr-sr-x  2 root bind 4096 Dec 19 18:48 `zones`
-rw-r--r--  1 root root 1317 Sep 15  2020 zones.rfc1918
```

```bash
$ cd /etc/bind/zones/
$ ls -la

drwxr-sr-x 2 root bind 4096 Dec 19 18:48 .
drwxr-sr-x 3 root bind 4096 Dec 19 18:48 

```
OBS: é possível verificar que já foi adicionado o diretório, mas, ao entrar nele, percebe-se que ainda não há arquivo db. Portanto, o próximo passo é criar.

#### Zona direta

```
* Após a criação do diretório, criar um arquivo db no diretório /etc/bind/zones a partir de uma cópia do arquivo /etc/bind/db.empty
``` 

```bash 
$cd /etc/bind/zones
$ sudo cp /etc/bind/db.empty /etc/bind/zones/db.labredes.ifalarapiraca.local 
$ ls -la
```

```
total 12
drwxr-sr-x 2 root bind 4096 Dec 19 18:57 .
drwxr-sr-x 3 root bind 4096 Dec 19 18:48 ..
-rw-r--r-- 1 root bind  353 Dec 19 18:57 db.labredes.ifalarapiraca.local
```
OBS: Veja que agora existe um arquivo db com o dommínio labredes.ifalarapiraca.local, mas é preciso mudar o domínio para EmanuellyLaryssa924.labredes.ifalarapiraca.local. Assim é preciso digitar o seguinte comando: 

```bash
$ sudo mv db.labredes.ifalarapiraca.local db.EmanuellyLaryssa924.labredes.ifalarapiraca.local  
```
Verificar: 

```bash
$ ls -la
drwxr-sr-x 2 root bind 4096 Dec 19 19:15 .
drwxr-sr-x 3 root bind 4096 Dec 19 18:48 ..
-rw-r--r-- 1 root bind  704 Dec 19 19:15 db.EmanuellyLaryssa924.labredes.ifalarapiraca.local
```










