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
	ms.date="05/05/2016"
	ms.author="cynthn"/>

# Criar sua primeira máquina virtual do Windows no Portal do Azure

Este tutorial mostra como é fácil criar uma VM do Windows em apenas alguns minutos usando o portal do Azure.

Se não tiver uma assinatura do Azure, você poderá criar uma [conta gratuita](https://azure.microsoft.com/free/) em apenas alguns minutos.

Aqui está um [passo a passo em vídeo](https://channel9.msdn.com/Blogs/Azure-Documentation-Shorts/Create-A-Virtual-Machine-Running-Windows-In-The-Azure-Preview-Portal) deste tutorial.


## Escolher a imagem de VM no Marketplace

Usamos uma imagem do Windows Server 2012 R2 Datacenter como exemplo, mas ela é apenas uma das muitas imagens que o Azure oferece. Suas opções de imagem dependem de sua assinatura. Por exemplo, imagens da área de trabalho podem estar disponíveis para [assinantes do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

1. Entre no [Portal do Azure](https://portal.azure.com).

2. No menu Hub, clique em **Novo** > **Máquinas Virtuais** > **Windows Server 2012 R2 Datacenter**.

	![Captura de tela que mostra as imagens da VM do Azure disponíveis no portal](./media/virtual-machines-windows-hero-tutorial/marketplace-new.png)


3. Na página **Windows Server 2012 R2 Datacenter**, em **Selecionar um modelo de implantação**, verifique se **Gerenciador de Recursos** está selecionado. Clique em **Criar**.

	![Captura de tela que mostra o modelo de implantação a ser selecionado para a VM](./media/virtual-machines-windows-hero-tutorial/deployment-model.png)

## Criar sua primeira máquina virtual do Windows

Depois de selecionar a imagem, você poderá usar as configurações padrão do Azure para a maior parte das configurações e criar rapidamente a máquina virtual.

1. Na folha **Básico**, insira um **Nome** para a máquina virtual. O nome deve ter de 1 a 15 caracteres e não pode conter caracteres especiais.

2. Insira um **Nome de usuário**, e uma **Senha** forte que será usada para criar uma conta local na VM. A conta local é usada para fazer logon e gerenciar a VM.

	A senha deve ter pelo menos entre 8 e 123 caracteres e ter pelo menos 3 dos seguintes: um caractere minúsculo, um caractere maiúsculo, um número e um caractere especial.


3. Selecione [Grupo de recursos ](../resource-group-overview.md#resource-groups) existente ou digite o nome de um novo. Digite um **Local** de datacenter do Azure, por exemplo **Oeste dos EUA**.

4. Quando terminar, clique em **OK** para continuar na próxima seção.

	![Captura de tela que mostra as configurações na folha Básico para configurar uma VM do Azure](./media/virtual-machines-windows-hero-tutorial/basics-blade.png)

	
5. Escolha um [tamanho](virtual-machines-windows-sizes.md) de VM e, em seguida, clique em **Selecionar** para continuar.

	![Captura de tela da folha Tamanho que mostra a VM do Azure que você pode selecionar](./media/virtual-machines-windows-hero-tutorial/size-blade.png)

6. Na folha **Configurações**, você pode alterar as opções de rede e de armazenamento. Para uma primeira máquina virtual, geralmente você pode aceitar as configurações padrão. Se tiver selecionado um tamanho de máquina virtual compatível, você poderá experimentar o Armazenamento Premium, selecionando **Premium (SSD)** em **Tipo de disco**. Quando você terminar de fazer alterações, clique em **OK**.

	![Captura de tela da folha Configurações, na qual você pode configurar os recursos ideais para uma VM do Azure](./media/virtual-machines-windows-hero-tutorial/settings-blade.png)

7. Clique em **Resumo** para examinar suas opções. Quando terminar, clique em **OK**.

	![Captura de tela da página Resumo que mostra as opções de configuração feitas para a VM do Azure](./media/virtual-machines-windows-hero-tutorial/summary-blade.png)

8. Enquanto o Azure cria a máquina virtual, você pode acompanhar o andamento em **Máquinas Virtuais**, no menu do hub.


## Conectar-se à máquina virtual e fazer logon

1.	No menu Hub, clique em **Máquinas Virtuais**.

2.	Selecione a máquina virtual na lista.

3. Na folha da máquina virtual, clique em **Conectar**. Isso cria e baixa um arquivo .rdp (Protocolo de Área de Trabalho Remota) que é como um atalho para se conectar ao seu computador. Convém salvar o arquivo em sua área de trabalho para facilitar o acesso. **Abra** esse arquivo para se conectar à sua VM.

	![Captura de tela do portal do Azure mostrando como conectar sua VM.](./media/virtual-machines-windows-hero-tutorial/connect.png)

4. Você receberá um aviso de que o .rdp é proveniente de um editor desconhecido. Isso é normal. Na janela de Área de Trabalho Remota, clique em **Conectar** para continuar.

	![Captura de tela de um aviso sobre um editor desconhecido.](./media/virtual-machines-windows-hero-tutorial/rdp-warn.png)

5. Na janela de Segurança do Windows, digite o nome de usuário e a senha para a conta local que você criou durante a criação da VM. O nome de usuário é inserido como *vmname*& #92;*nome de usuário*, depois, clique em **OK**.

	![Captura de tela da inserção do nome da VM, do nome de usuário e da senha.](./media/virtual-machines-windows-hero-tutorial/credentials.png)
 	
6.	Você receberá um aviso de que o certificado não pode ser verificado. Isso é normal. Clique em **Sim** para verificar a identidade da máquina virtual e concluir o logon.

	![Captura de tela mostrando uma mensagem sobre como verificar a identidade da VM.](./media/virtual-machines-windows-hero-tutorial/cert-warning.png)


Se você tiver problemas ao tentar se conectar, consulte [Solucionar Problemas de conexões da Área de Trabalho Remota para uma Máquina Virtual do Azure baseada no Windows](virtual-machines-windows-troubleshoot-rdp-connection.md).

Você agora pode trabalhar com a máquina virtual, como faria com qualquer outro servidor.

## Pare a VM.

É aconselhável interromper a VM para não incorrer em encargos quando você não a estiver usando. Basta clicar no botão **Parar** e, em seguida, clicar em **Sim**.

![Captura de tela mostrando o botão para interromper uma VM.](./media/virtual-machines-windows-hero-tutorial/stop-vm.png)
	
Basta clicar no botão **Iniciar** para reiniciar a VM quando você estiver pronto para usá-la novamente.


## Próximas etapas

* Você também pode experimentar [anexar um disco de dados](virtual-machines-windows-attach-disk-portal.md) à sua máquina virtual. Os discos de dados oferecem mais armazenamento para sua máquina virtual.

* Você também pode [criar uma VM do Windows usando o Powershell](virtual-machines-windows-ps-create.md) ou [criar uma máquina virtual do Linux](virtual-machines-linux-quick-create-cli.md) usando a CLI do Azure.

<!----HONumber=AcomDC_0511_2016-->