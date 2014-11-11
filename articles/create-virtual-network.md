<properties linkid="manage-services-create-a-virtual-network" urlDisplayName="Tutorial: Create a cloud-only virtual network" pageTitle="Tutorial: Create a cloud-only virtual network" metaKeywords="" description="Learn how to create an example cloud-only Azure Virtual Network in this tutorial." metaCanonical="" services="virtual-machines,virtual-network" documentationCenter="" title="Tutorial: Create a Clound-only Virtual Network in Azure" authors="cherylmc" solutions="" manager="adinah" editor="" />

<tags ms.service="virtual-network" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/29/2014" ms.author="cherylmc" />

# Tutorial: Criar uma rede virtual somente de nuvem no Azure

Este tutorial explica as etapas no Portal de Gerenciamento do Azure para criar uma rede virtual somente de nuvem de exemplo que contenha dois sub-redes. A rede de virtual resultante terá a aparência a seguir:

![createvnet][createvnet]

Por exemplo, o FrontEndSubnet poderia ser usado para servidores web e o BackendSubnet poderia ser usado para SQL servers ou controladores de domínio.

Este tutorial pressupõe que você não tem experiência anterior com o Azure. O objetivo é ajudar a familiarizar-se com as etapas requeridas para criar sua própria rede virtual, o orientando por uma configuração de exemplo. Se você quiser criar uma rede virtual somente de nuvem que funcione para sua configuração específica, consulte [Configurar uma rede virtual apenas de nuvem no Portal de Gerenciamento][Configurar uma rede virtual apenas de nuvem no Portal de Gerenciamento]. Se você estiver procurando cenários de design e informações avançadas sobre Rede Virtual, consulte [Visão geral de Rede Virtual do Windows Azure][Visão geral de Rede Virtual do Windows Azure].

<div class="dev-callout"> 
<b>Observa&ccedil;&atilde;o</b> 
<p>Este tutorial n&atilde;o lhe orienta para criar uma configura&ccedil;&atilde;o entre locais, na qual a rede virtual &eacute; conectada &agrave; sua rede organizacional. Para um tutorial que lhe oriente para criar uma rede virtual com conectividade entre locais e a conex&atilde;o VPO site-a-site (por exemplo, conectar-se ao Active Directory ou SharePoint localizado na sua companhia), consulte o <a href="/pt-br/manage/services/networking/cross-premises-connectivity/">Tutorial: Criar uma rede virtual para conectividade site a site entre instala&ccedil;&otilde;es</a>.</p> 
</div>

## Objetivos

Neste tutorial você aprenderá como configurar uma rede virtual básica somente de nuvem do Azure com duas sub-redes.

## Pré-requisitos

-   Uma conta do Microsoft com pelo menos uma assinatura do Azure válida e ativa. Se você ainda não tiver uma conta do Azure, pode se inscrever para uma conta de avaliação gratuita em [Teste o Azure][Teste o Azure]. Se você tiver uma assinatura do MSDN, consulte [Preço especial do Microsoft Azure: Benefícios do MSDN, MPN, e Bizspark][Preço especial do Microsoft Azure: Benefícios do MSDN, MPN, e Bizspark].

## Criar a rede virtual para este tutorial

Para criar esta rede virtual somente de nuvem de exemplo, faça o seguinte

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].

2.  Na esquina inferior esquerda da tela, clique em **Novo** \> **Serviços de Rede** \> **Rede Virtual** e, em seguida, clique em **Criação Personalizada** para começar o assistente de configuração.

    ![][0]

3.  Na página **Detalhes da rede virtual**, insira as seguintes informações:

-   **Nome -** digite **YourVirtualNetwork**.

-   **Região -** A rede virtual será criada em um datacenter localizado na região especificada. Para obter o melhor desempenho, seleciona a região à qual você pertence da lista suspensa.

    ![][1]

1.  Clique na seta de avanço na parte inferior direita. Para obter mais informações sobre as configurações nesta página, consulte a seção da página Detalhes da Rede Virtual em [Sobre como configurar uma rede virtual usando o Portal de Gerenciamento][Sobre como configurar uma rede virtual usando o Portal de Gerenciamento].

2.  Na página **Servidores DNS e Conectividade VPN**, clique na seta do avanço na parte inferior direita. O Azure atribuirá um Servidor DNS do Azure baseado na Internet a novas máquinas virtuais que sejam adicionadas a esta rede virtual, o que permitirá que elas acessem a recursos da Internet. Para obter mais informações sobre as configurações nessa página, consulte a página de Servidores DNS e Conectividade VPN em [Sobre como configurar uma rede virtual no Portal de Gerenciamento][Sobre como configurar uma rede virtual usando o Portal de Gerenciamento].

3.  Igual que uma rede real, a rede virtual precisa um intervalo de endereços IP (conhecido como espaço de endereço) para atribuir máquinas virtuais que você coloque nele. A rede virtual também oferece suporte a sub-redes, que precisam seus próprios espaços de endereço, derivado do espaço de endereço da rede virtual. Para este tutorial, criaremos o BackEndSubnet e FrontEndSubnet. Na página **Espaços de endereço da rede virtual**, configure o seguinte:

    -   Para Espaço de Endereço, selecione **/16 (65535)** no **CIDR (CONTAGEM DE ENDEREÇOS)**.

    -   Para sub-redes, na primeira fila, digite **BackEndSubnet** sobre o nome existente e **10.0.1.0** para o IP inicial, em seguida, selecione **/24 (256)** em **CIDR (CONTAGEM DE ENDEREÇOS)**. Clique em **adicionar sub-rede** e, em seguida, digite **FrontEndSubnet** para o nome e **10.0.2.0** para o IP inicial.

    ![][2]

