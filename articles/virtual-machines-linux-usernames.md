<properties linkid="services-linux-user-names" urlDisplayName="Nomes de usuário no Linux" pageTitle="Selecionando nomes de usuário para Linux no Azure" metaKeywords="" description="Saiba como selecionar nomes de usuário para uma máquina virtual Linux no Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Selecionando nomes de usuário para Linux no Azure" authors="" solutions="" manager="" editor="" />





#Selecionando nomes de usuário para Linux no Azure#

Ao criar uma instância de máquina virtual para imagens Linux no Azure, você tem a capacidade de selecionar o nome de usuário para provisionar.

O Linux define um conjunto de nomes de usuário que você não é possível usar. É muito importante que você **evite usar esses nomes**. Se usar um desses nomes de usuário, você não poderá provisionar a imagem.

Além disso, a API de gerenciamento do serviço retornará um erro: Falha ao criar usuário *xxxxxx*. Isso também será verdadeiro se você usar um nome de usuário já existente na imagem como resultado de uma operação de captura anterior que não desprovisionou o nome de usuário já criado na imagem. 

A seguir estão os nomes de usuário que não é possível usar. 



OpenSUSE
-------------------
- abrt
- adm
- audio
- bin
- bin
- cdrom
- cgred
- daemon
- dbus
- dialout
- dip
- disk
- floppy
- ftp
- games
- gopher
- haldaemon
- halt
- kmem
- lock
- lp
- mail
- man
- mem
- nfsnobody
- nobody
- ntp
- operator
- oprofile
- postdrop
- postfix
- qpidd
- root
- rpc
- rpcuser
- saslauth
- shutdown
- slocate
- sshd
- stapdev
- stapusr
- sync
- sys
- tape
- test
- tcpdump
- tty
- users
- utempter
- utmp
- uucp
- vcsa
- video
- wheel

SLES
------------------
- audio
- bin
- cdrom
- console
- daemon
- dialout
- disk
- floppy
- ftp
- ftp
- games
- haldaemon
- kmem
- lp
- lp
- mail
- maildrop
- man
- messagebus
- modem
- news
- news
- nobody
- nogroup
- polkituser
- postfix
- public
- root
- shadow
- sshd
- sys
- test
- trusted
- tty
- users
- utmp
- uucp
- uuidd
- video
- wheel
- www
- wwwrun
- xok

 
CentOS
-------------------
- abrt
- adm
- audio
- bin
- cdrom
- cgred
- daemon
- dbus
- dialout
- dip
- disk
- floppy
- ftp
- ftp
- games
- gopher
- haldaemon
- halt
- kmem
- lock
- lp
- mail
- man
- mem
- nfsnobody
- nobody
- ntp
- operator
- oprofile
- postdrop
- postfix
- qpidd
- root
- rpc
- rpcuser
- saslauth
- shutdown
- slocate
- sshd
- stapdev
- stapusr
- sync
- sys
- tape
- test
- tcpdump
- tty
- users
- utempter
- utmp
- uucp
- vcsa
- video
- wheel

UBUNTU
-------------------
- adm
- admin
- audio
- backup
- bin
- cdrom
- crontab
- daemon
- dialout
- dip
- disk
- fax
- floppy
- fuse
- games
- gnats
- irc
- kmem
- landscape
- libuuid
- list
- lp
- mail
- man
- messagebus
- mlocate
- netdev
- news
- nobody
- nogroup
- operator
- plugdev
- proxy
- root
- sasl
- shadow
- src
- ssh
- sshd
- staff
- sudo
- sync
- sys
- syslog
- tape
- tty
- users
- utmp
- uucp
- video
- voice
- whoopsie
- www-data

