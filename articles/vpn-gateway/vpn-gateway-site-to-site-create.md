<properties
   pageTitle="Criar uma rede virtual com uma conexão VPN site a site usando o Portal de Gerenciamento | Microsoft Azure"
   description="Criar uma rede virtual com uma conexão VPN site a site entre configurações locais e híbridas"
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
   ms.date="07/23/2015"
   ms.author="cherylmc"/>

# Criar uma rede virtual com uma conexão VPN site a site usando o Portal de Gerenciamento

> [AZURE.SELECTOR]
- [Azure Portal](vpn-gateway-site-to-site-create.md)
- [PowerShell - Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)

Você pode conectar seu local a uma rede virtual ao criar uma conexão VPN site a site. Esse procedimento conduzirá você pela criação de uma rede virtual e pela criação de uma conexão VPN site a site entre a rede virtual recém-criada e seu local.

Atualmente, o Azure tem dois modos de gerenciamento: Gerenciamento de Serviços do Azure e ARM (Gerenciador de Recursos do Azure). A configuração site a site é diferente; depende de como sua rede virtual foi criada. Essas instruções se aplicam ao Gerenciamento de Serviços do Azure. Se desejar criar uma conexão VPN site a site do Gerenciador de Recursos do Azure, consulte [Criar uma conexão VPN site a site usando o Gerenciador de Recursos do Azure e o PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md).


## Antes de começar

