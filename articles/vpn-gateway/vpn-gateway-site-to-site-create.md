<properties
   pageTitle="Criar uma rede virtual com uma conexão VPN site a site usando o Portal Clássico do Azure | Microsoft Azure"
   description="Criar uma rede virtual com uma conexão VPN site a site para configurações entre locais e híbridas usando o modelo de implantação clássico."
   services="vpn-gateway"
   documentationCenter=""
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/21/2015"
   ms.author="cherylmc"/>

# Criar uma rede virtual com uma conexão VPN site a site usando o Portal Clássico do Azure

> [AZURE.SELECTOR]
- [Azure Classic Portal](vpn-gateway-site-to-site-create.md)
- [PowerShell - Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)

Este artigo o orientará pela criação de uma rede virtual e de uma conexão VPN site a site com sua rede local. Este artigo se aplica ao modelo de implantação clássico.

>[AZURE.NOTE]É importante saber que atualmente o Azure funciona com dois modelos de implantação: Gerenciador de Recursos e clássico. Antes de começar a configuração, entenda as ferramentas e os modelos de implantação. Para saber mais sobre os modelos de implantação, consulte [Modelos de implantação do Azure](../azure-classic-rm.md).

Você pode selecionar o artigo do modelo de implantação e da ferramenta de implantação usando as guias acima. Por exemplo, se você quiser criar uma conexão gateway de VPN site a site usando o modelo do Gerenciador de Recursos do Azure em vez de usar o modelo clássico, clique na guia **PowerShell - Gerenciador de Recursos** (acima) para navegar até [Criar uma conexão VPN site a site usando o Gerenciador de Recursos do Azure e o PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md).

 
## Antes de começar

Verifique se você tem os itens a seguir antes de iniciar a configuração.

- Um dispositivo VPN compatível e alguém que possa configurá-lo. Consulte [Sobre Dispositivos VPN](vpn-gateway-about-vpn-devices.md). Se você não estiver familiarizado com a configuração de seu dispositivo VPN ou se não estiver familiarizado com os intervalos de endereços IP localizados em sua configuração de rede local, será necessário coordenar com alguém que possa fornecer os detalhes para você.

-  Um endereço IP público voltado para o exterior para seu dispositivo VPN. Esse endereço IP não pode estar localizado atrás de um NAT.

- Uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [benefícios de assinante do MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para uma [avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/).


## Criar sua rede virtual

1. Faça logon no **Portal Clássico do Azure**.

2. No canto inferior esquerdo da tela, clique em **Novo**. No painel de navegação, clique em **Serviços de Rede** e, em seguida, clique em **Rede Virtual**. Clique em **Criação Personalizada** para iniciar o assistente de configuração.

3. Preencha as informações nas páginas a seguir para criar sua rede virtual.

## Página Detalhes da Rede Virtual

Insira as seguintes informações.

- **Nome**: nome da sua rede virtual. Por exemplo, *EastUSVNet*. Você usará esse nome de rede virtual quando implantar suas VMs e instâncias de PaaS. Portanto, é melhor não criar um nome muito complicado.
- **Local**: o local está diretamente relacionado ao local físico (região) onde você deseja que os recursos (VMs) residam. Por exemplo, se você desejar que as VMs implantadas nesta rede virtual estejam localizadas fisicamente no *leste dos EUA*, selecione esse local. Você não pode alterar a região associada à sua rede virtual depois de criá-la.

## Página Servidores DNS e Conectividade VPN
Insira as informações a seguir e, em seguida, clique na seta de avanço no canto inferior direito.

- **Servidores DNS**: insira o nome do servidor DNS e o endereço IP ou selecione um servidor DNS previamente registrado no menu de atalho. Essa configuração não cria um servidor DNS. Ela permite que você especifique os servidores DNS que deseja usar para a resolução de nomes para essa rede virtual.
- **Configurar VPN Site a Site**: marque a caixa de seleção **Configurar uma VPN site a site**.
- **Rede Local**: uma rede local representa sua localização física no local. Você pode selecionar uma rede local já criada anteriormente ou pode criar uma nova rede local. No entanto, se você optar por usar uma rede local criada anteriormente, vai querer acessar a página de configuração **Redes Locais** e garantir que o endereço IP do Dispositivo VPN (endereço IPv4 voltado para o público) para o dispositivo VPN usado nesta conexão seja preciso.

## Página Conectividade Site a Site
Se estiver criando uma nova rede local, você verá a página **Conectividade Site a Site**. Se você quiser usar uma rede local criada anteriormente, essa página não será exibida no assistente e você poderá ir para a próxima seção.

Insira as informações a seguir e, em seguida, clique na seta de avanço.

- 	**Nome**: o nome que você deseja dar ao site de rede local.
- 	**Endereço IP do Dispositivo VPN**: é o endereço IPv4 voltado para o público do seu dispositivo VPN local que você usará para conectar-se ao Azure. O dispositivo VPN não pode ser localizado por trás de um NAT.
- 	**Espaço de Endereço**: inclua o IP Inicial e a Contagem de Endereços (CIDR). É o local onde você especifica os intervalos de endereços que deseja enviar por meio do gateway de rede virtual para o local. Se um endereço IP de destino estiver dentro dos intervalos especificados aqui, ele será roteado por meio do gateway de rede virtual.
- 	**Adicionar espaço de endereço**: se você tiver vários intervalos de endereços que deseja enviar pelo gateway da rede virtual, é o local onde você especifica cada intervalo de endereços adicionais. Você poderá adicionar ou remover intervalos posteriormente na página **Rede Local**.

## Página Espaços de endereço de rede virtual
Especifique o intervalo de endereços que você deseja usar para sua rede virtual. Esses são os DIPS (endereços IP dinâmicos) que serão atribuídos às VMs e a outras instâncias de função que você implantar nessa rede virtual.

É particularmente importante selecionar um intervalo que não se sobreponha a nenhum dos intervalos usados para sua rede local. Você precisará coordenar com o administrador da rede. Talvez seu administrador da rede precise reservar um intervalo de endereços IP de seu espaço de endereço de rede local para que você possa usar para sua rede virtual.

Insira as informações a seguir e clique na marca de seleção no canto inferior direito para configurar sua rede.

- **Espaço de Endereço**: inclui o IP Inicial e a Contagem de Endereços. Verifique se os espaços de endereço especificados não se sobrepõem a qualquer espaço de endereço em sua rede local.
- **Adicionar sub-rede**: incluindo o IP Inicial e a Contagem de Endereços. Sub-redes adicionais não são necessárias, mas convém criar uma sub-rede separada para VMs que terão DIPS estáticos. Ou então, você pode colocar suas VMs em uma sub-rede separada das outras instâncias de função.
- **Adicionar sub-rede de gateway**: clique para adicionar a sub-rede de gateway. A sub-rede de gateway é usada apenas para o gateway de rede virtual e é necessária para esta configuração.

Clique na marca de seleção na parte inferior da página e sua rede virtual começará a ser criada. Quando ela for concluída, você verá **Criada** listada em **Status** na página **Redes** no Portal Clássico do Azure. Depois que a rede virtual tiver sido criada, você poderá configurar seu gateway de rede virtual.

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## Configurar seu Gateway de Rede Virtual

Em seguida, você configurará o gateway de rede virtual para criar uma conexão VPN site a site segura. Consulte [Configurar um gateway de rede virtual no Portal Clássico do Azure](vpn-gateway-configure-vpn-gateway-mp.md).

## Próximas etapas

Você pode saber mais sobre a conectividade entre locais para redes virtuais neste artigo: [Sobre a conectividade segura entre locais para redes virtuais](vpn-gateway-cross-premises-options.md).

Se você quiser configurar uma conexão VPN ponto a site, consulte [Configurar uma conexão VPN ponto a site](vpn-gateway-point-to-site-create.md).

Você pode adicionar máquinas virtuais à sua rede virtual. Confira [Como criar uma máquina virtual personalizada](../virtual-machines/virtual-machines-create-custom.md).

Se você quiser configurar uma conexão entre sua rede virtual clássica e uma rede virtual criada usando o modo do Gerenciador de Recursos do Azure, consulte [Como conectar Redes Virtuais clássicas a Redes Virtuais do Gerenciador de Recursos do Azure](../virtual-network/virtual-networks-arm-asm-s2s-howto.md).

<!---HONumber=AcomDC_1203_2015-->