<properties 
	pageTitle="Tutorial: criar uma rede virtual para conectividade site a site entre instalações" 
	description="Aprenda a criar uma rede virtual do Azure com conectividade entre locais neste tutorial." 
	services="virtual-network" 
	documentationCenter="" 
	authors="cherylmc" 
	manager="adinah" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/28/2015" 
	ms.author="cherylmc"/>



# Tutorial: criar uma rede virtual para conectividade site a site entre instalações

Este tutorial explica as etapas para criar um exemplo de rede virtual entre instalações com uma conexão site a site.

Se você quiser criar uma rede virtual somente em nuvem, confira [Tutorial: criar uma rede virtual somente em nuvem no Azure](../virtual-machines/create-virtual-network.md). Se você desejar criar uma VPN ponto a site usando certificados e um cliente VPN, consulte [Configurar uma VPN ponto a site no Portal de Gerenciamento](http://go.microsoft.com/fwlink/p/?LinkId=296653)

Este tutorial pressupõe que você não tem experiência anterior com o Azure. O objetivo é ajudá-lo a familiarizar-se com as etapas necessárias para criar uma rede virtual entre instalações. Se você estiver procurando cenários de design e informações avançadas sobre Rede Virtual, confira [Visão geral da Rede Virtual do Azure](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx).

Depois de concluir este tutorial, você terá uma rede virtual de exemplo entre instalações. A figura a seguir mostra os detalhes, com base nas configurações de exemplo deste tutorial.

![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_12_TutorialCrossPremVNet.png)

Para obter uma cópia da figura e outra que possa ser usada para representar sua própria rede virtual entre instalações, confira [Figura de exemplo de rede virtual entre instalações do tópico do tutorial](http://gallery.technet.microsoft.com/Example-cross-premises-e5ecb8bb).

Observe que as definições exemplo de configuração usadas nesse tutorial não são personalizadas para a rede de sua empresa. Se você configurar a rede virtual e a conexão site a site usando as definições exemplo de configuração descritas nesse tópico, não funcionará. Para configurar a rede virtual entre instalações que funcione, você deve trabalhar com o seu departamento de TI e o administrador de rede para obter as configurações corretas. Para obter mais informações, consulte a seção "**Pré-requisitos** neste documento.

Para obter informações sobre como adicionar uma máquina virtual e estender seu Active Directory local para a Rede Virtual do Azure, consulte o seguinte:

-  [Como criar uma máquina virtual de forma personalizada](http://go.microsoft.com/fwlink/p/?LinkID=294356)

-  [Instalar uma réplica do controlador de domínio do Active Directory na rede virtual do Azure](http://go.microsoft.com/fwlink/p/?LinkId=299877)

Para obter as diretrizes sobre a instalação do AD DS em Máquinas Virtuais do Azure, consulte [Diretrizes para implantação do Active Directory do Windows Server em Máquinas Virtuais do Azure](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx).

Para obter procedimentos e definições da configuração Rede Virtual adicionais, consulte [Tarefas de configuração de Rede Virtual do Azure](http://go.microsoft.com/fwlink/p/?LinkId=296652).

##  Objetivos

Neste tutorial, você irá aprender:

-  Como configurar rede virtual entre instalações do Azure à qual você pode adicionar serviços do Azure.

-  Como configurar a rede virtual para se comunicar com a rede da empresa.

##  Pré-requisitos

-  Um conta do Windows com pelo menos uma assinatura válida e ativa. Se você não tiver uma conta do Azure, será possível se inscrever em uma conta gratuita no [Experimente o Azure](http://azure.microsoft.com/pricing/free-trial/). Se você tiver uma assinatura do MSDN, consulte [Preço especial do Microsoft Azure: Benefícios do MSDN, MPN, e Bizspark](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

Se você estiver usando esse tutorial para configurar uma rede virtual entre instalações de trabalho personalizada para a sua empresa, será necessário o seguinte:

-  Os espaços de endereço IPv4 privado (em notação CIDR) a ser usado para a rede virtual e suas sub-redes.

-  O nome e o endereço IP do servidor DNS local.

-  Um dispositivo VPN com um endereço IPv4 público. Você precisará do endereço IP para concluir o assistente. O dispositivo VPN não pode ser localizado por trás de um tradutor de endereço de rede (NAT) e deve atender aos padrões mínimos de dispositivos. Consulte [Sobre dispositivos VPN da rede virtual](http://go.microsoft.com/fwlink/p/?LinkID=248098) para obter mais informações.

	Observação: você pode usar o Serviço de Roteamento e Acesso Remoto (RRAS) no Windows Server como parte da sua solução de VPN. No entanto, este tutorial não explicará as etapas de configuração do RRAS. Para saber mais sobre a configuração do RRAS, confira [Modelos do serviço de roteamento e acesso remoto](http://msdn.microsoft.com/library/windowsazure/dn133801.aspx).

-  Experimente com a configuração de um roteador para uma conexão de modo de túnel ou com alguém que possa ajudá-lo com esta etapa.

-  O grupo de espaços de endereço (em notação CIDR) que resumem os locais acessíveis da sua rede local.


## Etapas de alto nível

1.	[Criar uma rede virtual](#CreateVN)

2.	[Iniciar o gateway e reunir informações para o administrador da rede](#StartGateway)

3.  [Configurar o dispositivo de VPN](#ConfigVPN)

##  <a name="CreateVN">Criar uma rede virtual</a>

Para criar um exemplo de rede virtual que se conecte à rede da empresa:

1.	Faça logon no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com/).

2.	No canto inferior esquerdo da tela, clique em **Nova**. No painel de navegação, clique em **Redes** e, em seguida, em **Máquina Virtual**. Clique em **Criação Personalizada** para iniciar o assistente de configuração.

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_01_OpenVirtualNetworkWizard.png)

3.	Na página **Detalhes da Rede Virtual**, insira as informações a seguir e, em seguida, clique na seta de avanço na parte inferior direita. Para obter mais informações sobre as configurações na página de detalhes, consulte a seção **Detalhes da Rede Virtual** em [Sobre como configurar uma rede virtual no Portal de Gerenciamento](http://go.microsoft.com/fwlink/p/?LinkID=248092).

	-  **Nome:** nome da sua rede virtual. Por exemplo, nesse tutorial, digite **YourVirtualNetwork**.

	-  **Local:** na lista suspensa, selecione a região desejada. Sua rede virtual será criada no datacenter do Azure localizado na região especificada.

	
4.	Na página **Conectividade entre Servidores DNS e VPN**, insira as informações a seguir e, em seguida, clique na seta de avanço na parte inferior direita.

> [AZURE.NOTE]É possível selecionar simultaneamente as configurações **Ponto a Site** e **Site a Site** nessa página. Para o objetivo deste tutorial, selecionaremos configurar apenas **Site a Site**. Para obter mais informações sobre as configurações nessa página, consulte a seção **Conectividade entre Servidores DNS e VPN** em [Sobre como configurar uma rede virtual no Portal de Gerenciamento](http://go.microsoft.com/fwlink/p/?LinkID=248092).

	-  **DNS SERVERS:** Enter the DNS server name and IP address that you want to use for name resolution. Typically this would be a DNS server that you use for on-premises name resolution. This setting does not create a DNS server. For the example in this tutorial, type **YourDNS** for the name and **10.1.0.4** for the IP address.
	-  **Configure Point-To-Site VPN:** Leave this field blank. 
	-  **Configure Site-To-Site VPN:** Select checkbox.
	-  **LOCAL NETWORK:** Select **Specify a New Local Network** from the drop-down list.
 
	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_03_DNSServersandVPNConnectivity.png)

5.	Na página **Conectividade Site a Site**, insira as informações abaixo e clique na marca de seleção no canto inferior direito da página. Para obter mais informações sobre as configurações nessa página, consulte a seção **Conectividade Site a Site** em [Sobre como configurar uma rede virtual no Portal de Gerenciamento](http://go.microsoft.com/fwlink/p/?LinkID=248092). 

	-  **NOME:** para exemplo neste tutorial, digite **YourCorpHQ**.

	-  **ENDEREÇO IP DO DISPOSITIVO VPN:** para exemplo neste tutorial, digite **3.2.1.1**. Digite o endereço IP público do seu dispositivo VPN. Se não tiver essas informações, você precisará obtê-las antes de continuar com as próximas etapas do assistente. Observe que o dispositivo VPN não pode ser localizado por trás de um NAT. Para saber mais sobre dispositivos VPN, confira [Sobre dispositivos VPN da rede virtual](http://msdn.microsoft.com/library/windowsazure/jj156075.aspx).

	-  **ESPAÇO DE ENDEREÇO:** para exemplo neste tutorial, digite **10.1.0.0/16**.
	-  **Adicionar espaço de endereço:** este tutorial não requer espaço de endereço adicional.

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_04_SitetoSite.png)

6.  Na página **Espaços de Endereço de Rede Virtual**, insira as informações abaixo e clique na marca de seleção na parte inferior direita para configurar sua rede.

	O espaço de endereço deve ser um intervalo de endereço privado, especificado em notação dos espaços de endereço CIDR 10.0.0.0/8, 172.16.0.0/12 ou 192.168.0.0/16 (conforme especificado pela RFC 1918). Para obter mais informações sobre as configurações nessa página, consulte a página **Espaços de Endereço da Rede Virtual** em [Sobre como configurar uma rede virtual no Portal de Gerenciamento](http://go.microsoft.com/fwlink/?LinkID=248092).

	-  **Espaço de endereço:** para exemplo neste tutorial, clique em **CIDR** no canto superior direito e digite o seguinte:
		-  **IP Inicial:** 10.4.0.0
		-  **CIDR:** /16
	-  **Adicionar sub-rede:** para exemplo neste tutorial, digite o seguinte:
		-  **Renomeie a Sub-Rede-1** para **FrontEndSubnet** com o IP Inicial **10.4.2.0/24**.
		-  Adicione uma sub-rede chamada **BackEndSubnet** com o IP inicial **10.4.3.0/24**.
		-  Adicione uma sub-rede chamada **ADDNSSubnet** com o IP inicial **10.4.4.0/24**.
		-  Adicione uma sub-rede do gateway com o IP inicial **10.4.1.0/24**.
	-  Por exemplo, nesse tutorial, verifique se agora você tem três sub-redes e uma sub-rede do gateway criadas e, em seguida, clique na marca de seleção na parte inferior direita para criar sua rede virtual.

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_05_VirtualNetworkAddressSpaces.png)

7.	Depois de clicar na marca de seleção, a rede virtual começará a ser criada. Quando sua rede virtual tiver sido criada, você verá a denominação **Criada** listada sob **Status** na página Rede no Portal de Gerenciamento.

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_06_VirtualNetworkCreatedStatus.png)

##  <a name="StartGateway">Iniciar o Gateway</a>

Depois de criar sua Rede Virtual do Azure, use o procedimento a seguir para configurar o gateway de rede virtual para criar seu VPN site a site. Este procedimento requer que você tenha um dispositivo VPN que atenda aos requisitos mínimos. Para obter mais informações sobre a configuração de dispositivos VPN e outros dispositivos, consulte [Sobre dispositivos VPN da rede virtual](http://go.microsoft.com/fwlink/p/?LinkID=248098).

**Para iniciar o gateway:**

1.	Quando sua rede virtual tiver sido criada, a página **Redes** mostrará **Criada** como o status de sua rede virtual.

	Na coluna **NOME**, clique em **YourVirtualNetwork** (por exemplo criado no tutorial) para abrir o painel.
 
	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_07_ClickYourVirtualNetwork.png)

2.	Clique em **PAINEL** na parte superior da página. Na página Painel, na parte inferior da página, clique em **CRIAR GATEWAY**. Selecione **Roteamento Dinâmico** ou **Roteamento Estático** para o tipo de Gateway que você deseja criar.

	Observe que se quiser usar essa rede virtual para conexões ponto a site além de site a site, você deverá selecionar **Roteamento Dinâmico** como o tipo do gateway. Antes de criar o gateway, verifique se o dispositivo VPN será compatível com o tipo do gateway que você deseja criar. Consulte [Sobre dispositivos VPN da Rede Virtual](http://go.microsoft.com/fwlink/p/?LinkID=248098). Quando o sistema solicitar que você confirme se deseja criar o gateway, clique em **Sim**.

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_08_CreateGateway.png)

3.	Quando a criação do gateway for iniciada, você verá uma mensagem que informa que o gateway foi iniciado.

	Pode levar até 15 minutos para que o gateway seja criado.

4.	Após o gateway ter sido criado, você precisará obter as seguintes informações que serão usadas para configurar o dispositivo VPN.

	-  Endereço IP do gateway
	-  Chave compartilhada
	-  Modelo de script de configuração de dispositivo VPN

	As próximas etapas irão orientá-lo através desse processo.

5.	Para localizar o Endereço IP do Gateway: o endereço IP do gateway está localizado na página **PAINEL** da rede virtual. Veja um exemplo:

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_09_GatewayIP.png)

6.	Para adquirir a Chave Compartilhada: a chave compartilhada está localizada na página **PAINEL** da rede virtual. Clique em **Gerenciar Chaves** na parte inferior da tela e, em seguida, copie a chave exibida na caixa de diálogo. Você precisará dessa chave para configurar o túnel IPsec no dispositivo de VPN da sua empresa.

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_10_ManageSharedKey.png)

7.	Para baixar o modelo de script de configuração do dispositivo VPN: no painel, clique em **Baixar script do dispositivo VPN**.

8.	Na caixa de diálogo **Baixar Script da Configuração do Dispositivo VPN**, selecione o fornecedor, a plataforma e o sistema operacional do dispositivo VPN de sua empresa. Clique no botão de marca de seleção para salvar o arquivo.

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_11_DownloadVPNDeviceScript.png)

Se você não vir seu dispositivo VPN na lista suspensa, consulte [Sobre dispositivos VPN da Rede Virtual](http://go.microsoft.com/fwlink/p/?LinkID=248098) na biblioteca do MSDN para obter modelos de script adicionais.


##  <a name="ConfigVPN">Configurar o dispositivo VPN (administrador da rede)</a>

Como cada dispositivo VPN é diferente, esse é apenas um procedimento de alto nível. Esse procedimento deve ser executado pelo administrador da rede.

Você pode obter o script de configuração de VPN no Portal de Gerenciamento ou na página [Sobre dispositivos VPN da Rede Virtual](http://go.microsoft.com/fwlink/p/?LinkId=248098), que também explica os tipos de roteamento e os dispositivos que são compatíveis com a configuração de roteamento que você optar por usar.

Para obter informações adicionais sobre como configurar um gateway de rede virtual, consulte [Configurar um gateway de rede virtual no Portal de Gerenciamento](http://go.microsoft.com/fwlink/p/?LinkId=299878) e consulte a documentação de seu dispositivo VPN.

Esse procedimento pressupõe o seguinte:

-  A pessoa que está configurando o dispositivo VPN é proficiente na configuração do dispositivo que foi selecionado. Devido ao número de dispositivos que são compatíveis com a rede virtual e as configurações que são específicas a cada família de dispositivos, essas etapas não explicam a configuração do dispositivo em um nível granular. Portanto, é importante que a pessoa que configura o dispositivo esteja familiarizada com o dispositivo e suas configurações. 

-  O dispositivo que você selecionou é compatível com a rede virtual. Verifique a compatibilidade do dispositivo [aqui](http://go.microsoft.com/fwlink/p/?LinkID=248098).


**Para configurar o dispositivo VPN:**

1.	Modifique o script de configuração da VPN. Você irá configurar o seguinte:

	a. Diretivas de segurança

	b. Túnel de entrada

	c. Túnel de saída

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
<td><b>Verificar as SAs de modo principal</b></td>
<td><FONT FACE="courier" SIZE="-1">show crypto isakmp sa</FONT></td>
<td><FONT FACE="courier" SIZE="-1">show crypto isakmp sa</FONT></td>
<td><FONT FACE="courier" SIZE="-1">get ike cookie</FONT></td>
<td><FONT FACE="courier" SIZE="-1">show security ike security-association</FONT></td>
</tr>

<tr>
<td><b>Verificar as SAs de modo rápido</b></td>
<td><FONT FACE="courier" SIZE="-1">show crypto ipsec sa</FONT></td>
<td><FONT FACE="courier" SIZE="-1">show crypto ipsec sa</FONT></td>
<td><FONT FACE="courier" SIZE="-1">get sa</FONT></td>
<td><FONT FACE="courier" SIZE="-1">show security ipsec security-association</FONT></td>
</tr>
</table>


##  Próximas etapas
Para estender o Active Directory local para a rede virtual recém-criada, continue com os seguintes tutoriais:

-  [Como criar uma máquina virtual de forma personalizada](http://go.microsoft.com/fwlink/p/?LinkID=294356)

-  [Instalar uma réplica do controlador de domínio do Active Directory na rede virtual do Azure](http://go.microsoft.com/fwlink/p/?LinkId=299877)

Se você desejar exportar as configurações de rede virtual para um arquivo de configuração de rede para fazer o backup de sua configuração ou para usá-la como um modelo, consulte [Exportar as configurações de Rede Virtual para um arquivo de configuração de rede](http://go.microsoft.com/fwlink/p/?LinkID=299880).

## Consulte também

-  [Visão geral técnica da rede virtual do Azure](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx)

-  [Perguntas frequentes sobre rede virtual](http://msdn.microsoft.com/library/windowsazure/dn133803.aspx)

-  [Configurar uma rede virtual usando arquivos de configuração de rede](virtual-networks-using-network-configuration-file.md)

-  [Adicionar uma máquina virtual a uma rede virtual](../virtual-machines/virtual-machines-create-custom.md)

-  [Sobre dispositivos VPN para Rede Virtual(http://msdn.microsoft.com/library/windowsazure/jj15] 75.aspx)

-  [Resolução de nomes de máquinas virtuais e instâncias de função](http://go.microsoft.com/fwlink/p/?LinkId=248097)
-  [Configurar um ambiente de nuvem híbrida para teste](virtual-networks-setup-hybrid-cloud-environment-testing.md)





 

<!---HONumber=July15_HO4-->