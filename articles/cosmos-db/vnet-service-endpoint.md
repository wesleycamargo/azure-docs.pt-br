---
title: Proteger o acesso a uma conta do Azure Cosmos DB usando o ponto de extremidade de serviço da Rede Virtual do Azure | Microsoft Docs
description: Este documento descreve as etapas necessárias para configurar o ponto de extremidade de serviço de rede virtual do Azure Cosmos DB.
services: cosmos-db
author: kanshiG
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: govindk
ms.openlocfilehash: de52521824c146f63fb16e2690e2a24167ae2efe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "36333905"
---
# <a name="secure-access-to-an-azure-cosmos-db-account-by-using-azure-virtual-network-service-endpoint"></a>Proteger o acesso a uma conta do Azure Cosmos DB usando o ponto de extremidade de serviço da Rede Virtual do Azure

As contas do Azure CosmosDB podem ser configuradas para permitir o acesso somente da sub-rede específica da Rede Virtual do Azure. Habilitando um [Ponto de Extremidade de Serviço](../virtual-network/virtual-network-service-endpoints-overview.md) para o Azure CosmosDB a partir de uma Rede Virtual e sua sub-rede, o tráfego tem a garantia de uma rota ideal e segura para o Azure Cosmos DB.  

O Azure Cosmos DB é um serviço de multimodelo de banco de dados distribuído globalmente. Você pode replicar os dados presentes na conta do Azure Cosmos DB para várias regiões. Quando o Azure Cosmos DB é configurado com um ponto de extremidade de serviço de rede virtual, cada rede virtual permite o acesso de IPs que pertencem a uma sub-rede específica. A imagem a seguir mostra uma ilustração de um Azure Cosmos DB que tem o ponto de extremidade de serviço de rede virtual habilitado:

![arquitetura do ponto de extremidade de serviço de rede virtual](./media/vnet-service-endpoint/vnet-service-endpoint-architecture.png)

Depois que uma conta do Azure Cosmos DB é configurada com um ponto de extremidade de serviço de rede virtual, ela pode ser acessada somente por meio da sub-rede especificada, todo o acesso público/internet é removido. Para saber os detalhes sobre pontos de extremidade de serviço, consulte o artigo [Visão geral de pontos de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md) do Azure.

> [!NOTE]
> Atualmente, os pontos de extremidade de serviço de Rede Virtual podem ser configurados para a API do SQL do Azure Cosmos DB ou para as contas da API Mongo. A capacidade de configurar pontos de extremidade de serviço para outras APIs e nuvens soberanas, como Azure Alemanha ou Azure Governamental, estará disponível em breve. Se você tiver um firewall IP existente configurado para a conta do Azure Cosmos DB, observe a configuração do firewall, remova o firewall IP e, em seguida, configure a ACL do ponto de extremidade de serviço. Depois de configurar o ponto de extremidade de serviço, você poderá habilitar novamente o firewall IP, se necessário.

## <a name="configure-service-endpoint-by-using-azure-portal"></a>Configurar o ponto de extremidade de serviço usando o portal do Azure
### <a name="configure-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Configurar o ponto de extremidade de serviço para uma rede virtual do Azure existente e a sub-rede

1. Na folha **Todos os recursos**, localize a rede virtual na qual você deseja configurar o ponto de extremidade de serviço para o Azure Cosmos DB. Navegue até a folha **Pontos de extremidade de serviço** e certifique-se de que a sub-rede da rede virtual tenha sido habilitada para o ponto de extremidade de serviço "Azure.CosmosDB".  

   ![Confirmar ponto de extremidade de serviço habilitado](./media/vnet-service-endpoint/confirm-service-endpoint-enabled.png)

2. Na folha **Todos os recursos**, localize a conta do Azure Cosmos DB que você deseja proteger.  

3. Antes de habilitar o ponto de extremidade de serviço de rede virtual, copie as informações de firewall IP associadas à sua conta do Azure Cosmos DB para uso futuro. Você pode habilitar o firewall IP novamente depois de configurar o ponto de extremidade de serviço.  

4. Selecione **Firewalls e redes virtuais** no menu Configurações e escolha Permitir acesso em **Redes selecionadas**.  

3. Para conceder acesso a uma sub-rede da rede virtual existente, em Redes virtuais, selecione **Adicionar rede virtual do Azure existente**.  

