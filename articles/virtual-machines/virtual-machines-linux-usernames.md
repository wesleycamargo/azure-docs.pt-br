<properties 
	pageTitle="Selecionando nomes de usuário para Linux | Microsoft Azure" 
	description="Saiba como selecionar nomes de usuário para uma máquina virtual Linux no Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/29/2015" 
	ms.author="szark"/>



#Selecionando nomes de usuário para Linux no Azure#

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Ao provisionar uma máquina virtual do Linux no Azure, você deve especificar o nome de um usuário não raiz que possa usar posteriormente para fazer logon na VM. Você pode escolher o nome do novo usuário, ou se o provisionamento for por meio do portal clássico do Azure, você pode aceitar o nome padrão "azureuser".

Na maioria dos casos, esse usuário não existe na imagem de base e é criado durante o processo de provisionamento. Se o usuário já existir na imagem de base da máquina virtual, o agente do Linux do Azure simplesmente configura a senha (e/ou chave SSH) para o usuário com base na informação especificada ao criar a máquina virtual.

**Entretanto**, o Linux define um conjunto de nomes de usuário que não deve ser usado ao criar novos usuários. O processo de provisionamento irá **falhar** se você tentar provisionar uma máquina virtual Linux usando um usuário existente que esteja definido como um usuário com UID 0-99. Um exemplo típico é o usuário `root`, que tem o UID 0.

 - Consulte também: [Base padrão do Linux - Intervalos de ID de usuário](http://refspecs.linuxfoundation.org/LSB_4.1.0/LSB-Core-generic/LSB-Core-generic/uidrange.html)

A seguir estão nomes de usuário que devem ser evitados ao fazer o provisionamento de uma máquina virtual do Linux. Recomendamos que você **não use esses nomes de usuário**; se o fizer, o processo de provisionamento de VM poderá falhar.


## openSUSE
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


## SLES
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
- ctrusted
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

 
## CentOS
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


## Ubuntu
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

 

<!---HONumber=AcomDC_1203_2015-->