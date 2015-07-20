<properties
	pageTitle="Criando uma máquina virtual para um projeto Web que usa o Visual Studio"
	description="Criar uma máquina virtual para um site"
	services="virtual-machines"
	documentationCenter=""
	authors="kempb"
	manager="douge"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/10/2015"
	ms.author="kempb"/>

# Criar uma máquina virtual para um site com o Visual Studio

Ao criar um projeto Web para um site do Azure, você pode provisionar uma máquina virtual no Azure. Em seguida, é possível configurar a máquina virtual com software adicional ou usar a máquina virtual para fins de diagnóstico ou depuração.

Para criar uma máquina virtual ao criar um site, siga estas etapas:

1. No Visual Studio, escolha **Arquivo**, **Novo Projeto**, escolha **Web**, e, em seguida, escolha **Aplicativo Web ASP.NET** (sob os nós **Visual C#** ou **Visual Basic**).
2. Na caixa de diálogo **Novo Projeto do ASP.NET**, selecione o tipo de aplicativo Web desejado e, na seção Azure da caixa de diálogo (no canto inferior direito), não se esqueça de marcar a caixa de seleção **Hospedar na nuvem** (ela é rotulada como **Criar recursos remotos** em algumas instalações).

	![][0]

3. Na caixa de listagem suspensa sob o Microsoft Azure, escolha **Máquina Virtual** e, em seguida, escolha o botão **OK**.
4. Conecte-se ao Azure, se for solicitado. A caixa de diálogo **Criar Máquina Virtual** é exibida.

	![][2]

5. Na caixa de nome DNS, digite um nome para a máquina virtual. O nome DNS deve ser exclusivo no Azure. Se o nome inserido não estiver disponível, um ponto de exclamação vermelho será exibido.
6. Na lista **Imagem**, escolha a imagem da VM em que você deseja basear a máquina virtual. É possível escolher qualquer uma das imagens padrão da VM do Azure ou a sua própria imagem carregada no Azure.
7. Deixe a caixa de seleção **Habilitar IIS e Implantação da Web** marcada, a menos que você pretenda instalar um servidor Web diferente. Você não poderá publicar usando o Visual Studio se desabilitar a Implantação da Web. É possível adicionar IIS e Implantação da Web a qualquer uma das imagens do Windows Server no pacote, inclusive as próprias imagens.
8. Na lista **Tamanho**, escolha o tamanho da máquina virtual.
9. Especifique as credenciais de logon da máquina virtual. Anote-as porque você precisará delas para acessar a máquina por meio da Área de Trabalho Remota.
10. Na lista **Local**, escolha a região na qual hospedar a máquina virtual.
11. Escolha o botão **OK** para começar a criar a máquina virtual. Você pode acompanhar o progresso da operação na **janela Saída **. 
	![][3]

12. Quando a máquina virtual está provisionada, os scripts de publicação são criados em um nó **PublishScripts** da solução. O script de publicação é executado e provisiona uma máquina virtual no Azure. A janela **Saída** mostra o status. O script realiza as seguintes ações para configurar a máquina virtual:

	* Cria a máquina virtual, caso ela ainda não exista.
	* Cria uma conta de armazenamento com um nome começando por `devtest`, mas apenas se ainda não houver uma conta de armazenamento assim na região especificada.
	* Cria um serviço de nuvem como um contêiner para a máquina virtual e cria uma função web para o site.
	* Configura a Implantação da Web na máquina virtual.
	* Configura IIS e ASP.NET na máquina virtual.

	![][4]

<br/> 13. (Opcional) Você pode se conectar à nova máquina virtual. Em **Gerenciador de Servidores**, expanda o nó **Máquinas Virtuais**, escolha o nó para a máquina virtual que você criou e, no menu de atalho, escolha **Conectar-se à área de trabalho remota**.

 ![][5]


## Próximas etapas

Se você quiser personalizar os scripts de publicação criados, veja informações mais aprofundadas [aqui](http://msdn.microsoft.com/library/dn642480.aspx).

[0]: ./media/virtual-machines-dotnet-create-visual-studio-powershell/CreateVM_NewProject.PNG
[1]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_SignIn.PNG
[2]: ./media/virtual-machines-dotnet-create-visual-studio-powershell/CreateVM_CreateVM.PNG
[3]: ./media/virtual-machines-dotnet-create-visual-studio-powershell/CreateVM_Provisioning.png
[4]: ./media/virtual-machines-dotnet-create-visual-studio-powershell/CreateVM_SolutionExplorer.png
[5]: ./media/virtual-machines-dotnet-create-visual-studio-powershell/VS_Create_VM_Connect.png

<!---HONumber=July15_HO2-->