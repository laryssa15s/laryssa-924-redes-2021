# Implementação DNS Master

## Objetivo: 

    * Instalação e configuração do servidor DNS master com o Bind9

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

```
Tabela 2: Definições do domínio:
|      Apelido      |               NOME                                   |
|:------------------|:-----------------------------------------------------|
| gateway (gw)      | gw.emanuellylaryssa924.labredes.ifalarapiraca.local  |
| nameserver1 (ns1) | ns1.emanuellylaryssa924.labredes.ifalarapiraca.local |
| nameserver2 (ns2) | ns2.emanuellylaryssa924.labredes.ifalarapiraca.local |
| dupla (vm2)       | vm2.emanuellylaryssa924.labredes.ifalarapiraca.local |
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

* Verificar o status do serviço:

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


* Em caso de não rodar é preciso ativar o DNS

```bash
$ sudo systemctl `enable` bind9
```

###





