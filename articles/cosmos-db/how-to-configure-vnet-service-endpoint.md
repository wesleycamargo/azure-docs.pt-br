---
title: Configurar acesso à rede virtual e à sub-rede para sua conta do Azure Cosmos DB
description: Este documento descreve as etapas necessárias para configurar um ponto de extremidade de serviço de rede virtual para o Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: 37cc7dcb3157459f6f6387323cc44d6f97954e92
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54035807"
---
# <a name="access-azure-cosmos-db-resources-from-virtual-networks"></a>Acessar Microsoft Azure Cosmos DB a partir de redes virtuais

Você pode configurar as contas de banco de dados do Microsoft Azure Cosmos DB para permitir o acesso apenas de uma sub-rede específica de uma rede virtual do Azure. Para limitar o acesso a uma conta do Azure Cosmos DB com conexões de uma sub-rede em uma rede virtual:
 
1. Habilite a sub-rede para enviar a identidade da sub-rede e da rede virtual para o Azure Cosmos DB. Você pode conseguir isso habilitando um ponto de extremidade de serviço para o Azure Cosmos DB na sub-rede específica.

1. Adicione uma regra na conta do Azure Cosmos DB para especificar a sub-rede como uma fonte da qual a conta pode ser acessada.

> [!NOTE]
> Quando um ponto de extremidade de serviço da sua conta do Azure Cosmos DB é habilitado em uma sub-rede, a origem do tráfego que chega ao Azure Cosmos DB muda de um IP público para uma rede virtual e uma sub-rede. A troca de tráfego se aplica a qualquer conta do Azure Cosmos DB acessada a partir dessa sub-rede. Se suas contas de banco de dados do Microsoft Azure Cosmos DB tiverem um firewall baseado em IP para permitir essa sub-rede, as solicitações da sub-rede habilitada para serviço não corresponderão mais às regras de firewall de IP e serão rejeitadas. 
>
> Para saber mais, consulte as etapas descritas na seção [Migrando de uma regra de firewall de IP para uma lista de controle de acesso à rede virtual](#migrate-from-firewall-to-vnet) deste artigo. 

As seções a seguir descrevem como configurar o ponto de extremidade do serviço VNET para uma conta do Azure Cosmos DB.

## <a id="configure-using-portal"></a>Configurar um ponto de extremidade de serviço usando o portal do Azure

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Configurar um ponto de extremidade de serviço para uma rede virtual e sub-rede do Azure existente

1. No blade **Todos os recursos**, localize a conta do Azure Cosmos DB que você deseja proteger.

1. Selecione **Firewalls e redes virtuais** no menu de configurações e opte por permitir o acesso a partir de **Redes selecionadas**.

1. Para conceder acesso à sub-rede de uma rede virtual existente, em **Redes virtuais**, selecione **Adicionar rede virtual existente do Azure**.

1. Selecione a **Assinatura** da qual você deseja adicionar uma rede virtual do Azure. Selecione as **Redes virtuais** do Azure e as **Sub-redes** que você deseja fornecer acesso à sua conta do Azure Cosmos DB. Em seguida, selecione **Habilitar** para habilitar as redes selecionadas com pontos de extremidade de serviço para "Microsoft.AzureCosmosDB". Quando estiver concluído, selecione **Adicionar**. 

   ![Selecionar rede virtual e sub-rede](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)


1. Depois que a conta do Azure Cosmos DB estiver habilitada para acesso de uma rede virtual, ela permitirá o tráfego somente dessa sub-rede escolhida. A rede virtual e a sub-rede que você adicionou devem aparecer como mostrado na captura de tela a seguir:

   ![Rede virtual e sub-rede configuradas com sucesso](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> Para habilitar terminais de serviço de rede virtual, você precisaria das seguintes permissões de assinatura:
  * Assinatura com rede virtual: Colaborador de rede
  * Assinatura com a conta do Azure Cosmos DB: Colaborador da Conta do DocumentDB

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Configurar o ponto de extremidade de serviço para uma rede virtual do Azure e uma sub-rede novas

1. No blade **Todos os recursos**, localize a conta do Azure Cosmos DB que você deseja proteger.  

1. Selecione **Firewalls e redes virtuais do Azure** no menu de configurações e escolha permitir o acesso a partir de **Redes selecionadas**.  

1. Para conceder acesso a uma nova rede virtual do Azure, em **Redes virtuais**, selecione **Adicionar nova rede virtual**.  

1. Forneça os detalhes necessários para criar uma nova rede virtual e, em seguida, selecione **Criar**. A sub-rede será criada com um ponto de extremidade de serviço para "Microsoft.AzureCosmosDB" habilitado.

   ![Selecione uma rede virtual e uma sub-rede para uma nova rede virtual](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Se sua conta do Microsoft Azure Cosmos DB for usada por outros serviços do Azure como o Azure Search ou for acessada a partir da análise de Fluxo ou do Power BI, você permitirá acesso selecionando **Aceitar conexões de datacenters públicos do Azure**.

Para garantir que você tenha acesso às métricas do Microsoft Azure Cosmos DB no portal, é necessário habilitar as opções **Permitir acesso do portal do Azure**. Para saber mais sobre essas opções, consulte o artigo [Configurar um firewall IP](how-to-configure-firewall.md). Depois de habilitar o acesso, selecione **salvar** para salvar as configurações.

## <a id="remove-vnet-or-subnet"></a>Remover uma rede virtual ou sub-rede 

1. No blade **Todos os recursos**, localize a conta do Microsoft Azure Cosmos DB para a qual você atribuiu pontos de extremidade de serviço.  

2. Para saber mais sobre essas opções, consulte o artigo **Configurar um firewall IP**.  

3. Para remover uma regra de rede virtual ou sub-rede, selecione **...** ao lado da rede virtual ou sub-rede e selecione **Remover**.

   ![Remover uma rede virtual](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

4.  Selecione **Salvar** para salvar suas alterações.

## <a id="configure-using-powershell"></a>Configurar endpoint de serviço usando o Azure PowerShell

> [!NOTE]
> Quando você estiver usando o PowerShell ou a CLI do Azure, especifique a lista completa de filtros IP e ACLs de rede virtual em parâmetros, não apenas os que precisam ser adicionados.

Use as etapas a seguir para configurar um ponto de extremidade de serviço para uma conta de banco de dados do Microsoft Azure Cosmos DB usando o Azure PowerShell:  

1. Instale o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) e [faça login em](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  

1. Habilite o ponto de extremidade de serviço para uma sub-rede existente de uma rede virtual.  

   ```powershell
   $rgname = "<Resource group name>"
   $vnName = "<Virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzureRmVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzureRmVirtualNetworkSubnetConfig `
    -Name $sname  `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzureRmVirtualNetwork
   ```

1. Obtenha informações de rede virtual.

   ```powershell
   $vnProp = Get-AzureRmVirtualNetwork `
     -Name $vnName `
     -ResourceGroupName $rgName
   ```

1. Obtenha as propriedades da conta do Azure Cosmos DB executando o seguinte cmdlet:  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Inicialize as variáveis para uso posterior. Configure todas as variáveis da definição de conta existente.

   ```powershell
   $locations = @()

   foreach ($readLocation in $cosmosDBConfiguration.Properties.readLocations) {
      $locations += , @{
         locationName     = $readLocation.locationName;
         failoverPriority = $readLocation.failoverPriority;
      }
   }

   $virtualNetworkRules = @(@{
      id = "$($vnProp.Id)/subnets/$sname";
   })

   if ($cosmosDBConfiguration.Properties.isVirtualNetworkFilterEnabled) {
      $virtualNetworkRules = $cosmosDBConfiguration.Properties.virtualNetworkRules + $virtualNetworkRules
   }
   ```

1. Atualize propriedades de conta do Azure Cosmos DB com a nova configuração executando os seguintes cmdlets: 

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName `
     -Properties $CosmosDBProperties
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

## <a id="configure-using-cli"></a> Configurar um ponto de extremidade de serviço usando a CLI do Azure 

1. Ative o ponto de extremidade de serviço para uma sub-rede em uma rede virtual.

1. Atualize a conta existente do Azure Cosmos DB com listas de controle de acesso (ACLs) de sub-rede.

   ```azurecli-interactive

   name="<Azure Cosmos DB account name>"
   resourceGroupName="<Resource group name>"

   az cosmosdb update \
    --name $name \
    --resource-group $resourceGroupName \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/frontend"
   ```

1. Crie uma nova conta do Azure Cosmos DB com ACLs de sub-rede.

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

## <a id="migrate-from-firewall-to-vnet"></a>Migrando de uma regra de firewall de IP para uma ACL de rede virtual 

Use as etapas a seguir apenas para contas de banco de dados do Microsoft Azure Cosmos DB com regras de firewall de IP existentes que permitam uma sub-rede, quando você quiser usar ACLs de rede virtual e de sub-rede em vez de uma regra de firewall de IP.

Depois que um ponto de extremidade de serviço para uma conta de banco de dados do Microsoft Azure Cosmos DB é ativado para uma sub-rede, as solicitações são enviadas com uma fonte que contém informações de rede virtual e de sub-rede em vez de um IP público. Essas solicitações não correspondem a um filtro IP. Essa alternância de origem acontece para todas as contas do Azure Cosmos DB acessadas da sub-rede com um ponto de extremidade de serviço habilitado. Para evitar o tempo de inatividade, use as seguintes etapas:

1. Obtenha as propriedades da conta do Azure Cosmos DB executando o seguinte cmdlet:

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Inicialize as variáveis para usá-las mais tarde. Configure todas as variáveis da definição de conta existente. Adicione a ACL da rede virtual a todas as contas do Azure Cosmos DB acessadas a partir da sub-rede com o sinalizador `ignoreMissingVNetServiceEndpoint`.

   ```powershell
   $locations = @()

   foreach ($readLocation in $cosmosDBConfiguration.Properties.readLocations) {
      $locations += , @{
         locationName     = $readLocation.locationName;
         failoverPriority = $readLocation.failoverPriority;
      }
   }

   $subnetID = "Subnet ARM URL" e.g "/subscriptions/f7ddba26-ab7b-4a36-a2fa-7d01778da30b/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/subnet1"

   $virtualNetworkRules = @(@{
      id = $subnetID;
      ignoreMissingVNetServiceEndpoint = "True";
   })

   if ($cosmosDBConfiguration.Properties.isVirtualNetworkFilterEnabled) {
      $virtualNetworkRules = $cosmosDBConfiguration.Properties.virtualNetworkRules + $virtualNetworkRules
   }
   ```

1. Atualize propriedades de conta do Azure Cosmos DB com a nova configuração executando os seguintes cmdlets:

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzureRmResource `
      -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
      -ApiVersion $apiVersion `
      -ResourceGroupName $rgName `
      -Name $acctName `
      -Properties $CosmosDBProperties
   ```

1. Repita as etapas de 1 a 3 para todas as contas de banco de dados do Microsoft Azure Cosmos DB que você acessa da sub-rede.

1.  Aguarde 15 minutos e atualize a sub-rede para ativar o terminal em serviço.

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

1. Remova a regra de firewall IP da sub-rede.

## <a name="next-steps"></a>Próximas etapas

* Para configurar um firewall para o Azure Cosmos DB, consulte o artigo [Suporte ao firewall](firewall-support.md).
