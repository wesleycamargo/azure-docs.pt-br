---
title: Criar uma conexão de VPN site a site entre duas redes virtuais em diferentes ambientes de Kit de desenvolvimento do Azure Stack | Microsoft Docs
description: Procedimento passo a passo que um administrador de nuvem usa para criar uma conexão de VPN site a site entre dois ambientes de Kit de desenvolvimento do Azure Stack de nó único.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 3f1b4e02-dbab-46a3-8e11-a777722120ec
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/12/2018
ms.author: brenduns
ms.reviewer: scottnap
ROBOTS: NOINDEX
ms.openlocfilehash: a219f44cb27bb6ebe4e17079ad487457ae8852f0
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/12/2018
ms.locfileid: "44718110"
---
# <a name="create-a-site-to-site-vpn-connection-between-two-virtual-networks-in-different-azure-stack-development-kit-environments"></a>Criar uma conexão de VPN site a site entre duas redes virtuais em diferentes ambientes de Kit de desenvolvimento do Azure Stack
## <a name="overview"></a>Visão geral
Este artigo mostra como criar uma conexão de VPN site a site entre duas redes virtuais em dois ambientes separados do Kit de desenvolvimento do Azure Stack. Ao configurar as conexões, você aprenderá como funcionam os gateways VPN no Azure Stack.

### <a name="connection-diagram"></a>Diagrama de conexão
O diagrama a seguir mostra o que a configuração de conexão deve ter aparência quando você terminar.

![Configuração de conexão de VPN site a site](media/azure-stack-create-vpn-connection-one-node-tp2/OneNodeS2SVPN.png)

### <a name="before-you-begin"></a>Antes de começar
Para concluir a configuração de conexão, certifique-se de que você tenha os seguintes itens antes de começar:

