---
title: Instalar o IIS em sua primeira VM do Windows | Microsoft Docs
description: "Teste sua primeira máquina virtual do Windows instalando o IIS e abrindo a porta 80 usando o Portal do Azure."
keywords: 
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 6334ea45-db6b-4e08-abb5-1f98927ebc34
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 63a3ffb0e0c52643994f95dbf588f3534c8de06f


---
# <a name="experiment-with-installing-a-role-on-your-windows-vm"></a>Experimentar a instalação de uma função em sua VM do Windows
Quando você tiver sua primeira VM (máquina virtual) em funcionamento, passe para a instalação de software e serviços. Para este tutorial, vamos usar o Gerenciador de Servidores na VM do Windows Server para instalar o IIS. Em seguida, criaremos um NSG (Grupo de Segurança de Rede) usando o Portal do Azure para abrir a porta 80 para tráfego IIS. 

Se você ainda não criou sua primeira VM, volte para [Criar sua primeira máquina virtual do Windows no portal do Azure](virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) antes de continuar com este tutorial.

## <a name="make-sure-the-vm-is-running"></a>Verificar se a VM está em execução
1. Abra o [Portal do Azure](https://portal.azure.com).
2. No menu hub, clique em **Máquinas Virtuais**. Selecione a máquina virtual na lista.
3. Se o status for **Parado (Desalocado)**, clique no botão **Iniciar** na folha **Essentials** da VM. Se o status indicar **Em Execução**, passe para a próxima etapa.

## <a name="connect-to-the-virtual-machine-and-sign-in"></a>Conectar-se à máquina virtual e entrar
1. No menu hub, clique em **Máquinas Virtuais**. Selecione a máquina virtual na lista.
2. Na folha da máquina virtual, clique em **Conectar**. Isso cria e baixa um arquivo .rdp (Protocolo de Área de Trabalho Remota) que é como um atalho para se conectar ao seu computador. Convém salvar o arquivo em sua área de trabalho para facilitar o acesso. **Abra** esse arquivo para conectar sua VM.
   
    ![Captura de tela do portal do Azure mostrando como conectar sua VM](./media/virtual-machines-windows-hero-tutorial/connect.png)
3. Você receberá um aviso de que o .rdp é proveniente de um editor desconhecido. Isso é normal. Na janela de Área de Trabalho Remota, clique em **Conectar** para continuar.
   
    ![Captura de tela de um aviso sobre um editor desconhecido](./media/virtual-machines-windows-hero-tutorial/rdp-warn.png)
4. Na janela de Segurança do Windows, digite o nome de usuário e a senha para a conta local que você criou durante a criação da VM. O nome de usuário é inserido como *vmname*&#92;*nome de usuário*, depois, clique em **OK**.
   
    ![Captura de tela da inserção do nome da VM, do nome de usuário e da senha](./media/virtual-machines-windows-hero-tutorial/credentials.png)
5. Você receberá um aviso de que o certificado não pode ser verificado. Isso é normal. Clique em **Sim** para verificar a identidade da máquina virtual e concluir o logon.
   
   ![Captura de tela mostrando uma mensagem sobre como verificar a identidade da VM](./media/virtual-machines-windows-hero-tutorial/cert-warning.png)

Se você tiver problemas ao tentar conectar, consulte [Solucionar problemas de conexões da Área de Trabalho Remota com uma Máquina Virtual do Azure baseada no Windows](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="install-iis-on-your-vm"></a>Instalar o IIS em sua VM
Agora que você está conectado à VM, iremos instalar uma função de servidor para que possa experimentar mais.

1. Abra o **Gerenciador de Servidores** se ainda não estiver aberto. Clique no menu **Iniciar** e em **Gerenciador de Servidores**.
2. No **Gerenciador de Servidores**, selecione **Servidor Local** no painel à esquerda. 
3. No menu, selecione **Gerenciar** > **Adicionar Funções e Recursos**.
4. No Assistente para Adicionar Funções e Recursos, na página **Tipo de Instalação**, escolha **Instalação baseada em função ou recurso**, em seguida, clique em **Próximo**.
   
    ![Captura de tela mostrando a guia Assistente para Adicionar Funções e Recursos para o Tipo de Instalação](./media/virtual-machines-windows-hero-tutorial/role-wizard.png)
5. Selecione a VM no pool de servidores e clique em **Próximo**.
6. Na página **Funções do Servidor**, selecione **Servidor Web (IIS)**.
   
    ![Captura de tela mostrando a guia Assistente para Adicionar Funções e Recursos para as Funções do Servidor](./media/virtual-machines-windows-hero-tutorial/add-iis.png)
7. No menu pop-up para adicionar recursos necessários para o IIS, verifique se a opção **Incluir ferramentas de gerenciamento** está selecionada, em seguida, clique em **Adicionar Recursos**. Quando o pop-up fechar, clique em **Próximo** no assistente.
   
    ![Captura de tela mostrando o menu pop-up para confirmar a adição da função do IIS](./media/virtual-machines-windows-hero-tutorial/confirm-add-feature.png)
8. Na página de recursos, clique em **Próximo**.
9. Na página **Função do Servidor Web (IIS)**, clique em **Próximo**. 
10. Na página **Serviços da Função**,clique em **Próximo**. 
11. Na página **Confirmação**, clique em **Instalar**. 
12. Quando a instalação for concluída, clique em **Fechar** no assistente.

## <a name="open-port-80"></a>Abrir a porta 80
Para sua VM aceitar o tráfego de entrada na porta 80, você precisa adicionar uma regra de entrada ao grupo de segurança da rede. 

1. Abra o [Portal do Azure](https://portal.azure.com).
2. Em **Máquinas virtuais** , selecione a VM criada.
3. Nas configurações das máquinas virtuais, selecione **Interfaces de rede** e, em seguida, selecione a interface de rede existente.
   
    ![Captura de tela mostrando a configuração da máquina virtual para as interfaces de rede](./media/virtual-machines-windows-hero-tutorial/network-interface.png)
4. Em **Essentials** para a interface de rede, clique no **Grupo de Segurança da Rede**.
   
    ![Captura de tela mostrando a seção Essentials para a interface de rede](./media/virtual-machines-windows-hero-tutorial/select-nsg.png)
5. Na folha **Essentials** do NSG, você deve ter uma regra de entrada padrão existente para **default-allow-rdp**, que permite fazer logon na VM. Você adicionará outra regra de entrada para permitir o tráfego do IIS. Clique em **Regra de segurança de entrada**.
   
    ![Captura de tela mostrando a seção Essentials para o NSG](./media/virtual-machines-windows-hero-tutorial/inbound.png)
6. Nas **Regras de segurança de entrada**, clique em **Adicionar**.
   
    ![Captura de tela mostrando o botão para adicionar uma regra de segurança](./media/virtual-machines-windows-hero-tutorial/add-rule.png)
7. Nas **Regras de segurança de entrada**, clique em **Adicionar**. Digite **80** no intervalo de portas e verifique se **Permitir** está selecionado. Quando terminar, clique em **OK**.
   
    ![Captura de tela mostrando o botão para adicionar uma regra de segurança](./media/virtual-machines-windows-hero-tutorial/port-80.png)

Para obter mais informações sobre os NSGs, regras de entrada e saída, consulte [Permitir o acesso externo à sua VM usando o portal do Azure](virtual-machines-windows-nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="connect-to-the-default-iis-website"></a>Conectar ao site do IIS padrão
1. No portal do Azure, clique em **Máquinas virtuais** , em seguida, selecione sua VM.
2. Na folha **Essentials**, copie seu **Endereço IP público**.
   
    ![Captura de tela mostrando onde encontrar o endereço IP público da VM](./media/virtual-machines-windows-hero-tutorial/ipaddress.png)
3. Abra um navegador e na barra de endereços, digite seu endereço IP público assim: http://<publicIPaddress> e clique em **Enter** para ir para esse endereço.
4. Seu navegador deve abrir a página Web padrão do IIS. É algo semelhante a isto:
   
    ![Captura de tela mostrando a aparência da página padrão do IIS em um navegador](./media/virtual-machines-windows-hero-tutorial/iis-default.png)

## <a name="next-steps"></a>Próximas etapas
* Você também pode experimentar [anexar um disco de dados](virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) à sua máquina virtual. Os discos de dados oferecem mais armazenamento para sua máquina virtual.




<!--HONumber=Nov16_HO3-->


