---
title: Início Rápido - Criar uma VM Windows no Portal do Azure | Microsoft Docs
description: Neste início rápido, você aprende a usar o portal do Azure para criar uma máquina virtual Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/09/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: c28686c3b6494a0cf8938d39ab9b8338de7aa0c1
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012573"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-the-azure-portal"></a>Início Rápido: Crie uma máquina virtual do Windows no Portal do Azure

As máquinas virtuais (VM) do Azure podem ser criadas por meio do Portal do Azure. Esse método fornece uma interface de usuário baseada em navegador para criar as VMS seus recursos relacionados. Este início rápido mostra como usar portal do Azure para implantar uma VM (máquina virtual) no Azure que executa o Windows Server 2016. Para ver a VM em ação, você habilita o protocolo RDP na VM e instala o servidor Web do IIS.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon no Portal do Azure em https://portal.azure.com.

## <a name="create-virtual-machine"></a>Criar máquina virtual

1. Escolha **Criar um recurso** no canto superior esquerdo do portal do Azure.

2. Na caixa de pesquisa acima da lista de recursos do Azure Marketplace, procure e selecione **Windows Server 2016 Datacenter**, em seguida escolha **Criar**.

3. Forneça um nome VM, como *myVM*, deixe o tipo de disco como *SSD*, em seguida, forneça um nome de usuário, como *azureuser*. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](faq.md#what-are-the-password-requirements-when-creating-a-vm).

    ![Insira as informações básicas sobre sua VM na folha do portal](./media/quick-create-portal/create-windows-vm-portal-basic-blade.png)

5. Escolha **Criar novo** grupo de recurso, em seguida, forneça um nome, como *myResourceGroup* Escolha seu **local** desejado, em seguida, selecione **Ok**.

4. Selecione um tamanho para a VM. Você pode filtrar por *Tipo de computação* ou *Tipo de disco*, por exemplo. Uma VM sugerida é do tamanho *D2s_v3*.

    ![A captura de tela que mostra os tamanhos da VM](./media/quick-create-portal/create-windows-vm-portal-sizes.png)

5. Em **Configurações**, deixe todos os padrões e selecione **OK**.

6. Na página **Resumo**, selecione {0}Criar{0} para iniciar a implantação da máquina virtual.

7. A VM será fixada ao painel do portal do Azure. Depois que a implantação for concluída, a folha de resumo da VM abrirá automaticamente.

## <a name="connect-to-virtual-machine"></a>Conectar-se à máquina virtual

Inicie uma conexão da área de trabalho remota para a máquina virtual. Estas instruções ensinam a se conectar aàsua VM de um computador com Windows. Em um Mac, você precisa de um cliente RDP, como este [Cliente de Área de Trabalho Remota](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) da Mac App Store.

1. Clique no botão **Conectar** na página de propriedades da máquina virtual. 

    ![Conecte-se a uma VM do Azure no portal](./media/quick-create-portal/quick-create-portal/portal-quick-start-9.png)
    
2. Na página **Conectar-se à máquina virtual**, mantenha as opções padrão para conectar-se ao nome DNS na porta 3389 e clique em **Realizar o download do arquivo RDP**.

2. Abra o arquivo RDP baixado e clique em **Conectar** quando solicitado. 

3. Na janela **Segurança do Windows**, selecione **Mais opções** e **Usar uma conta diferente**. Digite o nome do usuário *vmname*\**, insira a senha que você criou para a máquina virtual e, sem seguida, clique em **OK**.

4. Você pode receber um aviso do certificado durante o processo de logon. Clique em **Sim** ou em **Continuar** para prosseguir com o processo de conexão.

## <a name="install-web-server"></a>Instalar servidor Web

Para ver a VM em ação, instale o servidor Web do IIS. Abra um prompt do PowerShell na VM e execute o seguinte comando:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Quando terminar, feche a conexão RDP com a VM.

## <a name="open-port-80-for-web-traffic"></a>Abra a porta 80 para tráfego da Web

Um Grupo de Segurança de Rede (NSG) protege o tráfego de entrada e saída. Quando uma VM é criada no portal do Azure, uma regra de entrada é criada na porta 3389 para conexões RDP. Como essa VM hospeda um servidor Web, uma regra NSG precisa ser criada para a porta 80.

1. Na página de visão geral de VM, selecione **Rede**.
2. A lista de regras de entrada e saídas existentes é apresentada. Escolha a regra **Adicionar porta de entrada**.
3. Selecione a opção **Básico** opção na parte superior, em seguida, escolha *HTTP* da lista de serviços disponíveis. A porta 80, uma prioridade e o nome, são fornecidos.
4. Para criar a regra, selecione **Adicionar**

## <a name="view-the-iis-welcome-page"></a>Exibir a página de boas-vindas do IIS

Com o IIS instalado e a porta 80 agora aberta na VM pela Internet, use um navegador da Web de sua escolha para exibir a página inicial padrão do IIS. Use o endereço IP público da VM obtido em uma etapa anterior. O seguinte exemplo mostra o site padrão do IIS:

![Site do IIS padrão](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando o grupo de recursos, a máquina virtual e todos os recursos relacionados não forem mais necessários, exclua-os. Para fazer isso, selecione o grupo de recursos da máquina virtual, selecione **Excluir**, em seguida, confirme o nome do grupo de recursos para excluir.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou uma máquina virtual simples, abriu uma porta de rede para o tráfego da Web e instalou um servidor Web básico. Para saber mais sobre máquinas virtuais do Azure, continue o tutorial para VMs do Windows.

> [!div class="nextstepaction"]
> [Tutoriais de máquina virtual do Windows Azure](./tutorial-manage-vm.md)
