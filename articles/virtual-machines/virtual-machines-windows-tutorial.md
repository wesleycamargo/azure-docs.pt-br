<properties
	pageTitle="Criar uma máquina virtual que execute o Windows no portal do Azure | Microsoft Azure"
	description="Saiba como criar uma VM do Azure ou um computador virtual que execute o Windows usando o Azure Marketplace no portal do Azure"
	keywords="vm do azure, criar uma máquina virtual, computador virtual, configurando uma máquina virtual"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>
<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="08/14/2015"
	ms.author="cynthn"/>

# Criar uma máquina virtual executando o Windows no portal do Azure#

> [AZURE.SELECTOR]
- [Azure Portal - Windows](virtual-machines-windows-tutorial.md)
- [Azure PowerShell](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md)
- [Azure PowerShell - Template](virtual-machines-create-windows-powershell-resource-manager-template.md)
- [Azure Portal - Linux](virtual-machines-linux-tutorial-portal-rm.md)
- [Azure CLI](virtual-machines-linux-tutorial.md)

<br>



Este tutorial mostra como é fácil e rápido criar uma máquina virtual do Azure no portal do Azure. Usaremos uma imagem do Windows Server 2012 R2 Datacenter como exemplo para criar a máquina virtual, mas essa é apenas uma das muitas imagens oferecidas pelo Azure. Suas opções de imagem dependem de sua assinatura. Por exemplo, imagens de desktop podem estar disponíveis para assinantes do MSDN.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico. Para obter detalhes sobre o Gerenciador de Recursos, consulte [Visão geral do Gerenciador de Recursos do Azure](resource-group-overview.md).

Você também pode criar máquinas virtuais usando suas próprias imagens, com modelos do Gerenciador de Recursos ou com ferramentas de automação. Para saber mais sobre esse e outros métodos, consulte [Diferentes maneiras de criar uma máquina virtual do Windows](virtual-machines-windows-choices-create-vm.md).

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Passo a passo em vídeo

Aqui está um passo a passo deste tutorial.

[AZURE.VIDEO create-a-virtual-machine-running-windows-in-the-azure-preview-portal]

## Selecionar a imagem da máquina virtual

1. Entre no Portal do Azure.

2. No menu Hub, clique em **Novo** > **Computação** > **Windows Server 2012 R2 Datacenter**.

	![Captura de tela que mostra as imagens de VM do Azure disponíveis no portal de visualização](./media/virtual-machines-windows-tutorial/marketplace_new.png)

	>[AZURE.TIP]Para localizar outras imagens, clique em **Marketplace**e pesquise ou filtre os itens disponíveis.

3. Na página **Windows Server 2012 R2 Datacenter**, em **Selecionar um modelo de implantação**, selecione **Gerenciador de Recursos**. Clique em **Criar**.

	![Captura de tela que mostra o modelo de implantação a ser selecionado para uma VM do Azure](./media/virtual-machines-windows-tutorial/marketplace_search_select.png)

## Criar uma máquina virtual

Depois de selecionar a imagem, você poderá usar as configurações padrão do Azure para a maior parte das configurações e criar rapidamente a máquina virtual.

1. Na folha **Criar máquina virtual**, clique em **Noções básicas**. Insira um **Nome** desejado para a máquina virtual, o **Nome de usuário** administrativo e uma **Senha** forte. Se você tiver mais de uma assinatura, especifique a assinatura certa para a nova máquina virtual, bem como um **Grupo de recursos** novo ou existente e um **Local** para o data center do Azure.

	![Captura de tela que mostra as configurações básicas a serem definidas para uma VM do Azure](./media/virtual-machines-windows-tutorial/create_vm_basics.PNG)

	>[AZURE.NOTE]O **Nome de usuário** se refere à conta administrativa que você usa para gerenciar o servidor. Crie uma senha que seja difícil para outras pessoas adivinharem mas da qual você possa se lembrar. **Você precisará do nome do usuário e da senha para fazer logon na máquina virtual**.

