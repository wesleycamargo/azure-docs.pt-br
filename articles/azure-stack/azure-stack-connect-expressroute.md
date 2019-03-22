---
title: Conectar-se a pilha do Azure para o Azure usando o ExpressRoute
description: Saiba como conectar redes virtuais no Azure Stack para redes virtuais no Azure usando o ExpressRoute.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 10/22/2018
ms.openlocfilehash: 0ebd17eca363d7fc02daeb851bb24b8d1d307efc
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339594"
---
# <a name="connect-azure-stack-to-azure-using-azure-expressroute"></a>Conectar-se a pilha do Azure para o Azure usando o Azure ExpressRoute

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Este artigo descreve como conectar uma rede virtual do Azure Stack para uma rede virtual do Azure usando um [o Microsoft Azure ExpressRoute](/azure/expressroute/) direcionar a conexão.

Você pode usar este artigo como um tutorial e usar os exemplos para configurar o mesmo ambiente de teste. Ou, você pode usar o artigo como um passo a passo que orienta você pela configuração de seu próprio ambiente de ExpressRoute.

## <a name="overview-assumptions-and-prerequisites"></a>Visão geral, as suposições e pré-requisitos

O Azure ExpressRoute permite que você estenda suas redes locais para a nuvem da Microsoft por meio de uma conexão privada fornecida por um provedor de conectividade. O ExpressRoute não é uma conexão VPN pela internet pública.

Para obter mais informações sobre o ExpressRoute do Azure, consulte a [visão geral do ExpressRoute](../expressroute/expressroute-introduction.md).

### <a name="assumptions"></a>Suposições

Este artigo pressupõe que:

* Você tem um conhecimento prático do Azure.
* Você tem uma compreensão básica do Azure Stack.
* Você tem uma compreensão básica da rede.

### <a name="prerequisites"></a>Pré-requisitos

Para conectar o Azure Stack e o Azure usando o ExpressRoute, você deve atender aos seguintes requisitos:

* Um provisionados [circuito do ExpressRoute](../expressroute/expressroute-circuit-peerings.md) por meio de uma [provedor de conectividade](../expressroute/expressroute-locations.md).
* Uma assinatura do Azure para criar um circuito do ExpressRoute e redes virtuais no Azure.
* Um roteador deve:
  * Suporte a conexões VPN Site a Site entre sua interface de LAN e um Gateway multilocatário do Azure Stack.
  * Suporte à criação de vários VRFs (roteamento Virtual e o encaminhamento) se não houver mais de um locatário em sua implantação do Azure Stack.
* Um roteador que tem:
  * Uma porta de rede de longa distância conectado ao circuito de ExpressRoute.
  * Uma porta LAN conectada para o Gateway multilocatário do Azure Stack.

### <a name="expressroute-network-architecture"></a>Arquitetura de rede de ExpressRoute

O diagrama a seguir mostra os ambientes do Azure e o Azure Stack depois de concluir a configuração do ExpressRoute usando os exemplos neste artigo:

*Figura 1. Rede de ExpressRoute*

![Rede de ExpressRoute](media/azure-stack-connect-expressroute/Conceptual.png)

O diagrama a seguir mostra como vários locatários se conectam da infraestrutura do Azure Stack por meio do roteador de ExpressRoute para o Azure no Microsoft edge:

*Figura 2. Conexões de vários locatários*

![Conexões de vários locatários com o ExpressRoute](media/azure-stack-connect-expressroute/Architecture.png)

O exemplo neste artigo usa a mesma arquitetura de multilocatário, mostrada na Figura 2 para conectar o Azure Stack no Azure usando o emparelhamento privado do ExpressRoute. A conexão é feita usando uma conexão de VPN site a site do gateway de rede virtual no Azure Stack para um roteador de ExpressRoute.

