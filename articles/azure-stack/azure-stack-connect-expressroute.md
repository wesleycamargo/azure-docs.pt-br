---
title: Conecte-se a pilha do Azure no Azure usando o rota expressa
description: Como conectar redes virtuais na pilha do Azure para redes virtuais no Azure usando o ExpressRoute.
services: azure-stack
documentationcenter: 
author: victorar
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: victorh
ms.openlocfilehash: aa6973939c6cfe0688f5781fdcea5d39670249df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connect-azure-stack-to-azure-using-expressroute"></a>Conecte-se a pilha do Azure no Azure usando o rota expressa

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Há dois métodos para se conectar a redes virtuais na pilha do Azure para redes virtuais no Azure:
   * **Site a site**

     Uma conexão VPN sobre IPsec (IKE v1 e IKE v2). Esse tipo de conexão exige um dispositivo VPN ou RRAS. Para obter mais informações, consulte [conectar-se a pilha do Azure para o Azure usando VPN](azure-stack-connect-vpn.md).
   * **ExpressRoute**

     Uma conexão direta para o Azure da sua implantação do Azure pilha. Rota expressa é **não** uma conexão VPN pela Internet pública. Para obter mais informações sobre a rota expressa do Azure, consulte [visão geral do ExpressRoute](../expressroute/expressroute-introduction.md).

Este artigo mostra um exemplo de uso da rota expressa para conectar-se a pilha do Azure para o Azure.
## <a name="requirements"></a>Requisitos
Esses são os requisitos específicos para se conectar a pilha do Azure e o Azure usando o ExpressRoute:
* Uma assinatura do Azure para criar um circuito de rota expressa e VNets no Azure.
* Provisionado de um circuito de rota expressa por meio de um [provedor de conectividade](../expressroute/expressroute-locations.md).
* Um roteador que tem o circuito de rota expressa conectado às suas portas WAN.
* Lado da rede do roteador está vinculado ao Gateway de multilocatário de pilha do Azure.
* O roteador deve oferecer suporte a conexões VPN Site a Site entre sua interface de LAN e um Gateway de multilocatário de pilha do Azure.
* Se mais de um locatário é adicionado na sua implantação de pilha do Azure, o roteador deve ser capaz de criar várias VRFs (roteamento Virtual e encaminhamento).

O diagrama a seguir mostra uma exibição conceitual de rede depois de concluir a configuração:

![Diagrama conceitual](media/azure-stack-connect-expressroute/Conceptual.png)

**Diagrama 1**

O diagrama de arquitetura a seguir mostra como vários locatários se conectam da infraestrutura de pilha do Azure por meio do roteador de rota expressa do Azure na borda da Microsoft:

![Diagrama da arquitetura](media/azure-stack-connect-expressroute/Architecture.png)

**Diagrama 2**

O exemplo mostrado neste artigo usa a mesma arquitetura para se conectar ao Azure por meio do emparelhamento privado da rota expressa. Isso é feito usando uma conexão VPN Site a Site do gateway de rede virtual na pilha do Azure para um roteador de rota expressa. As etapas neste artigo a seguir mostram como criar uma conexão de ponta a ponta entre dois VNets de duas diferentes locatários na pilha do Azure para seus respectivos VNets no Azure. Você pode optar por adicionar muitos VNets de locatário e replicam as etapas para cada locatário ou usam este exemplo para implantar apenas um único locatário VNet.

## <a name="configure-azure-stack"></a>Configurar a pilha do Azure
Agora você pode criar os recursos que necessários para configurar seu ambiente de pilha do Azure como um locatário. As etapas a seguir ilustram o que você precisa fazer. Estas instruções mostram como criar recursos usando o portal de pilha do Azure, mas você também pode usar o PowerShell.

![Etapas de recursos de rede](media/azure-stack-connect-expressroute/image2.png)
### <a name="before-you-begin"></a>Antes de começar
Antes de iniciar a configuração, você precisa:
* Uma implantação de pilha do Azure.

   Para obter informações sobre a implantação do Kit de desenvolvimento de pilha do Azure, consulte [início rápido de implantação do Kit de desenvolvimento de pilha do Azure](azure-stack-deploy-overview.md).
* Uma oferta na pilha do Azure que o usuário pode se inscrever.

  Para obter instruções, consulte [tornam as máquinas virtuais disponíveis para os usuários do Azure pilha](azure-stack-tutorial-tenant-vm.md).