* Dois servidores e outros pré-requisitos que atendem aos requisitos de hardware do Kit de desenvolvimento do Azure Stack, conforme descrito em [guia de início rápido: avaliar o Kit de desenvolvimento do Azure Stack](azure-stack-deploy-overview.md). 
* O [Kit de desenvolvimento do Azure Stack](https://azure.microsoft.com/overview/azure-stack/try/) pacote de implantação.

## <a name="deploy-the-azure-stack-development-kit-environments"></a>Implante os ambientes do Kit de desenvolvimento do Azure Stack
Para concluir a configuração de conexão, você deve implantar dois ambientes de Kit de desenvolvimento do Azure Stack.
> [!NOTE] 
> Para cada Kit de desenvolvimento de pilha do Azure que você implanta, execute as [instruções de implantação](azure-stack-run-powershell-script.md). Neste artigo, os ambientes do Kit de desenvolvimento do Azure Stack são chamados *POC1* e *POC2*.


## <a name="prepare-an-offer-on-poc1-and-poc2"></a>Preparar uma oferta em POC1 e POC2
Em POC1 e POC2, prepare uma oferta para que um usuário pode assinar a oferta e implantar as máquinas virtuais. Para obter informações sobre como criar uma oferta, consulte [disponibilizar as máquinas virtuais para os usuários do Azure Stack](azure-stack-tutorial-tenant-vm.md).

## <a name="review-and-complete-the-network-configuration-table"></a>Revise e complete a tabela de configuração de rede
A tabela a seguir resume a configuração de rede para ambos os ambientes do Kit de desenvolvimento do Azure Stack. Use o procedimento que aparece após a tabela para adicionar o endereço externo BGPNAT que seja específico para sua rede.

**Tabela de configuração de rede**
|   |POC1|POC2|
|---------|---------|---------|
|Nome da rede virtual     |VNET-01|VNET-02 |
|Espaço de endereço de rede virtual |10.0.10.0/23|10.0.20.0/23|
|Nome da sub-rede     |Subrede-01|Subnet-02|
|Intervalo de endereços da sub-rede|10.0.10.0/24 |10.0.20.0/24 |
|Gateway de sub-rede     |10.0.11.0/24|10.0.21.0/24|
|Endereço BGPNAT externo     |         |         |

> [!NOTE]
> Os endereços IP BGPNAT externos no ambiente de exemplo são 10.16.167.195 para POC1 e 10.16.169.131 para POC2. Use o procedimento a seguir para determinar os endereços IP BGPNAT externos para seus hosts do Kit de desenvolvimento do Azure Stack e, em seguida, adicioná-los para a tabela anterior de configuração de rede.


### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>Obter o endereço IP do adaptador externo da VM NAT
1. Entrar no computador físico do Azure Stack para POC1.
2. Edite o seguinte código do Powershell para substituir a senha de administrador e, em seguida, execute o código no host do POC:

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
Agora você pode criar os recursos de rede POC1 que você precisa para definir seus gateways. As instruções a seguir mostram como criar os recursos usando o portal do usuário. Você também pode usar o código do PowerShell para criar os recursos.

![Fluxo de trabalho que é usado para criar recursos](media/azure-stack-create-vpn-connection-one-node-tp2/image2.png)

### <a name="sign-in-as-a-tenant"></a>Entrar como um locatário
Um administrador de serviço pode entrar como um locatário para testar os planos, ofertas e assinaturas que seus locatários podem usar. Se você ainda não tiver uma, [criar uma conta de locatário](azure-stack-add-new-user-aad.md) antes de entrar no.

### <a name="create-the-virtual-network-and-vm-subnet"></a>Criar a rede virtual e sub-rede de VM
1. Use uma conta de locatário para entrar no portal do usuário.
2. No portal do usuário, selecione **+ criar um recurso**.
3. Vá para **Marketplace**e, em seguida, selecione **Networking**.
4. Selecione **Rede virtual**.
5. Para **nome**, **espaço de endereço**, **nome da sub-rede**, e **intervalo de endereços de sub-rede**, use os valores que aparecem anteriormente na rede tabela de configuração.
6. Na **assinatura**, a assinatura que você criou anteriormente é exibida.
7. Para **grupo de recursos**, você pode criar um grupo de recursos ou se você já tiver um, selecione **usar existente**.
8. Verifique o local padrão.
9. Selecione **Fixar no painel**.
10. Selecione **Criar**.

### <a name="create-the-gateway-subnet"></a>Criar a sub-rede de gateway
1. No painel, abra o recurso de rede virtual VNET-01 que você criou anteriormente.
2. Na folha **Configurações**, selecione **Sub-redes**.
3. Para adicionar uma sub-rede de gateway à rede virtual, selecione **sub-rede de Gateway**.
   
    ![Adicionar sub-rede de gateway](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)

4. Por padrão, o nome da sub-rede é definido como **GatewaySubnet**.
   As sub-redes de gateway são especiais. Para funcionar corretamente, eles devem usar o *GatewaySubnet* nome.
5. Na **intervalo de endereços**, verifique se o endereço está **10.0.11.0/24**.
6. Selecione **Okey** para criar a sub-rede de gateway.

### <a name="create-the-virtual-network-gateway"></a>Criar o gateway de rede virtual
1. No portal do Azure, selecione **+ criar um recurso**. 
2. Vá para **Marketplace**e, em seguida, selecione **Networking**.
3. Na lista de recursos de rede, selecione **gateway de rede Virtual**.
4. Na **nome**, insira **GW1**.
5. Selecione o **rede Virtual** item para escolher uma rede virtual.
   Selecione **VNET-01** na lista.
6. Selecione o **endereço IP público** item de menu. Quando o **escolher endereço IP público** folha abrir, selecione **criar novo**.
7. Na **nome**, insira **GW1-PiP**e, em seguida, selecione **Okey**.
8.  Por padrão, para **tipo de VPN**, **baseado em rota** está selecionado.
    Manter o **baseado em rota** tipo de VPN.
9. Verifique se **Assinatura** e **Local** estão corretos. Você pode fixar o recurso no painel. Selecione **Criar**.

### <a name="create-the-local-network-gateway"></a>Criar o gateway de rede local
A implementação de um *gateway de rede local* nessa implantação de avaliação do Azure Stack é um pouco diferente do que uma implantação real do Azure.

Em uma implantação do Azure, um gateway de rede local representa um dispositivo físico local (no Locatário), que você usa para se conectar a um gateway de rede virtual no Azure. Nessa implantação de avaliação do Azure Stack, ambas as extremidades da conexão são gateways de rede virtual!

Uma maneira de pensar sobre isso de forma mais genérica é que o recurso de gateway de rede local sempre indica o gateway remoto na outra extremidade de conexão. Por causa da maneira que o Kit de desenvolvimento do Azure Stack foi projetado, você precisa fornecer o endereço IP do adaptador de rede externo na conversão de endereços de rede (NAT) VM com o outro do Azure Stack Kit de desenvolvimento como o endereço IP público do gateway de rede local. Você, em seguida, crie mapeamentos NAT na VM NAT para certificar-se de que ambas as extremidades sejam conectadas corretamente.


### <a name="create-the-local-network-gateway-resource"></a>Criar o recurso de gateway de rede local
1. Entrar no computador físico do Azure Stack para POC1.
2. No portal do usuário, selecione **+ criar um recurso**.
3. Vá para **Marketplace**e, em seguida, selecione **Networking**.
4. Na lista de recursos, selecione **gateway de rede local**.
5. Na **nome**, insira **POC2-GW**.
6. Na **endereço IP**, insira o endereço externo BGPNAT para POC2. Esse endereço aparece anteriormente na tabela de configuração de rede.
7. Na **espaço de endereço**, para o espaço de endereço da rede virtual POC2 que você cria posteriormente, insira **10.0.20.0/23**.
8. Verifique sua **assinatura**, **grupo de recursos**, e **local** estão corretas e, em seguida, selecione **criar**.

### <a name="create-the-connection"></a>Criar a conexão
1. No portal do usuário, selecione **+ criar um recurso**.
2. Vá para **Marketplace**e, em seguida, selecione **Networking**.
3. Na lista de recursos, selecione **Conexão**.
4. Sobre o **Noções básicas** folha configurações, para o **tipo de Conexão**, selecione **Site a site (IPSec)**.
5. Selecione o **assinatura**, **grupo de recursos**, e **local**e, em seguida, selecione **Okey**.
6. Sobre o **as configurações** folha, selecione **gateway de rede Virtual**e, em seguida, selecione **GW1**.
7. Selecione **gateway de rede Local**e, em seguida, selecione **POC2-GW**.
8. Na **nome de Conexão**, insira **POC1-POC2**.
9. Na **chave compartilhada (PSK)**, insira **12345**e, em seguida, selecione **Okey**.
10. Sobre o **resumo** folha, selecione **Okey**.

### <a name="create-a-vm"></a>Criar uma máquina virtual
Para validar os dados que trafegam por meio de conexão de VPN, você precisa de máquinas virtuais para enviar e receber dados em cada Kit de desenvolvimento do Azure Stack. Criar uma máquina virtual em POC1 agora e, em seguida, em sua rede virtual, coloco na sub-rede de VM.

1. No portal do Azure, selecione **+ criar um recurso**.
2. Vá para **Marketplace**e, em seguida, selecione **computação**.
3. Na lista de imagens de máquina virtual, selecione a **Windows Server 2016 Datacenter Eval** imagem.
4. Sobre o **Noções básicas** folha, na **nome**, insira **VM01**.
5. Insira um nome de usuário válido e uma senha. Use essa conta para entrar na VM após sua criação.
6. Fornecer um **assinatura**, **grupo de recursos**, e **local**e, em seguida, selecione **Okey**.
7. Sobre o **tamanho** folha para esta instância, selecione um tamanho de máquina virtual e, em seguida, selecione **selecione**.
8. Sobre o **configurações** folha, aceite os padrões. Certifique-se de que o **VNET-01** rede virtual está selecionada. Verificar se a sub-rede é definida como **10.0.10.0/24**. Depois, selecione **OK**.
9. Sobre o **resumo** folha, examine as configurações e, em seguida, selecione **Okey**.



## <a name="create-the-network-resources-in-poc2"></a>Criar os recursos de rede em POC2

A próxima etapa é criar os recursos de rede para POC2. As instruções a seguir mostram como criar os recursos usando o portal do usuário.

### <a name="sign-in-as-a-tenant"></a>Entrar como um locatário
Um administrador de serviço pode entrar como um locatário para testar os planos, ofertas e assinaturas que seus locatários podem usar. Se você ainda não tiver uma, [criar uma conta de locatário](azure-stack-add-new-user-aad.md) antes de entrar no.

### <a name="create-the-virtual-network-and-vm-subnet"></a>Criar a rede virtual e sub-rede de VM

1. Entrar usando uma conta de locatário.
2. No portal do usuário, selecione **+ criar um recurso**.
3. Vá para **Marketplace**e, em seguida, selecione **Networking**.
4. Selecione **Rede virtual**.
5. Use as informações que aparecem anteriormente na tabela de configuração de rede para identificar os valores para o POC2 **nome**, **espaço de endereço**, **nome da sub-rede**e **Intervalo de endereços de sub-rede**.
6. Na **assinatura**, a assinatura que você criou anteriormente é exibida.
7. Para **grupo de recursos**, crie um novo grupo de recursos ou, se você já tiver um, selecione **usar existente**.
8. Verifique se o padrão **local**.
9. Selecione **Fixar no painel**.
10. Selecione **Criar**.

### <a name="create-the-gateway-subnet"></a>Criar a Sub-rede de Gateway
1. Abra o recurso de rede Virtual criado (**VNET-02**) do painel.
2. Na folha **Configurações**, selecione **Sub-redes**.
3. Selecione **sub-rede de Gateway** para adicionar uma sub-rede de gateway à rede virtual.
4. O nome da sub-rede é definido como **Sub-rede de Gateway** por padrão.
   Sub-redes de gateway são especiais e devem ter esse nome específico para funcionar corretamente.
5. No **intervalo de endereços** campo, verifique se o endereço está **10.0.21.0/24**.
6. Selecione **Okey** para criar a sub-rede de gateway.

### <a name="create-the-virtual-network-gateway"></a>Criar o gateway de rede virtual
1. No portal do Azure, selecione **+ criar um recurso**.  
2. Vá para **Marketplace**e, em seguida, selecione **Networking**.
3. Na lista de recursos de rede, selecione **gateway de rede Virtual**.
4. Na **nome**, insira **GW2**.
5. Para escolher uma rede virtual, selecione **rede Virtual**. Em seguida, selecione **VNET-02** na lista.
6. Selecione **endereço IP público**. Quando o **escolher endereço IP público** folha abrir, selecione **criar novo**.
7. Na **nome**, insira **GW2-PiP**e, em seguida, selecione **Okey**.
8. Por padrão, para **tipo de VPN**, **baseado em rota** está selecionado.
    Manter o **baseado em rota** tipo de VPN.
9. Verifique se **Assinatura** e **Local** estão corretos. Você pode fixar o recurso no painel. Selecione **Criar**.

### <a name="create-the-local-network-gateway-resource"></a>Criar o recurso de gateway de rede local

1. No portal do usuário POC2, selecione **+ criar um recurso**. 
4. Vá para **Marketplace**e, em seguida, selecione **Networking**.
5. Na lista de recursos, selecione **gateway de rede Local**.
6. Na **nome**, insira **POC1-GW**.
7. Na **endereço IP**, insira o endereço externo BGPNAT POC1 listada anteriormente na tabela de configuração de rede.
8. Na **espaço de endereço**, em POC1, digite o **10.0.10.0/23** espaço de endereço da **VNET-01**.
9. Verifique sua **assinatura**, **grupo de recursos**, e **local** estão corretas e, em seguida, selecione **criar**.

## <a name="create-the-connection"></a>Criar a conexão
1. No portal do usuário, selecione **+ criar um recurso**. 
2. Vá para **Marketplace**e, em seguida, selecione **Networking**.
3. Na lista de recursos, selecione **Conexão**.
4. Sobre o **básica** folha configurações, para o **tipo de Conexão**, escolha **Site a site (IPSec)**.
5. Selecione o **assinatura**, **grupo de recursos**, e **local**e, em seguida, selecione **Okey**.
6. Sobre o **as configurações** folha, selecione **gateway de rede Virtual**e, em seguida, selecione **GW2**.
7. Selecione **gateway de rede Local**e, em seguida, selecione **POC1-GW**.
8. Na **nome de Conexão**, insira **POC2-POC1**.
9. Na **chave compartilhada (PSK)**, insira **12345**. Se você escolher um valor diferente, lembre-se que ele *deve* corresponde ao valor da chave compartilhada que você criou em POC1. Selecione **OK**.
10. Examine os **resumo** folha e, em seguida, selecione **Okey**.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
Criar uma máquina virtual em POC2 agora e colocá-lo em sua sub-rede VM em sua rede virtual.

1. No portal do Azure, selecione **+ criar um recurso**.
2. Vá para **Marketplace**e, em seguida, selecione **computação**.
3. Na lista de imagens de máquina virtual, selecione a **Windows Server 2016 Datacenter Eval** imagem.
4. Sobre o **Noções básicas** folha, para **nome**, insira **VM02**.
5. Insira um nome de usuário válido e uma senha. Use essa conta para entrar na máquina virtual após sua criação.
6. Fornecer um **assinatura**, **grupo de recursos**, e **local**e, em seguida, selecione **Okey**.
7. Sobre o **tamanho** folha, selecione uma máquina virtual de tamanho para esta instância e, em seguida, selecione **selecione**.
8. Sobre o **configurações** folha, você pode aceitar os padrões. Certifique-se de que o **VNET-02** rede virtual está selecionado e verificar se a sub-rede é definida como **10.0.20.0/24**. Selecione **OK**.
9. Examine as configurações na **resumo** folha e, em seguida, selecione **Okey**.

## <a name="configure-the-nat-virtual-machine-on-each-azure-stack-development-kit-for-gateway-traversal"></a>Configurar a máquina virtual NAT em cada Kit de desenvolvimento do Azure Stack para percurso de gateway
Como o Kit de desenvolvimento do Azure Stack é independente e isolada da rede na qual o host físico é implantado, o *externo* rede VIP que os gateways estão conectados não é realmente externa. Em vez disso, a rede VIP está oculto por trás de um roteador que executa a conversão de endereço de rede. 

O roteador é uma máquina virtual do Windows Server, chamada *AzS-bgpnat01*, que executa a função de roteamento e os serviços de acesso remoto (RRAS) na infraestrutura do Kit de desenvolvimento do Azure Stack. Você deve configurar NAT na máquina virtual AzS-bgpnat01 para permitir a conexão de VPN site a site conectar-se em ambas as extremidades. 

Para configurar a conexão VPN, você deve criar uma rota de mapa NAT estática que mapeia a interface externa na máquina virtual BGPNAT para o VIP do Pool de Gateway de borda. Uma rota estática de mapa NAT é necessária para cada porta em uma conexão VPN.

> [!NOTE]
> Essa configuração é necessária para ambientes do Kit de desenvolvimento do Azure Stack apenas.
> 
> 

### <a name="configure-the-nat"></a>Configurar o NAT
> [!IMPORTANT]
> Você deve concluir este procedimento para *ambos* ambientes do Kit de desenvolvimento do Azure Stack.

1. Determinar a **endereço IP interno** para usar no script do PowerShell a seguir. Abra o gateway de rede virtual (GW1 e GW2) e, em seguida, na **visão geral** folha, salve o valor para o **endereço IP público** para uso posterior.
![Endereço IP interno](media/azure-stack-create-vpn-connection-one-node-tp2/InternalIP.PNG)
2. Entrar no computador físico do Azure Stack para POC1.
3. Copie e edite o seguinte script do PowerShell. Para configurar o NAT em cada Kit de desenvolvimento do Azure Stack, execute o script em um Windows PowerShell ISE elevado. No script, adicione valores para o *endereço externo BGPNAT* e *endereço IP interno* espaços reservados:

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
Agora que a conexão site a site é estabelecida, você deve validar que você pode obter o tráfego flua através dela. Para validar, entrar em uma das máquinas virtuais que você criou em um ambiente do Kit de desenvolvimento do Azure Stack. Em seguida, execute ping a máquina virtual que você criou em outro ambiente. 

Para garantir que você envia o tráfego por meio de conexão site a site, certifique-se de que você execute ping no endereço de IP (DIP) direto da máquina virtual na sub-rede remota, não o VIP. Para fazer isso, localize o endereço DIP na outra extremidade de conexão. Salve o endereço para uso posterior.

### <a name="sign-in-to-the-tenant-vm-in-poc1"></a>Entrar para a VM de locatário em POC1
1. Entrar no computador físico do Azure Stack para POC1 e, em seguida, use uma conta de locatário para entrar no portal do usuário.
2. Na barra de navegação à esquerda, selecione **computação**.
3. Na lista de VMs, localize **VM01** que você criou anteriormente e, em seguida, selecioná-lo.
4. Na folha da máquina virtual, clique em **Connect**e, em seguida, abra o arquivo de VM01.rdp.
   
     ![Botão conectar](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)
5. Entre com a conta que você configurou quando criou a máquina virtual.
6. Abrir com privilégios elevados **Windows PowerShell** janela.
7. Insira **ipconfig/all**.
8. Na saída, localize o **endereço IPv4**, em seguida, salve o endereço para uso posterior. Esse é o endereço que você executará ping de POC2. No ambiente de exemplo, o endereço é **10.0.10.4**, mas, em seu ambiente, pode ser diferente. Ela deverá cair dentro de **10.0.10.0/24** sub-rede que você criou anteriormente.
9. Para criar uma regra de firewall que permite que a máquina virtual para responder a pings, execute o seguinte comando do PowerShell:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-poc2"></a>Entrar para a VM de locatário em POC2
1. Entrar no computador físico do Azure Stack para POC2 e, em seguida, use uma conta de locatário para entrar no portal do usuário.
2. Na barra de navegação à esquerda, clique em **computação**.
3. Na lista de máquinas virtuais, localize **VM02** que você criou anteriormente e, em seguida, selecioná-lo.
4. Na folha da máquina virtual, clique em **Conectar**.
5. Entre com a conta que você configurou quando criou a máquina virtual.
6. Abrir com privilégios elevados **Windows PowerShell** janela.
7. Insira **ipconfig/all**.
8. Você deve ver um endereço IPv4 que esteja dentro **10.0.20.0/24**. No ambiente de exemplo, é o endereço **10.0.20.4**, mas seu endereço pode ser diferente.
9. Para criar uma regra de firewall que permite que a máquina virtual para responder a pings, execute o seguinte comando do PowerShell:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

10. Da máquina virtual em POC2, execute ping a máquina virtual em POC1, através do túnel. Para fazer isso, você pode executar ping o DIP que registrou na VM01.
   No ambiente de exemplo, isso é **10.0.10.4**, mas não se esqueça de executar ping no endereço que você anotou no laboratório. Você verá um resultado que é semelhante ao seguinte:
   
    ![Ping com êxito](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
11. Uma resposta da máquina virtual remota indica um teste bem-sucedido! Você pode fechar a janela da máquina virtual. Para testar sua conexão, você pode tentar outros tipos de transferências de dados, como uma cópia de arquivo.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Exibindo estatísticas de transferência de dados com a conexão de gateway
Se você quiser saber a quantidade de dados passa por meio de sua conexão site a site, essas informações estão disponíveis sobre o **Conexão** folha. Esse teste também é outra maneira de verificar que o ping que você acabou de enviar realmente atravessou a conexão VPN.

1. Enquanto você estiver conectado à máquina virtual locatário em POC2, use sua conta de locatário para entrar no portal do usuário.
2. Vá para **todos os recursos**e, em seguida, selecione o **POC2-POC1** conexão. **Conexões** é exibida.
4. Sobre o **Conexão** folha, as estatísticas de **dados no** e **dados de saída** aparecem. Na captura de tela a seguir, os números grandes são atribuídos à transferência de arquivo adicionais. Você deve ver alguns valores de diferentes de zero.
   
    ![Entrada e saída de dados](media/azure-stack-create-vpn-connection-one-node-tp2/image20.png)