As etapas neste artigo mostram como criar uma conexão de ponta a ponta entre duas redes virtuais de dois locatários diferentes no Azure Stack para redes virtuais correspondente no Azure. Configurar dois locatários é opcional; Você também pode usar estas etapas para um único locatário.

## <a name="configure-azure-stack"></a>Configurar o Azure Stack

Para configurar o ambiente do Azure Stack para o locatário primeiro, use as etapas no diagrama a seguir como guia. Se você estiver configurando mais de um locatário, repita essas etapas:

>[!NOTE]
>Estas etapas mostram como criar recursos usando o portal do Azure Stack, mas você também pode usar o PowerShell.

![Configuração de rede do Azure Stack](media/azure-stack-connect-expressroute/image2.png)

### <a name="before-you-begin"></a>Antes de começar

Antes de começar a configurar o Azure Stack, você precisa:

* Uma implantação de sistema integrado do Azure Stack ou em uma implantação do Kit de desenvolvimento na pilha do Azure (ASDK). Para obter informações sobre como implantar o ASDK, consulte o [início rápido de implantação do Kit de desenvolvimento do Azure Stack](azure-stack-deploy-overview.md).
* Uma oferta no Azure Stack que os usuários podem assinar. Para obter mais informações, consulte [planos, ofertas e assinaturas](azure-stack-plan-offer-quota-overview.md).

### <a name="create-network-resources-in-azure-stack"></a>Criar recursos de rede no Azure Stack

Use os procedimentos a seguir para criar os recursos de rede necessária no Azure Stack para um locatário.

#### <a name="create-the-virtual-network-and-vm-subnet"></a>Criar a rede virtual e sub-rede de VM

1. Entrar no portal do usuário com uma conta de usuário (Locatário).

2. No portal, selecione **+ criar um recurso**.

3. Sob **do Azure Marketplace**, selecione **Networking**.

4. Sob **em destaque**, selecione **rede Virtual**.

5. Sob **criar rede virtual**, insira os valores mostrados na tabela a seguir nos campos apropriados:

   |Campo  |Valor  |
   |---------|---------|
   |Nome     |Tenant1VNet1         |
   |Espaço de endereço     |10.1.0.0/16|
   |Nome da sub-rede     |Tenant1-Sub1|
   |Intervalo de endereço de sub-rede     |10.1.1.0/24|

6. Você deverá ver a assinatura que você criou anteriormente populada na **assinatura** campo. Para os campos restantes:

    * Sob **grupo de recursos**, selecione **criar novo** para criar um novo grupo de recursos ou se você já tiver um, selecione **usar existente**.
    * Verifique se o padrão **local**.
    * Clique em **Criar**.
    * (Opcional) Clique em **fixar no painel**.

#### <a name="create-the-gateway-subnet"></a>Criar a sub-rede de gateway

1. Sob **rede Virtual**, selecione **Tenant1VNet1**.
1. Em **CONFIGURAÇÕES**, selecione **Sub-redes**.
1. Selecione **+ subrede de Gateway** para adicionar uma sub-rede de gateway à rede virtual.
1. O nome da sub-rede é definido como **Sub-rede de Gateway** por padrão. As sub-redes de gateway são um caso especial e devem usar esse nome para funcionar corretamente.
1. Verifique se que o **intervalo de endereços** é **10.1.0.0/24**.
1. Clique em **OK** para criar a sub-rede de gateway.

#### <a name="create-the-virtual-network-gateway"></a>Criar o gateway de rede virtual

1. No portal do usuário do Azure Stack, clique em **+ criar um recurso**.
1. Sob **do Azure Marketplace**, selecione **Networking**.
1. Selecione **Gateway de Rede Virtual** na lista de recursos de rede.
1. No **nome** , insira **GW1**.
1. Selecione **Rede virtual**.
1. Selecione **Tenant1VNet1** na lista suspensa.
1. Selecione **endereço IP público**, em seguida, **escolher endereço IP público**e, em seguida, clique em **criar novo**.
1. No **nome** , digite **GW1-PiP**e, em seguida, clique em **Okey**.
1. O **Tipo de VPN** deve ter **Baseado em rota** selecionado por padrão. Mantenha essa configuração.
1. Verifique se **Assinatura** e **Local** estão corretos. Clique em **Criar**.