### <a name="create-network-resources-in-azure-stack"></a>Criar recursos de rede na pilha do Azure

Use os procedimentos a seguir para criar os recursos de rede necessários na pilha do Azure para cada locatário:

#### <a name="create-the-virtual-network-and-vm-subnet"></a>Criar a rede virtual e sub-rede de VM
1. Faça logon no portal do usuário com uma conta de usuário (Locatário).

2. No portal, clique em **novo**.

   ![](media/azure-stack-connect-expressroute/MAS-new.png)

3. Selecione **Rede** no menu do Marketplace.

4. Clique em **Rede virtual** no menu.

5. Digite os valores nos campos apropriados, usando a tabela a seguir:

   |Campo  |Valor  |
   |---------|---------|
   |Nome     |Tenant1VNet1         |
   |Espaço de endereço     |10.1.0.0/16|
   |Nome da sub-rede     |Sub1 locatário1|
   |Intervalo de endereços da sub-rede     |10.1.1.0/24|

6. Você deverá ver a assinatura que criou anteriormente populada no campo **Assinatura**.

    a. Para o grupo de recursos, você pode criar um grupo de recursos ou se você já tiver um, selecione **usar existente**.

    b. Verifique o local padrão.

    c. Clique em **Fixar no painel**.

    d. Clique em **Criar**.



#### <a name="create-the-gateway-subnet"></a>Criar a sub-rede de gateway
1. Abra o recurso de rede Virtual criada (Tenant1VNet1) do painel.
2. Na seção de configurações, selecione **sub-redes**.
3. Clique em **Sub-rede de Gateway** para adicionar uma sub-rede de gateway à rede virtual.
   
    ![](media/azure-stack-connect-expressroute/gatewaysubnet.png)
4. O nome da sub-rede é definido como **Sub-rede de Gateway** por padrão.
   Sub-redes de gateway são especiais e devem ter esse nome específico para funcionar corretamente.
5. No **um intervalo de endereços** campo, verifique se o endereço é **10.1.0.0/24**.
6. Clique em **OK** para criar a sub-rede de gateway.

#### <a name="create-the-virtual-network-gateway"></a>Criar o gateway de rede virtual
1. No portal do usuário de pilha do Azure, clique em **novo**.
   
2. Selecione **Rede** no menu do Marketplace.
3. Selecione **Gateway de Rede Virtual** na lista de recursos de rede.
4. No campo **Nome**, digite **GW1**.
5. Clique no item **Rede virtual** para escolher uma rede virtual.
   Selecione **Tenant1VNet1** na lista.
6. Clique no item de menu **Endereço IP público**. Quando o **escolher endereço IP público** seção abre clique **criar novo**.
7. No campo **Nome**, digite **GW1-PiP** e clique em **OK**.
8. O **Tipo de VPN** deve ter **Baseado em rota** selecionado por padrão.
    Mantenha essa configuração.
9. Verifique se **Assinatura** e **Local** estão corretos. Se desejar, você pode fixar o recurso para o painel. Clique em **Criar**.

#### <a name="create-the-local-network-gateway"></a>Criar o gateway de rede local

A finalidade do recurso de gateway de rede Local é indicar o gateway remoto na outra extremidade da conexão de VPN. Neste exemplo, o lado remoto é subinterface de LAN do roteador de rota expressa. Para 1 de locatário neste exemplo, o endereço remoto é 10.60.3.255, conforme mostrado no diagrama 2.

1. Faça logon no computador físico do Azure Stack.
2. Entre no portal do usuário com sua conta de usuário e clique em **novo**.
3. Selecione **Rede** no menu do Marketplace.
4. Selecione **gateway de rede local** na lista de recursos.
5. No **nome** o tipo de campo **ER-roteador-GW**.
6. Para o **endereço IP** campo, consulte o diagrama 2. O endereço IP da subinterface de LAN do roteador de rota expressa para 1 de locatário é 10.60.3.255. Para o seu próprio ambiente, digite o endereço IP da interface correspondente do seu roteador.
7. No **espaço de endereço** , digite o espaço de endereço das VNets que você deseja se conectar no Azure. Neste exemplo, consulte o diagrama 2. Para 1 de locatário, observe que as sub-redes necessárias são **192.168.2.0/24** (esse é o Hub Vnet no Azure) e **10.100.0.0/16** (essa é a rede virtual Spoke no Azure). Digite as sub-redes correspondentes para seu ambiente.
   > [!IMPORTANT]
   > Este exemplo presume que você está usando rotas estáticas para a conexão VPN Site a Site entre o gateway de pilha do Azure e o roteador de rota expressa.

