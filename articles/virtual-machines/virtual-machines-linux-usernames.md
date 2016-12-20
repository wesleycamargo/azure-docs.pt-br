---
title: "Selecionando nomes de usuário para Linux | Microsoft Docs"
description: "Saiba como selecionar nomes de usuário para uma máquina virtual Linux no Azure."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 33b50c97-92f1-46c9-a623-e37f67459c5c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: szark
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: eaea0ce42537789278d3a0476b261e6a750dde2b


---
# <a name="selecting-user-names-for-linux-on-azure"></a>Selecionando nomes de usuário para Linux no Azure
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Ao provisionar uma máquina virtual do Linux no Azure, você deve especificar o nome de um usuário não raiz que possa usar posteriormente para fazer logon na VM. Você pode escolher o nome do novo usuário, ou se o provisionamento for por meio do portal clássico do Azure, você pode aceitar o nome padrão "azureuser".

Na maioria dos casos, esse usuário não existe na imagem de base e é criado durante o processo de provisionamento. Se o usuário já existir na imagem de base da máquina virtual, o agente do Linux do Azure simplesmente configurará a senha e/ou chave SSH para o usuário com base nas informações especificadas ao criar a VM.

**Entretanto**, o Linux define um conjunto de nomes de usuário que não deve ser usado ao criar novos usuários. O processo de provisionamento irá **falhar** se você tentar provisionar uma máquina virtual Linux usando um usuário existente que esteja definido como um usuário com UID 0-99. Um exemplo típico é o usuário `root` , que tem o UID 0.

* Consulte também: [Base padrão do Linux - Intervalos de ID de usuário](http://refspecs.linuxfoundation.org/LSB_4.1.0/LSB-Core-generic/LSB-Core-generic/uidrange.html)

A seguir está uma lista de usuários do sistema internos e comuns para o CentOS e Ubuntu que você deve evitar usar ao provisionar uma máquina virtual do Linux no Azure. Essa lista é apenas um exemplo. Consulte a documentação de sua distribuição para garantir que o nome de usuário escolhido não entra em conflito com um usuário do sistema existente.

## <a name="centos"></a>CentOS
* abrt
* adm
* audio
* bin
* cdrom
* cgred
* daemon
* dbus
* dialout
* dip
* disk
* floppy
* ftp
* ftp
* games
* gopher
* haldaemon
* halt
* kmem
* lock
* lp
* mail
* man
* mem
* nfsnobody
* nobody
* ntp
* operator
* oprofile
* postdrop
* postfix
* qpidd
* root
* rpc
* rpcuser
* saslauth
* shutdown
* slocate
* sshd
* stapdev
* stapusr
* sync
* sys
* tape
* test
* tcpdump
* tty
* users
* utempter
* utmp
* uucp
* vcsa
* video
* wheel

## <a name="ubuntu"></a>Ubuntu
* adm
* admin
* audio
* backup
* bin
* cdrom
* crontab
* daemon
* dialout
* dip
* disk
* fax
* floppy
* fuse
* games
* gnats
* irc
* kmem
* landscape
* libuuid
* list
* lp
* mail
* man
* messagebus
* mlocate
* netdev
* news
* nobody
* nogroup
* operator
* plugdev
* proxy
* root
* sasl
* shadow
* src
* ssh
* sshd
* staff
* sudo
* sync
* sys
* syslog
* tape
* tty
* users
* utmp
* uucp
* video
* voice
* whoopsie
* www-data




<!--HONumber=Nov16_HO3-->


