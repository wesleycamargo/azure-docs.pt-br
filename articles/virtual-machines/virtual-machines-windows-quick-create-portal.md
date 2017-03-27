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
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/14/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 56e9deea4e070a99e47b7de7c5686526a4528fb6
ms.lasthandoff: 03/15/2017

---

# <a name="create-a-windows-virtual-machine-with-the-azure-portal"></a>Criar uma máquina virtual do Windows com o Portal do Azure

Máquinas virtuais do Azure podem ser criadas por meio do Portal do Azure. Esse método fornece uma interface do usuário baseada em navegador para a criação e configuração de VMs e todos os recursos do Azure relacionados.

## <a name="log-in-to-azure"></a>Fazer logon no Azure 

Faça logon no Portal do Azure em http://portal.azure.com.

## <a name="create-virtual-machine"></a>Criar máquina virtual

2. Clique no botão **Novo** no canto superior esquerdo do Portal do Azure.

3. Selecione **Computação** na folha **Novo**, selecione **Datacenter do Windows Server 2016** na folha **Computação** e, em seguida, clique no botão **Criar**.

4. Preencha o formulário **Básico** da máquina virtual. Para **Grupo de Recursos**, crie um novo. Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são criados e gerenciados coletivamente. Ao concluir, clique em **OK**.

    ![Insira as informações básicas sobre sua VM na folha do portal](./media/virtual-machine-quick-start/create-windows-vm-portal-basic-blade.png)  

5. Escolha um tamanho para a VM e clique em **Selecionar**. 

6. Na folha de configurações, selecione **Sim** em **Usar discos gerenciados**, mantenha os padrões para o restante das configurações e clique em **OK**.

7. Na página de resumo, clique em **OK** para iniciar a implantação da máquina virtual.

8. Para monitorar o status de implantação, clique na máquina virtual. A VM pode ser encontrada no painel do Portal do Azure ou selecionando **Máquinas Virtuais** no menu esquerdo. Quando a VM tiver sido criada, o status será alterado para **Em implantação** ou **Em execução**.

## <a name="connect-to-virtual-machine"></a>Conectar-se à máquina virtual

Após a implantação, crie uma conexão de área de trabalho remota com a máquina virtual.

1. Clique no botão **Conectar** na folha da máquina virtual. Isso cria e baixa um arquivo .rdp (Protocolo de Área de Trabalho Remota) que é como um atalho para se conectar ao seu computador. Abra esse arquivo para conectar-se à sua VM.

    ![Portal 9](./media/virtual-machine-quick-start/portal-quick-start-9.png) 

3. Clique em **Conectar** na janela de conexão de área de trabalho remota.

    ![Portal 10](./media/virtual-machine-quick-start/portal-quick-start-10.png) 

4. Insira o nome de usuário e a senha especificados na criação da máquina virtual e clique em **OK**.

5. Você pode receber um aviso de certificado, clique em **Sim** para prosseguir com a conexão.

## <a name="delete-virtual-machine"></a>Excluir máquina virtual

Quando o grupo de recursos, a máquina virtual e todos os recursos relacionados não forem mais necessários, exclua-os. Para fazer isso, selecione o grupo de recursos na folha da máquina virtual e clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

[Tutorial de instalação de uma função e configuração do firewall](./virtual-machines-windows-hero-role.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Explorar as amostras de CLI de implantação de VM](./virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
