---
title: Como configurar o acesso baseado em rede virtual e sub-rede para sua conta do Azure Cosmos
description: Este documento descreve as etapas necessárias para configurar o ponto de extremidade de serviço de rede virtual do Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: a7c2d1e41fa4ac26854e2e6ab57184cd6ed0bd0c
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51633675"
---
# <a name="how-to-access-azure-cosmos-db-resources-from-virtual-networks"></a>Como acessar os recursos do banco de dados do Microsoft Azure Cosmos DB de redes virtuais

As contas do Azure CosmosDB podem ser configuradas para permitir o acesso somente da sub-rede específica da Rede Virtual do Azure. Há duas etapas necessárias para limitar o acesso à conta do Azure Cosmos com conexões de uma sub-rede em uma rede virtual (VNET).
 
1. Habilite a sub-rede para enviar a sub-rede e a identidade VNET para o Azure Cosmos DB. Você pode conseguir isso ativando o ponto de extremidade de serviço do Azure Cosmos DB na sub-rede específica.

1. Adicione uma regra na conta do Azure Cosmos especificando a sub-rede como uma fonte da qual a conta pode ser acessada.

> [!NOTE]
> Quando o endpoint de serviço da sua conta do Azure Cosmos é habilitado em uma sub-rede, a origem do tráfego que chega ao Azure Cosmos DB muda de IP público para VNET e sub-rede. A troca de tráfego se aplica a qualquer conta do Azure Cosmos acessada a partir dessa sub-rede. Se sua conta do Azure Cosmos tiver firewall baseado em IP para permitir essa sub-rede, as solicitações da sub-rede habilitada para serviço não corresponderão mais às regras de firewall de IP e serão rejeitadas. Para saber mais, consulte as etapas descritas na seção [migração da regra de firewall de IP para a lista de Serviço de controle de acesso do Microsoft Azure AD](#migrate-from-firewall-to-vnet) deste artigo. 

As seções a seguir descrevem como configurar o ponto de extremidade do serviço VNET para uma conta do Azure Cosmos DB.

## <a id="configure-using-portal"></a>Configurar o ponto de extremidade de serviço por meio do portal do Azure

### <a name="configure-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Configurar o ponto de extremidade de serviço para uma rede virtual do Azure existente e a sub-rede

1. Na folha **Todos os recursos**, localize a conta do Azure Cosmos que deseja proteger.

1. Selecione **Firewalls e redes virtuais** no menu Configurações e escolha Permitir acesso em **Redes selecionadas**.

1. Para conceder acesso à sub-rede de uma rede virtual existente, em **Redes virtuais**, selecione **Adicionar rede virtual existente do Azure**.

1. Selecione a **Assinatura** da qual você deseja adicionar a rede virtual do Azure. Selecione as **Redes virtuais** do Azure e as **sub-redes** que você deseja fornecer acesso à sua conta do Azure Cosmos. Em seguida, selecione **Habilitar** para habilitar as redes selecionadas com pontos de extremidade de serviço para "Microsoft.AzureCosmosDB". Quando estiver concluído, selecione **Adicionar**. 

   ![Selecionar rede virtual e sub-rede](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)


1. Depois que a conta do Azure Cosmos estiver habilitada para acessar a partir de uma rede virtual, ela só permitirá o tráfego dessa sub-rede escolhida. A rede virtual e a sub-rede que você adicionou devem aparecer como mostrado na captura de tela a seguir:

   ![rede virtual e sub-rede configuradas com êxito](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> Para habilitar terminais de serviço de rede virtual, você precisaria das seguintes permissões de assinatura:
  * Assinatura com a rede virtual: Colaborador de rede
  * Assinatura com a conta do Azure Cosmos: DocumentDB Account Contributor

### <a name="configure-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Configurar o ponto de extremidade de serviço para uma rede virtual do Azure e uma sub-rede novas

1. Na folha **Todos os recursos**, localize a conta do Azure Cosmos DB que você deseja proteger.  

2. Antes de habilitar o ponto de extremidade de serviço de rede virtual, copie as informações de firewall IP associadas à sua conta do Azure Cosmos DB para uso futuro. Você pode habilitar o firewall IP novamente depois de configurar o ponto de extremidade de serviço.  

3. Selecione **Firewalls e redes virtuais do Azure** no menu Configurações e escolha Permitir acesso em **Redes selecionadas**.  

4. Para conceder acesso a uma rede virtual do Azure novo, em redes virtuais, selecione **Adicionar nova rede virtual**.  

5. Forneça os detalhes necessários para criar uma nova rede virtual e, em seguida, selecione Criar. A sub-rede será criada com um ponto de extremidade de serviço para "Microsoft.AzureCosmosDB" habilitado.

   ![Selecionar a rede virtual e a sub-rede para a nova rede virtual](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Se a sua conta do Azure Cosmos for usada por outros serviços do Azure como o Azure Search ou acessada a partir da análise de Fluxo ou do Power BI, você permitirá o acesso marcando Permitir acesso aos Serviços do Azure.

Para garantir que você tenha acesso às métricas do Azure Cosmos DB pelo portal, é preciso habilitar as opções de **Permitir acesso ao portal do Azure**. Para obter mais informações sobre essas opções, consulte as solicitações do portal do Azure e as seções de solicitação de serviços do PaaS do Azure do artigo [firewall IP](how-to-configure-firewall.md) do configure. Depois de selecionar o acesso, selecione **Salvar** para salvar as configurações.

## <a id="remove-vnet-or-subnet"></a>Remover uma rede virtual ou sub-rede 

1. Na folha **Todos os recursos**, localize a conta do Azure Cosmos DB para a qual você atribuiu pontos de extremidade de serviço.  

2. Selecione **Firewalls e redes virtuais** no menu de configurações.  

3. Para remover uma regra de sub-rede ou rede virtual, selecione "..." ao lado da rede virtual ou da sub-rede e selecione **Remover**.

   ![Remover uma rede virtual](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

4.  Clique em **Salvar** para aplicar suas alterações.

## <a id="configure-using-powershell"></a>Configurar endpoint de serviço usando o Azure PowerShell 

Use as seguintes etapas para configurar o ponto de extremidade de serviço para uma conta do Azure Cosmos DB usando o Azure PowerShell:  

1. Instale o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) mais recente e [faça logon](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  

1. Habilite o ponto de extremidade de serviço para uma sub-rede existente de uma rede virtual.  

   ```powershell
   $rgname= "<Resource group name>"
   $vnName = "<virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzureRmVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzureRmVirtualNetworkSubnetConfig `
    -Name $sname  `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzureRmVirtualNetwork
   ```

1. Prepare-se para a ativação da ACL na conta do Azure Cosmos, certificando-se de que a rede virtual e a sub-rede tenham o ponto de extremidade de serviço ativado para sua conta.

   ```powershell
   $vnProp = Get-AzureRmVirtualNetwork `
     -Name $vnName  -ResourceGroupName $rgName
   ```

1. Obtenha as propriedades de conta do Azure Cosmos DB executando o seguinte cmdlet:  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzureRmResource -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Inicialize as variáveis para uso posterior. Configure todas as variáveis a partir da definição de conta existente. Se você tiver vários locais, será preciso adicioná-los como parte da matriz. Nesta etapa, você também deve configurar o ponto de extremidade de serviço de rede virtual configurando a variável "accountVNETFilterEnabled" para "True". Posteriormente, esse valor é atribuído ao parâmetro "isVirtualNetworkFilterEnabled". 

   ```powershell
   $locations = @(@{})

   <# If you have read regions in addition to a write region, use the following code to set the $locations variable instead.

   $locations = @(@{"locationName"="<Write location>"; 
                 "failoverPriority"=0}, 
               @{"locationName"="<Read location>"; 
                  "failoverPriority"=1}) #>

   $consistencyPolicy = @{}
   $cosmosDBProperties = @{}

   $locations[0]['failoverPriority'] = $cosmosDBConfiguration.Properties.failoverPolicies.failoverPriority
   $locations[0]['locationName'] = $cosmosDBConfiguration.Properties.failoverPolicies.locationName

   $consistencyPolicy = $cosmosDBConfiguration.Properties.consistencyPolicy

   $accountVNETFilterEnabled = $True
   $subnetID = $vnProp.Id+"/subnets/" + $sname  
   $virtualNetworkRules = @(@{"id"=$subnetID})
   $databaseAccountOfferType = $cosmosDBConfiguration.Properties.databaseAccountOfferType
   ```

1. Atualize propriedades de conta do Azure Cosmos DB com a nova configuração executando os seguintes cmdlets: 

   ```powershell
   $cosmosDBProperties['databaseAccountOfferType'] = $databaseAccountOfferType
   $cosmosDBProperties['locations'] = $locations
   $cosmosDBProperties['consistencyPolicy'] = $consistencyPolicy
   $cosmosDBProperties['virtualNetworkRules'] = $virtualNetworkRules
   $cosmosDBProperties['isVirtualNetworkFilterEnabled'] = $accountVNETFilterEnabled

   Set-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName -Properties $CosmosDBProperties
   ```

1. Execute o seguinte comando para verificar se sua conta do Azure Cosmos DB está atualizada com o ponto de extremidade de serviço de rede virtual que você configurou na etapa anterior:

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a id="configure-using-cli"></a>Configurar ponto de extremidade de serviço usando a CLI do Azure 

1. Ative o ponto de extremidade de serviço para uma sub-rede em uma rede virtual.

1. Atualizar conta existente do Azure Cosmos com ACLs de sub-rede

   ```azurecli-interactive

   name="<Azure Cosmos account name>"
   resourceGroupName="<Resource group name>"

   az cosmosdb update \
      --name $name \
    --resource-group $resourceGroupName \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/frontend"
   ```

1. Criar uma nova conta do Azure Cosmos com ACLs de sub-rede

   ```azurecli-interactive
   az cosmosdb create \
    --name $name \
    --kind GlobalDocumentDB \
    --resource-group $resourceGroupName \
    --max-interval 10 \
    --max-staleness-prefix 200 \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/default"
   ```

## <a id="migrate-from-firewall-to-vnet"></a>Migrando da regra de firewall IP a ACL de VNET 

As etapas a seguir são necessárias apenas para contas do Azure Cosmos com regras de firewall IP existentes que permitem uma sub-rede e você deseja usar as ACLs baseadas em sub-rede e VNET em vez da regra de firewall IP.

Quando a extremidade do serviço para a conta do Azure Cosmos está ativada para uma sub-rede, as solicitações são enviadas com a fonte que contém informações de VNET e de sub-rede, em vez de IP público. Portanto, essas solicitações não correspondem a um filtro IP. Essa alternância de origem acontece para todas as contas do Azure Cosmos acessadas a partir da sub-rede com o ponto de extremidade de serviço habilitado. Para evitar o tempo de inatividade, use as seguintes etapas:

1. Obter propriedades de conta do Cosmos do Azure executando o seguinte cmdlet:

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Cosmos account name>"

   $cosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Inicialize as variáveis para usá-las mais tarde. Configure todas as variáveis da definição de conta existente. Adicione a ACL VNET a todas as contas do Azure Cosmos acessadas a partir da sub-rede com o sinalizador `ignoreMissingVNetServiceEndpoint`.  

   Se você tiver vários locais, precisará adicioná-los como parte da matriz. Nesta etapa, você também deve configurar o ponto de extremidade de serviço de rede virtual configurando a variável "accountVNETFilterEnabled" para "True". Posteriormente, esse valor é atribuído ao parâmetro "isVirtualNetworkFilterEnabled".

   ```powershell
   $locations = @(@{})

   <# If you have read regions in addition to a write region, use the following code to set the $locations variable instead.

   $locations = @(@{"locationName"="<Write location>"; 
              "failoverPriority"=0}, 
            @{"locationName"="<Read location>"; 
               "failoverPriority"=1}) #>

   $consistencyPolicy = @{}
   $cosmosDBProperties = @{}

   $locations[0]['failoverPriority'] = $cosmosDBConfiguration.Properties.failoverPolicies.failoverPriority
   $locations[0]['locationName'] = $cosmosDBConfiguration.Properties.failoverPolicies.locationName
   $consistencyPolicy = $cosmosDBConfiguration.Properties.consistencyPolicy
   $accountVNETFilterEnabled = $True
   $subnetID = “Subnet ARM URL” e.g "/subscriptions/f7ddba26-ab7b-4a36-a2fa-7d01778da30b/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/subnet1"

   $virtualNetworkRules = @(@{"id"=$subnetID, "ignoreMissingVNetServiceEndpoint"="True"})
   $databaseAccountOfferType = $cosmosDBConfiguration.Properties.databaseAccountOfferType
   ```

1. Atualize as propriedades da conta do Azure Cosmos com a nova configuração executando os seguintes cmdlets:

   ```powershell
   $cosmosDBProperties['databaseAccountOfferType'] = $databaseAccountOfferType
   $cosmosDBProperties['locations'] = $locations
   $cosmosDBProperties['consistencyPolicy'] = $consistencyPolicy
   $cosmosDBProperties['virtualNetworkRules'] = $virtualNetworkRules
   $cosmosDBProperties['isVirtualNetworkFilterEnabled'] = $accountVNETFilterEnabled

   Set-AzureRmResource `
    -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
    -ApiVersion $apiVersion `
    -ResourceGroupName $rgName `
    -Name $acctName -Properties $CosmosDBProperties
   ```

1. Repita as etapas de 1 a 3 para todas as contas do Azure Cosmos acessadas a partir da sub-rede.

1.  Aguarde de 15 minutos e, em seguida, atualize a sub-rede para habilitar o ponto de extremidade de serviço.

1.  Habilite o ponto de extremidade de serviço para uma sub-rede existente de uma rede virtual.

   ```powershell
   $rgname= "<Resource group name>"
   $vnName = "<virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzureRmVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzureRmVirtualNetworkSubnetConfig `
    -Name $sname `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzureRmVirtualNetwork
   ```

1. Remova regra de firewall IP para a sub-rede.

## <a name="next-steps"></a>Próximas etapas

* Para configurar um firewall para o Azure Cosmos DB, consulte o artigo [suporte de firewall](firewall-support.md).

