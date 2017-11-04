---
title: "Criar uma conexão de VPN site a site entre duas redes virtuais em diferentes ambientes de Kit de desenvolvimento de pilha do Azure | Microsoft Docs"
description: "Procedimento passo a passo que um administrador de nuvem usa para criar uma conexão de VPN site a site entre dois ambientes de Kit de desenvolvimento de pilha do Azure de nó único."
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: darmour
editor: 
ms.assetid: 3f1b4e02-dbab-46a3-8e11-a777722120ec
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/10/2017
ms.author: scottnap
ms.openlocfilehash: fa2a940620e06521fa110fa13dcbc3050635a502
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-site-to-site-vpn-connection-between-two-virtual-networks-in-different-azure-stack-development-kit-environments"></a>Criar uma conexão de VPN site a site entre duas redes virtuais em diferentes ambientes de Kit de desenvolvimento de pilha do Azure
## <a name="overview"></a>Visão geral
Este artigo mostra como criar uma conexão de VPN site a site entre duas redes virtuais em dois ambientes separados do Kit de desenvolvimento de pilha do Azure. Ao configurar as conexões, você aprenderá como funcionam os gateways VPN na pilha do Azure.

### <a name="connection-diagram"></a>Diagrama de conexão
O diagrama a seguir mostra o que a configuração de conexão deve ter aparência quando terminar.

![Configuração de conexão VPN site a site](media/azure-stack-create-vpn-connection-one-node-tp2/OneNodeS2SVPN.png)

### <a name="before-you-begin"></a>Antes de começar
Para concluir a configuração de conexão, certifique-se de que você tenha os seguintes itens antes de começar:

