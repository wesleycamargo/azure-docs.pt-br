---
title: "Início Rápido do Azure – Portal Criar VM do Windows | Microsoft Docs"
description: "Início Rápido do Azure – Portal Criar VM do Windows"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/21/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: a13ac5ab425ccbbe53d77cb9f5a8ebf02d009370
ms.lasthandoff: 03/22/2017

---

# <a name="create-a-windows-virtual-machine-with-the-azure-portal"></a>Criar uma máquina virtual do Windows com o Portal do Azure

Máquinas virtuais do Azure podem ser criadas por meio do Portal do Azure. Esse método fornece uma interface do usuário baseada em navegador para a criação e configuração de máquinas virtuais e todos os recursos relacionados. Esse Início Rápido percorre a criação de uma máquina virtual usando o portal do Azure. 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-azure"></a>Fazer logon no Azure 

Faça logon no Portal do Azure em http://portal.azure.com.

## <a name="create-virtual-machine"></a>Criar máquina virtual

2. Clique no botão **Novo** no canto superior esquerdo do Portal do Azure.

3. Selecione **Computação** na folha **Novo**, selecione **Datacenter do Windows Server 2016** na folha **Computação** e, em seguida, clique no botão **Criar**.

4. Preencha o formulário **Básico** da máquina virtual. O nome do usuário e a senha inseridos aqui são usados para fazer logon na máquina virtual. Para **Grupo de Recursos**, crie um novo. Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são criados e gerenciados coletivamente. Ao concluir, clique em **OK**.

    ![Insira as informações básicas sobre sua VM na folha do portal](./media/virtual-machine-quick-start/create-windows-vm-portal-basic-blade.png)  

5. Escolha um tamanho para a VM e clique em **Selecionar**. 

6. Na folha de configurações, selecione **Sim** em **Usar discos gerenciados**, mantenha os padrões para o restante das configurações e clique em **OK**.

7. Na página de resumo, clique em **OK** para iniciar a implantação da máquina virtual.

8. Para monitorar o status de implantação, clique na máquina virtual. A VM pode ser encontrada no painel do Portal do Azure ou selecionando **Máquinas Virtuais** no menu esquerdo. Quando a VM tiver sido criada, o status será alterado para **Em implantação** ou **Em execução**.

## <a name="connect-to-virtual-machine"></a>Conectar-se à máquina virtual

Após concluir a implantação, crie uma conexão de área de trabalho remota com a máquina virtual.

1. Clique no botão **Conectar** na folha da máquina virtual. Um arquivo do protocolo RDP (.rdp) é criado e baixado.

    ![Portal 9](./media/virtual-machine-quick-start/portal-quick-start-9.png) 

2. Para se conectar à sua VM, abra o arquivo RDP baixado. Se solicitado, clique em **Conectar**. Em um Mac, você precisa de um cliente RDP, como este [Cliente de Área de Trabalho Remota](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) da Mac App Store.

3. Insira o nome de usuário e a senha especificados na criação da máquina virtual e clique em **Ok**.

4. Você pode receber um aviso do certificado durante o processo de logon. Clique em **Sim** ou em **Continuar** para prosseguir com o processo de conexão.

## <a name="delete-virtual-machine"></a>Excluir máquina virtual

Quando o grupo de recursos, a máquina virtual e todos os recursos relacionados não forem mais necessários, exclua-os. Para fazer isso, selecione o grupo de recursos na folha da máquina virtual e clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

[Tutorial de instalação de uma função e configuração do firewall](./virtual-machines-windows-hero-role.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Explorar as amostras de CLI de implantação de VM](./virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
