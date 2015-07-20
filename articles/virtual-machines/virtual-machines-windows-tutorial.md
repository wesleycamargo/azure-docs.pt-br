<properties
	pageTitle="Criar uma máquina virtual executando o Windows no Portal de Visualização do Azure"
	description="Aprenda a criar uma máquina virtual do Azure (VM) que executa Windows, usando o Azure Marketplace no Portal de Visualização do Azure"
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""/>
<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="06/03/2015"
	ms.author="kathydav"/>

# Criar uma máquina virtual executando o Windows no Portal de Visualização do Azure#

> [AZURE.SELECTOR]
- [Azure Preview Portal](virtual-machines-windows-tutorial.md)
- [Azure Portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell - Resource Management](virtual-machines-deploy-rmtemplates-powershell.md)
- [PowerShell - Service Management](virtual-machines-ps-create-preconfigure-windows-vms.md)

Este tutorial mostra como é fácil criar uma VM (máquina virtual) do Azure no Portal de visualização do Azure. Vamos usar uma imagem do Windows Server como exemplo, mas ela é apenas uma das muitas imagens que o Azure oferece. Observe que as opções de imagem dependem de sua assinatura. Por exemplo, imagens de desktop podem estar disponíveis para assinantes do MSDN.

Você também pode criar VMs usando [suas próprias imagens](virtual-machines-create-upload-vhd-windows-server.md). Para saber mais sobre esse e outros métodos, consulte [Diferentes Maneiras de Criar uma Máquina Virtual do Windows](virtual-machines-windows-choices-create-vm.md).

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]


## Criar a máquina virtual

Usando o Windows Server 2012 R2 Datacenter, por exemplo, você pode criar uma VM para testar em apenas alguns minutos. É possível usar as configurações padrão do Azure na maior parte da configuração.

1. Entre no [Portal de visualização](https://portal.azure.com).

2. No menu Ajuda, clique em **Novo**.

3. Na lâmina **Novo**, clique em **Computação** **>** **Windows Server 2012 R2 Datacenter**.

	![Selecionar uma imagem de VM no Marketplace](./media/virtual-machines-windows-tutorial/marketplace_portal.png)

4. Na faixa **Criar VM**, preencha o **Nome de Host** desejado para a VM, o **Nome de Usuário** administrativo e uma **Senha** forte. O **Nome de Usuário** se refere à conta administrativa que você usa para gerenciar o servidor. Crie uma senha que seja difícil para outras pessoas adivinharem mas da qual você possa se lembrar. **Você precisará do nome do usuário e da senha para fazer logon na máquina virtual**. Se esquecer a senha, você poderá redefini-la usando [estas instruções](virtual-machines-windows-reset-password.md)

	![Configurar nome de host e credenciais de logon](./media/virtual-machines-windows-tutorial/create_vm_name_pwd_portal.png)

5. Revise as configurações padrão, como **Camada de Preços** e **Configuração Opcional**. Essas opções afetam o tamanho da VM, bem como as opções de rede, tais como a associação do domínio. Por exemplo, para testar o Armazenamento Premium em uma máquina virtual, você precisará escolher uma região e o tamanho que dá suporte a ela. Para sua primeira máquina virtual, as configurações padrão normalmente são suficientes.

	>[AZURE.NOTE]O Armazenamento Premium está disponível para máquinas virtuais de série DS em determinadas regiões. O Armazenamento Premium é a melhor opção de armazenamento para cargas de trabalho de uso intensivo de dados, como um banco de dados. Para obter detalhes, confira [Armazenamento Premium: armazenamento de alto desempenho para as cargas de trabalho das máquinas virtuais do Azure](storage-premium-storage-preview-portal.md).

6. Quando você terminar a revisão ou atualização das configurações, clique em **Criar**.

7. Enquanto o Azure cria a VM, é possível acompanhar o progresso em **Notificações**, no menu Hub. Após o Azure criar a máquina virtual, você a verá no Quadro Inicial a menos que tenha desmarcado **Fixar no Quadro Inicial** na lâmina **Criar VM**.

## Faça logon na máquina virtual

Após criar a VM, você pode fazer logon nela para que possa gerenciar as configurações e os aplicativos que serão executados na VM.

>[AZURE.NOTE]Para mais dicas de requisitos e solução de problemas, consulte [Conecte-se à uma máquina virtual Azure com RDP ou SSH](https://msdn.microsoft.com/library/azure/dn535788.aspx).

1. Se ainda não fez isso, entre no [Portal de visualização](https://portal.azure.com).

2. Clique na VM no quadro inicial. Se você precisar encontrá-la, clique em **Procurar** > **Máquinas virtuais**. Em seguida, selecione sua VM na lista.

3. Na lâmina da VM, clique em **Conectar**.

	![Faça logon na máquina virtual](./media/virtual-machines-windows-tutorial/connect_vm_portal.png)

4. Clique em **Abrir** para usar o arquivo do protocolo RDP criado automaticamente para a máquina virtual.

5. Clique em **Conectar**.

6. Digite o nome de usuário e a senha que você especificou quando criou a máquina virtual e clique em **OK**.

7. Clique em **Sim** para verificar a identidade da máquina virtual.

	Você agora pode trabalhar com a máquina virtual, como faria com qualquer outro servidor.

## Próximas etapas

Para saber mais sobre a configuração de máquinas virtuais do Windows no Azure, consulte os artigos a seguir:

[Conectar Máquinas virtuais a uma Rede virtual ou Serviço de Nuvem](cloud-services-connect-virtual-machine.md)

[Anexar discos de dados à uma máquina virtual](storage-windows-attach-disk.md)

[Gerenciar a disponibilidade das máquinas virtuais](../manage-availability-virtual-machines.md)

[Sobre as definições de configuração da VM do Azure](http://msdn.microsoft.com/library/azure/dn763935.aspx)
 

<!---HONumber=July15_HO2-->