- Verifique se o dispositivo VPN que você deseja usar atende aos requisitos necessários para criar uma conexão de rede virtual entre locais. Consulte [Sobre dispositivos VPN para Conectividade de Rede Virtual](http://go.microsoft.com/fwlink/p/?LinkId=615099) para obter mais informações.

- Obter um IP IPv4 voltado para o exterior para seu dispositivo VPN. Esse endereço IP é necessário para uma configuração site a site e é usado para seu dispositivo VPN, que não pode estar localizado atrás de uma NAT.

>[AZURE.IMPORTANT]Se você não estiver familiarizado com a configuração de seu dispositivo VPN ou se não estiver familiarizado com os intervalos de endereços IP localizados em sua configuração de rede local, será necessário coordenar com alguém que possa fornecer os detalhes para você.

## Criar sua rede virtual

1. Faça logon no **Portal de Gerenciamento**.

2. No canto inferior esquerdo da tela, clique em **Nova**. No painel de navegação, clique em **Serviços de Rede** e, em seguida, clique em **Rede Virtual**. Clique em **Criação Personalizada** para iniciar o assistente de configuração.

3. Preencha as informações nas páginas a seguir para criar sua rede virtual.

## Página Detalhes da Rede Virtual

Insira as informações abaixo.

- **Nome**: nome da sua rede virtual. Por exemplo, *EastUSVNet*. Você usará esse nome de rede virtual quando implantar suas VMs e instâncias de PaaS. Portanto, é melhor não criar um nome muito complicado.
- **Local**: o local está diretamente relacionado ao local físico (região) onde você deseja que os recursos (VMs) residam. Por exemplo, se você desejar que as VMs implantadas nesta rede virtual estejam localizadas fisicamente no *leste dos EUA*, selecione esse local. Você não pode alterar a região associada à sua rede virtual depois de criá-la.

## Página Servidores DNS e Conectividade VPN
Insira as informações a seguir e, em seguida, clique na seta de avanço no canto inferior direito.

- **Servidores DNS**: insira o nome do servidor DNS e o endereço IP ou selecione um servidor DNS previamente registrado no menu suspenso. Essa configuração não cria um servidor DNS. Ela permite que você especifique os servidores DNS que deseja usar para a resolução de nomes para essa rede virtual.
- **Configurar VPN Site a Site**: marque a caixa de seleção **Configurar uma VPN site a site**.
- **Rede Local**: uma rede local representa sua localização física no local. Você pode selecionar uma rede local já criada anteriormente ou pode criar uma nova rede local. No entanto, se você optar por usar uma rede local criada anteriormente, vai querer acessar a página de configuração **Redes Locais** e garantir que o endereço IP do Dispositivo VPN (endereço IPv4 voltado para o público) para o dispositivo VPN usado nesta conexão seja preciso.

## Página Conectividade Site a Site
Se estiver criando uma nova rede local, você verá a página **Conectividade Site a Site**. Se você quiser usar uma rede local criada anteriormente, essa página não será exibida no assistente e você poderá ir para a próxima seção.

Insira as informações a seguir e, em seguida, clique na seta de avanço.

- 	**Nome**: o nome que você deseja dar ao site de rede local.
- 	**Endereço IP do Dispositivo VPN**: é o endereço IPv4 voltado para o público do seu dispositivo VPN local que você usará para conectar-se ao Azure. O dispositivo VPN não pode ser localizado por trás de um NAT.
- 	**Espaço de Endereço**: inclua o IP Inicial e a Contagem de Endereços (CIDR). É o local onde você especifica os intervalos de endereços que deseja enviar por meio do gateway de rede virtual para o local. Se um endereço IP de destino estiver dentro dos intervalos especificados aqui, ele será roteado por meio do gateway de rede virtual.
- 	**Adicionar espaço de endereço**: se você tiver vários intervalos de endereços que deseja enviar pelo gateway da rede virtual, é o local onde você especifica cada intervalo de endereços adicionais. Você pode adicionar ou remover intervalos posteriormente na página Rede Local.

## Página Espaços de endereço de rede virtual
Especifique o intervalo de endereços que você deseja usar para sua rede virtual. Esses são os DIPS (endereços IP dinâmicos) que serão atribuídos às VMs e a outras instâncias de função que você implantar nessa rede virtual.

É particularmente importante selecionar um intervalo que não se sobreponha a nenhum dos intervalos usados para sua rede local. Você precisará coordenar com o administrador da rede. Talvez seu administrador da rede precise reservar um intervalo de endereços IP de seu espaço de endereço de rede local para que você possa usar para sua rede virtual.

Insira as informações a seguir e clique na marca de seleção no canto inferior direito para configurar sua rede.

- **Espaço de Endereço**: inclui o IP Inicial e a Contagem de Endereços. Verifique se os espaços de endereço especificados não se sobrepõem a qualquer espaço de endereço em sua rede local.
- **Adicionar sub-rede**: incluindo o IP Inicial e a Contagem de Endereços. Sub-redes adicionais não são necessárias, mas convém criar uma sub-rede separada para VMs que terão DIPS estáticos. Ou então, você pode colocar suas VMs em uma sub-rede separada das outras instâncias de função.
- **Adicionar sub-rede de gateway**: clique para adicionar a sub-rede de gateway. A sub-rede de gateway é usada apenas para o gateway de rede virtual e é necessária para esta configuração.

Clique na marca de seleção na parte inferior da página e sua rede virtual começará a ser criada. Quando ela for concluída, você verá **Criado** listado em **Status** na página **Redes** no Portal de Gerenciamento. Depois que a rede virtual tiver sido criada, você poderá configurar seu gateway de rede virtual.

## Configurar seu Gateway de Rede Virtual

Em seguida, você configurará o **Gateway de Rede Virtual** para criar uma conexão VPN site a site segura. Veja [Configurar um Gateway de Rede Virtual no Portal de Gerenciamento](vpn-gateway-configure-vpn-gateway-mp.md).

## Próximas etapas

Você pode saber mais sobre a conectividade entre locais da Rede Virtual neste artigo: [Sobre conectividade segura entre locais da rede virtual](https://msdn.microsoft.com/library/azure/dn133798.aspx).

Se você quiser configurar uma conexão VPN ponto a site, veja [Configurar uma conexão VPN site a site](vpn-gateway-point-to-site-create.md)

Você pode adicionar máquinas virtuais à sua rede virtual. Veja [Como criar uma máquina virtual personalizada](../virtual-machines/virtual-machines-create-custom.md).

Se você quiser configurar uma conexão VNet usando o RRAS, consulte [Configurar uma VPN site a site usando o RRAS (Serviço de Roteamento e Acesso Remoto) do Windows Server 2012](https://msdn.microsoft.com/library/dn636917.aspx).
 

<!---HONumber=July15_HO5-->