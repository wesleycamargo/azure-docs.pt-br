<properties urlDisplayName="Create a virtual machine in the Preview Portal" pageTitle="Criar uma máquina virtual com Windows no Portal de visualização do Azure" metaKeywords="Azure image gallery vm" description="Saiba como criar uma máquina virtual (VM) do Azure executando o Windows, usando a Galeria de VMs do Portal de Visualização do Azure" metaCanonical="" services="virtual-machines" documentationCenter="" title="Create a Virtual Machine Running Windows in the Azure Preview Portal" authors="danlep,kathydav,rasquill" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="12/11/2014" ms.author="danlep,kathydav,rasquill" />

<!---Workflow can be confusing...have to select the correct size with **Browse all pricing tiers** then click Select on that pane and the **Recommended pricing tiers** pane to apply. But even after that, it didn't seem to pick the Premium storage account...showed up as 'standard GRS' when I inspected what type of storage account would be used for the VM. 
-->

# Criar uma Máquina Virtual no Portal de visualização do Azure#

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/virtual-machines-windows-tutorial/" title="Azure Portal">Portal do Azure</a><a href="/pt-br/documentation/articles/virtual-machines-windows-tutorial-azure-preview/" title="Azure Preview Portal" class="current">Portal de visualização do Azure</a></div>

Este tutorial mostra como é fácil criar uma máquina virtual (VM) no Azure executando o Windows, usando como exemplo uma imagem do Windows Server da Galeria de Imagens no Portal de Visualização do Azure. A Galeria oferece várias imagens, inclusive sistema operacionais Windows, sistemas operacionais baseados em Linux e imagens de aplicativos. 

