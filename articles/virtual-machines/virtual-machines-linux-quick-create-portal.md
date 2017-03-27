---
title: "Início Rápido do Azure – Criar Portal da VM | Microsoft Docs"
description: "Início Rápido do Azure – Criar Portal da VM"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/10/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 348407b57bbb3329d7d27a6f38623e052aecc58b
ms.lasthandoff: 03/14/2017

---

# <a name="create-a-linux-virtual-machine-with-the-azure-portal"></a>Criar uma máquina virtual Linux com o Portal do Azure

Máquinas virtuais do Azure podem ser criadas por meio do Portal do Azure. Esse método fornece uma interface do usuário baseada em navegador para a criação e configuração de VMs e todos os recursos do Azure relacionados.

Antes de começar, são necessárias tanto uma chave SSH pública quanto uma privada. Para obter informações detalhadas sobre a criação de chaves SSH para o Azure, consulte [Criar chaves SSH do Azure](./virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="log-in-to-azure"></a>Fazer logon no Azure 

Faça logon no Portal do Azure em http://portal.azure.com.

## <a name="create-virtual-machine"></a>Criar máquina virtual

2. Clique no botão **Novo** no canto superior esquerdo do Portal do Azure.

3. Selecione **Computação** na folha **Novo**, selecione **Ubuntu Server 16.04 LTS** na folha **Computação** e, em seguida, clique no botão **Criar**.

4. Preencha o formulário **Básico** da máquina virtual. Para **Tipo de autenticação**, SSH é recomendado. Ao colar na sua **chave pública SSH**, tome cuidado para remover qualquer espaço em branco à direita ou à esquerda. Para **Grupo de Recursos**, crie um novo. Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são criados e gerenciados coletivamente. Ao concluir, clique em **OK**.

    ![Insira as informações básicas sobre sua VM na folha do portal](./media/virtual-machine-quick-start/create-vm-portal-basic-blade.png)  

5. Escolha um tamanho para a VM e clique em **Selecionar**. 

    ![Selecione um tamanho para a sua VM na folha do portal](./media/virtual-machine-quick-start/create-vm-portal-size-blade.png)

6. Na folha de configurações, selecione **Sim** em **Usar discos gerenciados**, mantenha os padrões para o restante das configurações e clique em **OK**.

7. Na página de resumo, clique em **OK** para iniciar a implantação da máquina virtual.

## <a name="connect-to-virtual-machine"></a>Conectar-se à máquina virtual

Após a implantação, crie uma conexão SSH com a máquina virtual.

1. Clique na máquina virtual. A VM pode ser encontrada na tela inicial do Portal do Azure ou selecionando **Máquinas Virtuais** no menu esquerdo.

2. Clique no botão **Conectar** . O botão conectar exibe uma cadeia de conexão SSH que pode ser usada para conectar-se à máquina virtual.

    ![Portal 9](./media/virtual-machine-quick-start/portal-quick-start-9.png) 

3. Execute o seguinte comando para criar uma sessão SSH. Substitua a cadeia de conexão com aquela que você copiou do Portal do Azure.

```bash 
ssh <replace with IP address>
```
## <a name="delete-virtual-machine"></a>Excluir máquina virtual

Quando o grupo de recursos, a máquina virtual e todos os recursos relacionados não forem mais necessários, exclua-os. Para fazer isso, selecione o grupo de recursos na folha da máquina virtual e clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

[Tutorial Criar máquinas virtuais altamente disponíveis](./virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Explorar as amostras de CLI de implantação de VM](./virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

