<properties linkid="manage-services-create-a-virtual-network" urlDisplayName="Criar uma rede virtual" pageTitle="Criar uma rede virtual - Gerenciamento de serviço do Azure" metaKeywords="" description="Aprender a criar uma Rede Virtual do Azure." metaCanonical="" services="virtual-machines,virtual-network" documentationCenter="" title="Criar uma Rede Virtual no Azure" authors=""  solutions="" writer="" manager="" editor=""  />





<h1 id="vnettut1">Criar uma Rede Virtual no Azure</h1>

Este tutorial explica as etapas para criar uma Rede Virtual do Azure básica usando o Portal de Gerenciamento do Azure. Para obter mais informações sobre Redes Virtuais do Azure, consulte [Visão geral da rede virtual do Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/jj156007.aspx). 

Este tutorial pressupõe que você não tem experiência anterior com o Azure. O objetivo é ajudá-lo a familiarizar-se com as etapas necessárias para criar uma rede virtual. Se você estiver procurando cenários de design e informações avançadas sobre Rede Virtual, consulte [Visão geral de Rede Virtual do Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/jj156007.aspx).

Depois que concluir o tutorial, você terá uma rede virtual na qual poderá implantar seus serviços do Azure e máquinas virtuais. 

<div class="dev-callout"> 
<b>Observação</b> 
<p>Este tutorial não explicará a criação da configuração entre locais. Para um tutorial passo a passo sobre como criar uma rede virtual com conectividade entre locais (isto é, conectar ao Active Directory ou SharePoint localizado em sua empresa), consulte <a href="/pt-br/manage/services/networking/cross-premises-connectivity/">Criar uma rede virtual para conectividade entre locais</a>.</p> 
</div>

Para obter procedimentos e definições da configuração Rede Virtual adicionais, consulte [Tarefas de configuração de Rede Virtual do Azure](http://go.microsoft.com/fwlink/?LinkId=296652).

Para obter as diretrizes sobre a instalação do AD DS em Máquinas Virtuais do Azure, consulte [Diretrizes para implantação do Active Directory do Windows Server em máquinas virtuais do Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/jj156090.aspx).

##  Objetivos

Neste tutorial, você irá aprender:

*  Como configurar rede virtual básica do Azure à qual você pode adicionar serviços de nuvem e máquinas virtuais do Azure.

##  Pré-requisitos

* Conta do Windows Live com pelo menos uma assinatura válida e ativa.

## Criar uma rede virtual

**Para criar uma rede virtual somente de nuvem:**

1.	Faça logon no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com/).

2. No canto inferior esquerdo da tela, clique em **Nova**. No painel de navegação, clique em **Redes** e, em seguida, em **Máquina Virtual**. Clique em **Criação Personalizada** para iniciar o assistente de configuração.

	![][Image1]

3. Na página **Detalhes da Rede Virtual**, insira as informações a seguir e, em seguida, clique na seta de avanço na parte inferior direita. Para obter mais informações sobre as configurações na página de detalhes, consulte a seção da página **Detalhes da Rede Virtual** em <a href="http://go.microsoft.com/fwlink/?LinkID=248092">Sobre como configurar uma rede virtual usando o Portal de Gerenciamento</a>.

- **Nome -** Nome da sua rede virtual. Digite *YourVirtualNetwork*.

-  **Grupo de Afinidade:** Na lista suspensa, selecione **Criar um novo grupo de afinidade**. Grupos de afinidade são uma forma de agrupar fisicamente os serviços do Azure no mesmo data center, para aumentar o desempenho. Somente uma rede virtual pode ser atribuída a um grupo de afinidade.

- **Região:** Na lista suspensa, selecione a opção desejada. Sua rede virtual será criada em um datacenter localizado na região especificada.

- **Nome do Grupo de Afinidade -** Nome do novo grupo de afinidade. Digite *YourAffinityGroup*.

	![][Image2]

