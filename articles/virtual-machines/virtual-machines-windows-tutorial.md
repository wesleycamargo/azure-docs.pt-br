<properties
	pageTitle="Criar uma máquina virtual do Windows no Portal do Azure | Microsoft Azure"
	description="Saiba como criar uma máquina virtual do Windows ou uma máquina virtual usando o Azure Marketplace no portal do Azure"
	keywords="máquina virtual do Windows, criar uma máquina virtual, máquina virtual, configurando uma máquina virtual"
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
	ms.date="01/07/2016"
	ms.author="cynthn"/>

# Criar uma máquina virtual do Windows no Portal do Azure#

> [AZURE.SELECTOR]
- [Portal - Windows](virtual-machines-windows-tutorial.md)
- [PowerShell](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md)
- [PowerShell - Modelo](virtual-machines-create-windows-powershell-resource-manager-template.md)
- [Portal - Linux](virtual-machines-linux-tutorial-portal-rm.md)
- [CLI](virtual-machines-linux-tutorial.md)

Este tutorial mostra como é fácil e rápido criar uma máquina virtual do Windows no portal do Azure. Usaremos uma imagem do Windows Server 2012 R2 Datacenter como exemplo para criar a máquina virtual, mas essa é apenas uma das muitas imagens oferecidas pelo Azure. Suas opções de imagem dependem de sua assinatura. Por exemplo, imagens de desktop podem estar disponíveis para assinantes do MSDN.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.

<br>

>[AZURE.TIP] Ao usar o portal, se quiser que sua VM faça parte de um conjunto de disponibilidade, você precisará criar o conjunto de disponibilidade antes ou durante a criação da primeira VM no conjunto de disponibilidade. Para obter mais informações sobre como criar e usar conjuntos de disponibilidade, confira [Gerenciar a disponibilidade de máquinas virtuais](virtual-machines-manage-availability.md).

Você também pode criar máquinas virtuais usando suas próprias imagens, com modelos do Gerenciador de Recursos ou com ferramentas de automação. Para saber mais sobre esse e outros métodos, consulte [Diferentes maneiras de criar uma máquina virtual do Windows](virtual-machines-windows-choices-create-vm.md).

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Passo a passo em vídeo

Aqui está um passo a passo deste tutorial.

[AZURE.VIDEO create-a-virtual-machine-running-windows-in-the-azure-preview-portal]

## Selecionar a imagem da máquina virtual do Windows

1. Entre no Portal do Azure.

2. No menu Hub, clique em **Novo** > **Computação** > **Windows Server 2012 R2 Datacenter**.

	![Captura de tela que mostra as imagens de VM do Azure disponíveis no portal de visualização](./media/virtual-machines-windows-tutorial/marketplace_new.png)

	>[AZURE.TIP] Para localizar outras imagens, clique em **Marketplace**e pesquise ou filtre os itens disponíveis.

3. Na página **Windows Server 2012 R2 Datacenter**, em **Selecionar um modelo de implantação**, selecione **Gerenciador de Recursos**. Clique em **Criar**.

	![Captura de tela que mostra o modelo de implantação a ser selecionado para uma VM do Azure](./media/virtual-machines-windows-tutorial/marketplace_search_select.png)

## Criar sua primeira máquina virtual do Windows

Depois de selecionar a imagem, você poderá usar as configurações padrão do Azure para a maior parte das configurações e criar rapidamente a máquina virtual.

1. Na folha **Criar máquina virtual**, clique em **Noções básicas**.

2. Insira um **Nome** para a máquina virtual. O nome não pode conter caracteres especiais.

3. Insira um **Nome de usuário** administrativo e uma **Senha** forte. A senha deve ter entre 8 e 123 caracteres e ter pelo menos 3 dos seguintes: um caractere minúsculo, um caractere maiúsculo, um número e um caractere especial. **Você precisará do nome do usuário e da senha para fazer logon na máquina virtual**.