2. Clique em **Tamanho** e selecione um tamanho de máquina virtual apropriado para suas necessidades. Cada tamanho especifica o número de núcleos de computação, memória e outros recursos, como suporte para Armazenamento Premium, o que afetará o preço. O Azure recomenda automaticamente determinados tamanhos, dependendo da imagem escolhida.

	![Captura de tela que mostra os tamanhos de VM do Azure que você pode selecionar](./media/virtual-machines-windows-tutorial/create_vm_size.PNG)

	>[AZURE.NOTE]O Armazenamento Premium está disponível para máquinas virtuais de série DS em determinadas regiões. O Armazenamento Premium é a melhor opção de armazenamento para cargas de trabalho de uso intensivo de dados, como um banco de dados. Para obter detalhes, confira [Armazenamento Premium: armazenamento de alto desempenho para as cargas de trabalho das máquinas virtuais do Azure](storage-premium-storage-preview-portal.md).

3. Clique em **Configurações** para ver as configurações de rede e o armazenamento da nova máquina virtual. Para uma primeira máquina virtual, geralmente você pode aceitar as configurações padrão. Se tiver selecionado um tamanho de máquina virtual compatível, você poderá experimentar o Armazenamento Premium, selecionando **Premium (SSD)** em **Tipo de disco**.

	![Captura de tela que mostra os recursos opcionais que você pode configurar para uma VM do Azure](./media/virtual-machines-windows-tutorial/create_vm_settings.PNG)

6. Clique em **Resumo** para examinar suas opções de configuração. Quando terminar a revisão ou a atualização das configurações, clique em **Criar**.

	![Captura de tela que mostra o resumo das opções de configuração feitas para a VM do Azure](./media/virtual-machines-windows-tutorial/create_vm_summary.PNG)

8. Enquanto o Azure cria a máquina virtual, é possível acompanhar o andamento em **Notificações**, no menu Hub. Após o Azure criar a máquina virtual, você a verá no Quadro Inicial, a menos que tenha desmarcado **Fixar no Quadro Inicial** na folha **Criar máquina virtual**.

## Faça logon na máquina virtual

Após criar a máquina virtual, você poderá fazer logon para gerenciar as configurações e os aplicativos que serão executados nela.

>[AZURE.NOTE]Para mais dicas de requisitos e solução de problemas, consulte [Conecte-se à uma máquina virtual Azure com RDP ou SSH](https://msdn.microsoft.com/library/azure/dn535788.aspx).

1. Se ainda não tiver feito isso, entre no portal do Azure.

2. Clique em sua máquina virtual no Quadro inicial. Se precisar encontrá-la, clique em **Procurar Tudo** > **Recente** ou **Procurar Tudo** > **Máquinas virtuais**. Em seguida, selecione sua máquina virtual na lista.

3. Na folha da máquina virtual, clique em **Conectar**.

	![Captura de tela que mostra onde encontrar o botão Conectar na folha VM do Azure](./media/virtual-machines-windows-tutorial/connect_vm_portal.png)

4. Clique em **Abrir** para usar o arquivo Protocolo RDP criado automaticamente para a máquina virtual do Windows Server.

5. Clique em **Conectar**.

6. Digite o nome de usuário e a senha especificados na criação da máquina virtual e clique em **OK**.

7. Clique em **Sim** para verificar a identidade da máquina virtual.

	Você agora pode trabalhar com a máquina virtual, como faria com qualquer outro servidor.

## Próximas etapas

* Use o Azure PowerShell e CLI do Azure para [localizar e selecionar imagens da máquina virtual](resource-groups-vm-searching.md).
* Automatize a máquina virtual e a implantação e o gerenciamento da carga de trabalho usando o [Gerenciador de Recursos do Azure](virtual-machines-how-to-automate-azure-resource-manager.md) e os [modelos do Gerenciador de Recursos do Azure](http://azure.microsoft.com/documentation/templates/).

<!---HONumber=AcomDC_1217_2015-->