4. Na página **Conectividade entre Servidores DNS e VPN**, insira as informações a seguir e clique na seta avançar na parte inferior direita. Para obter mais informações sobre as configurações nessa página, consulte a página **Conectividade entre Servidores DNS e VPN** em <a href="http://go.microsoft.com/fwlink/?LinkID=248092">Sobre como configurar uma rede virtual no Portal de Gerenciamento</a>.

	- **Servidores DNS opcionais -** Digite o nome do servidor DNS e o endereço IP que você deseja usar. Essa configuração não cria um servidor DNS, ela se refere a um servidor DNS já existente.

		<div class="dev-callout"> 
		<b>Observação</b> 
		<p>Se você deseja usar um serviço DNS público, é possível inserir essa informação nesta tela. Caso contrário, a resolução de nomes será padrão para o serviço do Azure. Para obter mais informações, consulte <a href="http://go.microsoft.com/fwlink/?linkid=248097">Visão geral da resolução de nomes do Azure</a></p> 
		</div>

	- **Não marcar a caixa de seleção para conectividade de site para site ou ponto para site**. A rede virtual que estamos criando neste tutorial não foi projetada para conectividade entre locais.

	![][Image3]

5.	Na página **Espaços de Endereço de Rede Virtual**, insira as seguintes informações e clique na marca de seleção na parte inferior direita para configurar sua rede. O espaço de endereço deve ser um intervalo de endereço privado, especificado em notação CIDR 10.0.0.0/8, 172.16.0.0/12 ou 192.168.0.0/16 (conforme especificado pela RFC 1918). Para obter mais informações sobre as configurações nessa página, consulte a página **Espaços de Endereço da Rede Virtual** em <a href="http://go.microsoft.com/fwlink/?LinkID=248092">Sobre como configurar uma rede virtual no Portal de Gerenciamento</a>.

	- **Espaço de endereço:** clique em CIDR no canto superior direito e, em seguida, digite o seguinte:

		- **IP Inicial:** 10.4.0.0

		- **CIDR:** /16

	- **Adicionar subrede:** insira o seguinte:

		- **Renomeie a subrede-1** para *FrontEndSubnet* com o IP Inicial *10.4.2.0/24* e, em seguida, clique em **Adicionar subrede**.

		- **Criar uma subrede** chamada *BackEndSubnet* com o IP inicial *10.4.3.0/24*.

		- Verifique se agora você tem duas subredes e clique na marca de seleção na parte inferior direita para criar sua rede virtual.

	![][Image4]

6. Depois de clicar na marca de seleção, a rede virtual começará a ser criada. Quando sua rede virtual tiver sido criada, você verá a denominação **Criada** listada sob **Status** na página Rede no Portal de Gerenciamento. 

	![][Image5]

7.	Quando sua rede virtual tiver sido criado, você pode continuar com os seguintes tutoriais:

	- <a href="/pt-br/manage/services/networking/add-a-vm-to-a-virtual-network/">Adicionar uma máquina virtual com uma rede Virtual</a> - Use este tutorial básico para instalar uma máquina virtual à rede virtual.

	- Para obter mais informações sobre opções de instalação e de máquinas virtuais, consulte <a href="/pt-br/manage/windows/how-to-guides/custom-create-a-vm/">Como criar uma máquina virtual personalizada</a> e <a href="/pt-br/manage/windows/">Máquinas virtuais do Azure</a>.

	- <a href="/pt-br/manage/services/networking/active-directory-forest/">Instalar uma nova floresta do Active Directory no Azure</a> - Usar o tutorial para instalar uma nova floresta do Active Directory sem conectividade com qualquer outra rede. O tutorial explicará as etapas específicas necessárias para criar uma máquina virtual (VM) para uma nova instalação de floresta. Se você planeja usar este tutorial, não crie qualquer VMs usando o Portal de Gerenciamento.

## Consulte também

-  [Visão geral da rede virtual do Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/jj156007.aspx)

-  [Perguntas frequentes da rede virtual do Azure](http://go.microsoft.com/fwlink/?LinkId=296650)

-  [Tarefas de configuração da rede virtual do Azure](http://go.microsoft.com/fwlink/?LinkId=296652)

-  [Configurando uma rede virtual usando arquivos de configuração de rede](http://msdn.microsoft.com/pt-br/library/windowsazure/jj156097.aspx)

-  [Resolução de nomes do Azure](http://go.microsoft.com/fwlink/?LinkId=248097)


[Image1]: ./media/create-virtual-network/createVNet_01_OpenVirtualNetworkWizard.png
[Image2]: ./media/create-virtual-network/createVNet_02_VirtualNetworkDetails.png
[Image3]: ./media/create-virtual-network/createVNet_03_DNSServersandVPNConnectivity.png
[Image4]: ./media/create-virtual-network/createVNet_04_VirtualNetworkAddressSpaces.png
[Image5]: ./media/create-virtual-network/createVNet_05_VirtualNetworkCreatedStatus.png