8. Verifique seu **assinatura**, **grupo de recursos**, e **local** estão corretas e clique em **criar**.

#### <a name="create-the-connection"></a>Criar a conexão
1. No portal do usuário de pilha do Azure, clique em **novo**.
2. Selecione **Rede** no menu do Marketplace.
3. Selecione **Conexão** na lista de recursos.
4. No **Noções básicas de** seção de configurações, escolha **(IPSec) Site a site** como o **tipo de Conexão**.
5. Selecione o **assinatura**, **grupo de recursos**, e **local** e clique em **Okey**.
6. No **configurações** seção, clique em **gateway de rede Virtual** clique **GW1**.
7. Clique em **gateway de rede Local**e clique em **ER roteador GW**.
8. No **nome de Conexão** , digite **ConnectToAzure**.
9. No **compartilhado PSK (chave)** , digite **abc123** e clique em **Okey**.
10. Sobre o **resumo** seção, clique em **Okey**.

    Depois que a conexão é criada, você pode ver o endereço IP público usado pelo gateway de rede virtual. Para localizar o endereço no portal do Azure pilha, navegue até o gateway de rede Virtual. Em **visão geral**, localize o **endereço IP público**. Anote esse endereço; você o usará como o *endereço IP interno* na próxima seção (se aplicável para sua implantação).

    ![](media/azure-stack-connect-expressroute/GWPublicIP.png)

#### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
Para validar dados transportados por meio de Conexão VPN, você precisa de máquinas virtuais para enviar e receber dados na rede virtual do Azure de pilha. Criar uma máquina virtual agora e colocá-la em sua sub-rede da VM em sua rede virtual.

1. No portal do usuário de pilha do Azure, clique em **novo**.
2. Selecione **Máquinas Virtuais** no menu do Marketplace.
3. Na lista de imagens da máquina virtual, selecione o **Datacenter avaliação do Windows Server 2016** da imagem e clique em **criar**.
4. Sobre o **Noções básicas** seção, o **nome** o tipo de campo **VM01**.
5. Digite um nome de usuário e uma senha válidos. Você usará essa conta para fazer logon na VM depois que ela for criada.
6. Forneça um **assinatura**, **grupo de recursos**, e **local** e, em seguida, clique em **Okey**.
7. Sobre o **tamanho** seção, clique em um tamanho de máquina virtual para essa instância e, em seguida, clique em **selecione**.
8. Sobre o **configurações** seção, você pode aceitar os padrões. Mas certifique-se de que a rede virtual selecionada está **Tenant1VNet1** e a sub-rede é definida como **10.1.1.0/24**. Clique em **OK**.
9. Examine as configurações no **resumo** seção e clique em **Okey**.

Para cada locatário de rede virtual que você deseja se conectar, repita as etapas anteriores de **criar a rede virtual e a sub-rede VM** por meio de **criar uma máquina virtual** seções.

### <a name="configure-the-nat-virtual-machine-for-gateway-traversal"></a>Configurar a máquina virtual NAT para a passagem de gateway
> [!IMPORTANT]
> Esta seção destina-se somente a implantações do Kit de desenvolvimento de pilha do Azure. O NAT não é necessária para implantações de vários nós.

O Kit de desenvolvimento de pilha do Azure é isolado da rede na qual o host físico é implantado e autossuficiente. Assim, a rede de VIP "Externo" os gateways conectadas ao não é externa, mas em vez disso, está oculto por trás de um roteador que realiza a conversão de endereço de rede (NAT).
 
O roteador for uma máquina virtual Windows Server (**AzS BGPNAT01**) executando a função de roteamento e os serviços de acesso remoto (RRAS) na infraestrutura do Kit de desenvolvimento de pilha do Azure. Você deve configurar a NAT na máquina virtual AzS BGPNAT01 para permitir a Conexão de VPN Site a Site conectar-se em ambas as extremidades.

#### <a name="configure-the-nat"></a>Configurar a NAT

