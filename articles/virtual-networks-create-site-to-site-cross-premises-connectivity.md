<properties linkid="manage-services-cross-premises-connectivity" urlDisplayName="Conectividade entre instalações" pageTitle="Criar uma rede virtual entre instalações - Azure" metaKeywords="" description="Saiba como criar uma Rede Virtual do Azure com conectividade entre instalações." metaCanonical="" services="virtual-network" documentationCenter="" title="Criar uma Rede Virtual para conectividade site a site entre instalações" authors=""  solutions="" writer="" manager="" editor=""  />





<h1 id="vnettut1">Criar uma rede virtual para conectividade site a site entre instalações</h1>

Este tutorial explica as etapas para criar uma rede virtual entre instalações. O tipo de conexão que iremos criar é uma conexão site a site. Se você desejar criar uma VPN ponto a site usando certificados e um cliente VPN, consulte [Configurar uma VPN ponto a site no Portal de Gerenciamento](http://go.microsoft.com/fwlink/?LinkId=296653)

Este tutorial pressupõe que você não tem experiência anterior com o Azure. O objetivo é ajudá-lo a familiarizar-se com as etapas necessárias para criar uma rede virtual site a site. Se você estiver procurando cenários de design e informações avançadas sobre Rede Virtual, consulte [Visão geral de Rede Virtual do Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/jj156007.aspx)

Depois de concluir este tutorial, você terá uma rede virtual onde você pode implantar serviços e Máquinas Virtuais do Azure que podem se comunicar diretamente com a rede da sua empresa.

Para obter informações sobre como adicionar uma máquina virtual e estender seu Active Directory local para a Rede Virtual do Azure, consulte o seguinte:

-  [Como criar uma máquina virtual personalizada (a página pode estar em inglês)](http://go.microsoft.com/fwlink/?LinkID=294356)

-  [Instalar uma réplica do Controlador de Domínio do Active Directory na Rede Virtual do Azure (a página pode estar em inglês)](http://go.microsoft.com/fwlink/?LinkId=299877)

Para obter as diretrizes sobre a instalação do AD DS em Máquinas Virtuais do Azure, consulte [Diretrizes para implantação do Active Directory do Windows Server em Máquinas Virtuais do Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/jj156090.aspx).

Para obter procedimentos e definições da configuração Rede Virtual adicionais, consulte [Tarefas de configuração de Rede Virtual do Azure](http://go.microsoft.com/fwlink/?LinkId=296652).

##  Objetivos

Neste tutorial, você irá aprender:

- Como configurar rede virtual básica do Azure à qual você pode adicionar serviços do Azure.

- Como configurar a rede virtual para se comunicar com a rede da empresa.

##  Pré-requisitos

- Conta do Windows Live com pelo menos uma assinatura válida e ativa.

- Espaço de endereço (em notação CIDR) a ser usado para a rede virtual e as sub-redes.

- O nome e o endereço IP do servidor DNS (se você desejar usar o servidor DNS local para a resolução de nomes).

- Um dispositivo VPN com um endereço IPv4 público. Você precisará do endereço IP para concluir o assistente. O dispositivo VPN não pode ser localizado por trás de um NAT e deve atender aos padrões mínimos de dispositivos. Consulte [Sobre dispositivos VPN da rede virtual](http://go.microsoft.com/fwlink/?LinkID=248098) para obter mais informações. 

	Observação: você pode usar o RRAS como parte de sua solução de VPN. No entanto, este tutorial não explicará as etapas de configuração do RRAS. 

	Para obter informações sobre a configuração do RRAS, consulte [Modelos do serviço de roteamento e acesso remoto](http://msdn.microsoft.com/library/windowsazure/dn133801.aspx) 

- Experimente com a configuração de um roteador ou alguém que possa ajudá-lo com esta etapa.

- O espaço de endereço de sua rede local (rede local).


## Etapas de alto nível

1.	[Criar uma rede virtual](#CreateVN)

2.	[Iniciar o gateway e reunir informações para o administrador de rede](#StartGateway)

3.  [Configurar o dispositivo de VPN](#ConfigVPN)

##  <a name="CreateVN">Criar uma rede virtual</a>

**Para criar uma rede virtual que se conecte à rede da sua empresa:**

1.	Faça logon no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com/).

2.	No canto inferior esquerdo da tela, clique em **Nova**. No painel de navegação, clique em **Redes** e, em seguida, em **Máquina Virtual**. Clique em **Criação Personalizada** para iniciar o assistente de configuração. 

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_01_OpenVirtualNetworkWizard.png)

3.	Na página **Detalhes da Rede Virtual**, insira as informações a seguir e, em seguida, clique na seta de avanço na parte inferior direita. Para obter mais informações sobre as configurações na página de detalhes, consulte a seção **Detalhes da Rede Virtual** em [Sobre como configurar uma rede virtual no Portal de Gerenciamento](http://go.microsoft.com/fwlink/?LinkID=248092).

-  **NOME:** nome da sua rede virtual. Digite *YourVirtualNetwork*.

-  **GRUPO DE AFINIDADE:** na lista suspensa, selecione **Criar um novo grupo de afinidade**. Grupos de afinidade são uma forma de agrupar fisicamente os serviços do Azure no mesmo data center, para aumentar o desempenho. Somente uma rede virtual pode ser atribuída a um grupo de afinidade.

-  **REGIÃO:** na lista suspensa, selecione a opção desejada. Sua rede virtual será criada em um datacenter localizado na região especificada.

-  **NOME DO GRUPO DE AFINIDADE:** nome do novo grupo de afinidade. Digite *YourAffinityGroup*.

	![](./ media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_02_VirtualNetworkDetails.png)

4.	Na página **Conectividade entre Servidores DNS e VPN**, insira as informações a seguir e, em seguida, clique na seta de avanço na parte inferior direita. 

	<div class="dev-callout"> 
	<b>Observação</b> 
	<p>É possível selecionar simultaneamente as configurações **Ponto a Site** e **Site a Site** nessa página. Para o objetivo deste tutorial, selecionaremos configurar apenas **Site a Site**. Para obter mais informações sobre as configurações nessa página, consulte a seção **Conectividade entre Servidores DNS e VPN** em <a href="http://go.microsoft.com/fwlink/?LinkID=248092">Sobre como configurar uma rede virtual no Portal de Gerenciamento</a>.</p> 
	</div>

-  **SERVIDORES DNS:** digite o nome do servidor DNS e o endereço IP que você deseja usar para a resolução de nomes. Normalmente, esse deve ser um servidor DNS que você usa para resolução de nomes locais. Essa configuração não cria um servidor DNS. Digite *YourDNS* para o nome e *10.1.0.4* para o endereço IP.
-  **Configurar a VPN Ponto a Site:** mantenha esse campo em branco. 
-  **Configurar VPN Site a Site:** selecione a caixa de seleção.
-  **REDE LOCAL:** selecione **Especificar uma nova rede local** na lista suspensa.
 
	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_03_DNSServersandVPNConnectivity.png)

5.	Na página **Conectividade Site a Site**, insira as informações abaixo e clique na marca de seleção no canto inferior direito da página. Para obter mais informações sobre as configurações nessa página, consulte a seção **Conectividade Site a Site** em [Sobre como configurar uma rede virtual no Portal de Gerenciamento](http://go.microsoft.com/fwlink/?LinkID=248092). 

-  **NOME:** digite *YourCorpHQ*.

-  **ENDEREÇO IP DO DISPOSITIVO VPN:** digite o endereço IP público do dispositivo VPN. Se não tiver essas informações, você precisará obtê-las antes de continuar com as próximas etapas do assistente. Observe que o dispositivo VPN não pode estar por trás de um NAT. Para obter mais informações sobre dispositivos VPN, consulte [Sobre dispositivos VPN da rede virtual](http://msdn.microsoft.com/pt-br/library/windowsazure/jj156075.aspx).

-  **ESPAÇO DE ENDEREÇO**: digite *10.1.0.0/16*.
-  **Adicionar espaço de endereço:** este tutorial não requer espaço de endereço adicional.

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_04_SitetoSite.png)

6.  Na página **Espaços de Endereço de Rede Virtual**, insira as informações abaixo e clique na marca de seleção na parte inferior direita para configurar sua rede. 

	O espaço de endereço deve ser um intervalo de endereço privado, especificado em notação CIDR 10.0.0.0/8, 172.16.0.0/12 ou 192.168.0.0/16 (conforme especificado pela RFC 1918). Para obter mais informações sobre as configurações nessa página, consulte a página **Espaços de Endereço da Rede Virtual** em [Sobre como configurar uma rede virtual no Portal de Gerenciamento](http://go.microsoft.com/fwlink/?LinkID=248092).

-  **Espaço de endereço:** clique em **CIDR** no canto superior direito e, em seguida, digite o seguinte:
	-  **IP Inicial:** 10.4.0.0
	-  **CIDR:** /16
-  **Adicionar Sub-Rede:** insira o seguinte:
	-  **Renomeie a Sub-Rede-1** para *FrontEndSubnet* com o IP Inicial *10.4.2.0/24* e, em seguida, clique em **Adicionar Sub-Rede**.
	-  **Adicione uma sub-rede** chamada *BackEndSubnet* com o IP inicial *10.4.3.0/24*.
	-  **Adicione uma sub-rede** chamada *ADDNSSubnet* com o IP inicial *10.4.4.0/24*.
	-  **Adicione a sub-rede do gateway** com o IP inicial *10.4.1.0/24*.
	-  **Verifique** se agora você tem três sub-redes e uma sub-rede do gateway criadas e, em seguida, clique na marca de seleção na parte inferior direita para criar sua rede virtual.

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_05_VirtualNetworkAddressSpaces.png)

7.	Depois de clicar na marca de seleção, a rede virtual começará a ser criada. Quando sua rede virtual tiver sido criada, você verá a denominação Criada listada sob Status na página Rede no Portal de Gerenciamento. 

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_06_VirtualNetworkCreatedStatus.png)

##  <a name="StartGateway">Iniciar o Gateway</a>

Depois de criar sua Rede Virtual do Azure, use o procedimento a seguir para configurar o gateway da rede virtual para criar seu VPN site a site. Este procedimento requer que você tenha um dispositivo VPN que atenda aos requisitos mínimos. Para obter mais informações sobre a configuração de dispositivos VPN e outros dispositivos, consulte [Sobre dispositivos VPN da rede virtual](http://go.microsoft.com/fwlink/?LinkID=248098).

**Para iniciar o gateway:**

1.	Quando sua rede virtual tiver sido criada, a página **Redes** mostrará **Criada** como o status de sua rede virtual.

	Na coluna **NOME**, clique em **YourVirtualNetwork** para abrir o painel.
 
	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_07_ClickYourVirtualNetwork.png)

2.	Clique em **PAINEL** na parte superior da página. Na página Painel, na parte inferior da página, clique em **CRIAR GATEWAY**. Selecione **Roteamento Dinâmico** ou **Roteamento Estático** para o tipo de Gateway que você deseja criar. 

	Observe que se quiser usar essa rede virtual para conexões ponto a site além de site a site, você deverá selecionar Roteamento Dinâmico como o tipo do gateway. Antes de criar o gateway, verifique se o dispositivo VPN será compatível com o tipo do gateway que você deseja criar. Consulte [Sobre dispositivos VPN da Rede Virtual](http://go.microsoft.com/fwlink/?LinkID=248098). Quando o sistema solicitar que você confirme se deseja criar o gateway, clique em **Sim**.

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_08_CreateGateway.png)

3.	Quando a criação do gateway for iniciada, você verá uma mensagem que informa que o gateway foi iniciado.

	Pode levar até 15 minutos para que o gateway seja criado.

4.	Após o gateway ter sido criado, você precisará obter as seguintes informações que serão usadas para configurar o dispositivo VPN. 

- Endereço IP do gateway
- Chave compartilhada
- Modelo de script de configuração de dispositivo VPN

	As próximas etapas irão orientá-lo através desse processo.

5.	Para localizar o Endereço IP do Gateway - O endereço IP do Gateway está localizado na página **PAINEL** da rede virtual. 

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_09_GatewayIP.png)

6.	Para adquirir a Chave Compartilhada - A chave compartilhada está localizada na página **PAINEL** da rede virtual. Clique em Gerenciar Chaves na parte inferior da tela e, em seguida, copie a chave exibida na caixa de diálogo. 

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_10_ManageSharedKey.png)

7.	Baixe o modelo do script da configuração do dispositivo VPN. No painel, clique em **Baixar Script do Dispositivo VPN**.

8.	Na caixa de diálogo **Baixar Script da Configuração do Dispositivo VPN**, selecione o fornecedor, a plataforma e o sistema operacional do dispositivo VPN de sua empresa. Clique no botão de marca de seleção para salvar o arquivo. 

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_11_DownloadVPNDeviceScript.png)

Se você não vir seu dispositivo VPN na lista suspensa, consulte [Sobre dispositivos VPN da Rede Virtual](http://go.microsoft.com/fwlink/?LinkID=248098) na biblioteca do MSDN para obter modelos de script adicionais.


##  <a name="ConfigVPN">Configurar o dispositivo VPN (administrador da rede)</a>

Como cada dispositivo VPN é diferente, esse é apenas um procedimento de alto nível. Esse procedimento deve ser executado pelo administrador da rede.

Você pode obter o script de configuração de VPN no Portal de Gerenciamento ou na página [Sobre dispositivos VPN da Rede Virtual](http://go.microsoft.com/fwlink/?LinkId=248098), que também explica os tipos de roteamento e os dispositivos que são compatíveis com a configuração de roteamento que você optar por usar.

Para obter informações adicionais sobre como configurar um gateway de rede virtual, consulte [Configurar um gateway de rede virtual no Portal de Gerenciamento](http://go.microsoft.com/fwlink/?LinkId=299878) e consulte a documentação de seu dispositivo VPN.

Esse procedimento pressupõe o seguinte:

- A pessoa que está configurando o dispositivo VPN é proficiente na configuração do dispositivo que foi selecionado. Devido ao número de dispositivos que são compatíveis com a rede virtual e as configurações que são específicas a cada família de dispositivos, essas etapas não explicam a configuração do dispositivo em um nível granular. Portanto, é importante que a pessoa que configura o dispositivo esteja familiarizada com o dispositivo e suas configurações. 

- O dispositivo que você selecionou é compatível com a rede virtual. Verifique a compatibilidade do dispositivo [aqui](http://go.microsoft.com/fwlink/?LinkID=248098).


**Para configurar o dispositivo VPN:**

1.	Modifique o script de configuração da VPN. Você irá configurar o seguinte:

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


##  Próxima etapas
Para estender o Active Directory local para a rede virtual recém-criada, continue com os seguintes tutoriais:

-  [Como criar uma máquina virtual personalizada (a página pode estar em inglês)](http://go.microsoft.com/fwlink/?LinkID=294356)

-  [Instalar uma réplica do Controlador de Domínio do Active Directory na Rede Virtual do Azure (a página pode estar em inglês)](http://go.microsoft.com/fwlink/?LinkId=299877)

Se você desejar exportar as configurações de rede virtual para um arquivo de configuração de rede para fazer o backup de sua configuração ou para usá-la como um modelo, consulte [Exportar as configurações de Rede Virtual para um arquivo de configuração de rede](http://go.microsoft.com/fwlink/?LinkID=299880).

## Consulte também

-  [Rede Virtual do Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/jj156007.aspx)

-  [Perguntas frequentes da rede virtual](http://msdn.microsoft.com/library/windowsazure/dn133803.aspx)

-  [Configurar uma Rede Virtual usando arquivos de configuração de rede](http://msdn.microsoft.com/pt-br/library/windowsazure/jj156097.aspx)

-  [Adicionar uma máquina virtual a uma rede virtual (a página pode estar em inglês)](http://www.windowsazure.com/pt-br/manage/services/networking/add-a-vm-to-a-virtual-network/)

-  [Sobre dispositivos VPN da rede virtual](http://msdn.microsoft.com/pt-br/library/windowsazure/jj156075.aspx)

-  [Resolução de nomes do Azure](http://go.microsoft.com/fwlink/?LinkId=248097)






