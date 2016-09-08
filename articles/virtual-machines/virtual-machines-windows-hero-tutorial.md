<properties
	pageTitle="Criar sua primeira VM do Windows | Microsoft Azure"
	description="Saiba como criar sua primeira máquina virtual do Windows usando o Portal do Azure"
	keywords="máquina virtual do Windows, criar uma máquina virtual, máquina virtual, configurando uma máquina virtual"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>
<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="08/29/2016"
	ms.author="cynthn"/>

# Criar sua primeira máquina virtual do Windows no Portal do Azure

Este tutorial mostra como é fácil criar uma VM do Windows em apenas alguns minutos usando o portal do Azure.

Se você não tiver uma assinatura do Azure, poderá criar uma [conta gratuita](https://azure.microsoft.com/free/) em poucos minutos.

Aqui está um [passo a passo em vídeo](https://channel9.msdn.com/Blogs/Azure-Documentation-Shorts/Create-A-Virtual-Machine-Running-Windows-In-The-Azure-Preview-Portal) deste tutorial.


## Escolher a imagem de VM no Marketplace

Usamos uma imagem do Windows Server 2012 R2 Datacenter como exemplo, mas ela é apenas uma das muitas imagens que o Azure oferece. Suas opções de imagem dependem de sua assinatura. Por exemplo, imagens da área de trabalho podem estar disponíveis para [assinantes do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

1. Entre no [Portal do Azure](https://portal.azure.com).

2. No menu Hub, clique em **Novo** > **Máquinas Virtuais** > **Windows Server 2012 R2 Datacenter**.

	![Captura de tela que mostra as imagens da VM do Azure disponíveis no portal](./media/virtual-machines-windows-hero-tutorial/marketplace-new.png)


3. Na folha **Windows Server 2012 R2 Datacenter**, em **Selecionar um modelo de implantação**, verifique se o **Gerenciador de Recursos** está selecionado. Clique em **Criar**.

	![Captura de tela que mostra o modelo de implantação a ser selecionado para a VM](./media/virtual-machines-windows-hero-tutorial/deployment-model.png)

## Criar sua primeira máquina virtual do Windows

Depois de selecionar a imagem, você poderá usar as configurações padrão do Azure para a maior parte das configurações e criar rapidamente a máquina virtual.

1. Na folha **Básico**, insira um **Nome** para a máquina virtual. O nome deve ter de 1 a 15 caracteres e não pode conter caracteres especiais.

2. Insira um **Nome de usuário**, e uma **Senha** forte que será usada para criar uma conta local na VM. A conta local é usada para fazer logon e gerenciar a VM.

	A senha deve ter entre 8 e 123 caracteres e atender três dos quatro requisitos de complexidade a seguir: um caractere minúsculo, um caractere maiúsculo, um número e um caractere especial. Confira mais sobre os [requisitos de nome de usuário e senha](virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm).


3. Selecione um [Grupo de recursos](../resource-group-overview.md#resource-groups) existente ou digite o nome de um novo. Digite um **Local** do datacenter do Azure, por exemplo, **Oeste dos EUA**.

4. Quando terminar, clique em **OK** para continuar na próxima seção.

	![Captura de tela que mostra as configurações na folha **Básico** para configurar uma VM do Azure](./media/virtual-machines-windows-hero-tutorial/basics-blade.png)

	
5. Escolha um [tamanho](virtual-machines-windows-sizes.md) de VM, em seguida, clique em **Selecionar** para continuar.

	![Captura de tela da folha Tamanho que mostra a VM do Azure que você pode selecionar](./media/virtual-machines-windows-hero-tutorial/size-blade.png)

6. Na folha **Configurações**, você pode alterar as opções de rede e armazenamento. Para este tutorial, aceite as configurações padrão. Se tiver selecionado um tamanho de máquina virtual compatível, você poderá experimentar o Armazenamento Premium, selecionando **Premium (SSD)** em **Tipo de disco**. Quando terminar de fazer as alterações, clique em **OK**.

	![Captura de tela da folha Configurações, na qual você pode configurar os recursos ideais para uma VM do Azure](./media/virtual-machines-windows-hero-tutorial/settings-blade.png)

7. Clique em **Resumo** para examinar suas opções. Quando você vir a mensagem **Validação passada**, clique em **OK**.

	![Captura de tela da página Resumo que mostra as opções de configuração feitas para a VM do Azure](./media/virtual-machines-windows-hero-tutorial/summary-blade.png)

8. Enquanto o Azure cria a máquina virtual, você pode acompanhar o andamento em **Máquinas Virtuais**, no menu do hub.


## Conectar-se à máquina virtual e fazer logon

1.	No menu Hub, clique em **Máquinas Virtuais**.

2.	Selecione a máquina virtual na lista.

3. Na folha da máquina virtual, clique em **Conectar**. Isso cria e baixa um arquivo .rdp (Protocolo de Área de Trabalho Remota) que é como um atalho para se conectar ao seu computador. Convém salvar o arquivo em sua área de trabalho para facilitar o acesso. **Abra** esse arquivo para conectar sua VM.

	![Captura de tela do portal do Azure mostrando como conectar sua VM.](./media/virtual-machines-windows-hero-tutorial/connect.png)

4. Você receberá um aviso de que o .rdp é proveniente de um editor desconhecido. Isso é normal. Na janela de Área de Trabalho Remota, clique em **Conectar** para continuar.

	![Captura de tela de um aviso sobre um editor desconhecido.](./media/virtual-machines-windows-hero-tutorial/rdp-warn.png)

5. Na janela de Segurança do Windows, digite o nome de usuário e a senha para a conta local que você criou durante a criação da VM. O nome de usuário é inserido como *vmname*& #92;*nome de usuário*, depois, clique em **OK**.

	![Captura de tela da inserção do nome da VM, do nome de usuário e da senha.](./media/virtual-machines-windows-hero-tutorial/credentials.png)
 	
6.	Você receberá um aviso de que o certificado não pode ser verificado. Isso é normal. Clique em **Sim** para verificar a identidade da máquina virtual e concluir o logon.

	![Captura de tela mostrando uma mensagem sobre como verificar a identidade da VM.](./media/virtual-machines-windows-hero-tutorial/cert-warning.png)


Se você tiver problemas ao tentar conectar, consulte [Solucionar problemas de conexões da Área de Trabalho Remota com uma Máquina Virtual do Azure baseada no Windows](virtual-machines-windows-troubleshoot-rdp-connection.md).

Você agora pode trabalhar com a máquina virtual, como faria com qualquer outro servidor.

## Instalar o IIS em sua VM

Agora que você está conectado à VM, iremos instalar uma função de servidor para que possa experimentar mais.

1. Abra o **Gerenciador de Servidores** se ainda não estiver aberto. Clique no menu **Iniciar** e clique em **Gerenciador de Servidores**.
2. Em **Gerenciador de Servidores**, selecione **Servidor Local** no painel à esquerda.
3. No menu, selecione **Gerenciar** > **Adicionar Funções e Recursos**.
4. No Assistente para Adicionar Funções e Recursos, na página **Tipo de Instalação**, escolha **Instalação baseada em funções ou recursos**, em seguida, clique em **Próximo**.

	![Captura de tela mostrando a guia Assistente para Adicionar Funções e Recursos para o Tipo de Instalação.](./media/virtual-machines-windows-hero-tutorial/role-wizard.png)

5. Selecione a VM no pool de servidores e clique em **Próximo**.
6. Na página **Funções do Servidor**, selecione **Web Server (IIS)**.

	![Captura de tela mostrando a guia Assistente para Adicionar Funções e Recursos para as Funções do Servidor.](./media/virtual-machines-windows-hero-tutorial/add-iis.png)

7. No menu pop-up para adicionar os recursos necessários para o IIS, verifique se **Incluir ferramentas de gerenciamento** está selecionado e, em seguida, clique em **Adicionar Recursos**. Quando o pop-up fechar, clique em **Próximo** no assistente.

	![Captura de tela mostrando o menu pop-up para confirmar a adição da função do IIS.](./media/virtual-machines-windows-hero-tutorial/confirm-add-feature.png)

8. Na página de recursos, clique em **Próximo**.
9. Na página **Função do Servidor Web (IIS)**, clique em **Próximo**.
10. Na página **Serviços da Função**, clique em **Próximo**.
11. Na página **Confirmação**, clique em **Instalar**.
12. Quando a instalação for concluída, clique em **Fechar** no assistente.



## Abrir a porta 80 

Para sua VM aceitar o tráfego de entrada na porta 80, você precisa adicionar uma regra de entrada ao grupo de segurança da rede.

1. Abra o [Portal do Azure](https://portal.azure.com).
2. Em **Máquinas virtuais**, selecione a VM criada.
3. Nas configurações das máquinas virtuais, selecione **Interfaces de rede** e, em seguida, selecione a interface de rede existente.

	![Captura de tela mostrando a configuração da máquina virtual para as interfaces de rede.](./media/virtual-machines-windows-hero-tutorial/network-interface.png)

4. Em **Essentials** da interface de rede, clique no **Grupo de segurança da rede**.

	![Captura de tela mostrando a seção Essentials para a interface de rede.](./media/virtual-machines-windows-hero-tutorial/select-nsg.png)

5. Na folha **Essentials** do NSG, você deve ter uma regra de entrada padrão existente para **default-allow-rdp**, que permite fazer logon na VM. Você adicionará outra regra de entrada para permitir o tráfego do IIS. Clique em **Regra de segurança de entrada**.

	![Captura de tela mostrando a seção Essentials para o NSG.](./media/virtual-machines-windows-hero-tutorial/inbound.png)

6. Em **Regras de segurança de entrada**, clique em **Adicionar**.

	![Captura de tela mostrando o botão para adicionar uma regra de segurança.](./media/virtual-machines-windows-hero-tutorial/add-rule.png)

7. Em **Regras de segurança de entrada**, clique em **Adicionar**. Digite **80** no intervalo de portas e verifique se **Permitir** está selecionado. Quando terminar, clique em **OK**.

	![Captura de tela mostrando o botão para adicionar uma regra de segurança.](./media/virtual-machines-windows-hero-tutorial/port-80.png)
 
Para obter mais informações sobre os NSGs, regras de entrada e saída, consulte [Permitir o acesso externo à sua VM usando o portal do Azure](virtual-machines-windows-nsg-quickstart-portal.md)
 
## Conectar ao site do IIS padrão

1. No portal do Azure, clique em **Máquinas virtuais**, em seguida, selecione sua VM.
2. Na folha **Essentials**, copie o **endereço IP público**.

	![Captura de tela mostrando onde encontrar o endereço IP público de sua VM.](./media/virtual-machines-windows-hero-tutorial/ipaddress.png)

2. Abra um navegador e na barra de endereços, digite seu endereço IP público como, http://<publicIPaddress>, e clique em **Enter** para ir para o endereço.
3. Seu navegador deverá ir para a página Web do IIS padrão e a página será semelhante a esta:

	![Captura de tela mostrando a aparência da página padrão do IIS em um navegador.](./media/virtual-machines-windows-hero-tutorial/iis-default.png)


## Pare a VM.

É aconselhável interromper a VM para não incorrer em encargos quando você não a estiver usando. Basta clicar no botão **Parar**, em seguida, clicar em **Sim**.

![Captura de tela mostrando o botão para interromper uma VM.](./media/virtual-machines-windows-hero-tutorial/stop-vm.png)
	
Basta clicar no botão **Iniciar** para reiniciar a VM quando você estiver pronto para usá-la novamente.


## Próximas etapas

* Você também pode experimentar [anexar um disco de dados](virtual-machines-windows-attach-disk-portal.md) à sua máquina virtual. Os discos de dados oferecem mais armazenamento para sua máquina virtual.

* Você também pode [criar uma VM do Windows usando o Powershell](virtual-machines-windows-ps-create.md) ou [criar uma máquina virtual do Linux](virtual-machines-linux-quick-create-cli.md) usando a CLI do Azure.

* Se você estiver interessado na automatização das implantações, dê uma olhada em [Criar uma máquina virtual do Windows usando um modelo do Resource Manager](virtual-machines-windows-ps-template.md).

<!---HONumber=AcomDC_0831_2016-->