#### <a name="create-the-local-network-gateway"></a>Criar o gateway de rede local

O recurso de gateway de rede local identifica o gateway remoto na outra extremidade da conexão de VPN. Neste exemplo, a extremidade remota da conexão é sub interface LAN do roteador de ExpressRoute. Para o locatário 1, mostrada na Figura 2, o endereço remoto é 10.60.3.255.

1. Entre no portal do usuário do Azure Stack com sua conta de usuário e selecione **+ criar um recurso**.
1. Sob **do Azure Marketplace**, selecione **Networking**.
1. Selecione **gateway de rede local** na lista de recursos.
1. No **nome** , digite **ER-roteador-GW**.
1. Para o **endereço IP** campo, consulte a Figura 2. O endereço IP da interface ExpressRoute roteador LAN subpropriedades para locatário 1 é 10.60.3.255. Para o seu próprio ambiente, insira o endereço IP da interface de correspondente do seu roteador.
1. No **espaço de endereço** , insira o espaço de endereço das redes virtuais que você deseja se conectar no Azure. As sub-redes de locatário 1 em *Figura 2* são da seguinte maneira:

   * 192.168.2.0/24 é o hub de rede virtual no Azure.
   * 10.100.0.0/16 é a rede virtual no Azure do spoke.

   > [!IMPORTANT]
   > Este exemplo pressupõe que você está usando rotas estáticas para a conexão VPN Site a Site entre o gateway do Azure Stack e o roteador de ExpressRoute.

1. Verifique sua **assinatura**, **grupo de recursos**, e **local** estão corretos. Em seguida, selecione **Criar**.

#### <a name="create-the-connection"></a>Criar a conexão

1. No portal do usuário do Azure Stack, selecione **+ criar um recurso**.
1. Sob **do Azure Marketplace**, selecione **Networking**.
1. Selecione **Conexão** na lista de recursos.
1. Sob **Noções básicas**, escolha **Site a site (IPSec)** como o **tipo de Conexão**.
1. Selecione o **assinatura**, **grupo de recursos**, e **local**. Clique em **OK**.
1. Sob **as configurações**, selecione **gateway de rede Virtual**e, em seguida, selecione **GW1**.
1. Selecione **gateway de rede Local**e, em seguida, selecione **ER roteador GW**.
1. No **nome de Conexão** , insira **ConnectToAzure**.
1. No **chave compartilhada (PSK)** , insira **abc123** e, em seguida, selecione **Okey**.
1. Sob **resumo**, selecione **Okey**.

#### <a name="get-the-virtual-network-gateway-public-ip-address"></a>Obter o endereço IP público do gateway de rede virtual

Depois de criar o gateway de rede virtual, você pode obter o endereço IP público do gateway. Anote esse endereço no caso de você precisará dela mais tarde para sua implantação. Dependendo da sua implantação, esse endereço é usado como o **endereço IP interno**.

1. No portal do usuário do Azure Stack, selecione **todos os recursos**.
1. Sob **todos os recursos**, selecione o gateway de rede virtual, que é **GW1** no exemplo.
1. Sob **gateway de rede Virtual**, selecione **visão geral** da lista de recursos. Como alternativa, você pode selecionar **propriedades**.
1. O endereço IP que você deseja observar está listado em **endereço IP público**. Para a configuração de exemplo, esse endereço é 192.68.102.1.

#### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Para testar o tráfego de dados sobre a conexão VPN, você precisa de máquinas virtuais para enviar e receber dados na rede virtual do Azure Stack. Criar uma máquina virtual e implantá-lo para a sub-rede VM para sua rede virtual.

