---
title: Como conectar redes virtuais clássicas a redes virtuais do Resource Manager no portal | Microsoft Docs
description: Saiba como criar uma conexão VPN entre Rede Virtuais clássicas e Rede Virtuais do Resource Manager usando o Gateway de VPN e o portal
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: ''
tags: azure-service-management,azure-resource-manager

ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/03/2016
ms.author: cherylmc

---
# <a name="connect-virtual-networks-from-different-deployment-models-in-the-portal"></a>Conectar redes virtuais de diferentes modelos de implantação no portal
> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Atualmente, o Azure tem dois modelos de gerenciamento: o clássico e o RM (Resource Manager). Se você já usa o Azure há algum tempo, provavelmente terá as VMs do Azure e as funções de instância em execução em uma Rede Virtual clássica. Suas VMs e instâncias de função mais recentes podem estar em execução em uma Rede Virtual criada no Resource Manager. Este artigo explica como conectar redes virtuais clássicas a redes virtuais do Resource Manager para permitir que os recursos localizados nos modelos de implantação separados se comuniquem entre si usando uma conexão de gateway. 

Você pode criar uma conexão entre redes virtuais em assinaturas e regiões diferentes. Você também pode conectar redes virtuais que já têm conexões com redes locais, desde que o gateway com o qual foram configuradas seja dinâmico ou baseado em rota. Para saber mais sobre conexões de Rede Virtual a Rede Virtual, consulte as [Perguntas frequentes sobre Rede Virtual para Rede Virtual](#faq) no final deste artigo.

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Modelos de implantação e métodos para conexões de VNet a VNet
[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Atualizamos a tabela a seguir conforme os novos artigos, e ferramentas adicionais ficam disponíveis para esta configuração. Quando um artigo estiver disponível, o vincularemos diretamente da tabela.<br><br>

**Rede Virtual a Rede Virtual**

[!INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

#### <a name="vnet-peering"></a>Emparelhamento VNet
[!INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="before-beginning"></a>Antes de começar
As etapas a seguir mostrarão as configurações necessárias para configurar um gateway dinâmico ou baseado em rota para cada Rede Virtual, e para criar uma conexão VPN entre os gateways. Essa configuração não dá suporte a gateways estáticos ou baseados em política. 

Neste artigo, usamos o portal clássico, o Portal do Azure e o PowerShell. Atualmente, não é possível criar esta configuração usando apenas o Portal do Azure.

### <a name="prerequisites"></a>Pré-requisitos
* Ambas as redes virtuais já foram criadas.
* Os intervalos de endereços das redes virtuais não se sobrepõem entre eles ou aos intervalos de outras conexões às quais os gateways podem estar conectados.
* Você instalou os cmdlets do PowerShell mais recentes (1.0.2 ou mais recente). Consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações. Instale os dois cmdlets do Resource Manager (RM) e do Gerenciamento de Serviços (SM). 

### <a name="<a-name="values"></a>example-settings"></a><a name="values"></a>Configurações de exemplo
Você pode usar as configurações de exemplo como referência.

**Configurações da Rede Virtual clássica**

Nome da rede virtual = ClassicVNet  <br>
Local = Oeste dos EUA <br>
Espaços de Endereço da Rede Virtual = 10.0.0.0/24 <br>
Subnet-1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Nome da rede local = RMVNetLocal <br>

**Configurações de Rede Virtual do Resource Manager**

Nome da VNet = RMVNet  <br>
Grupo de recursos = RG1 <br>
Espaços de Endereço do IP da Rede Virtual = 192.168.0.0/16 <br>
Subnet-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Local = Leste dos EUA <br>
Nome do gateway de rede virtual = RMGateway <br>
Nome do IP Público do Gateway = gwpip <br>
Tipo de gateway = VPN <br>
Tipo de VPN = baseada em rota <br>
Gateway de rede local = ClassicVNetLocal <br>

## <a name="<a-name="createsmgw"></a>section-1:-configure-classic-vnet-settings"></a><a name="createsmgw"></a>Seção 1: Definir configurações de Rede Virtual clássica
Nesta seção, criamos a rede local e o gateway para sua Rede Virtual clássica. As instruções nesta seção usam o portal clássico. Atualmente, o Portal do Azure não oferece todas as configurações que pertencem a uma Rede Virtual clássica.

### <a name="part-1---create-a-new-local-network"></a>Parte 1 - Criar uma nova rede local
Abra o [portal clássico](https://manage.windowsazure.com) e entre com sua conta do Azure.

1. No canto inferior esquerdo da tela, clique em **NOVO** > **Serviços de Rede** > **Rede Virtual** > **Adicionar Rede Local**.
2. Na janela **Especificar os detalhes de sua rede local** , digite um nome para a Rede Virtual RM a qual você deseja se conectar. Em **Endereço IP do dispositivo VPN (opcional)** , digite qualquer endereço IP público válido. Isso é apenas um espaço reservado temporário. Altere esse endereço IP mais tarde. Clique no botão de seta no canto inferior direito da página.
3. Na página **Especificar o espaço de endereço**, na caixa de texto **IP inicial**, digite o prefixo de rede e o bloco de CIDR para a Rede Virtual do Resource Manager a qual você deseja se conectar. Essa configuração é usada para especificar o espaço de endereço para rotear até a Rede Virtual RM.

### <a name="part-2---associate-the-local-network-to-your-vnet"></a>Parte 2 - Associar a rede local à sua Rede Virtual
1. Clique em **Redes Virtuais** na parte superior da página para alternar para a tela de Redes Virtuais, depois clique para selecionar sua Rede Virtual clássica. Na página de sua Rede Virtual, clique em **Configurar** para navegar até a página de configuração.
2. Na seção de conexão **conectividade site a site**, marque a caixa de seleção **Conectar-se à rede local**. Em seguida, selecione a rede local que você criou. Se você tiver criado várias redes locais, selecione aquela que você criou para representar sua Rede Virtual do Resource Manager no menu suspenso.
3. Na parte inferior da página, clique em **Salvar** .

### <a name="part-3---create-the-gateway"></a>Parte 3 - Criar o gateway
1. Depois de salvar as configurações, clique em **Painel** na parte superior da página para alterar para a página Painel. Na parte inferior da página Painel, clique em **Criar Gateway** e clique em **Roteamento Dinâmico**. Clique em **Sim** para começar a criar seu gateway. Um gateway de Roteamento Dinâmico é necessário para esta configuração.
2. Aguarde a criação do gateway. Às vezes, isso pode demorar 45 minutos ou mais para ser concluído.

### <a name="<a-name="ip"></a>part-4---view-the-gateway-public-ip-address"></a><a name="ip"></a>Parte 4 - Exibir o endereço IP público do gateway
Após a criação do gateway, você pode exibir o endereço IP do gateway na página **Painel** . Esse é o endereço IP público de seu gateway. Anote ou copie o endereço IP público. Você o usará em etapas posteriores ao criar a rede local para sua configuração de Rede Virtual do Resource Manager.

## <a name="<a-name="creatermgw"></a>section-2:-configure-resource-manager-vnet-settings"></a><a name="creatermgw"></a>Seção 2: Definir as configurações da Rede Virtual do Resource Manager
Nesta seção, criamos o gateway de Rede Virtual e a rede local para a Rede Virtual do Resource Manager. Não comece as etapas a seguir até recuperar o endereço IP público do gateway da Rede Virtual clássica.

As capturas de tela são fornecidas como exemplos. Substitua os valores pelos seus próprios. Se você estiver criando esta configuração como um exercício, consulte esses [valores](#values).

### <a name="part-1---create-a-gateway-subnet"></a>Parte 1 - Criar uma sub-rede de gateway
Antes de conectar sua Rede Virtual a um gateway, você precisará criar a sub-rede de gateway para a Rede Virtual à qual você deseja se conectar. Criar uma sub-rede de gateway com a contagem CIDR de /28 ou maior (/ 27, / 26 etc.)

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

Em um navegador, acesse o [Portal do Azure](http://portal.azure.com) e entre com sua conta do Azure.

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="part-2---create-a-virtual-network-gateway"></a>Parte 2 - Criar um gateway de Rede Virtual
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

### <a name="part-3---create-a-local-network-gateway"></a>Parte 3 - Criar um gateway de rede local
O gateway de rede local geralmente se refere ao seu local. Ele informa ao Azure quais intervalos de endereço IP rotear para o local, e o endereço IP público do dispositivo para esse local. No entanto, nesse caso, ele se refere ao intervalo de endereços e ao endereço IP público associado à sua Rede Virtual clássica e o gateway de Rede Virtual.

Dê um nome ao gateway de rede local ao qual o Azure possa fazer referência. Você pode criar seu gateway de rede local enquanto o gateway de Rede Virtual estiver sendo criado. Para essa configuração, use o endereço IP público atribuído ao seu gateway de Rede Virtual clássica na [seção anterior](#ip).

[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

### <a name="part-4---copy-the-public-ip-address"></a>Parte 4 - Copiar o endereço IP público
Após a criação do gateway de Rede Virtual, copie o endereço IP público que está associado ao gateway. Use-o quando você definir as configurações de rede local para sua Rede Virtual clássica. 

## <a name="section-3:-modify-the-local-network-for-the-classic-vnet"></a>Seção 3: Modificar a rede local para a Rede Virtual clássica
Abra o [portal clássico](https://manage.windowsazure.com).

1. No portal clássico, role para baixo no lado esquerdo e clique em **Redes**. Na página **redes**, clique em **Redes Locais** na parte superior da página. 
2. Clique para selecionar a rede local configurada na Parte 1. Clique em **Editar**na parte inferior da página.
3. Na página **Especificar os detalhes de sua rede local** , substitua o endereço IP de espaço reservado pelo endereço IP público do gateway do Resource Manager que você criou na seção anterior. Clique na seta para ir para a próxima seção. Verifique se o **Espaço de Endereço** está correto e, em seguida, clique na marca de seleção para aceitar as alterações.

## <a name="<a-name="connect"></a>section-4:-create-the-connection"></a><a name="connect"></a>Seção 4: Criar a conexão
Nesta seção, criamos a conexão entre as redes virtuais. As etapas exigem o PowerShell. Você não pode criar esta conexão em nenhum dos dois portais. Baixe e instale os cmdlets do PowerShell clássicos (SM) e do Resource Manager (RM).

1. Faça logon na sua conta do Azure no console do PowerShell. O cmdlet a seguir solicita as credenciais de logon para sua conta do Azure. Depois de entrar, as configurações da conta são baixadas para que estejam disponíveis para o Azure PowerShell.
   
        Login-AzureRmAccount 
   
    Obtenha uma lista de suas assinaturas do Azure, se você tiver mais de uma assinatura.
   
        Get-AzureRmSubscription
   
    Especifique a assinatura que você quer usar. 
   
        Select-AzureRmSubscription -SubscriptionName "Name of subscription"
2. Adicione sua Conta do Azure para usar os cmdlets do PowerShell clássicos. Para fazer isso, use o seguinte comando:
   
        Add-AzureAccount
3. Defina a chave compartilhada executando o exemplo a seguir. Neste exemplo, `-VNetName` é o nome da Rede Virtual clássica e `-LocalNetworkSiteName` é o nome que você especificou para a rede local quando configurou no portal clássico. O `-SharedKey` é um valor que você pode gerar e especificar. O valor especificado aqui deve ser o mesmo valor que você especifica na próxima etapa, ao criar sua conexão.
   
        Set-AzureVNetGatewayKey -VNetName ClassicVNet `
        -LocalNetworkSiteName RMVNetLocal -SharedKey abc123
4. Crie a conexão VPN executando os comandos a seguir:
   
    **Defina as variáveis**
   
        $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
        $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
   
    **Crie a conexão**<br> Observe que o `-ConnectionType` é 'IPsec', não 'Vnet2Vnet'. Neste exemplo, `-Name` é o nome que você deseja para sua conexão. O exemplo a seguir cria uma conexão chamada '*rm-to-classic-connection*'.
   
        New-AzureRmVirtualNetworkGatewayConnection -Name rm-to-classic-connection -ResourceGroupName RG1 `
        -Location "East US" -VirtualNetworkGateway1 `
        $vnet02gateway -LocalNetworkGateway2 `
        $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

## <a name="verify-your-connection"></a>Verificar a conexão
Você pode verificar a conexão usando o portal clássico, o Portal do Azure ou o PowerShell. Você pode usar as etapas a seguir para verificar a conexão. Substitua os valores pelos seus próprios.

[!INCLUDE [vpn-gateway-verify connection](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="<a-name="faq"></a>vnet-to-vnet-faq"></a><a name="faq"></a>Perguntas frequentes sobre Rede Virtual para Rede Virtual
Exiba os detalhes de perguntas frequentes para obter informações adicionais sobre conexões de Rede Virtual para Rede Virtual.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

<!--HONumber=Oct16_HO2-->