1. Faça logon máquina física pilha do Azure com sua conta de administrador.
2. Copiar e editar o seguinte script do PowerShell e execute em um ISE do Windows PowerShell com privilégios elevados. Substitua a senha de administrador. O endereço retornado é o *endereço externo BGPNAT*.

   ```
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "<your administrator password>" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "azs-bgpnat01" `
    -Password $Password
   ```
4. Para configurar a NAT, copiar e editar o seguinte script do PowerShell e execute em um ISE do Windows PowerShell com privilégios elevados. Edite o script para substituir o *endereço externo BGPNAT* e *endereço IP interno* (que você anotou anteriormente no **criar a Conexão** seção).

   Em diagramas de exemplo, o *endereço externo BGPNAT* é 10.10.0.62 e o *endereço IP interno* é 192.168.102.1.

   ```
   # Designate the external NAT address for the ports that use the IKE authentication.
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 499 `
      -PortEnd 501}
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 4499 `
      -PortEnd 4501}
   # create a static NAT mapping to map the external address to the Gateway
   # Public IP Address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 500 `
      -InternalPort 500}
   # Finally, configure NAT traversal which uses port 4500 to
   # successfully establish the complete IPSEC tunnel over NAT devices
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 4500 `
      -InternalPort 4500}
   ```

## <a name="configure-azure"></a>Configurar o Azure
Agora que você concluiu a configuração da pilha do Azure, você pode implantar alguns recursos do Azure. O diagrama a seguir mostra um locatário de exemplo rede virtual no Azure. Você pode usar qualquer nome e o esquema de endereçamento para a sua rede virtual no Azure. No entanto, o intervalo de endereços de VNets no Azure e o Azure pilha deve ser exclusivo e não se sobrepõem.

![Vnets do Azure](media/azure-stack-connect-expressroute/AzureArchitecture.png)

**Diagrama de 3**

Os recursos que você implanta no Azure são semelhantes aos recursos implantados na pilha do Azure. Da mesma forma, você deve implantar:
* Redes virtuais e sub-redes
* Uma sub-rede do gateway
* Um gateway de rede virtual
* Uma conexão
* Um circuito de rota expressa

A infraestrutura de rede do Azure de exemplo está configurada da seguinte maneira:
* Um hub padrão (192.168.2.0/24) e spoke (10.100.0.0./16) o modelo de rede virtual é usado.
* As cargas de trabalho são implantadas no rede virtual de spoke e o circuito de rota expressa está conectado ao hub de rede virtual.
* As dois VNets são vinculadas usando o recurso de emparelhamento VNet.

### <a name="configure-vnets"></a>Configurar Vnets
1. Entrar no portal do Azure com suas credenciais do Azure.
2. Cria o hub de rede virtual usando o espaço de endereço 192.168.2.0/24. Criar uma sub-rede usando o intervalo de endereços 192.168.2.0/25 e adicionar uma sub-rede do gateway usando o intervalo de endereços 192.168.2.128/27.
3. Criar o spoke intervalo de endereços de rede virtual e sub-rede usando o 10.100.0.0/16.