1. No portal do usuário do Azure Stack, selecione **+ criar um recurso**.
1. Sob **do Azure Marketplace**, selecione **computação**.
1. Na lista de imagens de máquina virtual, selecione a **Windows Server 2016 Datacenter Eval** imagem.

   >[!NOTE]
   >Se a imagem usada para este artigo não estiver disponível, peça ao seu operador do Azure Stack para fornecer uma imagem diferente do Windows Server.

1. Na **criar máquina virtual**, selecione **Noções básicas**, em seguida, digite **VM01** como o **nome**.
1. Insira um nome de usuário válido e uma senha. Você usará essa conta para entrar na VM após ela ter sido criada.
1. Fornecer um **assinatura**, **grupo de recursos**e um **local**. Selecione **OK**.
1. Sob **escolher um tamanho**, selecione um tamanho de máquina virtual para esta instância e, em seguida, selecione **selecione**.
1. Sob **configurações**, confirme se:

   * A rede virtual estiver **Tenant1VNet1**.
   * A sub-rede está definida como **10.1.1.0/24**.

   Use as configurações padrão e clique em **Okey**.

1. Sob **resumo**, examine a configuração da VM e, em seguida, clique em **Okey**.

Para adicionar mais locatários, repita as etapas que seguiu nestas seções:

