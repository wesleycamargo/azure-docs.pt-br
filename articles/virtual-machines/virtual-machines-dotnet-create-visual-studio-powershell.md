<properties
	pageTitle="Criando uma VM para um projeto da web usando o Visual Studio | Microsoft Azure"
	description="Criar uma máquina virtual para um aplicativo Web"
	services="virtual-machines"
	documentationCenter=""
	authors="TomArcher"
	manager="douge"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/05/2016"
	ms.author="tarcher"/>

# Criando uma máquina virtual para um aplicativo Web com o Visual Studio

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.

Ao criar um projeto de aplicativo Web para o Azure, você pode provisionar uma máquina virtual no Azure. Em seguida, é possível configurar a máquina virtual com software adicional ou usar a máquina virtual para fins de diagnóstico ou depuração.

Para criar uma máquina virtual ao criar um aplicativo Web, execute estas etapas:

1. No Visual Studio, clique em **Arquivo** > **Novo** > **Projeto** > **Web** e, em seguida, escolha **Aplicativo Web ASP.NET** (sob os nós **Visual C#** ou **Visual Basic**).
2. Na caixa de diálogo **Novo Projeto do ASP.NET**, selecione o tipo de aplicativo Web desejado e, na seção Azure da caixa de diálogo (no canto inferior direito), não se esqueça de marcar a caixa de seleção **Hospedar na nuvem** (ela é rotulada como **Criar recursos remotos** em algumas instalações).

	![][0]

3. Para este exemplo, na lista suspensa em Microsoft Azure, escolha **Máquina Virtual (v1)** e, em seguida, clique no botão **OK**.
4. Conecte-se ao Azure, se for solicitado. A caixa de diálogo **Criar Máquina Virtual** é exibida.

	![][2]

5. Na caixa **nome DNS**, insira um nome para a máquina virtual. O nome DNS deve ser exclusivo no Azure. Se o nome inserido não estiver disponível, um ponto de exclamação vermelho será exibido.
6. Na lista **Imagem**, escolha a imagem em que você deseja basear a máquina virtual. É possível escolher qualquer uma das imagens padrão da máquina virtual do Azure ou a sua imagem carregada no Azure.
7. Deixe a caixa de seleção **Habilitar IIS e Implantação da Web** marcada, a menos que você pretenda instalar um servidor Web diferente. Você não poderá publicar usando o Visual Studio se desabilitar a Implantação da Web. É possível adicionar IIS e Implantação da Web a qualquer uma das imagens do Windows Server no pacote, inclusive as próprias imagens.
8. Na lista **Tamanho**, escolha o tamanho da máquina virtual.
9. Especifique as credenciais para entrar nesta máquina virtual. Anote-as porque você precisará delas para acessar a máquina por meio da Área de Trabalho Remota.
10. Na lista **Local**, escolha a região para hospedar a máquina virtual.
11. Clique no botão **OK** para começar a criar a máquina virtual. Você pode acompanhar o progresso da operação na janela **Saída**.

	![][3]

12. Quando a máquina virtual está provisionada, os scripts publicados são criados em um nó **PublishScripts** da solução. O script publicado é executado e provisiona uma máquina virtual no Azure. A janela **Saída** mostra o status. O script realiza as seguintes ações para configurar a máquina virtual:

	* Cria a máquina virtual, caso ela ainda não exista.
	* Cria uma conta de armazenamento com um nome começando por `devtest`, mas apenas se ainda não houver uma conta de armazenamento assim na região especificada.
	* Cria um serviço de nuvem como um contêiner para a máquina virtual e cria uma função Web para o aplicativo Web.
	* Configura a Implantação da Web na máquina virtual.
	* Configura IIS e ASP.NET na máquina virtual.

	![][4]

13. (Opcional) Você pode se conectar à nova máquina virtual. Em **Gerenciador de Servidores**, expanda o nó **Máquinas Virtuais**, escolha o nó para a máquina virtual que você criou e, no menu de atalho, escolha **Conectar-se à área de trabalho remota**. Como alternativa, no **Gerenciador de Nuvem**, você pode escolher **Abrir no Portal** no menu de atalho e conectar-se à máquina virtual nesse local.

 ![][5]


## Próximas etapas

Se você quiser personalizar os scripts publicados que criou, leia informações mais detalhadas em [Usando Scripts do Windows PowerShell para publicar para ambientes de teste e desenvolvimento](http://msdn.microsoft.com/library/dn642480.aspx).

[0]: ./media/virtual-machines-dotnet-create-visual-studio-powershell/CreateVM_NewProject.PNG
[1]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_SignIn.PNG
[2]: ./media/virtual-machines-dotnet-create-visual-studio-powershell/CreateVM_CreateVM.PNG
[3]: ./media/virtual-machines-dotnet-create-visual-studio-powershell/CreateVM_Provisioning.png
[4]: ./media/virtual-machines-dotnet-create-visual-studio-powershell/CreateVM_SolutionExplorer.png
[5]: ./media/virtual-machines-dotnet-create-visual-studio-powershell/VS_Create_VM_Connect.png

<!---HONumber=AcomDC_0107_2016-->