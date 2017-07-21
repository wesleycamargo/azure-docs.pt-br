---
title: "Introdução ao Linux no Azure | Microsoft Docs"
description: "Saiba como usar máquinas virtuais Linux no Azure."
services: virtual-machines-linux
documentationcenter: python
author: szarkos
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: b13bf305-87bf-4df3-815e-e8f6337aa6ea
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: szark
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: 7bd0c5549a2e1f592681760d5ef464b9570ca4ab
ms.contentlocale: pt-br
ms.lasthandoff: 06/03/2017


---
# <a name="introduction-to-linux-on-azure"></a>Introdução ao Linux no Azure
Este tópico apresenta uma visão geral de alguns aspectos do uso de máquinas virtuais Linux na nuvem do Azure. Implantar uma máquina virtual Linux é um processo simples usando uma imagem da galeria.

## <a name="authentication-usernames-passwords-and-ssh-keys"></a>Autenticação: nomes de usuário, senhas e chaves SSH.
Ao criar uma máquina virtual Linux usando o portal do Azure, você deve fornecer um nome de usuário, senha ou uma chave pública SSH. A escolha de um nome de usuário para a implantação de uma máquina virtual Linux no Azure está sujeita à seguinte restrição: nomes de contas (UID <100) do sistema já presentes na máquina virtual não são permitidos - root por exemplo.

* Veja [Criar uma máquina virtual que executa Linux](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Veja [Como usar SSH com Linux no Azure](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="obtaining-superuser-privileges-using-sudo"></a>Obtendo privilégios de superusuário usando o `sudo`
A conta de usuário especificada durante a implantação da instância de máquina virtual no Azure é uma conta privilegiada. Essa conta é configurada pelo Agente Linux do Azure para poder elevar privilégios para raiz (conta de superusuário) usando o utilitário `sudo` . Depois de fazer logon usando essa conta de usuário, você poderá executar comandos como raiz usando a sintaxe de comando:

    # sudo <COMMAND>

Opcionalmente, você pode obter um shell de root usando **sudo -s**.

* Veja [Usando privilégios de raiz em máquinas virtuais Linux do Azure](use-root-privileges.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="firewall-configuration"></a>Configuração do firewall
O Azure fornece um filtro de pacote de entrada que restringe a conectividade a portas especificadas no portal do Azure. Por padrão, a única porta permitida é SSH. Você pode abrir o acesso a portas adicionais em sua máquina virtual Linux configurando pontos de extremidade no portal do Azure:

* Veja: [Como instalar pontos de extremidade em uma máquina virtual](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

As imagens do Linux na Galeria do Azure não habilitam o firewall *iptables* por padrão. Se desejado, o firewall poderá ser configurado para fornecer filtragem adicional.

## <a name="hostname-changes"></a>Alterações de nome do host
Ao implantar uma instância de uma imagem do Linux inicialmente, você precisa fornecer um nome de host para a máquina virtual. Quando a máquina virtual está em execução, esse nome de host é publicado nos servidores DNS da plataforma, de forma que as várias máquinas virtuais conectadas entre si possam executar pesquisas de endereço IP usando nomes de host.

Se forem desejadas alterações no nome do host depois da implantação de uma máquina virtual, use o comando

    # sudo hostname <newname>

O Agente Linux do Azure inclui uma funcionalidade para detectar automaticamente essa alteração de nome e configurar corretamente a máquina virtual para persistir nessa alteração e, além disso, publicá-la nos servidores DNS da plataforma.

* [Guia do usuário do agente Linux para o Azure](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="cloud-init"></a>Inicialização de nuvem
As imagens do **Ubuntu** e **CoreOS** utilizam inicialização de nuvem no Azure, que fornece recursos adicionais para inicializar uma máquina virtual.

* [Como injetar dados personalizados](../windows/classic/inject-custom-data.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Dados personalizados e inicialização de nuvem no Microsoft Azure](https://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/)
* [Criar partições de troca do Azure usando a nuvem Init](https://wiki.ubuntu.com/AzureSwapPartitions)
* [Como usar o CoreOS no Azure](https://coreos.com/os/docs/latest/booting-on-azure.html)

## <a name="virtual-machine-image-capture"></a>Captura de imagem da máquina virtual
O Azure oferece a possibilidade de capturar o estado de uma máquina virtual existente em uma imagem que pode ser usada depois na implantação de instâncias de máquina virtual. O Agente Linux do Azure pode ser usado para reverter algumas das personalizações que foram realizadas durante o processo de provisionamento. Você pode seguir as seguintes etapas para capturar uma máquina virtual como uma imagem:

1. Execute **waagent-deprovision** para desfazer a personalização do provisionamento. Ou **waagent -deprovision+user** para, opcionalmente, excluir a conta de usuário especificada durante o provisionamento e todos os dados associados.
2. Desligue a máquina virtual.
3. Clique em **Capturar** no portal clássico do Azure ou use as ferramentas Powershell ou CLI para capturar a máquina virtual como uma imagem.
   
   * Veja: [Como capturar uma máquina virtual Linux para ser usada como um modelo](classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

## <a name="attaching-disks"></a>Anexando discos
Cada máquina virtual tem um *disco de recursos* anexado. Como os dados em um disco de recurso talvez não sejam duráveis nas reinicializações, ele costuma ser usado por aplicativos e processos em execução na máquina virtual para o armazenamento de dados transitório e **temporário** . Ele também é usado para armazenar páginas ou trocar arquivos para o sistema operacional.

No Linux, o disco de recurso é normalmente gerenciado pelo agente do Linux do Azure e montado automaticamente em **/mnt/resource** (ou **/mnt** nas imagens do Ubuntu).

> [!NOTE]
> Observe que o disco de recurso é um disco **temporário** e pode ser excluído e reformatado quando a VM é reinicializada.
> 
> 

No Linux, o disco de dados pode ser nomeado pelo kernel como `/dev/sdc`, e os usuários precisarão particionar, formatar e montar esse recurso. Isso é abordado passo a passo no tutorial: [Como anexar um disco de dados a uma máquina virtual](../windows/classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

* **Veja também:** [Configurar o Software RAID no Linux](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) & [Configurar LVM em uma VM do Linux no Azure](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