* [Criar a rede virtual e a sub-rede VM](#create-the-virtual-network-and-vm-subnet)
* [Criar a sub-rede de gateway](#create-the-gateway-subnet)
* [Criar o gateway de rede virtual](#create-the-virtual-network-gateway)
* [Criar o gateway de rede local](#create-the-local-network-gateway)
* [Crie a conexão](#create-the-connection)
* [Criar uma máquina virtual](#create-a-virtual-machine)

Se você estiver usando o locatário 2 como um exemplo, lembre-se de alterar os endereços IP para evitar sobreposições.

### <a name="configure-the-nat-virtual-machine-for-gateway-traversal"></a>Configurar a máquina virtual NAT para percurso de gateway

> [!IMPORTANT]
> Esta seção destina-se somente para implantações do Azure Stack desenvolvimento ASDK (Kit). O NAT não é necessária para implantações de vários nós.

O Kit de desenvolvimento do Azure Stack é independente e isolada da rede em que o host físico é implantado. Que os gateways estão conectados à rede VIP não é externa; ele está oculto por trás de um roteador de realizar a conversão de endereço de rede (NAT).

O roteador é uma máquina virtual do Windows Server (AzS-BGPNAT01) que executa a função de roteamento e os serviços de acesso remoto (RRAS). Você deve configurar NAT na máquina virtual AzS-BGPNAT01 para permitir a conexão de VPN site a site conectar-se em ambas as extremidades.

#### <a name="configure-the-nat"></a>Configurar o NAT

1. Entrar no computador host Azure Stack com sua conta de administrador.
1. Copie e edite o seguinte script do PowerShell. Substitua `your administrator password` com sua senha de administrador e, em seguida, execute o script em um PowerShell ISE elevado. Esse script retorna sua **endereço externo BGPNAT**.

   ```PowerShell
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "your administrator password" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "azs-bgpnat01" `
    -Password $Password
   ```

1. Para configurar o NAT, copie e edite o seguinte script do PowerShell. Edite o script para substituir a `External BGPNAT address` e `Internal IP address` com os valores de exemplo a seguir:

   * Para *endereço externo BGPNAT* usar 10.10.0.62
   * Para *endereço IP interno* usar 192.168.102.1

   Execute o seguinte script de um PowerShell ISE elevado:

   ```PowerShell
   $ExtBgpNat = 'External BGPNAT address'
   $IntBgpNat = 'Internal IP address'

   # Designate the external NAT address for the ports that use the IKE authentication.
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 499 `
      -PortEnd 501}
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 4499 `
      -PortEnd 4501}
   # Create a static NAT mapping to map the external address to the Gateway public IP address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel.
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 500 `
      -InternalPort 500}
   # Configure NAT traversal which uses port 4500 to  establish the complete IPSEC tunnel over NAT devices.
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 4500 `
      -InternalPort 4500}
   ```

## <a name="configure-azure"></a>Configurar o Azure

Depois de concluir a configuração do Azure Stack, você pode implantar os recursos do Azure. A figura a seguir mostra um exemplo de uma rede virtual do locatário no Azure. Você pode usar qualquer nome e o esquema de endereçamento para sua rede virtual no Azure. No entanto, o intervalo de endereços das redes virtuais no Azure e o Azure Stack deve ser exclusivo e não deve se sobrepor:

*Figura 3. Azure VNets*

![Redes virtuais do Azure](media/azure-stack-connect-expressroute/AzureArchitecture.png)

Os recursos que você implanta no Azure são semelhantes aos recursos implantados no Azure Stack. Implantar os seguintes componentes:

* Redes virtuais e sub-redes
* Uma sub-rede de gateway
* Um gateway de rede virtual
* Uma conexão
* Um circuito do ExpressRoute

A infraestrutura de rede do Azure de exemplo está configurada da seguinte maneira:

* Um hub padrão (192.168.2.0/24) e o modelo de rede virtual spoke (10.100.0.0./16). Para obter mais informações sobre a topologia de rede hub-spoke, consulte [implementar uma topologia de rede hub-spoke no Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke).
* As cargas de trabalho são implantadas na rede virtual spoke e o circuito do ExpressRoute está conectado à VNet do hub.
* As duas redes virtuais são conectadas usando o emparelhamento VNet.

### <a name="configure-the-azure-vnets"></a>Configurar as redes virtuais do Azure

1. Entrar no portal do Azure com suas credenciais do Azure.
1. Crie o hub de rede virtual usando o intervalo de endereços 192.168.2.0/24.
1. Crie uma sub-rede usando o intervalo de endereços 192.168.2.0/25 e adicione uma sub-rede de gateway usando o intervalo de endereços 192.168.2.128/27.
1. Criar o spoke intervalo de endereços de rede virtual e sub-rede usando o 10.100.0.0/16.

Para obter mais informações sobre como criar redes virtuais no Azure, consulte [criar uma rede virtual](../virtual-network/manage-virtual-network.md#create-a-virtual-network).

### <a name="configure-an-expressroute-circuit"></a>Configurar um circuito do ExpressRoute

1. Analise os pré-requisitos do ExpressRoute [ExpressRoute pré-requisitos de & lista de verificação](../expressroute/expressroute-prerequisites.md).

1. Siga as etapas em [criar e modificar um circuito de ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) para criar um circuito de ExpressRoute usando sua assinatura do Azure.

   >[!NOTE]
   >Fornecer a chave de serviço para seu circuito para seu serviço para que eles podem configurar seu circuito do ExpressRoute de ponta.

1. Siga as etapas em [criar e modificar o emparelhamento de um circuito de ExpressRoute](../expressroute/expressroute-howto-routing-portal-resource-manager.md) para configurar o emparelhamento privado no circuito de ExpressRoute.

### <a name="create-the-virtual-network-gateway"></a>Criar o gateway de rede virtual

Siga as etapas em [configurar um gateway de rede virtual para ExpressRoute usando o PowerShell](../expressroute/expressroute-howto-add-gateway-resource-manager.md) para criar um gateway de rede virtual para ExpressRoute na VNet do hub.

### <a name="create-the-connection"></a>Criar a conexão

Para vincular o circuito do ExpressRoute para a VNet do hub, siga as etapas em [conectar uma rede virtual a um circuito de ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).

### <a name="peer-the-vnets"></a>Emparelhar as redes virtuais

Emparelhar o hub e spoke de redes virtuais usando as etapas em [criar um emparelhamento de rede virtual usando o portal do Azure](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md). Ao configurar o emparelhamento VNet, certifique-se de que usar as seguintes opções:

* Do hub no spoke **permitir trânsito de gateway**.
* Do spoke para o hub **usar gateway remoto**.

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Implante suas máquinas virtuais de carga de trabalho para a rede virtual spoke.

Repita essas etapas para qualquer locatário adicional redes virtuais que você deseja se conectar no Azure por meio de seus respectivos circuitos de ExpressRoute.

## <a name="configure-the-router"></a>Configurar o roteador

Você pode usar o seguinte diagrama de configuração do roteador de ExpressRoute como um guia para configurar seu roteador de ExpressRoute. Este diagrama mostra dois locatários (locatário 1 e 2 do Locatário) com suas respectivos circuitos do ExpressRoute. Cada locatário está vinculado a seu próprio VRF (roteamento Virtual e o encaminhamento) no lado do LAN e WAN do roteador de ExpressRoute. Essa configuração garante o isolamento de ponta a ponta entre os dois locatários. Anote os endereços IP usados em interfaces do roteador que você siga o exemplo de configuração.

*Figura 4. Configuração do roteador de ExpressRoute*

![Configuração do roteador de ExpressRoute](media/azure-stack-connect-expressroute/EndToEnd.png)

Você pode usar qualquer roteador que dá suporte a IKEv2 VPN e BGP para encerrar a conexão de VPN site a site do Azure Stack. Mesmo roteador é usado para se conectar ao Azure usando um circuito do ExpressRoute.

O seguinte exemplo de configuração do roteador de serviços de agregação de série Cisco ASR 1000 dá suporte a infraestrutura de rede mostrada a *configuração do roteador de ExpressRoute* diagrama.

```shell
ip vrf Tenant 1
 description Routing Domain for PRIVATE peering to Azure for Tenant 1
 rd 1:1
!
ip vrf Tenant 2
 description Routing Domain for PRIVATE peering to Azure for Tenant 2
 rd 1:5
!
crypto ikev2 proposal V2-PROPOSAL2
description IKEv2 proposal for Tenant 1
encryption aes-cbc-256
 integrity sha256
 group 2
crypto ikev2 proposal V4-PROPOSAL2
description IKEv2 proposal for Tenant 2
encryption aes-cbc-256
 integrity sha256
 group 2
!
crypto ikev2 policy V2-POLICY2
description IKEv2 Policy for Tenant 1
match fvrf Tenant 1
 match address local 10.60.3.255
 proposal V2-PROPOSAL2
description IKEv2 Policy for Tenant 2
crypto ikev2 policy V4-POLICY2
 match fvrf Tenant 2
 match address local 10.60.3.251
 proposal V4-PROPOSAL2
!
crypto ikev2 profile V2-PROFILE
description IKEv2 profile for Tenant 1
match fvrf Tenant 1
 match address local 10.60.3.255
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 1
!
crypto ikev2 profile V4-PROFILE
description IKEv2 profile for Tenant 2
 match fvrf Tenant 2
 match address local 10.60.3.251
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 2
!
crypto ipsec transform-set V2-TRANSFORM2 esp-gcm 256
 mode tunnel
crypto ipsec transform-set V4-TRANSFORM2 esp-gcm 256
 mode tunnel
!
crypto ipsec profile V2-PROFILE
 set transform-set V2-TRANSFORM2
 set ikev2-profile V2-PROFILE
!
crypto ipsec profile V4-PROFILE
 set transform-set V4-TRANSFORM2
 set ikev2-profile V4-PROFILE
!
interface Tunnel10
description S2S VPN Tunnel for Tenant 1
 ip vrf forwarding Tenant 1
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.211
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf Tenant 1
 tunnel protection ipsec profile V2-PROFILE
!
interface Tunnel20
description S2S VPN Tunnel for Tenant 2
 ip vrf forwarding Tenant 2
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.213
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf VNET3
 tunnel protection ipsec profile V4-PROFILE
!
interface GigabitEthernet0/0/1
 description PRIMARY ExpressRoute Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/1.100
description Primary WAN interface of Tenant 1
 description PRIMARY ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.1 255.255.255.252
!
interface GigabitEthernet0/0/1.102
description Primary WAN interface of Tenant 2
 description PRIMARY ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.17 255.255.255.252
!
interface GigabitEthernet0/0/2
 description BACKUP ExpressRoute Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/2.100
description Secondary WAN interface of Tenant 1
 description BACKUP ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.5 255.255.255.252
!
interface GigabitEthernet0/0/2.102
description Secondary WAN interface of Tenant 2
description BACKUP ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.21 255.255.255.252
!
interface TenGigabitEthernet0/1/0
 description Downlink to ---Port 1/47
 no ip address
!
interface TenGigabitEthernet0/1/0.211
 description LAN interface of Tenant 1
description Downlink to --- Port 1/47.211
 encapsulation dot1Q 211
 ip vrf forwarding Tenant 1
 ip address 10.60.3.255 255.255.255.254
!
interface TenGigabitEthernet0/1/0.213
description LAN interface of Tenant 2
 description Downlink to --- Port 1/47.213
 encapsulation dot1Q 213
 ip vrf forwarding Tenant 2
 ip address 10.60.3.251 255.255.255.254
!
router bgp 65530
 bgp router-id <removed>
 bgp log-neighbor-changes
 description BGP neighbor config and route advertisement for Tenant 1 VRF
 address-family ipv4 vrf Tenant 1
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.254 mask 255.255.255.254
  network 192.168.1.0 mask 255.255.255.252
  network 192.168.1.4 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65100
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 1
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.254 remote-as 4232570301
  neighbor 10.60.3.254 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.254 activate
  neighbor 10.60.3.254 route-map BLOCK-ALL out
  neighbor 192.168.1.2 remote-as 12076
  neighbor 192.168.1.2 description PRIMARY ER peer for Tenant 1 to Azure
  neighbor 192.168.1.2 ebgp-multihop 5
  neighbor 192.168.1.2 activate
  neighbor 192.168.1.2 soft-reconfiguration inbound
  neighbor 192.168.1.2 route-map Tenant 1-ONLY out
  neighbor 192.168.1.6 remote-as 12076
  neighbor 192.168.1.6 description BACKUP ER peer for Tenant 1 to Azure
  neighbor 192.168.1.6 ebgp-multihop 5
  neighbor 192.168.1.6 activate
  neighbor 192.168.1.6 soft-reconfiguration inbound
  neighbor 192.168.1.6 route-map Tenant 1-ONLY out
  maximum-paths 8
 exit-address-family
 !
description BGP neighbor config and route advertisement for Tenant 2 VRF
address-family ipv4 vrf Tenant 2
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.250 mask 255.255.255.254
  network 192.168.1.16 mask 255.255.255.252
  network 192.168.1.20 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65300
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 2
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.250 remote-as 4232570301
  neighbor 10.60.3.250 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.250 activate
  neighbor 10.60.3.250 route-map BLOCK-ALL out
  neighbor 192.168.1.18 remote-as 12076
  neighbor 192.168.1.18 description PRIMARY ER peer for Tenant 2 to Azure
  neighbor 192.168.1.18 ebgp-multihop 5
  neighbor 192.168.1.18 activate
  neighbor 192.168.1.18 soft-reconfiguration inbound
  neighbor 192.168.1.18 route-map VNET-ONLY out
  neighbor 192.168.1.22 remote-as 12076
  neighbor 192.168.1.22 description BACKUP ER peer for Tenant 2 to Azure
  neighbor 192.168.1.22 ebgp-multihop 5
  neighbor 192.168.1.22 activate
  neighbor 192.168.1.22 soft-reconfiguration inbound
  neighbor 192.168.1.22 route-map VNET-ONLY out
  maximum-paths 8
 exit-address-family
!
ip forward-protocol nd
!
ip as-path access-list 1 permit ^$
ip route vrf Tenant 1 10.1.0.0 255.255.0.0 Tunnel10
ip route vrf Tenant 2 10.1.0.0 255.255.0.0 Tunnel20
!
ip prefix-list BLOCK-ALL seq 5 deny 0.0.0.0/0 le 32
!
route-map BLOCK-ALL permit 10
 match ip address prefix-list BLOCK-ALL
!
route-map VNET-ONLY permit 10
 match as-path 1
!
```

## <a name="test-the-connection"></a>Testar a conexão

Teste sua conexão depois de estabelecer a conexão site a site e o circuito do ExpressRoute.

Execute os testes de ping a seguir:

* Entrar em uma das máquinas virtuais na rede virtual do Azure e executar o ping de máquina virtual criada no Azure Stack.
* Entrar em uma das máquinas virtuais criadas no Azure Stack e ping a máquina virtual que você criou na rede virtual do Azure.

>[!NOTE]
>Para verificar se que você estiver enviando o tráfego sobre o Site a Site e conexões do ExpressRoute, você deve executar o ping o endereço IP (DIP) dedicado da máquina virtual nas extremidades tanto e não o endereço VIP da máquina virtual.

### <a name="allow-icmp-in-through-the-firewall"></a>Permitir ICMP no através do firewall

Por padrão, o Windows Server 2016 não permite pacotes de ICMP de entrada por meio do firewall. Para cada máquina virtual que você usa para testes de ping, você deve permitir que os pacotes ICMP de entrada. Para criar uma regra de firewall de ICMP, execute o seguinte cmdlet em uma janela elevada do PowerShell:

```PowerShell
# Create ICMP firewall rule.
New-NetFirewallRule `
  –DisplayName “Allow ICMPv4-In” `
  –Protocol ICMPv4
```

### <a name="ping-the-azure-stack-virtual-machine"></a>Executar o ping de máquina virtual do Azure Stack

1. Entrar no portal do usuário do Azure Stack usando uma conta de locatário.

1. Localizar a máquina virtual que você criou e selecione a máquina virtual.

1. Selecione **Conectar**.

1. Em um prompt de comando Windows ou o PowerShell com privilégios elevados, digite **ipconfig/all**. Anote o endereço IPv4 retornado na saída.

1. Execute ping no endereço IPv4 da máquina virtual na rede virtual do Azure.

   No ambiente de exemplo, o endereço IPv4 é proveniente da sub-rede 10.1.1.x/24. Em seu ambiente, o endereço pode ser diferente, mas ele está na sub-rede que você criou para a sub-rede de rede virtual do locatário.

### <a name="view-data-transfer-statistics"></a>Exibir estatísticas de transferência de dados

Se você quiser saber a quantidade de tráfego está passando por meio de sua conexão, você pode encontrar essas informações no portal do usuário do Azure Stack. Isso também é uma boa maneira de descobrir se os dados de teste de ping deu por meio de conexões VPN e ExpressRoute:

1. Entre no portal do usuário do Azure Stack usando sua conta de locatário e selecione **todos os recursos**.
1. Navegue até o grupo de recursos para seu Gateway de VPN e selecione o **Conexão** tipo de objeto.
1. Selecione o **ConnectToAzure** conexão na lista.
1. Sob **conexões** > **visão geral**, você pode ver as estatísticas de **dados no** e **dados de saída**. Você deve ver alguns valores diferentes de zero.

   ![Dados e dados de saída](media/azure-stack-connect-expressroute/DataInDataOut.png)

## <a name="next-steps"></a>Próximas etapas

[Implantar aplicativos no Azure e o Azure Stack](azure-stack-solution-pipeline.md)
