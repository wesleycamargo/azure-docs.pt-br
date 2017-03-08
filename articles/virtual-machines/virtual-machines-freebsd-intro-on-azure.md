---
title: "Introdução ao FreeBSD no Azure | Microsoft Docs"
description: "Saiba como usar máquinas virtuais FreeBSD no Azure"
services: virtual-machines-linux
documentationcenter: 
author: KylieLiang
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 32b87a5f-d024-4da0-8bf0-77e233d1422b
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/09/2017
ms.author: kyliel
translationtype: Human Translation
ms.sourcegitcommit: 8c96cacadb34a3d4eca1fe523d8a159c69a0ebe3
ms.openlocfilehash: 01c855972d66d8ae2e975b206791ab8f9abcec41
ms.lasthandoff: 02/24/2017


---
# <a name="introduction-to-freebsd-on-azure"></a>Introdução ao FreeBSD no Azure
Este tópico fornece uma visão geral da execução da máquina virtual FreeBSD no Azure.

## <a name="overview"></a>Visão geral
O FreeBSD para Microsoft Azure é um sistema operacional avançado usado para capacitar servidores modernos, desktops e plataformas incorporadas.

A Microsoft Corporation está disponibilizando imagens do FreeBSD no Azure com o [Agente convidado da VM Azure](https://github.com/Azure/WALinuxAgent/) pré-configurado. No momento, as seguintes versões do FreeBSD são oferecidas como imagens pela Microsoft:

- FreeBSD 10.3-RELEASE
- FreeBSD 11.0-RELEASE

O agente é responsável pela comunicação entre a VM do FreeBSD e a malha do Azure para operações como provisionamento da VM no primeiro uso (nome de usuário, senha, chave SSH, nome do host, etc.) e habilitação da funcionalidade para extensões de VM seletivas.

Para versões futuras do FreeBSD, a estratégia é manter-se atualizado e disponibilizar as versões mais recentes logo após a publicação pela equipe de engenharia de versão do FreeBSD.

## <a name="deploying-a-freebsd-virtual-machine"></a>Implantando uma máquina virtual FreeBSD
A implantação de uma máquina virtual do FreeBSD é um processo simples que usa uma imagem do Azure Marketplace:

- [FreeBSD 10.3 no Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)
- [FreeBSD 11.0 no Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd110/)

## <a name="vm-extensions-for-freebsd"></a>Extensões de VM para FreeBSD
Os itens a seguir têm suporte de extensões de VM no FreeBSD.

### <a name="vmaccess"></a>VMAccess
A extensão [VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) pode:

* Redefinir a senha do usuário sudo original.
* Criar um novo usuário sudo com a senha especificada.
* Definir a chave pública do host com a chave fornecida.
* Redefinir a chave pública do host fornecida durante o provisionamento da VM se a chave do host não for fornecida.
* Abrir a porta (22) SSH e restaurar o sshd_config se reset_ssh estiver definido como true.
* Remover o usuário existente.
* Verificar os discos.
* Reparar o disco adicionado.

### <a name="customscript"></a>CustomScript
A extensão [CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) pode:

* Se for fornecida, baixar os scripts personalizados do Armazenamento do Azure ou do armazenamento público externo (por exemplo, GitHub).
* Executar o script de ponto de entrada.
* Oferecer suporte ao comando embutido.
* Converter automaticamente o estilo newline do Windows em scripts de Shell e Python.
* Remover automaticamente BOM em scripts de Shell e Python.
* Proteger dados confidenciais em CommandToExecute.

[!NOTE]A VM do FreeBSD só oferece suporte ao CustomScript versão 1. x até o momento.  

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Autenticação: nomes de usuário, senhas e chaves SSH
Ao criar uma máquina virtual FreeBSD usando o Portal do Azure, você deve fornecer um nome de usuário, uma senha ou uma chave pública SSH.
Os nomes de usuário para implantação de uma máquina virtual do FreeBSD no Azure não devem corresponder aos nomes de contas do sistema (UID <100) já presentes na máquina virtual (“root”, por exemplo).
Atualmente, há suporte apenas para a chave RSA SSH. Uma chave SSH multilinha deve começar com `---- BEGIN SSH2 PUBLIC KEY ----` e terminar com `---- END SSH2 PUBLIC KEY ----`.

## <a name="obtaining-superuser-privileges"></a>Obtendo privilégios de superusuário
A conta de usuário especificada durante a implantação da instância de máquina virtual no Azure é uma conta privilegiada. O pacote do sudo foi instalado na imagem de FreeBSD publicada.
Depois de fazer logon usando essa conta de usuário, você poderá executar comandos como raiz usando a sintaxe de comando.

    $ sudo <COMMAND>

Opcionalmente, é possível obter um shell root usando `sudo -s`.

## <a name="known-issues"></a>Problemas conhecidos
1. O [Agente convidado da VM Azure](https://github.com/Azure/WALinuxAgent/) versão 2.2.2 tem um [problema conhecido] (https://github.com/Azure/WALinuxAgent/pull/517) que causa a falha de provisionamento da VM do FreeBSD no Azure. A correção foi capturada pelo [Agente Convidado da VM do Azure](https://github.com/Azure/WALinuxAgent/) versão 2.2.3 e versões posteriores. 

## <a name="next-steps"></a>Próximas etapas
* Acesse o [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd110/) para criar uma VM FreeBSD.
* Se você quiser levar seu próprio FreeBSD para o Azure, veja [Criar e carregar um VHD FreeBSD no Azure](./virtual-machines-linux-classic-freebsd-create-upload-vhd.md).