* Dois servidores que atendam aos requisitos de hardware do Kit de desenvolvimento de pilha do Azure, que são definidos pelo [os pré-requisitos de implantação do Azure pilha](azure-stack-deploy.md). Certifique-se de que os outros pré-requisitos que aparecem no [artigo](azure-stack-deploy.md) sejam cumpridas muito.
* O [Kit de desenvolvimento de pilha do Azure](https://azure.microsoft.com/en-us/overview/azure-stack/try/) pacote de implantação.

## <a name="deploy-the-azure-stack-development-kit-environments"></a>Implantar os ambientes do Kit de desenvolvimento de pilha do Azure
Para concluir a configuração de conexão, você deve implantar dois ambientes de Kit de desenvolvimento de pilha do Azure.
> [!NOTE] 
> Para cada Kit de desenvolvimento de pilha do Azure que você implanta, siga o [instruções de implantação](azure-stack-run-powershell-script.md). Neste artigo, os ambientes do Kit de desenvolvimento de pilha do Azure são chamados *POC1* e *POC2*.


## <a name="prepare-an-offer-on-poc1-and-poc2"></a>Preparar uma oferta em POC1 e POC2
No POC1 e POC2, prepare uma oferta para que um usuário pode assinar a oferta e implantar as máquinas virtuais. Para obter informações sobre como criar uma oferta, consulte [tornam as máquinas virtuais disponíveis para os usuários do Azure pilha](azure-stack-tutorial-tenant-vm.md).

## <a name="review-and-complete-the-network-configuration-table"></a>Revise e conclua a tabela de configuração de rede
A tabela a seguir resume a configuração de rede para ambos os ambientes do Kit de desenvolvimento de pilha do Azure. Use o procedimento que aparece após a tabela para adicionar o endereço de BGPNAT externa que é específico para sua rede.

**Tabela de configuração de rede**
|   |POC1|POC2|
|---------|---------|---------|
|Nome de rede virtual     |REDE VIRTUAL-01|REDE VIRTUAL-02 |
|Espaço de endereço de rede virtual |10.0.10.0/23|10.0.20.0/23|
|Nome da sub-rede     |Subrede-01|Subrede-02|
|Intervalo de endereços da sub-rede|10.0.10.0/24 |10.0.20.0/24 |
|Gateway de sub-rede     |10.0.11.0/24|10.0.21.0/24|
|Endereço BGPNAT externo     |         |         |

> [!NOTE]
> Os endereços IP BGPNAT externos no ambiente de exemplo são 10.16.167.195 para POC1 e 10.16.169.131 para POC2. Use o procedimento a seguir para determinar os endereços IP BGPNAT externos para os hosts do Kit de desenvolvimento de pilha do Azure e, em seguida, adicioná-los para a tabela de configuração de rede anterior.


### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>Obter o endereço IP do adaptador externo da VM NAT
1. Entrar para a máquina física na pilha do Azure para POC1.
2. Edite o seguinte código do Powershell para substituir a senha de administrador e, em seguida, execute o código no host POC:

   ```powershell
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "<your administrator password>" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "AzS-bgpnat01" `
    -Password $Password
   ```
3. Adicione o endereço IP para a tabela de configuração de rede que aparece na seção anterior.

4. Repita esse procedimento em POC2.

## <a name="create-the-network-resources-in-poc1"></a>Criar os recursos de rede em POC1
Agora você pode criar os recursos de rede de POC1 necessárias para configurar seus gateways. As instruções a seguir mostram como criar os recursos usando o portal do usuário. Você também pode usar o código do PowerShell para criar os recursos.

![Fluxo de trabalho que é usado para criar recursos](media/azure-stack-create-vpn-connection-one-node-tp2/image2.png)

### <a name="sign-in-as-a-tenant"></a>Entrar como um locatário
Um administrador de serviço pode entrar como um locatário para testar os planos, ofertas e assinaturas que podem usar seus locatários. Se você ainda não tiver um, [criar uma conta de locatário](azure-stack-add-new-user-aad.md) antes de você entrar.

### <a name="create-the-virtual-network-and-vm-subnet"></a>Criar a rede virtual e sub-rede de VM
1. Use uma conta de locatário para entrar no portal do usuário.
2. No portal do usuário, selecione **novo**.

    ![Criar nova rede virtual](media/azure-stack-create-vpn-connection-one-node-tp2/image3.png)

3. Vá para **Marketplace**e, em seguida, selecione **rede**.
4. Selecione **rede Virtual**.
5. Para **nome**, **espaço de endereço**, **nome da sub-rede**, e **intervalo de endereços de sub-rede**, use os valores que aparecem anteriormente na rede tabela de configuração.
6. Em **assinatura**, a assinatura que você criou anteriormente é exibida.
7. Para **grupo de recursos**, você pode criar um grupo de recursos ou se você já tiver um, selecione **usar existente**.
8. Verifique o local padrão.
9. Selecione **Fixar no painel**.
10. Selecione **Criar**.

### <a name="create-the-gateway-subnet"></a>Criar a sub-rede de gateway
1. No painel, abra o recurso de rede virtual 01 VNET que você criou anteriormente.
2. Na folha **Configurações**, selecione **Sub-redes**.
3. Para adicionar uma sub-rede do gateway para a rede virtual, selecione **sub-rede de Gateway**.
   
    ![Adicionar sub-rede de gateway](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)

4. Por padrão, o nome da sub-rede é definido como **GatewaySubnet**.
   Sub-redes de gateway são especiais. Para funcionar corretamente, eles devem usar o *GatewaySubnet* nome.
5. Em **um intervalo de endereços**, verifique se o endereço é **10.0.11.0/24**.
6. Selecione **Okey** para criar a sub-rede de gateway.

### <a name="create-the-virtual-network-gateway"></a>Criar o gateway de rede virtual
1. No portal do Azure, selecione **novo**. 
2. Vá para **Marketplace**e, em seguida, selecione **rede**.
3. Na lista de recursos de rede, selecione **gateway de rede Virtual**.
4. Em **nome**, digite **GW1**.
5. Selecione o **rede Virtual** item para escolher uma rede virtual.
   Selecione **VNET-01** da lista.
6. Selecione o **endereço IP público** item de menu. Quando o **escolher endereço IP público** folha é aberta, selecione **criar novo**.
7. Em **nome**, digite **GW1 PiP**e, em seguida, selecione **Okey**.
8.  Por padrão, para **tipo VPN**, **baseadas em rota** está selecionado.
    Manter o **baseadas em rota** tipo de VPN.
9. Verifique se **Assinatura** e **Local** estão corretos. Você pode fixar o recurso para o painel. Selecione **Criar**.

### <a name="create-the-local-network-gateway"></a>Criar o gateway de rede local
A implementação de um *gateway de rede local* nessa implantação de avaliação do Azure Stack é um pouco diferente do que uma implantação real do Azure.

Em uma implantação do Azure, um gateway de rede local representa um dispositivo físico no local (no Locatário), que você usa para se conectar a um gateway de rede virtual no Azure. Nessa implantação de avaliação de pilha do Azure, ambas as extremidades da conexão são gateways de rede virtual!

Uma maneira de pensar sobre isso mais genericamente é que o recurso de gateway de rede local sempre indica o gateway remoto na outra extremidade de conexão. Por causa da forma que o Kit de desenvolvimento de pilha do Azure foi criado, você precisa fornecer o endereço IP do adaptador de rede externo sobre a conversão de endereços de rede (NAT) VM do outro Azure pilha Development Kit como o endereço IP público do gateway de rede local. Você então pode criar mapeamentos NAT na VM do NAT para certificar-se de que ambas as extremidades estão conectadas corretamente.


### <a name="create-the-local-network-gateway-resource"></a>Criar o recurso de gateway de rede local
1. Entrar para a máquina física na pilha do Azure para POC1.
2. No portal do usuário, selecione **novo**.
3. Vá para **Marketplace**e, em seguida, selecione **rede**.
4. Na lista de recursos, selecione **gateway de rede local**.
5. Em **nome**, digite **POC2-GW**.
6. Em **endereço IP**, digite o endereço externo BGPNAT POC2. Esse endereço aparece anteriormente na tabela de configuração de rede.
7. Em **espaço de endereço**, para o espaço de endereço do VNET POC2 que você criar posteriormente, digite **10.0.20.0/23**.
8. Verifique seu **assinatura**, **grupo de recursos**, e **local** estão corretas e, em seguida, selecione **criar**.

### <a name="create-the-connection"></a>Criar a conexão
1. No portal do usuário, selecione **novo**.
2. Vá para **Marketplace**e, em seguida, selecione **rede**.
3. Na lista de recursos, selecione **Conexão**.
4. Sobre o **Noções básicas** folha de configurações, para o **tipo de Conexão**, selecione **Site a site (IPSec)**.
5. Selecione o **assinatura**, **grupo de recursos**, e **local**e, em seguida, selecione **Okey**.
6. Sobre o **configurações** folha, selecione **gateway de rede Virtual**e, em seguida, selecione **GW1**.
7. Selecione **gateway de rede Local**e, em seguida, selecione **POC2-GW**.
8. Em **nome de Conexão**, digite **POC1 POC2**.
9. Em **compartilhado PSK (chave)**, digite **12345**e, em seguida, selecione **Okey**.
10. Sobre o **resumo** folha, selecione **Okey**.

### <a name="create-a-vm"></a>Criar uma máquina virtual
Para validar os dados que passa a conexão VPN, é necessário que as máquinas virtuais para enviar e receber dados em cada Kit de desenvolvimento de pilha do Azure. Criar uma máquina virtual no POC1 agora e, em seguida, em sua rede virtual, coloque-a na sub-rede VM.

1. No portal do Azure, selecione **novo**.
2. Vá para **Marketplace**e, em seguida, selecione **de computação**.
3. Na lista de imagens da máquina virtual, selecione o **Datacenter avaliação do Windows Server 2016** imagem.
4. No **Noções básicas de** folha, na **nome**, digite **VM01**.
5. Insira um nome de usuário válido e uma senha. Você pode usar essa conta para entrar para a máquina virtual após sua criação.
6. Forneça um **assinatura**, **grupo de recursos**, e **local**e, em seguida, selecione **Okey**.
7. Sobre o **tamanho** folha, nessa instância, selecione um tamanho de máquina virtual e, em seguida, selecione **selecione**.
8. Sobre o **configurações** folha, aceite os padrões. Certifique-se de que o **VNET-01** rede virtual está selecionado. Verificar se a sub-rede é definida como **10.0.10.0/24**. Depois, selecione **OK**.
9. Sobre o **resumo** folha, examine as configurações e, em seguida, selecione **Okey**.



## <a name="create-the-network-resources-in-poc2"></a>Criar os recursos de rede em POC2

A próxima etapa é criar os recursos de rede para POC2. As instruções a seguir mostram como criar os recursos usando o portal do usuário.

### <a name="sign-in-as-a-tenant"></a>Entrar como um locatário
Um administrador de serviço pode entrar como um locatário para testar os planos, ofertas e assinaturas que podem usar seus locatários. Se você ainda não tiver um, [criar uma conta de locatário](azure-stack-add-new-user-aad.md) antes de você entrar.

### <a name="create-the-virtual-network-and-vm-subnet"></a>Criar a rede virtual e sub-rede de VM

1. Entrar usando uma conta de locatário.
2. No portal do usuário, selecione **novo**.
3. Vá para **Marketplace**e, em seguida, selecione **rede**.
4. Selecione **rede Virtual**.
5. Use as informações que aparece anteriormente na tabela de configuração de rede para identificar os valores para o POC2 **nome**, **espaço de endereço**, **nome da sub-rede**e **Intervalo de endereços de sub-rede**.
6. Em **assinatura**, a assinatura que você criou anteriormente é exibida.
7. Para **grupo de recursos**, crie um novo grupo de recursos ou, se você já tiver um, selecione **usar existente**.
8. Verifique se o padrão **local**.
9. Selecione **Fixar no painel**.
10. Selecione **Criar**.

### <a name="create-the-gateway-subnet"></a>Criar a Sub-rede de Gateway
1. Abrir o recurso de rede Virtual criada por você (**VNET-02**) do painel.
2. Na folha **Configurações**, selecione **Sub-redes**.
3. Selecione **sub-rede de Gateway** para adicionar uma sub-rede do gateway para a rede virtual.
4. O nome da sub-rede é definido como **Sub-rede de Gateway** por padrão.
   Sub-redes de gateway são especiais e devem ter esse nome específico para funcionar corretamente.
5. No **um intervalo de endereços** campo, verifique se o endereço é **10.0.21.0/24**.
6. Selecione **Okey** para criar a sub-rede de gateway.

### <a name="create-the-virtual-network-gateway"></a>Criar o gateway de rede virtual
1. No portal do Azure, selecione **novo**.  
2. Vá para **Marketplace**e, em seguida, selecione **rede**.
3. Na lista de recursos de rede, selecione **gateway de rede Virtual**.
4. Em **nome**, digite **GW2**.
5. Para escolher uma rede virtual, selecione **rede Virtual**. Em seguida, selecione **VNET-02** da lista.
6. Selecione **endereço IP público**. Quando o **escolher endereço IP público** folha é aberta, selecione **criar novo**.
7. Em **nome**, digite **GW2 PiP**e, em seguida, selecione **Okey**.
8. Por padrão, para **tipo VPN**, **baseadas em rota** está selecionado.
    Manter o **baseadas em rota** tipo de VPN.
9. Verifique se **Assinatura** e **Local** estão corretos. Você pode fixar o recurso para o painel. Selecione **Criar**.

### <a name="create-the-local-network-gateway-resource"></a>Criar o recurso de gateway de rede local

1. No portal do usuário POC2, selecione **novo**. 
4. Vá para **Marketplace**e, em seguida, selecione **rede**.
5. Na lista de recursos, selecione **gateway de rede Local**.
6. Em **nome**, digite **POC1-GW**.
7. Em **endereço IP**, digite o endereço externo BGPNAT POC1 listado anteriormente na tabela de configuração de rede.
8. Em **espaço de endereço**, em POC1, digite o **10.0.10.0/23** espaço de endereço de **VNET-01**.
9. Verifique seu **assinatura**, **grupo de recursos**, e **local** estão corretas e, em seguida, selecione **criar**.

## <a name="create-the-connection"></a>Criar a conexão
1. No portal do usuário, selecione **novo**. 
2. Vá para **Marketplace**e, em seguida, selecione **rede**.
3. Na lista de recursos, selecione **Conexão**.
4. No **básica** folha de configurações para o **o tipo de Conexão**, escolha **Site a site (IPSec)**.
5. Selecione o **assinatura**, **grupo de recursos**, e **local**e, em seguida, selecione **Okey**.
6. Sobre o **configurações** folha, selecione **gateway de rede Virtual**e, em seguida, selecione **GW2**.
7. Selecione **gateway de rede Local**e, em seguida, selecione **POC1-GW**.
8. Em **nome de Conexão**, digite **POC2 POC1**.
9. Em **compartilhado PSK (chave)**, digite **12345**. Se você escolher um valor diferente, lembre-se que ele *deve* corresponde ao valor para a chave compartilhada que você criou no POC1. Selecione **OK**.
10. Examine o **resumo** folha e, em seguida, selecione **Okey**.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
Criar uma máquina virtual no POC2 agora e colocá-la na sua sub-rede da VM em sua rede virtual.

1. No portal do Azure, selecione **novo**.
2. Vá para **Marketplace**e, em seguida, selecione **de computação**.
3. Na lista de imagens da máquina virtual, selecione o **Datacenter avaliação do Windows Server 2016** imagem.
4. Sobre o **Noções básicas de** folha, para **nome**, insira **VM02**.
5. Insira um nome de usuário válido e uma senha. Você pode usar essa conta para entrar na máquina virtual após sua criação.
6. Forneça um **assinatura**, **grupo de recursos**, e **local**e, em seguida, selecione **Okey**.
7. Sobre o **tamanho** folha, selecione uma máquina virtual de tamanho para essa instância e, em seguida, selecione **selecione**.
8. Sobre o **configurações** folha, você pode aceitar os padrões. Certifique-se de que o **VNET-02** rede virtual está selecionado e verificar se a sub-rede é definida como **10.0.20.0/24**. Selecione **OK**.
9. Examine as configurações no **resumo** folha e, em seguida, selecione **Okey**.

## <a name="configure-the-nat-virtual-machine-on-each-azure-stack-development-kit-for-gateway-traversal"></a>Configurar a máquina virtual NAT em cada Kit de desenvolvimento de pilha do Azure para passagem de gateway
Como o Kit de desenvolvimento de pilha do Azure é autossuficiente e isolado da rede na qual o host físico é implantado, o *externo* rede VIP os gateways conectadas ao não é realmente externo. Em vez disso, a rede do VIP fique oculta atrás de um roteador que executa a conversão de endereço de rede. 

O roteador for uma máquina virtual do Windows Server, chamada *AzS bgpnat01*, que executa a função de roteamento e os serviços de acesso remoto (RRAS) na infraestrutura do Kit de desenvolvimento de pilha do Azure. Você deve configurar a NAT na máquina virtual AzS bgpnat01 para permitir a conexão de VPN site a site conectar-se em ambas as extremidades. 

Para configurar a conexão VPN, você deve criar uma rota estática de mapa NAT que mapeia a interface externa na máquina virtual BGPNAT para o VIP do Pool de Gateway de borda. Uma rota estática de mapa NAT é necessária para cada porta em uma conexão VPN.

> [!NOTE]
> Essa configuração é necessária para ambientes do Kit de desenvolvimento de pilha do Azure apenas.
> 
> 

### <a name="configure-the-nat"></a>Configurar a NAT
> [!IMPORTANT]
> Você deve concluir este procedimento para *ambos* ambientes do Kit de desenvolvimento de pilha do Azure.

1. Determinar a **endereço IP interno** para usar o seguinte script do PowerShell. Abrir o gateway de rede virtual (GW1 e GW2) e, em seguida, o **visão geral** folha, salve o valor para o **endereço IP público** para uso posterior.
![Endereço IP interno](media/azure-stack-create-vpn-connection-one-node-tp2/InternalIP.PNG)
2. Entrar para a máquina física na pilha do Azure para POC1.
3. Copiar e editar o seguinte script do PowerShell. Para configurar a NAT em cada Kit de desenvolvimento de pilha do Azure, execute o script em um ISE do Windows PowerShell com privilégios elevados. No script, adicionar valores para o *endereço externo BGPNAT* e *endereço IP interno* espaços reservados:

   ```powershell
   # Designate the external NAT address for the ports that use the IKE authentication.
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 499 `
      -PortEnd 501}
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 4499 `
      -PortEnd 4501}
   # create a static NAT mapping to map the external address to the Gateway
   # Public IP Address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
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
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 4500 `
      -InternalPort 4500}
   ```