> [WACOM.NOTE] Não é necessária nenhuma experiência com VMs do Azure para concluir este tutorial. Porém, você precisa de uma conta do Azure. Você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Criar uma conta do Azure](http://www.windowsazure.com/pt-br/develop/php/tutorials/create-a-windows-azure-account/). 

Este tutorial mostra:

- [Como criar a máquina virtual](#createvirtualmachine)
- [Como fazer o logon na máquina virtual após você criá-la](#logon)

Para saber mais, consulte [Máquinas Virtuais](http://go.microsoft.com/fwlink/p/?LinkID=271224).


##<a id="createvirtualmachine"> </a>Como criar a máquina virtual##

Esta seção mostra como usar o Portal de visualização para criar uma máquina virtual usando o Windows Server 2012 R2 Datacenter como exemplo. É possível usar as configurações padrão do Azure na maior parte da configuração e criar a VM em questão de minutos.

> [WACOM.NOTE] As imagens disponíveis para você dependem da sua assinatura. Este tutorial usa uma imagem do Windows Server, mas uma assinatura do MSDN pode oferecer imagens adicionais, incluindo imagens da área de trabalho. 
 

1. Entre no [Portal de visualização do Azure](https://portal.azure.com). Confira a oferta [Avaliação Gratuita](http://www.windowsazure.com/pt-br/pricing/free-trial/) caso você ainda não tenha uma assinatura.

2. No menu de Hub, clique em **Novo**.

	![Select New from the Command Bar](./media/virtual-machines-windows-tutorial-azure-preview/new_button_preview_portal.png)

3. Na faixa **Novo**, clique em **Tudo**, clique em **Máquinas virtuais**, clique em **Windows Server 2012 R2 Datacenter**e, em seguida, clique em **Criar**.

	![Select a VM image from the Gallery](./media/virtual-machines-windows-tutorial-azure-preview/image_gallery_preview_portal.png)
	
4. Na faixa **Criar VM**, preencha o **Nome de Host** desejado para a VM, o **Nome de Usuário** administrativo e uma **Senha** forte.  

	>[WACOM.NOTE] **O Nome de Usuário** se refere à conta administrativa que você usa para gerenciar o servidor. Crie uma senha exclusiva para essa conta e não se esqueça dela. **Você precisará do nome do usuário e da senha para fazer logon na máquina virtual**.
	

	![Configure host name and log on credentials](./media/virtual-machines-windows-tutorial-azure-preview/create_vm_name_pwd_preview_portal.png)
	
	
5. Revise as configurações padrão, como **Camada de Preços**, **Configuração Opcional**, e **local**. Essas opções afetam o tamanho da VM, bem como as opções de rede, tais como a associação do domínio. Por exemplo, para testar o Armazenamento Premium em uma máquina virtual, você precisará escolher uma região e o tamanho que dá suporte a ela. 

	>[WACOM.NOTE] O Armazenamento Premium está em Visualização, disponível para máquinas virtuais de série DS em determinadas regiões. Para obter detalhes, consulte [Armazenamento Premium: Armazenamento de alto desempenho para cargas de trabalho de Máquina Virtual do Azure](http://azure.microsoft.com/pt-br/documentation/articles/storage-premium-storage-preview-portal/).

	![Configure host name and log on credentials](./media/virtual-machines-windows-tutorial-azure-preview/create_vm_preview_portal.png)
	
6. Quando você terminar a revisão ou atualização das configurações, clique em **Criar**.	

7. Enquanto o Azure cria a VM, é possível acompanhar o progresso em **Notificações**, no menu Hub. Depois que o Azure criar a VM, você a verá no quadro inicial.

	![VM appears on the Startboard](./media/virtual-machines-windows-tutorial-azure-preview/vm_startboard_preview_portal.png)

## <a id="logon"> </a>Como fazer o logon na máquina virtual após criá-la ##

Esta seção mostra como fazer logon na VM de forma que você possa gerenciar as configurações e os aplicativos que serão executados nela.

>[WACOM.NOTE] Para mais dicas de requisitos e solução de problemas, consulte [Conectar-se a uma máquina virtual do Azure com RDP ou SSH](http://go.microsoft.com/fwlink/p/?LinkId=398294).

1. Se você ainda não fez isso, entre no [Portal de Visualização do Azure](https://portal.azure.com).

2. Clique na VM no quadro inicial. Se você precisar encontrá-la, clique em **Procurar** e em **Máquinas virtuais**. 

	![Browse to find the VM](./media/virtual-machines-windows-tutorial-azure-preview/browse_vm_preview_portal.png)

3. Na faixa da VM, clique em **Conectar** na parte superior.

	![Log on to the virtual machine](./media/virtual-machines-windows-tutorial-azure-preview/connect_vm_preview_portal.png)

4. Clique em **Abrir** para usar o arquivo do protocolo RDP criado automaticamente para a máquina virtual.
	
5. Clique em **Conectar** para prosseguir com o processo de conexão.

	![Continue with connecting](./media/virtual-machines-log-on-windows-server/connectpublisher.png)

6. Digite o nome do usuário e a senha da conta administrativa na máquina virtual e, em seguida, clique em **OK**.
	
7. Clique em **Sim** para verificar a identidade da máquina virtual.

	![Verify the identity of the machine](./media/virtual-machines-log-on-windows-server/connectverify.png)

	Você agora pode trabalhar com a máquina virtual, como faria com qualquer outro servidor.

##Próximas etapas 

Para saber mais sobre a configuração de máquinas virtuais do Windows no Azure, consulte os seguintes artigos:

[Como conectar máquinas virtuais em um serviço de nuvem](http://www.windowsazure.com/pt-br/documentation/articles/cloud-services-connect-virtual-machine/)

[Como criar e carregar um Disco Rígido Virtual que contém o sistema operacional Windows Server](http://www.windowsazure.com/pt-br/documentation/articles/virtual-machines-create-upload-vhd-windows-server/)

[Anexar um disco de dados à máquina virtual](http://www.windowsazure.com/pt-br/documentation/articles/storage-windows-attach-disk/)

[Gerenciar a disponibilidade de máquinas virtuais](http://www.windowsazure.com/pt-br/documentation/articles/manage-availability-virtual-machines/)

[Sobre as definições de configuração da VM do Azure](http://msdn.microsoft.com/library/azure/dn763935.aspx)

[Como criar a máquina virtual]: #custommachine
[Como fazer o logon na máquina virtual após você criá-la]: #logon

<!--HONumber=35.2-->
