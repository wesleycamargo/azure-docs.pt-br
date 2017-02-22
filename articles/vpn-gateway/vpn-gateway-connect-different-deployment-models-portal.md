---
title: "Conectar redes virtuais clássicas a VNets do Azure Resource Manager: Portal | Microsoft Docs"
description: "Saiba como criar uma conexão VPN entre Rede Virtuais clássicas e Rede Virtuais do Resource Manager usando o Gateway de VPN e o portal"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 5a90498c-4520-4bd3-a833-ad85924ecaf9
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/17/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 10985eaebd9148ef4fbd22eac0ba50076ca71ef5
ms.openlocfilehash: c20d72f25571e582310ae65ad1d887dbdb7b011c


---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>Conectar redes virtuais de diferentes modelos de implantação usando o portal
> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Atualmente, o Azure tem dois modelos de gerenciamento: o clássico e o RM (Resource Manager). Se você já usa o Azure há algum tempo, provavelmente terá as VMs do Azure e as funções de instância em execução em uma Rede Virtual clássica. Suas VMs e instâncias de função mais recentes podem estar em execução em uma Rede Virtual criada no Resource Manager. Este artigo explica como conectar redes virtuais clássicas a redes virtuais do Resource Manager para permitir que os recursos localizados nos modelos de implantação separados se comuniquem entre si usando uma conexão de gateway. 