4. Repita esse procedimento em POC2.

## <a name="test-the-connection"></a>Testar a conexão
Agora que a conexão site a site é estabelecida, você deve validar que você pode obter o tráfego que flui através dele. Para validar, entrar em uma das máquinas virtuais que você criou em um ambiente do Kit de desenvolvimento de pilha do Azure. Em seguida, execute ping a máquina virtual que você criou em outro ambiente. 

Para garantir que você envie tráfego por meio de conexão site a site, certifique-se de que você execute ping no endereço de IP (DIP) direto da máquina virtual na sub-rede remota, não o VIP. Para fazer isso, localize o endereço DIP na outra extremidade de conexão. Salve o endereço para uso posterior.

### <a name="sign-in-to-the-tenant-vm-in-poc1"></a>Entrar para o VM no POC1 de locatário
1. Entrar para a máquina física na pilha do Azure para POC1 e, em seguida, usar uma conta de locatário para entrar no portal do usuário.
2. Na barra de navegação à esquerda, selecione **de computação**.
3. Na lista de máquinas virtuais, localizar **VM01** que você criou anteriormente e selecioná-lo.
4. Na folha para a máquina virtual, clique em **conectar**e, em seguida, abra o arquivo VM01.rdp.
   
     ![Botão conectar](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)