Para obter mais informações sobre como criar redes virtuais no Azure, consulte [criar uma rede virtual com várias sub-redes](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

### <a name="configure-an-expressroute-circuit"></a>Configurar um circuito de rota expressa

1. Examine os pré-requisitos de rota expressa em [ExpressRoute pré-requisitos & lista de verificação](../expressroute/expressroute-prerequisites.md).
2. Siga as etapas em [criar e modificar um circuito ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) para criar um circuito de rota expressa usando sua assinatura do Azure.
3. Compartilhe a chave de serviço da etapa anterior com o hoster/provedor para provisionar o circuito de rota expressa seu final.
4. Siga as etapas em [criar e modificar o emparelhamento de um circuito ExpressRoute](../expressroute/expressroute-howto-routing-portal-resource-manager.md) para configurar o emparelhamento privado no circuito de rota expressa.

### <a name="create-the-virtual-network-gateway"></a>Criar o gateway de rede virtual

* Siga as etapas em [configurar um gateway de rede virtual para o ExpressRoute usando o PowerShell](../expressroute/expressroute-howto-add-gateway-resource-manager.md) para criar um gateway de rede virtual para o ExpressRoute no hub de rede virtual.

### <a name="create-the-connection"></a>Criar a conexão

* Para vincular o circuito de rota expressa para o hub de rede virtual, siga as etapas em [conectar uma rede virtual a um circuito de rota expressa](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).

### <a name="peer-the-vnets"></a>As Vnets ponto a ponto

* Pares de Hub e Spoke VNets usando as etapas no [criar um emparelhamento de rede virtual usando o portal do Azure](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md). Ao configurar o emparelhamento VNet, certifique-se de que você selecione as seguintes opções:
   * Do hub de spoke: **permitir tráfego de gateway**
   * De spoke Hub: **usar gateway remoto**

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

* Implante as máquinas virtuais de carga de trabalho para o rede virtual spoke.

Repita essas etapas para qualquer locatário adicional VNets que você deseja se conectar no Azure por meio de seus respectivos circuitos do ExpressRoute.

## <a name="configure-the-router"></a>Configurar o roteador

Você pode usar o seguinte diagrama de infraestrutura de ponta a ponta para orientar a configuração do roteador de rota expressa. Este diagrama mostra dois locatários (locatário 1 e 2 do Locatário) com seus respectivos circuitos de rota expressa. Cada um é vinculada a seu próprios VRF (Virtual Routing e encaminhamento) no lado de LAN e WAN do roteador rota expressa para garantir o isolamento de ponta a ponta entre dois locatários. Observe os endereços IP usados nas interfaces de roteador que você siga o exemplo de configuração.

![Ponta a ponta de diagrama](media/azure-stack-connect-expressroute/EndToEnd.png)

**Diagrama de 4**

Você pode usar qualquer roteador que oferece suporte a IKEv2 VPN e BGP para encerrar a conexão VPN Site a Site da pilha do Azure. O mesmo roteador é usado para se conectar ao Azure usando um circuito de rota expressa. 

Aqui está um exemplo de configuração do Cisco ASR 1.000 que dá suporte à infraestrutura de rede mostrada no diagrama de 4:

```
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
 description PRIMARY Express Route Link to AZURE over Equinix
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
 description BACKUP Express Route Link to AZURE over Equinix
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

Teste sua conexão depois de estabelecer a conexão Site a Site e o circuito de rota expressa. Essa tarefa é simple.  Faça logon em um das máquinas virtuais que você criou na sua rede virtual do Azure e executar o ping de máquina virtual criada no ambiente de pilha do Azure, ou vice-versa. 

Para garantir que você está enviando o tráfego por meio do Site a Site e conexões de rota expressa, você deve executar o ping do endereço IP (DIP) dedicado da máquina virtual, as extremidades e não o endereço VIP da máquina virtual. Portanto, você deve localizar e anote o endereço na outra extremidade de conexão.

### <a name="allow-icmp-in-through-the-firewall"></a>Permitir o ICMP no através do firewall
Por padrão, o Windows Server 2016 não permite pacotes ICMP no através do firewall. Portanto, para cada máquina virtual que você pode usar no teste, execute o seguinte cmdlet em uma janela elevada do PowerShell:


   ```
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="ping-the-azure-stack-virtual-machine"></a>A máquina virtual do Azure pilha de ping

1. Entrar no portal do usuário de pilha do Azure usando uma conta de locatário.
2. Clique em **Máquinas Virtuais** na barra de navegação à esquerda.
3. Localizar a máquina virtual que você criou anteriormente e clique nele.
4. Na seção para a máquina virtual, clique em **conectar**.
5. Abra um PowerShell elevado janela e digite **ipconfig /all**.
6. Localizar o endereço IPv4 na saída e anote a ele. Execute ping neste endereço da máquina virtual na rede virtual do Azure. No ambiente de exemplo, o endereço é a sub-rede 10.1.1.x/24. Em seu ambiente, o endereço pode ser diferente. No entanto, ele deve estar dentro da sub-rede que você criou para a sub-rede de rede virtual do locatário.


### <a name="view-data-transfer-statistics"></a>Exibir estatísticas de transferência de dados

Se você quiser saber a quantidade de tráfego está passando por meio de sua conexão, você pode encontrar essas informações na seção de Conexão no portal do usuário de pilha do Azure. Essas informações também são outra maneira de verificar se o ping que você acabou de ser enviado foi realmente através de conexões VPN e rota expressa.

1. Entrar no portal do usuário de pilha do Microsoft Azure usando sua conta de locatário.
2. Navegue até o grupo de recursos onde o Gateway VPN foi criado e selecione o **conexões** tipo de objeto.
3. Clique o **ConnectToAzure** conexão na lista.
4. Sobre o **Conexão** seção, você pode ver as estatísticas de **dados em** e **dados**. Você deverá ver alguns valores diferentes de zero.

   ![Dados de saída de dados](media/azure-stack-connect-expressroute/DataInDataOut.png)

## <a name="next-steps"></a>Próximas etapas
[Implantar aplicativos do Azure e o Azure pilha](azure-stack-solution-pipeline.md)