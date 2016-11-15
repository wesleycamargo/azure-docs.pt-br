---
title: Criar uma VM do Linux usando o portal do Azure | Microsoft Docs
description: Criar uma VM do Linux usando o Portal do Azure.
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: cc5dc395-dc54-4402-8804-2bb15aba8ea2
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: hero-article
ms.date: 10/28/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b1446cd8892e14988ff428eaa03233f8e9aefb8a


---
# <a name="create-a-linux-vm-on-azure-using-the-portal"></a>Criar uma VM do Linux no Azure usando o Portal
Este artigo mostra a você como usar o [Portal do Azure](https://portal.azure.com/) para criar uma Máquina Virtual do Linux.

Esses requisitos são:

* [uma conta do Azure](https://azure.microsoft.com/pricing/free-trial/)
* [arquivos de chave SSH pública e privada](virtual-machines-linux-mac-create-ssh-keys.md)

## <a name="sign-in"></a>Entrar
Conectado ao portal do Azure com sua identidade de conta do Azure, clique em **+ Novo** no canto superior esquerdo:

![tela1](../media/virtual-machines-linux-quick-create-portal/screen1.png)

## <a name="choose-vm"></a>Escolher VM
Clique em **Máquinas Virtuais** no **Marketplace**, em **Ubuntu Server 14.04 LTS** na lista de imagens **Aplicativos em Destaque**.  Verifique na parte inferior se o modelo de implantação é `Resource Manager` e clique em **Criar**.

![tela2](../media/virtual-machines-linux-quick-create-portal/screen2.png)

## <a name="enter-vm-options"></a>Inserir opções de VM
Na página **Noções Básicas** , insira:

* um nome para a máquina virtual
* um nome de usuário para o usuário administrador
* o Tipo de Autenticação definido como **chave pública SSH**
* sua Chave pública SSH como uma cadeia de caracteres (de seu diretório `~/.ssh/` )
* um nome de grupo de recursos ou selecione um grupo existente

e clique em **OK** para continuar e escolher o tamanho da VM. Ela deve ter a seguinte captura de tela:

![tela3](../media/virtual-machines-linux-quick-create-portal/screen3.png)

## <a name="choose-vm-size"></a>Escolher tamanho da VM
Escolha o tamanho **DS1**, que instala o Ubuntu em uma SSD Premium, e clique em **Selecionar** para definir as configurações.

![tela4](../media/virtual-machines-linux-quick-create-portal/screen4.png)

## <a name="storage-and-network"></a>Armazenamento e rede
Em **Configurações**, mantenha os valores Armazenamento e Rede como padrão e clique em **OK** para exibir o resumo.  Observe que o tipo de disco foi definido para SSD Premium escolhendo DS1; o **S** indica SSD.

![tela5](../media/virtual-machines-linux-quick-create-portal/screen5.png)

## <a name="confirm-vm-settings-and-launch"></a>Confirmar as configurações e o início de máquina virtual
Confirme as configurações para sua nova VM Ubuntu e clique em **OK**.

![tela6](../media/virtual-machines-linux-quick-create-portal/screen6.png)

## <a name="find-the-vm-nic"></a>Localizar a NIC da VM
Abra o Painel do Portal, em **Interfaces de rede** , escolha sua NIC

![tela7](../media/virtual-machines-linux-quick-create-portal/screen7.png)

## <a name="find-the-public-ip"></a>Localizar o IP público
Abra o menu Endereços IP Públicos nas configurações de NIC

![tela8](../media/virtual-machines-linux-quick-create-portal/screen8.png)

## <a name="ssh-to-the-vm"></a>SSH para a VM
SSH para o IP público usando sua chave pública SSH.  Em uma estação de trabalho Mac ou Linux, você pode fazer SSH diretamente do Terminal. Se você estiver em uma estação de trabalho do Windows, precisará usar PuTTY, MobaXTerm ou Cygwin SSH para Linux.  Se você ainda não tiver feito isso, aqui está um documento que prepara sua estação de trabalho do Windows para SSH para Linux.

[Como usar chaves SSH com o Windows no Azure](virtual-machines-linux-ssh-from-windows.md)

```
ssh -i ~/.ssh/azure_id_rsa ubuntu@13.91.99.206
```

## <a name="next-steps"></a>Próximas etapas
Agora que você criou uma VM Linux rapidamente a ser usada para fins de teste ou demonstração, Para criar uma VM do Linux personalizada para sua infraestrutura, você pode seguir qualquer um destes artigos.

* [Criar uma VM do Linux no Azure usando Modelos](virtual-machines-linux-cli-deploy-templates.md)
* [Criar uma VM do Linux Protegida por SSH no Azure usando Modelos](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
* [Criar uma VM do Linux usando a CLI do Azure](virtual-machines-linux-create-cli-complete.md)




<!--HONumber=Nov16_HO2-->