5. Entre com a conta que você configurou quando criou a máquina virtual.
6. Abra um elevado **do Windows PowerShell** janela.
7. Digite **ipconfig /all**.
8. Na saída, localize o **endereço IPv4**e, em seguida, salve o endereço para uso posterior. Este é o endereço que você irá executar o ping de POC2. No ambiente de exemplo, o endereço é **10.0.10.4**, mas, em seu ambiente, pode ser diferente. Ele deve estar dentro do **10.0.10.0/24** sub-rede que você criou anteriormente.
9. Para criar uma regra de firewall que permite que a máquina virtual responder a pings, execute o seguinte comando do PowerShell:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-poc2"></a>Entrar para o VM no POC2 de locatário
1. Entrar para a máquina física na pilha do Azure para POC2 e, em seguida, usar uma conta de locatário para entrar no portal do usuário.
2. Na barra de navegação à esquerda, clique em **de computação**.
3. Na lista de máquinas virtuais, localizar **VM02** que você criou anteriormente e selecioná-lo.
4. Na folha da máquina virtual, clique em **Conectar**.
5. Entre com a conta que você configurou quando criou a máquina virtual.
6. Abra um elevado **do Windows PowerShell** janela.
7. Digite **ipconfig /all**.
8. Você verá um endereço IPv4 que fica dentro de **10.0.20.0/24**. O ambiente de exemplo, o endereço é **10.0.20.4**, mas seu endereço pode ser diferente.
9. Para criar uma regra de firewall que permite que a máquina virtual responder a pings, execute o seguinte comando do PowerShell:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

