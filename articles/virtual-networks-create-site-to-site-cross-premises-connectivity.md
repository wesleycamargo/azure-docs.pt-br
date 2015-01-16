<properties urlDisplayName="Tutorial: Create a Cross-Premises Virtual Network for Site-to-Site Connectivity" pageTitle="Tutorial: Criar uma rede virtual para conectividade site a site entre instalações" metaKeywords="" description="Aprenda a criar uma rede virtual do Azure com conectividade entre locais neste tutorial." metaCanonical="" services="virtual-network" documentationCenter="" title="Create a Virtual Network for Site-to-Site Cross-Premises Connectivity" authors="cherylmc" solutions="" manager="adinah" editor="" />

<tags ms.service="virtual-network" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/23/2014" ms.author="cherylmc" />





<h1 id="vnettut1">Tutorial: Criar uma rede virtual para conectividade site a site entre instalações</h1>

Este tutorial explica as etapas para criar um exemplo de rede virtual entre instalações com uma conexão site a site. 

Se você deseja criar uma rede virtual somente em nuvem, consulte [Tutorial: Criar uma rede virtual somente de nuvem no Azure](http://azure.microsoft.com/pt-br/documentation/articles/create-virtual-network/). Se você desejar criar uma VPN ponto a site usando certificados e um cliente VPN, consulte [Configurar uma VPN ponto a site no Portal de Gerenciamento](http://go.microsoft.com/fwlink/?LinkId=296653).

Este tutorial pressupõe que você não tem experiência anterior com o Azure. O objetivo é ajudá-lo a familiarizar-se com as etapas necessárias para criar uma rede virtual entre instalações. Se você estiver procurando cenários de design e informações avançadas sobre Rede Virtual, consulte [Visão geral de Rede Virtual do Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/jj156007.aspx).

Depois de concluir este tutorial, você terá uma rede virtual de exemplo entre instalações. A figura a seguir mostra os detalhes, com base nas configurações de exemplo deste tutorial.

![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_12_TutorialCrossPremVNet.png)

Para obter uma cópia da figura e outra que pode ser usada para representar sua própria rede virtual entre instalações, consulte [Figura de exemplo entre instalações de rede virtual do tópico do tutorial](http://gallery.technet.microsoft.com/Example-cross-premises-e5ecb8bb).

Observe que as definições exemplo de configuração usadas nesse tutorial não são personalizadas para a rede de sua empresa. Se você configurar a rede virtual e a conexão site a site usando as definições exemplo de configuração descritas nesse tópico, não funcionará. Para configurar a rede virtual entre instalações que funcione, você deve trabalhar com o seu departamento de TI e o administrador de rede para obter as configurações corretas. Para obter mais informações, consulte a seção **Pré-requisitos** neste tópico.

Para obter informações sobre como adicionar uma máquina virtual e estender seu Active Directory local para a Rede Virtual do Azure, consulte o seguinte:

-  [Como criar uma máquina virtual personalizada](http://go.microsoft.com/fwlink/?LinkID=294356)

-  [Instalar uma réplica do controlador de domínio do Active Directory em uma Rede Virtual do Azure](http://go.microsoft.com/fwlink/?LinkId=299877)

Para obter as diretrizes sobre a instalação do AD DS em Máquinas Virtuais do Azure, consulte [Diretrizes para implantação do Active Directory do Windows Server em Máquinas Virtuais do Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/jj156090.aspx).

Para obter procedimentos e definições da configuração Rede Virtual adicionais, consulte [Tarefas de configuração de Rede Virtual do Azure](http://go.microsoft.com/fwlink/?LinkId=296652).

##  Objetivos

Neste tutorial, você irá aprender:

-  Como configurar rede virtual entre instalações do Azure à qual você pode adicionar serviços do Azure.

-  Como configurar a rede virtual para se comunicar com a rede da empresa.

##  Pré-requisitos

-  Uma conta da Microsoft com pelo menos uma assinatura do Azure válida e ativa.  Se você ainda não tiver uma conta do Azure, pode se inscrever para uma conta de avaliação gratuita em [Teste o Azure](http://www.windowsazure.com/pricing/free-trial/). Se você tiver uma assinatura do MSDN, consulte [Preço Especial do Microsoft Azure: Benefícios do MSDN, do MPN e do Bizspark](http://azure.microsoft.com/pt-br/pricing/member-offers/msdn-benefits-details/).

Se você estiver usando esse tutorial para configurar uma rede virtual entre instalações de trabalho personalizada para a sua empresa, será necessário o seguinte:

-  Os espaços de endereço IPv4 privado (em notação CIDR) a ser usado para a rede virtual e suas sub-redes.

-  O nome e o endereço IP do servidor DNS local.

-  Um dispositivo VPN com um endereço IPv4 público. Você precisará do endereço IP para concluir o assistente. O dispositivo VPN não pode ser localizado por trás de um tradutor de endereço de rede (NAT) e deve atender aos padrões mínimos de dispositivos. Consulte [Sobre dispositivos VPN da rede virtual](http://go.microsoft.com/fwlink/?LinkID=248098) para obter mais informações. 

Observação: Você pode usar o Serviço de Roteamento e Acesso Remoto (RRAS) no Windows Server como parte de sua solução de VPN. No entanto, este tutorial não explicará as etapas de configuração do RRAS. 
Para obter informações sobre a configuração do RRAS, consulte [Modelos do serviço de roteamento e acesso remoto](http://msdn.microsoft.com/library/windowsazure/dn133801.aspx). 

-  Experimente com a configuração de um roteador para uma conexão de modo de túnel ou com alguém que possa ajudá-lo com esta etapa.

-  O grupo de espaços de endereço (em notação CIDR) que resumem os locais acessíveis da sua rede local.


## Etapas de alto nível

1.	[Criar uma rede virtual](#CreateVN)

2.	[Iniciar o gateway e reunir informações para o administrador de rede](#StartGateway)

3.  [Configurar o dispositivo de VPN](#ConfigVPN)

##  <a name="CreateVN">Criar uma rede virtual</a>

Para criar um exemplo de rede virtual que se conecte à rede da empresa:

1.	Faça logon no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com/).

2.	No canto inferior esquerdo da tela, clique em **Novo**. No painel de navegação, clique em **Redes** e, em seguida, em **Máquina Virtual**. Clique em **Criação Personalizada** para iniciar o assistente de configuração. 

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_01_OpenVirtualNetworkWizard.png)

3.	Na página **Detalhes da Rede Virtual**, insira as informações a seguir e, em seguida, clique na seta de avanço na parte inferior direita. Para obter mais informações sobre as configurações na página de detalhes, consulte a seção **Detalhes da Rede Virtual** em [Sobre como configurar uma rede virtual no Portal de Gerenciamento](http://go.microsoft.com/fwlink/?LinkID=248092).

	-  **NOME:** NOME: nome da sua rede virtual. Por exemplo, neste tutorial, digite **YourVirtualNetwork**.

	-  **REGIÃO:** Na lista suspensa, selecione a opção desejada. Sua rede virtual será criada no datacenter do Azure localizado na região especificada.

	
4.	Na página **Conectividade entre Servidores DNS e VPN**, insira as informações a seguir e, em seguida, clique na seta de avanço na parte inferior direita. 

	<div class="dev-callout"> 
	<b>Observação</b> 
	<p>É possível selecionar simultaneamente as configurações <b>Ponto a Site</b> e <b>Site a Site</b> nessa página. Para o objetivo deste tutorial, selecionaremos configurar apenas <b>Site a Site</b>. Para obter mais informações sobre as configurações nessa página, consulte a seção <b>Conectividade entre Servidores DNS e VPN</b> em <a href="http://go.microsoft.com/fwlink/?LinkID=248092">Sobre como configurar uma rede virtual no Portal de Gerenciamento</a>.</p> 
	</div>

	-  **SERVIDORES DNS:** Digite o nome do servidor DNS e o endereço IP que você deseja usar para a resolução de nomes. Normalmente, esse deve ser um servidor DNS que você usa para resolução de nomes locais. Essa configuração não cria um servidor DNS. Por exemplo, nesse tutorial, digite **YourDNS** para o nome e **10.1.0.4** para o endereço IP.
	-  **Configurar o dispositivo de VPN Ponto a Site:** Deixe este campo em branco. 
	-  **Configurar VPN site a site:** Selecione a caixa de seleção.
	-  **REDE LOCAL:** Selecione **Especificar uma nova rede local** na lista suspensa.
 
	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_03_DNSServersandVPNConnectivity.png)

5.	Na página **Conectividade Site a Site**, insira as informações abaixo e clique na marca de seleção no canto inferior direito da página. Para obter mais informações sobre as configurações nessa página, consulte a seção **Conectividade Site a Site** em [Sobre como configurar uma rede virtual no Portal de Gerenciamento](http://go.microsoft.com/fwlink/?LinkID=248092). 

	-  **NOME:** Por exemplo, neste tutorial, digite **YourCorpHQ**.

	-  **ENDEREÇO IP DO DISPOSITIVO DE VPN:** Por exemplo, neste tutorial, digite **3.2.1.1**. Digite o endereço IP público do seu dispositivo VPN. Se não tiver essas informações, você precisará obtê-las antes de continuar com as próximas etapas do assistente. Observe que o dispositivo VPN não pode estar por trás de um NAT. Para obter mais informações sobre dispositivos VPN, consulte [Sobre dispositivos VPN da rede virtual](http://msdn.microsoft.com/pt-br/library/windowsazure/jj156075.aspx).

	-  **ESPAÇO DE ENDEREÇO:** Por exemplo, nesse tutorial, digite **10.1.0.0/16**.
	-  **Adicione o espaço de endereço:** Este tutorial não requer espaço de endereço adicional.

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_04_SitetoSite.png)

6.  Na página **Espaços de Endereço de Rede Virtual**, insira as informações abaixo e clique na marca de seleção na parte inferior direita para configurar sua rede. 

O espaço de endereço deve ser um intervalo de endereço privado, especificado em notação dos espaços de endereço CIDR 10.0.0.0/8, 172.16.0.0/12 ou 192.168.0.0/16 (conforme especificado pela RFC 1918). Para obter mais informações sobre as configurações nessa página, consulte a página **Espaços de Endereço da Rede Virtual** em [Sobre como configurar uma rede virtual no Portal de Gerenciamento](http://go.microsoft.com/fwlink/?LinkID=248092).

	-  **Espaço de endereço:** Por exemplo, nesse tutorial, clique em **CIDR** no canto superior direito e, em seguida, digite o seguinte:
		-  **Starting IP:** 10.4.0.0
		-  **CIDR:** /16
	-  **Adicione a sub-rede:** Por exemplo, neste tutorial, digite o seguinte:
		-  Renomeie a **Sub-rede-1** **FrontEndSubnet** com o IP inicial **10.4.2.0/24**.
		-  Adicione uma sub-rede chamada **BackEndSubnet** com o IP inicial **10.4.3.0/24**.
		-  Adicione uma sub-rede chamada **ADDNSSubnet** com o IP inicial **10.4.4.0/24**.
		-  Adicione a sub-rede do gateway com o IP inicial **10.4.1.0/24**.
	-  Por exemplo, nesse tutorial, verifique se agora você tem três sub-redes e uma sub-rede do gateway criadas e, em seguida, clique na marca de seleção na parte inferior direita para criar sua rede virtual.

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_05_VirtualNetworkAddressSpaces.png)

7.	Depois de clicar na marca de seleção, a rede virtual começará a ser criada. Quando sua rede virtual tiver sido criada, você verá a denominação **Criada** listada sob **Status** na página Rede no Portal de Gerenciamento. 

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_06_VirtualNetworkCreatedStatus.png)

##  <a name="StartGateway">Iniciar o Gateway</a>

Depois de criar sua Rede Virtual do Azure, use o procedimento a seguir para configurar o gateway de rede virtual para criar seu VPN site a site. Este procedimento requer que você tenha um dispositivo VPN que atenda aos requisitos mínimos. Para obter mais informações sobre a configuração de dispositivos VPN e outros dispositivos, consulte [Sobre dispositivos VPN da rede virtual](http://go.microsoft.com/fwlink/?LinkID=248098).

**Para iniciar o gateway:**

1.	Quando sua rede virtual tiver sido criada, a página **Redes** mostrará **Criada** como o status de sua rede virtual.

Na coluna **NOME**, clique em **YourVirtualNetwork** (por exemplo criado no tutorial) para abrir o painel.
 
	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_07_ClickYourVirtualNetwork.png)

2.	Clique em **PAINEL** na parte superior da página. Na página Painel, na parte inferior da página, clique em **CRIAR GATEWAY**. Selecione **Roteamento Dinâmico** ou **Roteamento Estático** para o tipo de Gateway que você deseja criar. 

Observe que se quiser usar essa rede virtual para conexões ponto a site além de site a site, você deverá selecionar **Roteamento Dinâmico** como o tipo do gateway. Antes de criar o gateway, verifique se o dispositivo VPN será compatível com o tipo do gateway que você deseja criar. Consulte [Sobre dispositivos VPN da Rede Virtual](http://go.microsoft.com/fwlink/?LinkID=248098). Quando o sistema solicitar que você confirme se deseja criar o gateway, clique em **SIM**.

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_08_CreateGateway.png)

3.	Quando a criação do gateway for iniciada, você verá uma mensagem que informa que o gateway foi iniciado.

	Pode levar até 15 minutos para que o gateway seja criado.

4.	Após o gateway ter sido criado, você precisará obter as seguintes informações que serão usadas para configurar o dispositivo VPN. 

	-  Endereço IP do gateway
	-  Chave compartilhada
	-  Modelo de script de configuração de dispositivo VPN

	As próximas etapas irão orientá-lo através desse processo.

5.	Para localizar o endereço IP do gateway: O endereço IP do Gateway está localizado na página **PAINEL** da rede virtual. Aqui está um exemplo:

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_09_GatewayIP.png)

6.	Para obter a Chave Compartilhada: A chave compartilhada está localizada na página **PAINEL** da rede virtual. Clique em **Gerenciar Chaves** na parte inferior da tela e, em seguida, copie a chave exibida na caixa de diálogo. Você precisará dessa chave para configurar o túnel IPsec no dispositivo VPN da sua empresa.

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_10_ManageSharedKey.png)

7.	Baixe o modelo do script da configuração do dispositivo VPN: No painel, clique em **Baixar Script do Dispositivo VPN**.

8.	Na caixa de diálogo **Baixar Script da Configuração do Dispositivo VPN**, selecione o fornecedor, a plataforma e o sistema operacional do dispositivo VPN de sua empresa. Clique no botão de marca de seleção para salvar o arquivo. 

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_11_DownloadVPNDeviceScript.png)

Se você não vir seu dispositivo VPN na lista suspensa, consulte [Sobre dispositivos VPN da Rede Virtual](http://go.microsoft.com/fwlink/?LinkID=248098) na biblioteca do MSDN para obter modelos de script adicionais.


##  <a name="ConfigVPN">Configurar o dispositivo VPN (administrador da rede)</a>

Como cada dispositivo VPN é diferente, esse é apenas um procedimento de alto nível. Esse procedimento deve ser executado pelo administrador da rede.

Você pode obter o script de configuração de VPN no Portal de Gerenciamento ou na página [Sobre dispositivos VPN da Rede Virtual](http://go.microsoft.com/fwlink/?LinkId=248098), que também explica os tipos de roteamento e os dispositivos que são compatíveis com a configuração de roteamento que você optar por usar.

Para obter informações adicionais sobre como configurar um gateway de rede virtual, consulte [Configurar um gateway de rede virtual no Portal de Gerenciamento](http://go.microsoft.com/fwlink/?LinkId=299878) e consulte a documentação de seu dispositivo VPN.

Esse procedimento pressupõe o seguinte:

-  A pessoa que está configurando o dispositivo VPN é proficiente na configuração do dispositivo que foi selecionado. Devido ao número de dispositivos que são compatíveis com a rede virtual e as configurações que são específicas a cada família de dispositivos, essas etapas não explicam a configuração do dispositivo em um nível granular. Portanto, é importante que a pessoa que configura o dispositivo esteja familiarizada com o dispositivo e suas configurações. 

-  O dispositivo que você selecionou é compatível com a rede virtual. Verifique a compatibilidade do dispositivo [aqui](http://go.microsoft.com/fwlink/?LinkID=248098).


**Para configurar o dispositivo VPN:**

1.	Modifique o script de configuração da VPN. Você vai configurar o seguinte:

a.	Diretivas de segurança

	b.	Túnel de entrada

c.	Túnel de saída

2.	Execute o script de configuração da VPN modificado para configurar o dispositivo VPN.

3.	Teste sua conexão executando um dos seguintes comandos:

	<table border="1">
	<tr>
	<th>-</th>
	<th>Cisco ASA</th>
	<th>Cisco ISR/ASR</th>
	<th>Juniper SSG/ISG</th>
	<th>Juniper SRX/J</th>
	</tr>
	
	<tr>
	<td><b>Verificar o modo principal SAs</b></td>
	<td><FONT FACE="courier" SIZE="-1">show crypto isakmp sa</FONT></td>
	<td><FONT FACE="courier" SIZE="-1">show crypto isakmp sa</FONT></td>
	<td><FONT FACE="courier" SIZE="-1">get ike cookie</FONT></td>
	<td><FONT FACE="courier" SIZE="-1">show security ike security-association</FONT></td>
	</tr>
	
	<tr>
	<td><b>Verificar o modo rápido SAs</b></td>
	<td><FONT FACE="courier" SIZE="-1">show crypto ipsec sa</FONT></td>
	<td><FONT FACE="courier" SIZE="-1">show crypto ipsec sa</FONT></td>
	<td><FONT FACE="courier" SIZE="-1">get sa</FONT></td>
	<td><FONT FACE="courier" SIZE="-1">show security ipsec security-association</FONT></td>
	</tr>
	</table>


##  Próximas etapas
Para estender o Active Directory local para a rede virtual recém-criada, continue com os seguintes tutoriais:

-  [Como criar uma máquina virtual personalizada](http://go.microsoft.com/fwlink/?LinkID=294356)

-  [Instalar uma réplica do controlador de domínio do Active Directory em uma Rede Virtual do Azure](http://go.microsoft.com/fwlink/?LinkId=299877)

Se você desejar exportar as configurações de rede virtual para um arquivo de configuração de rede para fazer o backup de sua configuração ou para usá-la como um modelo, consulte [Exportar as configurações de Rede Virtual para um arquivo de configuração de rede](http://go.microsoft.com/fwlink/?LinkID=299880).

## Consulte também

-  [Rede Virtual do Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/jj156007.aspx)

-  [Perguntas frequentes sobre rede virtual](http://msdn.microsoft.com/library/windowsazure/dn133803.aspx)

-  [Configurar uma Rede Virtual usando arquivos de configuração de rede](http://msdn.microsoft.com/pt-br/library/windowsazure/jj156097.aspx)

-  [Adicionar uma máquina virtual a uma rede virtual](http://www.windowsazure.com/pt-br/manage/services/networking/add-a-vm-to-a-virtual-network/)

-  [Sobre dispositivos VPN da rede virtual](http://msdn.microsoft.com/pt-br/library/windowsazure/jj156075.aspx)

-  [Resolução de nomes do Azure](http://go.microsoft.com/fwlink/?LinkId=248097)