4. Selecione a **Assinatura** da qual você deseja adicionar a rede virtual do Azure. Selecione as **Redes virtuais** e as **Sub-redes** do Azure às quais você deseja fornecer acesso à sua conta do Azure Cosmos DB. Em seguida, selecione **Habilitar** para habilitar as redes selecionadas com pontos de extremidade de serviço para "Microsoft.AzureCosmosDB". Quando estiver concluído, selecione **Adicionar**.  

   ![Selecionar rede virtual e sub-rede](./media/vnet-service-endpoint/choose-subnet-and-vnet.png)

   > [!NOTE]
   > Se o ponto de extremidade de serviço do Azure Cosmos DB configurado anteriormente não estiver configurado para as redes virtuais e as sub-redes do Azure, ele poderá ser configurado como parte dessa operação. A habilitação do acesso levará até 15 minutos para ser concluída. É muito importante desabilitar o firewall IP após anotar o conteúdo da ACL do firewall para habilitá-lo novamente mais tarde. 

   ![rede virtual e sub-rede configuradas com êxito](./media/vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

Agora, sua conta do Azure Cosmos DB só permitirá o tráfego proveniente dessa sub-rede escolhida. Se você tiver habilitado anteriormente o firewall IP, habilite-o novamente usando as informações anteriores.

### <a name="configure-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Configurar o ponto de extremidade de serviço para uma rede virtual do Azure e uma sub-rede novas

1. Na folha **Todos os recursos**, localize a conta do Azure Cosmos DB que você deseja proteger.  

> [!NOTE]
> Se você tiver um firewall IP existente configurado na conta do Azure Cosmos DB, observe a configuração do firewall, remova o firewall IP e habilite o ponto de extremidade de Serviço. Se você habilitar o ponto de extremidade de Serviço sem desabilitar o firewall, o tráfego desse intervalo de IP perderá a identidade do IP virtual e será descartado com uma mensagem de erro do filtro IP. Portanto, para evitar esse erro, você deve sempre desabilitar as regras de firewall, copiá-las, habilitar o ponto de extremidade de serviço da sub-rede e, finalmente, a ACL a sub-rede do Cosmos DB. Depois de configurar o ponto de extremidade de serviço e adicionar a ACL, você poderá habilitar novamente o firewall IP, se necessário.

2. Antes de habilitar o ponto de extremidade de serviço de rede virtual, copie as informações de firewall IP associadas à sua conta do Azure Cosmos DB para uso futuro. Você pode habilitar o firewall IP novamente depois de configurar o ponto de extremidade de serviço.  

3. Selecione **Firewalls e redes virtuais do Azure** no menu Configurações e escolha Permitir acesso em **Redes selecionadas**.  

4. Para conceder acesso a uma nova rede virtual do Azure, em Redes virtuais, selecione **Adicionar nova rede virtual**.  

5. Forneça os detalhes necessários para criar uma nova rede virtual e, em seguida, selecione Criar. A sub-rede será criada com um ponto de extremidade de serviço para "Microsoft.AzureCosmosDB" habilitado.

   ![Selecionar a rede virtual e a sub-rede para a nova rede virtual](./media/vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

## <a name="allow-access-from-azure-portal"></a>Permitir acesso pelo portal do Azure

Após os pontos de extremidade de serviço de Rede Virtual do Azure serem habilitados para a sua conta de banco de dados do Azure Cosmos DB, o acesso pelo portal ou outros serviços do Azure será desabilitado por padrão. O acesso à sua conta de banco de dados do Azure Cosmos DB em máquinas fora da sub-rede configurada é bloqueado, incluindo o acesso pelo portal.

![Permitir acesso pelo portal](./media/vnet-service-endpoint/allow-access-from-portal.png)

Se a sua conta do Azure Cosmos DB for usada por outros serviços do Azure, como Azure Search, ou acessada a partir do Stream Analytics ou do Power BI, você permitirá o acesso marcando **Permitir acesso aos Serviços do Azure**.

Para garantir que você tenha acesso às métricas do Azure Cosmos DB pelo portal, é preciso habilitar as opções de **Permitir acesso ao portal do Azure**. Para saber mais sobre essas opções, consulte as seções [Conexões do portal do Azure](firewall-support.md#connections-from-the-azure-portal) e [Conexões de serviços de PaaS do Azure](firewall-support.md#connections-from-global-azure-datacenters-or-azure-paas-services). Depois de selecionar o acesso, selecione **Salvar** para salvar as configurações.

## <a name="remove-a-virtual-network-or-subnet"></a>Remover uma rede virtual ou sub-rede 

1. Na folha **Todos os recursos**, localize a conta do Azure Cosmos DB para a qual você atribuiu pontos de extremidade de serviço.  

2. Selecione **Firewalls e redes virtuais** no menu de configurações.  

3. Para remover uma regra de sub-rede ou rede virtual, selecione "..." ao lado da rede virtual ou da sub-rede e selecione **Remover**.

   ![Remover uma rede virtual](./media/vnet-service-endpoint/remove-a-vnet.png)

4.  Clique em **Salvar** para aplicar suas alterações.

## <a name="configure-service-endpoint-by-using-azure-powershell"></a>Configurar o ponto de extremidade de serviço usando o Azure PowerShell 

Use as seguintes etapas para configurar o ponto de extremidade de serviço para uma conta do Azure Cosmos DB usando o Azure PowerShell:  

1. Instale o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) mais recente e [faça logon](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  Anote as configurações de firewall IP e exclua o firewall IP completamente antes de habilitar o ponto de extremidade de serviço para a conta.


> [!NOTE]
> Se você tiver um firewall IP existente configurado na conta do Azure Cosmos DB, observe a configuração do firewall, remova o firewall IP e habilite o ponto de extremidade de Serviço. Se você habilitar o ponto de extremidade de Serviço sem desabilitar o firewall, o tráfego desse intervalo de IP perderá a identidade do IP virtual e será descartado com uma mensagem de erro do filtro IP. Portanto, para evitar esse erro, você deve sempre desabilitar as regras de firewall, copiá-las, habilitar o ponto de extremidade de serviço da sub-rede e, finalmente, a ACL a sub-rede do Cosmos DB. Depois de configurar o ponto de extremidade de serviço e adicionar a ACL, você poderá habilitar novamente o firewall IP, se necessário.

2. Antes de habilitar o ponto de extremidade de serviço de rede virtual, copie as informações de firewall IP associadas à sua conta do Azure Cosmos DB para uso futuro. Você habilitará o firewall IP novamente depois de configurar o ponto de extremidade de serviço.  

3. Habilite o ponto de extremidade de serviço para uma sub-rede existente de uma rede virtual.  

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

4. Prepare-se para a habilitação de ACL na Conta CosmosDB, verificando se a rede virtual e a sub-rede têm o ponto de extremidade de serviço habilitado para o Azure Cosmos DB.

   ```powershell
   $vnProp = Get-AzureRmVirtualNetwork `
     -Name $vnName  -ResourceGroupName $rgName
   ```

5. Obtenha as propriedades de conta do Azure Cosmos DB executando o seguinte cmdlet:  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

6. Inicialize as variáveis para uso posterior. Configure todas as variáveis a partir da definição de conta existente. Se você tiver vários locais, será preciso adicioná-los como parte da matriz. Nesta etapa, você também deve configurar o ponto de extremidade de serviço de rede virtual configurando a variável "accountVNETFilterEnabled" para "True". Posteriormente, esse valor é atribuído ao parâmetro "isVirtualNetworkFilterEnabled".  

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

7. Atualize propriedades de conta do Azure Cosmos DB com a nova configuração executando os seguintes cmdlets: 

   ```powershell
   $cosmosDBProperties['databaseAccountOfferType'] = $databaseAccountOfferType
   $cosmosDBProperties['locations'] = $locations
   $cosmosDBProperties['consistencyPolicy'] = $consistencyPolicy
   $cosmosDBProperties['virtualNetworkRules'] = $virtualNetworkRules
   $cosmosDBProperties['isVirtualNetworkFilterEnabled'] = $accountVNETFilterEnabled

   Set-AzureRmResource `
     -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName -Properties $CosmosDBProperties
   ```

8. Execute o seguinte comando para verificar se sua conta do Azure Cosmos DB está atualizada com o ponto de extremidade de serviço de rede virtual que você configurou na etapa anterior:

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a name="add-virtual-network-service-endpoint-for-an-azure-cosmos-db-account-that-has-ip-firewall-enabled"></a>Adicionar o ponto de extremidade de serviço de rede virtual para uma conta do Azure Cosmos DB com firewall IP habilitado

1. Primeiro, desabilite o acesso do firewall IP à conta do Azure Cosmos DB.  

2. Habilite o ponto de extremidade de rede virtual para a conta do Azure Cosmos DB usando um dos métodos descritos nas seções anteriores.  

3. Habilite novamente o acesso ao firewall IP. 

## <a name="test-the-access"></a>Testar o acesso

Para verificar se os pontos de extremidade de serviço para o Azure Cosmos DB estão configurados conforme o esperado, use as seguintes etapas:

* Configure duas sub-redes em uma rede virtual como front-end e back-end, habilite o ponto de extremidade de serviço do Cosmos DB para a sub-rede de back-end.  

* Habilite o acesso na conta do Cosmos DB para a sub-rede de back-end.  

* Crie duas máquinas virtuais - uma na sub-rede de back-end e outra na sub-rede de front-end.  

* Tente acessar a conta do Azure Cosmos DB de ambas as máquinas virtuais. Você deverá ser capaz de se conectar pela máquina virtual criada na sub-rede de back-end, mas não pela criada na sub-rede de front-end. A solicitação apresentará um erro 404 quando você tentar se conectar por meio da sub-rede de front-end que confirma que o acesso ao Azure Cosmos DB é protegido usando o ponto de extremidade de serviço de rede virtual. A máquina na sub-rede de back-end poderá funcionar bem.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="what-happens-when-you-access-an-azure-cosmos-db-account-that-has-virtual-network-access-control-list-acl-enabled"></a>O que acontece quando você acessa uma conta do Azure Cosmos DB que tenha a Lista de Controle de Acesso (ACL) de rede virtual habilitada?  

O erro HTTP 404 é retornado.  

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-cosmos-db-account-in-another-region-for-example-if-azure-cosmos-db-account-is-in-west-us-or-east-us-and-virtual-networks-are-in-multiple-regions-can-the-virtual-network-access-azure-cosmos-db"></a>As sub-redes de uma rede virtual criada em regiões diferentes podem acessar uma conta do Azure Cosmos DB em outra região? Por exemplo, se a conta do Azure Cosmos DB estiver no leste dos EUA e as redes virtuais estiverem em várias regiões, a rede virtual conseguirá acessar o Azure Cosmos DB?  

Sim, as redes virtuais criadas em regiões diferentes podem ser acessadas pelo novo recurso. 

### <a name="can-an-azure-cosmos-db-account-have-both-virtual-network-service-endpoint-and-a-firewall"></a>Uma conta do Azure Cosmos DB pode ter um ponto de extremidade de serviço de rede virtual e um firewall?  

Sim, o ponto de extremidade de Serviço de Rede Virtual e um firewall podem coexistir. Em geral, você deve garantir que o acesso ao portal sempre esteja habilitado antes de configurar um ponto de extremidade de serviço de rede virtual para que você possa exibir as métricas associadas ao contêiner.

### <a name="can-i-allow-access-to-other-azure-services-from-a-given-azure-region-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Posso "permitir o acesso a outros serviços do Azure a partir de uma determinada região do Azure" quando o acesso do ponto de extremidade de serviço está habilitado para o Azure Cosmos DB?  

Isso é necessário somente quando você deseja que sua conta do Azure Cosmos DB seja acessada por outros serviços de terceiros do Azure primeiro, como Azure Data Factory, Azure Search ou qualquer serviço implantado em determinada região do Azure.

### <a name="how-many-virtual-network-service-endpoints-are-allowed-for-azure-cosmos-db"></a>Quantos pontos de extremidade de serviço de rede virtual são permitidos para o Azure Cosmos DB?  

Sessenta e quatro pontos de extremidade de serviço de rede virtual são permitidos para uma conta do Azure Cosmos DB.

### <a name="what-is-the-relationship-between-service-endpoint-and-network-security-group-nsg-rules"></a>Qual é a relação entre as regras NSG (Grupo de Segurança de Rede) e o Ponto de Extremidade de Serviço?  

As regras NSG no Azure Cosmos DB permitem restringir o acesso a um intervalo específico de endereços IP do Azure Cosmos DB. Se você quiser permitir o acesso a uma instância do Azure Cosmos DB presente em uma [região](https://azure.microsoft.com/global-infrastructure/regions/) específica, especifique a região no formato a seguir: 

    AzureCosmosDB.<region name>

Para saber mais sobre as marcas NSG, consulte o artigo [marcas de serviço de rede virtual](../virtual-network/security-overview.md#service-tags). 
  
### <a name="what-is-relationship-between-an-ip-firewall-and-virtual-network-service-endpoint-capability"></a>Qual é a relação entre um firewall IP e o recurso de ponto de extremidade do serviço de Rede Virtual?  

Esses dois recursos se complementam para garantir o isolamento dos ativos do Azure Cosmos DB e protegê-los. Usar o firewall IP garante que IPs estáticos possam acessar a conta do Azure Cosmos DB.  

### <a name="can-an-on-premise-devices-ip-address-that-is-connected-through-azure-virtual-network-gatewayvpn-or-express-route-gateway-access-azure-cosmos-db-account"></a>O endereço IP do dispositivo local que é conectado por meio do gateway de Rede Virtual (VPN) do Azure ou o gateway de rota expressa pode acessar a conta do Azure Cosmos DB?  

O endereço IP do dispositivo local ou o intervalo de endereços IP deve ser adicionado à lista de IPs estáticos para acessar a conta do Azure Cosmos DB.  

### <a name="what-happens-if-you-delete-a-virtual-network-that-has-service-endpoint-setup-for-azure-cosmos-db"></a>O que acontecerá se você excluir uma rede virtual que tenha o ponto de extremidade de serviço configurado para o Azure Cosmos DB?  

Quando uma rede virtual é excluída, as informações de ACL associadas a esse Azure Cosmos DB são excluídas, e ocorre a remoção da interação entre a rede virtual e a conta do Azure Cosmos DB. 

### <a name="what-happens-if-an-azure-cosmos-db-account-that-has-virtual-network-service-endpoint-enabled-is-deleted"></a>O que acontecerá se uma conta do Azure Cosmos DB que tem o ponto de extremidade de serviço de rede virtual habilitado for excluída?

Os metadados associados com à conta do Azure Cosmos DB específica são excluídos da sub-rede. E é responsabilidade do usuário final excluir a sub-rede e a rede virtual usadas.

### <a name="can-i-use-a-peered-virtual-network-to-create-service-endpoint-for-azure-cosmos-db"></a>Posso usar uma rede virtual emparelhado para criar o ponto de extremidade de serviço para o Azure Cosmos DB?  

Não, somente uma rede virtual direta e suas sub-redes podem criar pontos de extremidade de serviço do Azure Cosmos DB.

### <a name="what-happens-to-the-source-ip-address-of-resource-like-virtual-machine-in-the-subnet-that-has-azure-cosmos-db-service-endpoint-enabled"></a>O que acontece com o endereço IP de origem do recurso como a máquina virtual na sub-rede que tem o ponto de extremidade de serviço do Azure Cosmos DB habilitado?

Quando pontos de extremidade de serviço de rede virtual estão habilitados, os endereços IP de origem de recursos na sub-rede da rede virtual alternarão do uso de endereços IPV4 públicos para endereços privados da Rede Virtual do Azure do tráfego para o Azure Cosmos DB.

### <a name="does-azure-cosmos-db-reside-in-the-azure-virtual-network--provided-by-the-customer"></a>O Azure Cosmos DB reside na rede virtual do Azure fornecida pelo cliente?  

O Azure Cosmos DB é um serviço multilocatário com um endereço IP público. Quando você restringe o acesso a uma sub-rede de uma Rede Virtual do Azure usando o recurso de ponto de extremidade de serviço, o acesso é restrito para sua conta do Azure Cosmos DB por meio da Rede Virtual do Azure fornecida e sua sub-rede.  A conta do Azure Cosmos DB não reside na Rede Virtual do Azure. 

### <a name="what-if-anything-will-be-logged-in-log-analyticsoms-if-it-is-enabled"></a>E se tudo for registrado em log no Log Analytics/OMS se ele estiver habilitado?  

O Azure Cosmos DB efetuará o push de logs com o endereço IP (sem o último octeto) com o status 403 para solicitação bloqueada por ACL.  

## <a name="next-steps"></a>Próximas etapas
Para configurar um firewall para o Azure Cosmos DB, consulte o artigo [Suporte de firewall](firewall-support.md).