10. Da máquina virtual no POC2, execute ping a máquina virtual em POC1, por meio do túnel. Para fazer isso, você pode executar ping o DIP que você registrou de VM01.
   No ambiente de exemplo, isso é **10.0.10.4**, mas certifique-se de executar ping no endereço que você anotou no laboratório. Você verá um resultado que é semelhante ao seguinte:
   
    ![Ping com êxito](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
11. Uma resposta da máquina virtual remota indica um teste com êxito! Você pode fechar a janela da máquina virtual. Para testar sua conexão, você pode tentar outros tipos de transferências de dados como uma cópia do arquivo.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Exibindo estatísticas de transferência de dados com a conexão de gateway
Se você quiser saber a quantidade de dados passa por meio de sua conexão site a site, essas informações estão disponíveis no **Conexão** folha. Esse teste também é outra maneira de verificar se o ping que você acabou de ser enviado foi realmente por meio de conexão VPN.

1. Enquanto você estiver conectado à máquina virtual locatário POC2, use sua conta de locatário para entrar no portal do usuário.
2. Vá para **todos os recursos**e, em seguida, selecione o **POC2 POC1** conexão. **Conexões** é exibida.
4. Sobre o **Conexão** folha, as estatísticas de **dados em** e **dados** aparecem. Na captura de tela a seguir, os números grandes são atribuídos a transferência de arquivos adicionais. Você deve ver alguns valores de zero.
   
    ![Dados de entrada e saída](media/azure-stack-create-vpn-connection-one-node-tp2/image20.png)
