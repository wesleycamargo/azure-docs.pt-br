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
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/21/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: bcfd830a5e2f39f36460990cae7e84b04d9a5fbb
ms.lasthandoff: 03/22/2017

---

# <a name="create-a-linux-virtual-machine-with-the-azure-portal"></a>Criar uma máquina virtual Linux com o Portal do Azure

Máquinas virtuais do Azure podem ser criadas por meio do Portal do Azure. Esse método fornece uma interface do usuário baseada em navegador para a criação e configuração de máquinas virtuais e todos os recursos relacionados. Esse Início Rápido percorre a criação de uma máquina virtual usando o portal do Azure. 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-ssh-key-pair"></a>Criar o par de chaves SSH

Você precisa de um par de chaves SSH para concluir este início rápido. Se você já tiver um par de chave SSH, essa etapa pode ser ignorada. Se você estiver usando um computador Windows, siga as instruções descritas [aqui](./virtual-machines-linux-ssh-from-windows.md). 

Em um shell Bash, execute este comando e siga as orientações da tela. A saída do comando inclui o nome do arquivo da chave pública. O conteúdo desse arquivo é necessário ao criar a máquina virtual.

```bash
ssh-keygen -t rsa -b 2048
```

## <a name="log-in-to-azure"></a>Fazer logon no Azure 

Faça logon no Portal do Azure em http://portal.azure.com.

## <a name="create-virtual-machine"></a>Criar máquina virtual

1. Clique no botão **Novo** no canto superior esquerdo do Portal do Azure.

2. Selecione **Computação** na folha **Novo**, selecione **Ubuntu Server 16.04 LTS** na folha **Computação** e, em seguida, clique no botão **Criar**.

3. Preencha o formulário **Básico** da máquina virtual. Para **Tipo de autenticação**, selecione **SSH**. Ao colar na sua **chave pública SSH**, tome cuidado para remover qualquer espaço em branco à direita ou à esquerda. Para **Grupo de Recursos**, crie um novo. Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são criados e gerenciados coletivamente. Ao concluir, clique em **OK**.

    ![Insira as informações básicas sobre sua VM na folha do portal](./media/virtual-machine-quick-start/create-vm-portal-basic-blade.png)  

4. Escolha um tamanho para a VM e clique em **Selecionar**. 

    ![Selecione um tamanho para a sua VM na folha do portal](./media/virtual-machine-quick-start/create-vm-portal-size-blade.png)

5. Na folha de configurações, selecione **Sim** em **Usar discos gerenciados**, mantenha os padrões para o restante das configurações e clique em **OK**.

6. Na página de resumo, clique em **OK** para iniciar a implantação da máquina virtual.

7. Para monitorar o status de implantação, clique na máquina virtual. A VM pode ser encontrada no painel do Portal do Azure ou selecionando **Máquinas Virtuais** no menu esquerdo. Quando a VM tiver sido criada, o status será alterado para **Em implantação** ou **Em execução**.

## <a name="connect-to-virtual-machine"></a>Conectar-se à máquina virtual

Após a implantação, crie uma conexão SSH com a máquina virtual.

1. Clique no botão **Conectar** na folha da máquina virtual. O botão conectar exibe uma cadeia de conexão SSH que pode ser usada para conectar-se à máquina virtual.

    ![Portal 9](./media/virtual-machine-quick-start/portal-quick-start-9.png) 

2. Execute o seguinte comando para criar uma sessão SSH. Substitua a cadeia de conexão com aquela que você copiou do Portal do Azure.

```bash 
ssh <replace with IP address>
```
## <a name="delete-virtual-machine"></a>Excluir máquina virtual

Quando o grupo de recursos, a máquina virtual e todos os recursos relacionados não forem mais necessários, exclua-os. Para fazer isso, selecione o grupo de recursos na folha da máquina virtual e clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

[Tutorial Criar máquinas virtuais altamente disponíveis](./virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Explorar as amostras de CLI de implantação de VM](./virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

