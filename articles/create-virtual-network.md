<properties 
	pageTitle="Tutorial: Criar uma rede virtual somente na nuvem" 
	description="Saiba como criar um exemplo apenas na nuvem da rede virtual do Azure neste tutorial." 
	services="virtual-machines, virtual-network" 
	documentationCenter="" 
	authors="cherylmc" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/29/2014" 
	ms.author="cherylmc"/>

<h1 id="vnettut1">Tutorial: Criar uma rede virtual somente de nuvem no Azure</h1>

Este tutorial explica as etapas no Portal de gerenciamento do Azure para criar um exemplo de rede virtual do Azure somente de nuvem que contenha duas sub-redes. A rede de virtual resultante terá a aparência a seguir:

![createvnet](./media/create-virtual-network/createVNet_06_VNetExample.png)

Por exemplo, o FrontEndSubnet poderia ser usado para servidores web e o BackendSubnet poderia ser usado para SQL servers ou controladores de domínio.

Este tutorial pressupõe que você não tem experiência anterior com o Azure. O objetivo é ajudar a familiarizar-se com as etapas requeridas para criar sua própria rede virtual, o orientando por uma configuração de exemplo. Se você quiser criar uma rede virtual somente de nuvem que funcione para sua configuração específica, consulte [Configurar uma rede virtual apenas de nuvem no Portal de Gerenciamento](http://msdn.microsoft.com/library/azure/dn631643.aspx). Se você estiver procurando cenários de design e informações avançadas sobre Rede Virtual, consulte [Visão geral de Rede Virtual do Windows Azure](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx).


> [AZURE.NOTE] Este tutorial não lhe orienta para criar uma configuração entre locais, na qual a rede virtual é conectada à sua rede organizacional. Para um tutorial que lhe oriente para criar uma rede virtual com conectividade entre locais e a conexão VPO site-a-site (por exemplo, conectar-se ao Active Directory ou SharePoint localizado na sua companhia), consulte o [Tutorial: Criar uma rede virtual para conectividade site a site entre instalações](/manage/services/networking/cross-premises-connectivity/).


##  Objetivos

Neste tutorial você aprenderá como configurar uma rede virtual básica somente de nuvem do Azure com duas sub-redes.

##  Pré-requisitos

*  Uma conta do Microsoft com pelo menos uma assinatura do Azure válida e ativa. Se ainda não tiver uma assinatura do Azure, você poderá se inscrever para uma avaliação gratuita em [Teste o Azure](http://www.windowsazure.com/pricing/free-trial/). Se você tiver uma assinatura do MSDN, consulte [Preço Especial do Microsoft Azure: MSDN, MPN e Benefícios Bizspark](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

##  Criar a rede virtual para este tutorial

Para criar esta rede virtual somente de nuvem de exemplo, faça o seguinte

1. Faça logon no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com/).

2. No canto inferior esquerdo da tela, clique em **Novo** > **Serviços de Rede** > **Rede virtual** e, sem seguida, clique em **Criação personalizada** para iniciar o assistente de configuração.

	![][Image1]

3. Na página **Detalhes da rede virtual**, insira as seguintes informações:

- **Nome -** digite **sua_rede_virtual**.

- **Região -** A rede virtual será criada em um datacenter localizado na região especificada. Para obter o melhor desempenho, seleciona a região à qual você pertence da lista suspensa.
 

	![][Image2]

4. Clique na seta de avanço na parte inferior direita. Para obter mais informações sobre as configurações nesta página, consulte a seção da página Detalhes da Rede Virtual em [Sobre como configurar uma rede virtual usando o Portal de Gerenciamento](http://go.microsoft.com/fwlink/?linkid=248092&clcid=0x409).

5. Na página **Servidores DNS e conectividade VPN**, clique na seta de avanço na parte inferior direita. O Azure atribuirá um Servidor DNS do Azure baseado na Internet a novas máquinas virtuais que sejam adicionadas a esta rede virtual, o que permitirá que elas acessem a recursos da Internet. Para obter mais informações sobre as configurações nessa página, consulte a página de Servidores DNS e Conectividade VPN em [Sobre como configurar uma rede virtual no Portal de Gerenciamento](http://go.microsoft.com/fwlink/?linkid=248092&clcid=0x409).
	
6.	Igual que uma rede real, a rede virtual precisa um intervalo de endereços IP (conhecido como espaço de endereço) para atribuir máquinas virtuais que você coloque nele. A rede virtual também oferece suporte a sub-redes, que precisam seus próprios espaços de endereço, derivado do espaço de endereço da rede virtual. Para este tutorial, criaremos o BackEndSubnet e FrontEndSubnet. Na página **Espaços de endereço de rede virtual**, configure o seguinte:

	- Para Espaço de Endereço, selecione **/16 (65535)** no **CIDR (CONTAGEM DE ENDEREÇOS)**.

	- Para sub-redes, na primeira fila, digite **BackEndSubnet** sobre o nome existente e **10.0.1.0** para o IP inicial, em seguida, selecione **/24 (256)** em **CIDR (CONTAGEM DE ENDEREÇOS)**. Clique em **adicionar sub-rede** e, em seguida, digite **FrontEndSubnet** para o nome e **10.0.2.0** para o IP inicial.
		
	![][Image4]

 Ao retornar a nosso diagrama da rede virtual, você configurou os seguintes espaços de endereço:
 
	![][Image7]

 Observe que o espaço de endereço para uma rede virtual deve ser dos espaços de endereço privados de 10.0.0.0/8, 172.16.0.0/12 ou 192.168.0.0/16 e especificados na notação de Roteamento entre domínios sem classificação (CIDR) (também conhecida como notação de prefixo de rede). Para obter mais informações sobre as configurações nesta página, consulte a seção da página Espaços de Endereço da Rede Virtual em [Sobre como configurar uma rede virtual usando o Portal de Gerenciamento](http://go.microsoft.com/fwlink/?linkid=248092&clcid=0x409).


7. Clique na marca de seleção na parte inferior esquerda da página e sua rede virtual começará a ser criada. Quando sua rede virtual tiver sido criada, você verá a denominação **Criada** listada sob Status na página **Redes** no Portal de gerenciamento do Azure.  

	![][Image5]

Você pode continuar aprendendo sobre os serviços da infraestrutura do Azure com o seguinte:

- [Como criar uma máquina virtual personalizada](http://www.windowsazure.com/manage/windows/how-to-guides/custom-create-a-vm/) Use este tópico para instalar uma máquina virtual na sua rede virtual. Para obter mais informações sobre máquinas virtuais e opções de instalação, consulte [Máquinas Virtuais do Azure](http://www.windowsazure.com/manage/windows/).

- [Instalar uma nova floresta do Active Directory em uma rede virtual do Azure](http://www.windowsazure.com/manage/services/networking/active-directory-forest/) - Use este tópico para instalar uma nova floresta do Active Directory (AD) do Windows Server sem conectividade com qualquer outra rede. O tutorial explicará as etapas específicas necessárias para criar uma máquina virtual (VM) para uma nova instalação de floresta. Se você planeja usar este tutorial, não crie qualquer VMs usando o Portal de Gerenciamento. Para obter mais informações, consulte [Diretrizes para implantar o Active Directory do Windows Server em máquinas virtuais do Azure](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx).

Para remover esta rede virtual, selecione-a, clique em **Excluir** e, em seguida, clique em **Sim**.

Quanto você estiver pronto para criar uma rede virtual somente de nuvem que funcione para sua configuração específica, consulte [Configurar uma rede virtual apenas de nuvem no Portal de Gerenciamento](http://msdn.microsoft.com/library/azure/dn631643.aspx). 

Se você estiver procurando cenários de design e informações avançadas sobre Rede Virtual, consulte [Visão geral de Rede Virtual do Windows Azure](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx). 

Para obter procedimentos e definições da configuração Rede Virtual adicionais, consulte [Tarefas de configuração de Rede Virtual do Azure](http://go.microsoft.com/fwlink/?linkid=296652&clcid=0x409).


## Consulte também

-  [Perguntas Frequentes sobre a Rede virtual do Azure](http://go.microsoft.com/fwlink/?LinkId=296650)

-  [Tarefas de configuração da rede virtual do Azure](http://go.microsoft.com/fwlink/?LinkId=296652)

-  [Configurar uma Rede Virtual usando arquivos de configuração de rede](http://msdn.microsoft.com/library/windowsazure/jj156097.aspx)

-  [Resolução de nomes do Azure](http://go.microsoft.com/fwlink/?LinkId=248097)


[Image1]: ./media/create-virtual-network/createVNet_01_OpenVirtualNetworkWizard.png
[Image2]: ./media/create-virtual-network/createVNet_02_VirtualNetworkDetails.png
[Image3]: ./media/create-virtual-network/createVNet_03_DNSServersandVPNConnectivity.png
[Image4]: ./media/create-virtual-network/createVNet_04_VirtualNetworkAddressSpaces.png
[Image5]: ./media/create-virtual-network/createVNet_05_VirtualNetworkCreatedStatus.png
[Image7]: ./media/create-virtual-network/createVNet_07_VNetExampleSpaces.png
[Image8]: ./media/create-virtual-network/createVNet_07_VNetExampleSpaces.png


<!--HONumber=47-->