Ao retornar a nosso diagrama da rede virtual, você configurou os seguintes espaços de endereço:

    ![][Image7] 

Observe que o espaço de endereço para uma rede virtual deve ser dos espaços de endereço privados de 10.0.0.0/8, 172.16.0.0/12, ou 192.168.0.0/16 e especificados na notação de Roteamento entre domínios sem classificação (CIDR) (também conhecida como notação de prefixo de rede). Para obter mais informações sobre as configurações nesta página, consulte a seção da página Espaços de Endereço da Rede Virtual em [Sobre como configurar uma rede virtual usando o Portal de Gerenciamento][Sobre como configurar uma rede virtual usando o Portal de Gerenciamento].

1.  Clique na marca de seleção na parte inferior esquerda da página e sua rede virtual começará a ser criada. Quando sua rede virtual tiver sido criada, você verá a denominação **Criada** listada sob Status na página **Rede** no Portal de Gerenciamento do Azure.

    ![][3]

Você pode continuar aprendendo sobre os serviços da infraestrutura do Azure com o seguinte:

-   [Como criar uma máquina virtual personalizada][Como criar uma máquina virtual personalizada] Use este tópico para instalar uma máquina virtual na sua rede virtual. Para obter mais informações sobre máquinas virtuais e opções de instalação, consulte [Máquinas Virtuais do Azure][Máquinas Virtuais do Azure].

-   [Instalar uma nova floresta do Active Directory em uma rede virtual do Azure][Instalar uma nova floresta do Active Directory em uma rede virtual do Azure] - Use este tópico para instalar uma nova floresta do Active Directory (AD) do Windows Server sem conectividade com qualquer outra rede. O tutorial explicará as etapas específicas necessárias para criar uma máquina virtual (VM) para uma nova instalação de floresta. Se você planeja usar este tutorial, não crie qualquer VMs usando o Portal de Gerenciamento. Para obter mais informações, consulte [Diretrizes para implantar o Active Directory do Windows Server em máquinas virtuais do Azure][Diretrizes para implantar o Active Directory do Windows Server em máquinas virtuais do Azure].

Para remover esta rede virtual, selecione-a, clique em **Excluir** e, em seguida, clique em **Sim**.

Quanto você estiver pronto para criar uma rede virtual somente de nuvem que funcione para sua configuração específica, consulte [Configurar uma rede virtual apenas de nuvem no Portal de Gerenciamento][Configurar uma rede virtual apenas de nuvem no Portal de Gerenciamento].

Se você estiver procurando cenários de design e informações avançadas sobre Rede Virtual, consulte [Visão geral de Rede Virtual do Windows Azure][Visão geral de Rede Virtual do Windows Azure].

Para obter procedimentos e definições da configuração Rede Virtual adicionais, consulte [Tarefas de configuração de Rede Virtual do Azure][Tarefas de configuração de Rede Virtual do Azure].

## Consulte também

-   [Perguntas Frequentes sobre a Rede virtual do Azure][Perguntas Frequentes sobre a Rede virtual do Azure]

-   [Tarefas de configuração da rede virtual do Azure][Tarefas de configuração da rede virtual do Azure]

-   [Configurar uma Rede Virtual usando arquivos de configuração de rede][Configurar uma Rede Virtual usando arquivos de configuração de rede]

-   [Resolução de nomes do Azure][Resolução de nomes do Azure]

  [createvnet]: ./media/create-virtual-network/createVNet_06_VNetExample.png
  [Configurar uma rede virtual apenas de nuvem no Portal de Gerenciamento]: http://msdn.microsoft.com/library/azure/dn631643.aspx
  [Visão geral de Rede Virtual do Windows Azure]: http://msdn.microsoft.com/library/windowsazure/jj156007.aspx
  [Teste o Azure]: http://www.windowsazure.com/pricing/free-trial/
  [Preço especial do Microsoft Azure: Benefícios do MSDN, MPN, e Bizspark]: http://azure.microsoft.com/pt-br/pricing/member-offers/msdn-benefits-details/
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com/
  [0]: ./media/create-virtual-network/createVNet_01_OpenVirtualNetworkWizard.png
  [1]: ./media/create-virtual-network/createVNet_02_VirtualNetworkDetails.png
  [Sobre como configurar uma rede virtual usando o Portal de Gerenciamento]: http://go.microsoft.com/fwlink/?linkid=248092&clcid=0x409
  [2]: ./media/create-virtual-network/createVNet_04_VirtualNetworkAddressSpaces.png
  [3]: ./media/create-virtual-network/createVNet_05_VirtualNetworkCreatedStatus.png
  [Como criar uma máquina virtual personalizada]: http://www.windowsazure.com/pt-br/manage/windows/how-to-guides/custom-create-a-vm/
  [Máquinas Virtuais do Azure]: http://www.windowsazure.com/pt-br/manage/windows/
  [Instalar uma nova floresta do Active Directory em uma rede virtual do Azure]: http://www.windowsazure.com/pt-br/manage/services/networking/active-directory-forest/
  [Diretrizes para implantar o Active Directory do Windows Server em máquinas virtuais do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj156090.aspx
  [Tarefas de configuração de Rede Virtual do Azure]: http://go.microsoft.com/fwlink/?linkid=296652&clcid=0x409
  [Perguntas Frequentes sobre a Rede virtual do Azure]: http://go.microsoft.com/fwlink/?LinkId=296650
  [Tarefas de configuração da rede virtual do Azure]: http://go.microsoft.com/fwlink/?LinkId=296652
  [Configurar uma Rede Virtual usando arquivos de configuração de rede]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj156097.aspx
  [Resolução de nomes do Azure]: http://go.microsoft.com/fwlink/?LinkId=248097