Você pode criar uma conexão entre redes virtuais em assinaturas e regiões diferentes. Você também pode conectar redes virtuais que já têm conexões com redes locais, desde que o gateway com o qual foram configuradas seja dinâmico ou baseado em rota. Para saber mais sobre conexões de Rede Virtual para Rede Virtual, confira a seção [Considerações sobre Rede Virtual para Rede Virtual](#faq) no final deste artigo.

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Modelos de implantação e métodos para conexões de VNet a VNet
[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Atualizamos a tabela a seguir conforme os novos artigos, e ferramentas adicionais ficam disponíveis para esta configuração. Quando um artigo estiver disponível, o vincularemos diretamente da tabela.<br><br>

**Rede Virtual a Rede Virtual**

[!INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

**Emparelhamento VNet**

[!INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="a-namebeforeabefore-beginning"></a><a name="before"></a>Antes de começar
As etapas a seguir mostrarão as configurações necessárias para configurar um gateway dinâmico ou baseado em rota para cada Rede Virtual, e para criar uma conexão VPN entre os gateways. Essa configuração não dá suporte a gateways estáticos ou baseados em política. 

Neste artigo, usaremos o portal do Azure e o PowerShell. O PowerShell é necessário para criar conexões entre as redes virtuais. Você não pode criar conexões para essa configuração por meio do portal.

### <a name="prerequisites"></a>Pré-requisitos
* Ambas as redes virtuais já foram criadas.
* Os intervalos de endereços das redes virtuais não se sobrepõem entre eles ou aos intervalos de outras conexões às quais os gateways podem estar conectados.
* Você instalou os últimos cmdlets do PowerShell. Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) para obter mais informações. Instale os dois cmdlets do Resource Manager (RM) e do Gerenciamento de Serviços (SM). 

### <a name="a-namevaluesaexample-settings"></a><a name="values"></a>Configurações de exemplo
Você pode usar as configurações de exemplo como referência.

**Configurações da Rede Virtual clássica**

Nome da rede virtual = ClassicVNet  <br>
Espaço de endereço = 10.0.0.0/24 <br>
Subnet-1 = 10.0.0.0/27 <br>
Grupo de Recursos = ClassicRG <br>
Local = Oeste dos EUA <br>
GatewaySubnet = 10.0.0.32/28 <br>
Site local = RMVNetLocal <br>

**Configurações de Rede Virtual do Resource Manager**

Nome da VNet = RMVNet  <br>
Espaço de endereço = 192.168.0.0/16 <br>
Subnet-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Grupo de recursos = RG1 <br>
Local = Leste dos EUA <br>
Nome do gateway de rede virtual = RMGateway <br>
Tipo de gateway = VPN <br>
Tipo de VPN = baseada em rota <br>
Nome do IP Público do Gateway = gwpip <br>
Gateway de rede local = ClassicVNetLocal <br>

## <a name="a-namecreatesmgwasection-1-configure-classic-vnet-settings"></a><a name="createsmgw"></a>Seção 1: Definir configurações de Rede Virtual clássica
Nesta seção, vamos criar o local de rede (site local) e o gateway de rede virtual para sua rede virtual clássica. As instruções nesta seção usam o portal do Azure. As etapas aqui supõem que um gateway de VPN não foi criado para a rede virtual clássica. Se você já tiver um gateway, verifique se ele é um gateway dinâmico. Se ele for estático, primeiro você deve excluir o gateway de VPN e prosseguir com as etapas a seguir.

### <a name="part-1---configure-the-local-site"></a>Parte 1: configurar o site local

Abra o [portal do Azure](https://ms.portal.azure.com) e entre com sua conta do Azure.

1. Navegue até **todos os recursos** e localize a rede virtual clássica.
2. No **visão geral** folha, no **conexões VPN** seção, clique o **Gateway** gráfico para criar um gateway.

    ![Configurar um gateway de VPN](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "Configurar um gateway de VPN")
3. Na folha **Nova Conexão VPN**, para **Tipo de Conexão**, selecione **Site para site**.
4. Em **Site local**, clique em **Definir configurações obrigatórias**. Isso abrirá a folha **Site local**.
5. Na folha **Site local**, crie um nome que será usado para referir-se à Rede Virtual do Resource Manager. Por exemplo, RMVNetLocal.
6. Se o gateway de VPN para o Resource Manager de VNet já tem um endereço IP público, use o valor para o **endereço IP do gateway de VPN** campo. Se ainda não tiver criado um gateway de rede virtual para a VNet do Resource Manager, você pode usar um endereço IP de espaço reservado. Certifique-se de que o endereço IP de espaço reservado usa um formato válido. Posteriormente, você deve substituir o endereço IP de espaço reservado com o endereço IP público do gateway de rede virtual do Resource Manager.
7. Para **espaço de endereço de cliente**, use os valores para os espaços de endereço IP de rede virtual para o VNet do Resource Manager. Essa configuração é usada para especificar os espaços de endereço para rotear até a Rede Virtual RM.
8. Clique em **OK** para salvar os valores e retornar para o **nova conexão VPN** folha.

### <a name="part-2---create-the-virtual-network-gateway"></a>Parte 2 - Criar o gateway de rede virtual
1. Sobre o **nova conexão VPN** folha, selecione o **criar gateway imediatamente** caixa de seleção e clique em **configuração de gateway opcional** para abrir o **configuração de Gateway** folha. 

    ![Folha de configuração do gateway abra](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "folha de configuração de gateway aberto")
2. Clique em **Subnet - configurar as configurações necessárias** para abrir o **Adicionar sub-rede** folha. Nessa folha, você verá que o nome já está configurado com o valor necessário **GatewaySubnet**.
3. O **intervalo de endereços** refere-se ao intervalo para a sub-rede de gateway. Embora você possa criar uma sub-rede de gateway com uma/29 (3 endereços), intervalo de endereços é recomendável criar uma sub-rede de gateway que contém IP disponíveis mais endereços para acomodar as possíveis configurações futuras que exigem endereços IP disponíveis mais. Se possível, use/27 ou /28. Clique em **OK** para criar a sub-rede de gateway.
4. Sobre o **configuração de Gateway** folha, **tamanho** refere-se ao gateway SKU. Selecione o SKU de gateway do seu gateway de VPN.
5. Verifique se o **tipo de roteamento** é **dinâmico**, em seguida, clique em **OK** para retornar ao **nova conexão VPN** folha.
6. Sobre o **nova conexão VPN** folha, clique em **OK** para começar a criar seu gateway de VPN. Isso pode demorar até 45 minutos para ser concluído.


### <a name="a-nameipapart-3---copy-the-virtual-network-gateway-public-ip-address"></a><a name="ip"></a>Parte 3 - copie o endereço IP público do gateway de rede virtual
Após a criação do gateway de rede virtual, você pode exibir o endereço IP do gateway. 

1. Navegue até sua rede virtual clássica e clique em **visão geral**.
2. Clique em **conexões VPN** para abrir a folha de conexões VPN. Na folha conexões VPN, você pode exibir o endereço IP público. Esse é o endereço IP público atribuído ao seu gateway de rede virtual. Anote ou copie o endereço IP. Você irá usá-lo em etapas posteriores quando você trabalha com suas configurações do Resource Manager locais de rede gateway. Você também pode exibir o status das conexões do gateway. Observe que o site da rede local criado é listado como 'Conexão'. O status será alterado depois de ter criado as conexões. Feche a folha depois de copiar o endereço IP do gateway.

## <a name="a-namecreatermgwasection-2-configure-resource-manager-vnet-settings"></a><a name="creatermgw"></a>Seção 2: Definir as configurações da Rede Virtual do Resource Manager
Nesta seção, criaremos o gateway de Rede Virtual e a rede local para a Rede Virtual do Resource Manager. Capturas de tela são fornecidas como exemplos. Substitua os valores pelos seus próprios. Se você estiver criando esta configuração como um exercício, consulte esses [valores](#values).

### <a name="part-1---create-a-gateway-subnet"></a>Parte 1 - Criar uma sub-rede de gateway
Antes de criar um gateway de rede virtual, você precisa primeiro criar a sub-rede de gateway. Criar uma sub-rede de gateway com a contagem CIDR de /28 ou maior. (/&27;, /&26;, etc.)

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

Em um navegador, acesse o [Portal do Azure](http://portal.azure.com) e entre com sua conta do Azure.

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="part-2---create-a-virtual-network-gateway"></a>Parte 2 - Criar um gateway de Rede Virtual
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

Criar um gateway de rede virtual pode levar até 45 minutos. Você pode esperar para a criação de gateway de rede virtual do Resource Manager concluir, ou você pode prosseguir para [parte 3 - criar um gateway de rede local](#createlng). Quando o gateway tiver sido criado, ele é atribuído um endereço IP público. Em etapas posteriores, esse endereço IP é usado para substituir as espaço reservado para informações de endereço IP para as configurações de site local de rede virtual clássicas criado na seção 1. 

### <a name="a-namecreatelngapart-3---create-a-local-network-gateway"></a><a name="createlng"></a>Parte 3 - Criar um gateway de rede local

O gateway de rede local Especifica o intervalo de endereços e o endereço IP público associado à sua rede virtual clássica e seu gateway de rede virtual.

- Use o endereço IP público atribuído ao seu gateway de Rede Virtual clássica na [seção anterior](#ip). 
- Dê um nome ao gateway de rede local ao qual o Azure possa fazer referência. Por exemplo, 'ClassicVNetLocal'.
- Use o espaço de endereço que você atribuiu à sua rede virtual clássica (não apenas a sub-rede).

[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]


## <a name="section-3-modify-the-classic-vnet-local-site-settings"></a>Seção 3: Modificar as configurações de site local de rede virtual clássicas

Nesta seção, você irá trabalhar com a rede virtual clássica. Você substituirá o endereço IP de espaço reservado que você usou ao especificar as configurações de site local. Esta seção usa os cmdlets do PowerShell (SM) clássicos. Se você não o fez ainda, exibir o gateway de rede virtual para o Resource Manager de VNet e copie o endereço IP público. Esse é o endereço IP que você usará para substituir o endereço IP de espaço reservado.

Verifique se você baixou a versão mais recente desses cmdlets, como especificado no [antes de começar](#before) seção.

1. No portal do Azure, navegue até a rede virtual clássica.
2. Na folha de sua rede virtual, clique em **visão geral**.
3. No **conexões VPN** seção, clique no nome do seu site local no gráfico.

    ![Conexões VPN](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "conexões VPN")
4. Sobre o **conexões VPN Site a site** folha, clique no nome do site.

    ![Nome do site](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "nome do site Local")
5. Na folha de conexão para seu site local, clique no nome do site local para abrir o **site Local** folha.

    ![Abrir local-site](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "Abrir site local")
6. Sobre o **site Local** folha, substitua o endereço IP do gateway de VPN com o endereço IP do gateway do Resource Manager.

    ![Endereço de ip do gateway](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "endereço IP do Gateway")
7. Clique em **OK** para atualizar o endereço IP.


## <a name="a-nameconnectasection-4-create-the-connection"></a><a name="connect"></a>Seção 4: Criar a conexão
Nesta seção, você criará a conexão entre as Redes Virtuais. Essas etapas exigem o PowerShell. Você não pode criar esta conexão em nenhum dos dois portais. Baixe e instale os cmdlets do PowerShell clássicos (SM) e do Resource Manager (RM).

### <a name="part-1---log-in-to-your-azure-account"></a>Parte 1 - Fazer logon em sua conta do Azure

1. Abra o console do PowerShell com direitos elevados e faça logon em sua conta do Azure. O cmdlet a seguir solicita as credenciais de logon para sua conta do Azure. Depois de entrar, as configurações da conta são baixadas para que estejam disponíveis para o Azure PowerShell.
   
        Login-AzureRmAccount 
   
2. Obtenha uma lista de suas assinaturas do Azure, se você tiver mais de uma assinatura.
   
        Get-AzureRmSubscription
   
3. Especifique a assinatura que você quer usar. 
   
        Select-AzureRmSubscription -SubscriptionName "Name of subscription"

4. Adicione sua Conta do Azure para usar os cmdlets do PowerShell clássicos. Para fazer isso, use o seguinte comando:
   
        Add-AzureAccount

### <a name="part-2---download-your-network-configuration-file"></a>Parte 2 - Baixar o arquivo de configuração de rede

Às vezes, os nomes para sites de rede Local e redes virtuais clássicas são alterados no arquivo de configuração de rede durante a criação de configurações da rede virtual clássicas no portal do Azure devido a diferenças nos modelos de implantação. Por exemplo, estamos usando o portal do Azure, denominada a rede virtual clássica 'Rede virtual clássica' e criado em um grupo de recursos denominado 'ClassicRG'. O nome que está contido no arquivo de configuração de rede é convertido em 'Grupo ClassicRG rede virtual clássica'. Ao especificar o nome de uma rede virtual que contenha espaços, use aspas ao redor do valor.

1. Baixe o arquivo de configuração de rede do Azure executando o comando a seguir. Você pode alterar o local do arquivo a ser exportado para um local diferente, se necessário.
   
        Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
3. Use um editor de texto, como o bloco de notas, abra o arquivo. XML e exibir o conteúdo. Verifique se os valores para os nomes da rede virtual clássica e de rede local.

### <a name="part-3---set-the-shared-key"></a>Parte 3 - defina a chave compartilhada

Defina a chave compartilhada para a conexão de rede virtual clássica na VNet do Resource Manager. Ao usar esses cmdlets, certifique-se de verificar os valores de `-VNetName` e `-LocalNetworkSiteName` usando o arquivo de configuração de rede. Ao especificar nomes que contiver espaços, use aspas ao redor do valor. 

- Neste exemplo, `-VNetName` é o nome da rede virtual clássica. 
- `-LocalNetworkSiteName`é o nome especificado para o site local.
- O `-SharedKey` é um valor que você pode gerar e especificar. Neste exemplo, usamos *abc123*, mas você pode gerar algo mais complexo. O importante é que o valor especificado aqui deve ser o mesmo valor que você especifica na próxima etapa, ao criar sua conexão.

No console do PowerShell, defina a chave compartilhada executando o exemplo a seguir, certificando-se de substituir os valores pelos seus próprios. O retorno para o exemplo deve mostrar **Status: êxito**.
   
        Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
        -LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
### <a name="part-4---create-the-vpn-connection-by-running-the-following-commands"></a>Parte 4 - Criar a conexão VPN executando os comandos a seguir:
   
1. Defina as variáveis.
   
        $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
        $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
   
2. Crie a conexão. Neste exemplo, `-Name` é o nome que você deseja nomear sua conexão, não seja algo que você criou. O exemplo a seguir cria uma conexão denominada 'RM clássico'. Observe que o `-ConnectionType` é 'IPsec' não 'Vnet2Vnet' e que o `-SharedKey` corresponde à chave que você definiu anteriormente.
   
        New-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
        -Location "East US" -VirtualNetworkGateway1 `
        $vnet02gateway -LocalNetworkGateway2 `
        $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

## <a name="section-5-verify-your-connections"></a>Seção 5: Verificar suas conexões
Você pode verificar a conexão usando o portal do Azure ou o PowerShell. Ao verificar, talvez seja necessário aguardar um minuto ou dois como a conexão está sendo criado. Quando uma conexão for bem-sucedida, o estado de conectividade será alterado de 'Conexão' para 'Conectado'.

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Para verificar a conexão de sua rede virtual clássica para sua VNet do Resource Manager

####<a name="verify-your-connection-using-powershell"></a>Como verificar sua conexão usando o PowerShell


[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

####<a name="verify-your-connection-using-the-azure-portal"></a>Verificar sua conexão usando o Portal do Azure

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


###<a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Para verificar a conexão de seu VNet do Resource Manager para sua rede virtual clássica

####<a name="verify-your-connection-using-powershell"></a>Como verificar sua conexão usando o PowerShell

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

####<a name="verify-your-connection-using-the-azure-portal"></a>Verificar sua conexão usando o portal do Azure

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="a-namefaqavnet-to-vnet-considerations"></a><a name="faq"></a>Considerações sobre Rede Virtual para Rede Virtual

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]




<!--HONumber=Jan17_HO4-->


