<properties 
	pageTitle="Criar uma máquina virtual que executa o Windows no Azure" 
	description="Aprenda a criar a máquina virtual do Windows (VM) no Azure, em seguida, faça logon e anexe um disco de dados" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/20/2015" 
	ms.author="kathydav"/>



# Criação de uma Máquina virtual executando o Windows #

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/virtual-machines-windows-tutorial/" title="Azure Portal" class="current">Portal do Azure</a><a href="/pt-br/documentation/articles/virtual-machines-windows-tutorial-azure-preview/" title="Azure Preview Portal">Portal de visualização do Azure</a></div>

Este tutorial mostra como é fácil criar uma máquina virtual do Azure (VM). Ele usa uma imagem do Windows Server, mas isso é apenas uma das muitas imagens disponíveis por meio do Azure. Isso inclui imagens sistemas operacionais do Windows, sistemas operacionais baseados em Linux e aplicativos pré-instalados. As imagens que você pode escolher dependem do tipo de assinatura que você tem. Por exemplo, imagens de desktop podem estar disponíveis para assinantes do MSDN.

> [AZURE.NOTE] Não é necessário qualquer experiência com VMs do Azure para concluir este tutorial, mas você precisa de uma conta do Azure. Você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Criar uma conta do Azure](http://azure.microsoft.com/develop/php/tutorials/create-a-windows-azure-account/). 

Este tutorial mostra:

- [Como criar a máquina virtual](#createvirtualmachine)
- [Como fazer o logon na máquina virtual após você criá-la](#logon)
- [Como anexar um disco de dados à nova máquina virtual](#attachdisk)

Para saber mais, consulte [Máquinas Virtuais](http://go.microsoft.com/fwlink/p/?LinkID=271224).


##<a id="createvirtualmachine"> </a>Como criar a máquina virtual##

Esta seção mostra como usar a opção **Da galeria** no Portal de Gerenciamento para criar a máquina virtual. Esta opção fornece mais escolhas de configuração que a opção **Criação rápida**. Por exemplo, se desejar ingressar em uma máquina virtual em uma rede virtual, será necessário usar a opção **Da galeria**.

> [AZURE.NOTE] Você também pode experimentar o [Portal de Visualização do Azure](https://portal.azure.com), mais avançado e personalizável, para criar uma máquina virtual, automatizar a implantação de modelos de aplicativo de várias máquinas virtuais, usar os recursos de diagnóstico e monitoramento de VM aperfeiçoados e muito mais. Os opções de configuração da VM disponível nos dois Portais sobrepõem-se bastante, mas não são idênticos.  

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

## <a id="logon"> </a>Como fazer o logon na máquina virtual após você criá-la ##

É possível fazer logon na máquina virtual que você criou para gerenciar as configurações e os aplicativos que estão em execução.

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../includes/virtual-machines-log-on-win-server.md)]

## <a id="attachdisk"> </a>Como anexar um disco de dados à nova máquina virtual ##

Esta seção mostra como anexar um disco de dados vazio à máquina virtual. Consulte o [tutorial Anexar um disco de dados](http://azure.microsoft.com/documentation/articles/storage-windows-attach-disk/) para obter mais informações, inclusive como anexar discos existentes.

1. Entre no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com).

2. Clique em **Máquinas Virtuais** e, em seguida, selecione a máquina virtual **MyTestVM**.

	![Select MyTestVM](./media/virtual-machines-windows-tutorial/selectvm.png)
	
3. Primeiro, você talvez veja a página de início rápido. Se isso ocorrer, selecione **Painel** na parte superior.

	![Select Dashboard](./media/virtual-machines-windows-tutorial/dashboard.png)

4. Na barra de comandos, clique em **Anexar** e, em seguida, clique em **Anexar disco vazio** quando ele aparecer.

	![Select Attach from the command bar](./media/virtual-machines-windows-tutorial/commandbarattach.png)	

5. O **Nome da Máquina Virtual**, **Local de Armazenamento**, **Nome do Arquivo**, e **Preferência de cachê do Host** já estão definidos para você. Tudo que você tem para especificar é um tamanho para o disco. Por exemplo, digite **5** no campo **Tamanho**. Clique na marca de seleção para anexar o disco.


	>[AZURE.NOTE] Todos os discos são criados de arquivos .vhd no armazenamento do Azure. **Nome do arquivo** permite que você nomeie o arquivo .vhd que o disco usa, não o nome do disco. Azure atribui automaticamente um nome para o disco. 

	![Specify the size of the empty disk](./media/virtual-machines-windows-tutorial/emptydisksize.png)	
	
	>[AZURE.NOTE] Os arquivos .vhd são armazenados como blobs de página no armazenamento do Azure. Fora do Azure, os discos rígidos virtuais podem usar um formato VHD ou VHDX. Eles também podem ser fixos, ter expansão dinâmica ou diferenciação. O Azure é compatível com discos fixos de formato VHD. Para obter mais detalhes, consulte [Sobre VHDs no Azure](http://msdn.microsoft.com/library/azure/dn790344.aspx)  

6. Volte para o painel para verificar se o disco vazio foi anexado com sucesso à máquina virtual. Ele deve aparecer na lista **Discos** depois do Disco de OS.

	![Attach empty disk](./media/virtual-machines-windows-tutorial/disklistwithdatadisk.png)

	Quando você anexa um disco de dados, ele está offline e não inicializado. Antes de você poder usar os dados armazenados, você precisa fazer logon na máquina virtual e inicializar o disco.

7. Conecte-se e faça logon na máquina virtual usando as etapas listadas na seção anterior, [Como fazer logon na máquina virtual depois de criá-la]. (#logon).

8. Depois de fazer logon na máquina virtual, abra o **Gerenciador de Servidores**. No painel esquerdo, selecione **Arquivos e serviços de armazenamento**.

	![Expand File and Storage Services in Server Manager](./media/virtual-machines-windows-tutorial/fileandstorageservices.png)

9. Selecione **Discos** no menu expandido.

	![Expand File and Storage Services in Server Manager](./media/virtual-machines-windows-tutorial/selectdisks.png)	
	
10.	A seção **Discos** lista disco 0, disco 1 e disco 2. O disco 0 é o disco do OS, o disco 1 é um disco de recurso temporário (que não deve ser usado para o armazenamento de dados) e o disco 2 é o disco de dados anexado à máquina virtual. O disco de dados tem uma capacidade de 5 GB, com base no que você especificou quando anexou o disco. Clique com o botão direito no disco 2 e selecione **Inicializar**.

	![Start initialization](./media/virtual-machines-windows-tutorial/initializedisk.png)

11. Clique em **Sim**.

	![Continue initialization](./media/virtual-machines-windows-tutorial/yesinitialize.png)

12. Clique com o botão direito do mouse no disco 2 novamente e selecione **Novo Volume**. 

	![Create the volume](./media/virtual-machines-windows-tutorial/initializediskvolume.png)

13. Conclua o assistente usando os valores padrão. Quando o assistente for concluído, a seção **Volumes** lista o novo volume. Agora, o disco está online e pronto para armazenar dados. 

	![Create the volume](./media/virtual-machines-windows-tutorial/newvolumecreated.png)
	
##Próximas etapas 

Para saber mais sobre a configuração de máquinas virtuais do Windows no Azure, consulte:

[Como conectar máquinas virtuais em um serviço de nuvem](http://azure.microsoft.com/documentation/articles/cloud-services-connect-virtual-machine/)

[Como criar e carregar um Disco Rígido Virtual que contém o sistema operacional Windows Server](http://azure.microsoft.com/documentation/articles/virtual-machines-create-upload-vhd-windows-server/)

[Gerenciar a disponibilidade de máquinas virtuais](http://azure.microsoft.com/documentation/articles/manage-availability-virtual-machines/)

[Sobre as definições de configuração da VM do Azure](http://msdn.microsoft.com/library/azure/dn763935.aspx)

[VÍDEO: Introdução aos VHDs - O que realmente está acontecendo](http://azure.microsoft.com/documentation/videos/getting-started-with-azure-virtual-machines)

[VÍDEO: Perguntas frequentes com Mark Russinovich - O Microsoft Azure executa o Windows?](http://azure.microsoft.com/documentation/videos/mark-russinovich-windows-on-azure)

[VÍDEO: Adicionando uma nova máquina virtual a um Web Farm criando imagens reutilizáveis](http://azure.microsoft.com/documentation/videos/adding-virtual-machines-web-farm)

[VÍDEO: Adicionando discos rígidos virtuais, contas de armazenamento e dimensionando máquinas virtuais](http://azure.microsoft.com/documentation/videos/adding-drives-scaling-virtual-machines)

[VÍDEO: Scott Guthrie começa com as máquinas virtuais](http://azure.microsoft.com/documentation/videos/virtual-machines-scottgu)

[VÍDEO: Princípios fundamentais de armazenamento e disco para máquinas virtuais do Azure](http://azure.microsoft.com/documentation/videos/storage-and-disks-virtual-machines)



[Sobre máquinas virtuais no Azure]: #virtualmachine
[Como criar a máquina virtual]: #custommachine
[Como fazer o logon na máquina virtual após você criá-la]: #logon
[Como anexar um disco de dados à nova máquina virtual]: #attachdisk
[Como configurar a comunicação com a máquina virtual]: #endpoints

<!--HONumber=45--> 