4. Se você tiver mais de uma assinatura, especifique a assinatura certa para a nova máquina virtual, bem como um **Grupo de recursos** novo ou existente e um **Local** para o data center do Azure.

	![Captura de tela que mostra as configurações básicas a serem definidas para uma VM do Azure](./media/virtual-machines-windows-tutorial/create_vm_basics.PNG)

	
2. Clique em **Tamanho** e selecione um tamanho de máquina virtual apropriado para suas necessidades. Cada tamanho especifica o número de núcleos de computação, memória e outros recursos, como suporte para Armazenamento Premium, o que afetará o preço. O Azure recomenda automaticamente determinados tamanhos, dependendo da imagem escolhida.

	![Captura de tela que mostra os tamanhos de VM do Azure que você pode selecionar](./media/virtual-machines-windows-tutorial/create_vm_size.PNG)

	>[AZURE.NOTE] O Armazenamento Premium está disponível para máquinas virtuais de série DS em determinadas regiões. O Armazenamento Premium é a melhor opção de armazenamento para cargas de trabalho de uso intensivo de dados, como um banco de dados. Para obter detalhes, confira [Armazenamento Premium: armazenamento de alto desempenho para as cargas de trabalho das máquinas virtuais do Azure](storage-premium-storage.md).

3. Clique em **Configurações** para ver as configurações de rede e o armazenamento da nova máquina virtual. Para uma primeira máquina virtual, geralmente você pode aceitar as configurações padrão. Se tiver selecionado um tamanho de máquina virtual compatível, você poderá experimentar o Armazenamento Premium, selecionando **Premium (SSD)** em **Tipo de disco**.

	![Captura de tela que mostra os recursos opcionais que você pode configurar para uma VM do Azure](./media/virtual-machines-windows-tutorial/create_vm_settings.PNG)

6. Clique em **Resumo** para examinar suas opções de configuração. Quando terminar a revisão ou a atualização das configurações, clique em **Criar**.

	![Captura de tela que mostra o resumo das opções de configuração feitas para a VM do Azure](./media/virtual-machines-windows-tutorial/create_vm_summary.PNG)

8. Enquanto o Azure cria a máquina virtual, você pode acompanhar o andamento em **Máquinas Virtuais**, no menu do hub.

## Fazer logon na máquina virtual do Windows

Após criar a máquina virtual, você poderá fazer logon para gerenciar as configurações e os aplicativos que serão executados nela.

>[AZURE.NOTE] Para mais dicas de requisitos e solução de problemas, consulte [Conecte-se à uma máquina virtual Azure com RDP ou SSH](https://msdn.microsoft.com/library/azure/dn535788.aspx).

1. Se ainda não tiver feito isso, entre no portal do Azure.

2. Clique em sua máquina virtual no painel ou clique em Máquinas Virtuais e selecione-a na lista.

3. Na folha da máquina virtual, clique em **Conectar**.

	![Captura de tela que mostra onde encontrar o botão Conectar na folha VM do Azure](./media/virtual-machines-windows-tutorial/connect_vm_portal.png)

4. Clique em **Abrir** para usar o arquivo Protocolo RDP criado automaticamente para a máquina virtual do Windows Server.

5. Clique em **Conectar**.

6. Digite o nome de usuário e a senha especificados na criação da máquina virtual e clique em **OK**.

7. Clique em **Sim** para verificar a identidade da máquina virtual.

Você agora pode trabalhar com a máquina virtual, como faria com qualquer outro servidor.

## Próximas etapas

* Use o Azure PowerShell e CLI do Azure para [localizar e selecionar imagens da máquina virtual](resource-groups-vm-searching.md).
* Automatize a máquina virtual e a implantação, bem como o gerenciamento da carga de trabalho, usando os [modelos do Azure Resource Manager](https://azure.microsoft.com/documentation/templates/).

<!---HONumber=AcomDC_0302_2016-->