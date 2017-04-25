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
ms.date: 04/13/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 8a86cf64dcd65e74285a1073f7494eba0708ddcd
ms.lasthandoff: 04/15/2017

---

# <a name="create-a-windows-virtual-machine-with-the-azure-portal"></a>Criar uma máquina virtual do Windows com o Portal do Azure

Máquinas virtuais do Azure podem ser criadas por meio do Portal do Azure. Esse método fornece uma interface do usuário baseada em navegador para a criação e configuração de máquinas virtuais e todos os recursos relacionados. Esse Início Rápido percorre a criação de uma máquina virtual usando o portal do Azure. Depois que a implantação for concluída, nos conectamos ao servidor e instalamos o IIS.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/en-us/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon no Portal do Azure em http://portal.azure.com.

## <a name="create-virtual-machine"></a>Criar máquina virtual

2. Clique no botão **Novo** no canto superior esquerdo do Portal do Azure.

3. Selecione **Computação** na folha **Novo**, selecione **Datacenter do Windows Server 2016** na folha **Computação** e, em seguida, clique no botão **Criar**.

4. Preencha o formulário **Básico** da máquina virtual. O nome do usuário e a senha inseridos aqui são usados para fazer logon na máquina virtual. Para **Grupo de Recursos**, crie um novo. Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são criados e gerenciados coletivamente. Ao concluir, clique em **OK**.

    ![Insira as informações básicas sobre sua VM na folha do portal](./media/quick-create-portal/create-windows-vm-portal-basic-blade.png)  

5. Escolha um tamanho para a VM. Para ver mais tamanhos, selecione **Exibir todos os** ou altere o filtro **Tipo de disco com suporte**. 

    ![A captura de tela que mostra os tamanhos da VM](./media/quick-create-portal/create-windows-vm-portal-sizes.png)  

6. Na folha de configurações, selecione **Sim** em **Usar discos gerenciados**, mantenha os padrões para o restante das configurações e clique em **OK**.

7. Na página de resumo, clique em **OK** para iniciar a implantação da máquina virtual.

8. Para monitorar o status de implantação, clique na máquina virtual. A VM pode ser encontrada no painel do Portal do Azure ou selecionando **Máquinas Virtuais** no menu esquerdo. Quando a VM tiver sido criada, o status será alterado para **Em implantação** ou **Em execução**.

## <a name="open-port-80-for-web-traffic"></a>Abra a porta 80 para tráfego da Web 

Para permitir o tráfego para o IIS, você precisa abrir a porta 80 para tráfego da Web. Esta etapa orienta a criação de uma regra NSG (grupo de segurança de rede) para permitir conexões de entrada na porta 80.

1. Na folha da máquina virtual, na seção **Conceitos básicos**, clique no nome do **Grupo de recursos**.
2. Na folha do grupo de recursos, clique no **Grupo de segurança da rede** na lista de recursos. O nome NSG deve ser o nome da VM com - nsg acrescentado ao final.
3. Clique no cabeçalho **Regra de Segurança de Entrada** para abrir a lista de regras de entrada. Você deve ver uma regra de RDP já na lista.
4. Clique em **+ Adicionar** para abrir a folha **Adicionar regra de segurança de entrada**.
5. Em **Nome**, digite **IIS**. Verifique se o **Intervalo de portas** está definido para 80 e a **Ação** está definida para **Permitir**. Clique em **OK**.


## <a name="connect-to-virtual-machine"></a>Conectar-se à máquina virtual

Após concluir a implantação, crie uma conexão de área de trabalho remota com a máquina virtual.

1. Clique no botão **Conectar** na folha da máquina virtual. Um arquivo do protocolo RDP (.rdp) é criado e baixado.

    ![Portal 9](./media/quick-create-portal/quick-create-portal/portal-quick-start-9.png) 

2. Para se conectar à sua VM, abra o arquivo RDP baixado. Se solicitado, clique em **Conectar**. Em um Mac, você precisa de um cliente RDP, como este [Cliente de Área de Trabalho Remota](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) da Mac App Store.

3. Insira o nome de usuário e a senha especificados na criação da máquina virtual e clique em **Ok**.

4. Você pode receber um aviso do certificado durante o processo de logon. Clique em **Sim** ou em **Continuar** para prosseguir com o processo de conexão.


## <a name="install-iis-using-powershell"></a>Instalar o IIS usando o PowerShell

Na máquina virtual, abra um prompt do PowerShell e execute o seguinte comando para instalar o IIS e habilitar a regra de firewall local para permitir o tráfego da Web:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Exibir a página de boas-vindas do IIS

Com o IIS instalado e a porta 80 que agora está aberta na sua VM da Internet, você pode usar um navegador da Web de sua escolha para exibir a página de boas-vindas do IIS padrão. Obtenha o **Endereço IP público** da folha para a VM e use-o para visitar a página Web padrão. 

![Site do IIS padrão](./media/quick-create-powershell/default-iis-website.png) 

## <a name="delete-virtual-machine"></a>Excluir máquina virtual

Quando o grupo de recursos, a máquina virtual e todos os recursos relacionados não forem mais necessários, exclua-os. Para fazer isso, selecione o grupo de recursos na folha da máquina virtual e clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

[Tutorial de instalação de uma função e configuração do firewall](hero-role.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Explorar as amostras de CLI de implantação de VM](cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

