---
title: Como configurar o firewall de IP para sua conta do Azure Cosmos
description: Saiba como configurar políticas de controle de acesso de IP para suporte ao firewall em contas de banco de dados do Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: d9a00bccb83fc60c96594ffacc5abde98c0f8470
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51632570"
---
# <a name="how-to-configure-ip-firewall-for-your-azure-cosmos-account"></a>Como configurar o firewall de IP para sua conta do Azure Cosmos

Você pode proteger os dados armazenados em sua conta do Azure Cosmos usando firewalls de IP. O Azure Cosmos DB é compatível com controles de acesso baseados em IP para suporte de firewall de entrada. Você pode definir um firewall de IP na conta do Azure Cosmos usando uma das seguintes maneiras:

1. No portal do Azure
2. Declarativamente usando um modelo do Azure Resource Manager
3. Programaticamente por meio da CLI do Azure ou o Azure PowerShell atualizando a propriedade **ipRangeFilter**.

## <a id="configure-ip-policy"></a> Configurando o firewall de IP usando o portal do Azure

Para definir a política de controle de acesso IP no portal do Azure, navegue até a página da conta do Azure Cosmos DB, clique em **Firewall e redes virtuais** no menu de navegação, altere o valor de **Permitir acesso de** para **Redes selecionadas** e, em seguida, clique em **Salvar**. 

![Captura de tela mostrando como abrir a página do Firewall no Portal do Azure](./media/how-to-configure-firewall/azure-portal-firewall.png)

Quando o controle de acesso de IP estiver ativado, o portal do Azure fornecerá a capacidade de especificar endereços IP, intervalos de endereço IP, bem como opções para permitir o acesso a outros serviços do Azure e ao Portal do Azure. Detalhes sobre essas opções são fornecidos nas seções a seguir.

> [!NOTE]
> Após habilitar a política de controle de acesso de IP para sua conta do Azure Cosmos, todas as solicitações para sua conta do Azure Cosmos provenientes de computadores fora da lista de intervalos de endereços IP permitidos são rejeitadas. A procura de recursos do Azure Cosmos DB no portal também é bloqueada para garantir a integridade do controle de acesso.

### <a name="allow-requests-from-the-azure-portal"></a>Permitir solicitações do portal do Azure 

Quando você habilita uma política de controle de acesso IP de forma programática, você precisa adicionar o endereço IP do portal do Azure à propriedade **ipRangeFilter** para manter o acesso. Os endereços IP do portal são:

|Região|Endereço IP|
|------|----------|
|Alemanha|51.4.229.218|
|China|139.217.8.252|
|Gov dos EUA|52.244.48.71|
|Todas as regiões, exceto as três acima|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|

Você pode habilitar o acesso ao portal do Azure escolhendo a opção **Permitir o acesso do portal do Azure** conforme mostrado na captura de tela a seguir: 

![Captura de tela mostrando como habilitar o acesso ao portal do Azure](./media/how-to-configure-firewall/enable-azure-portal.png)

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>Permitir solicitações de datacenters do Azure global ou de outras fontes dentro do Azure

Se você acessar a conta do Azure Cosmos de serviços que não fornecerem um endereço IP estático, por exemplo, Azure Stream Analytics, Azure Functions etc., ainda poderá usar o firewall de IP para limitar o acesso. Para permitir o acesso à conta do Azure Cosmos de tais serviços, uma configuração de firewall deve ser habilitada. Para habilitar essa configuração de firewall, adicione o endereço IP - 0.0.0.0 à lista de endereços IP permitidos. 0.0.0.0 restringe as solicitações para sua conta do Azure Cosmos do intervalo de IP do datacenter do Azure. Essa configuração não permite o acesso de nenhum outro intervalo de IP à sua conta do Azure Cosmos.

> [!NOTE]
> Essa opção configura o firewall para permitir todas as solicitações do Azure, incluindo as solicitações das assinaturas de outros clientes implantados no Azure. A lista de IPs permitidos por essa opção é ampla, portanto, ela limita a eficácia da política de firewall. Essa opção deve ser usada somente se as solicitações não se originarem de IPs estáticos ou sub-redes em VNETs. Escolher essa opção automaticamente permite o acesso do portal do Azure, pois ele é implantado no Azure.

Você pode habilitar o acesso ao portal do Azure escolhendo a opção **Aceitar conexões de datacenters do Azure público** conforme mostrado na captura de tela a seguir: 

![Captura de tela mostrando como abrir a página do Firewall no Portal do Azure](./media/how-to-configure-firewall/enable-azure-services.png)

### <a name="requests-from-your-current-ip"></a>Solicitações do seu IP atual

Para simplificar o desenvolvimento, o portal do Azure ajuda você a identificar e adicionar o IP de seu computador cliente à lista de permissões, de forma que os aplicativos em execução no computador possam acessar a conta do Azure Cosmos. O endereço IP do cliente é detectado automaticamente pelo portal. Pode ser o endereço IP cliente de seu computador ou o endereço IP de seu gateway de rede. Certifique-se de remover esse endereço IP antes de levar suas cargas de trabalho para a produção. 

Para habilitar o IP atual, selecione **Adicionar meu IP atual**, que adiciona o IP atual à lista de IPs e, em seguida, clique em **Salvar**.

![Captura de tela mostrando como definir as configurações de firewall para o IP atual](./media/how-to-configure-firewall/enable-current-ip.png)

### <a name="requests-from-cloud-services"></a>Solicitações dos serviços de nuvem

No Azure, os serviços de nuvem são uma maneira muito comum de hospedar a lógica do serviço de camada intermediária usando o Azure Cosmos DB. Para habilitar o acesso à sua conta do Azure Cosmos de um serviço de nuvem, o endereço IP público do serviço de nuvem deve ser adicionado à lista de permissões de endereços IP associados à sua conta do Azure Cosmos com a [configuração da política de controle de acesso de IP](#configure-ip-policy). Isso garante que todas as instâncias de função dos serviços de nuvem têm acesso à sua conta do Azure Cosmos. Você pode recuperar endereços IP de seus serviços de nuvem no Portal do Azure, conforme mostra a captura de tela a seguir:

![Captura de tela mostrando o endereço IP público de um serviço de nuvem exibido no Portal do Azure](./media/how-to-configure-firewall/public-ip-addresses.png)

Quando você expandir seu serviço de nuvem adicionando outras instâncias de função, essas novas instâncias terão acesso automaticamente à conta do Azure Cosmos, desde que façam parte do mesmo serviço de nuvem.

### <a name="requests-from-virtual-machines"></a>Solicitações de máquinas virtuais

[Máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/) ou [conjuntos de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) também podem ser usados para hospedar serviços de camada intermediária usando o Azure Cosmos DB. Para configurar sua conta do Cosmos para permitir o acesso em máquinas virtuais, endereços IP públicos da máquina virtual e/ou conjunto de dimensionamento de máquinas virtuais devem ser configurados como um dos endereços IP permitidos da conta do Azure Cosmos com a [configuração da política de controle de acesso de IP](#configure-ip-policy). Você pode recuperar endereços IP de máquinas virtuais no Portal do Azure, conforme mostra a captura de tela a seguir:

![Captura de tela mostrando o endereço IP público de uma máquina virtual exibida no Portal do Azure](./media/how-to-configure-firewall/public-ip-addresses-dns.png)

Quando você adiciona outras instâncias de máquina virtual ao grupo, elas recebem automaticamente o acesso à sua conta do Azure Cosmos.

### <a name="requests-from-the-internet"></a>Solicitações da Internet

Quando você acessa sua conta do Azure Cosmos de um computador na Internet, o endereço IP do cliente ou o intervalo de endereços IP do computador deve ser adicionado à lista de permissões de endereços IP dela.

## <a id="configure-ip-firewall-arm"></a>Configurar o firewall IP usando o modelo do Resource Manager

Para configurar o controle de acesso à sua conta do Azure Cosmos, o modelo do Resource Manager deve especificar o atributo **ipRangeFilter** com uma lista de intervalos de IP, que devem estar na lista de permissões. Por exemplo, adicione o seguinte código JSON ao seu modelo:

```json
   {
     "apiVersion": "2015-04-08",
     "type": "Microsoft.DocumentDB/databaseAccounts",
     "kind": "GlobalDocumentDB",
     "name": "[parameters('databaseAccountName')]",
     "location": "[resourceGroup().location]",
     "properties": {
     "databaseAccountOfferType": "Standard",
     "name": "[parameters('databaseAccountName')]",
     "ipRangeFilter":"183.240.196.255, 104.42.195.92,40.76.54.131, 52.176.6.30,52.169.50.45,52.187.184.26"
   }
```

## <a id="configure-ip-firewall-cli"></a>Configurar a política de controle de acesso de IP usando a CLI do Azure

O comando a seguir mostra como criar uma conta do Azure Cosmos com o controle de acesso de IP: 

```azurecli-interactive

name="<Azure Cosmos account name>"
resourceGroupName="<Resource group name>"

az cosmosdb create \
  --name $name \
  --kind GlobalDocumentDB \
  --resource-group $resourceGroupName \
  --max-interval 10 \
  --max-staleness-prefix 200 \
  --ip-range-filter "183.240.196.255, 104.42.195.92,40.76.54.131, 52.176.6.30,52.169.50.45,52.187.184.26"
```

Para atualizar as configurações de firewall para uma conta existente, execute o seguinte comando:

```azurecli-interactive
az cosmosdb update \
      --name $name \
      --resource-group $resourceGroupName \
      --ip-range-filter "183.240.196.255, 104.42.195.92,40.76.54.131, 52.176.6.30,52.169.50.45,52.187.184.26"
```

## <a id="troubleshoot-ip-firewall"></a>Como solucionar problemas da política de controle de acesso de IP

Você pode solucionar problemas de política de controle de acesso de IP usando as seguintes opções: 

### <a name="azure-portal"></a>Portal do Azure 
Ao habilitar uma política de controle de acesso de IP para sua conta do Azure Cosmos, todas as solicitações para sua conta de computadores fora da lista de permissões de intervalos de endereços IP são bloqueadas. Portanto se você quiser habilitar as operações do plano de dados do portal, como navegar em contêineres e consultar documentos, será necessário permitir explicitamente o acesso ao portal do Azure usando o painel **Firewall** no portal.

### <a name="sdks"></a>SDKs 
Quando você acessa os recursos do Azure Cosmos usando SDKs de computadores que não estão na lista de permissões **uma resposta 404 Não encontrado genérica é retornada sem nenhum detalhe adicional**. Verifique a lista de IPs permitidos para sua conta e garanta que a configuração de política correta esteja aplicada à sua conta do Cosmos. 

### <a name="check-source-ips-in-blocked-requests"></a>Verificar os IPs de origem em solicitações bloqueadas
Habilite o log de diagnósticos da sua conta do Azure Cosmos, esses logs mostram cada solicitação e resposta. **As mensagens relacionadas ao firewall são internamente registradas com o código de retorno 403**. Ao filtrar essas mensagens, você pode ver os IPs de origem das solicitações bloqueadas. Confira [Log de diagnósticos do Azure Cosmos DB](logging.md).

### <a name="requests-from-subnet-with-service-endpoint-for-azure-cosmos-database-enabled"></a>Solicitações de sub-rede com o ponto de extremidade de serviço para o banco de dados do Azure Cosmos habilitado
As solicitações de uma sub-rede na VNET que tem o ponto de extremidade de serviço do Azure Cosmos DB habilitado enviam a identidade da VNET e da sub-rede para contas do Azure Cosmos. Essas solicitações não têm o IP público da origem, portanto, são rejeitadas pelos filtros de IP. Para permitir o acesso de determinadas sub-redes em VNETs, adicione a lista de controle de acesso da VNET descrita em [Como configurar o acesso baseado em rede virtual e sub-rede para sua conta do Azure Cosmos](how-to-configure-vnet-service-endpoint.md). Pode levar até 15 minutos para as regras de firewall serem aplicadas.


## <a name="next-steps"></a>Próximas etapas

Para configurar o ponto de extremidade de serviço de rede virtual para sua conta do Azure Cosmos DB, confira os seguintes artigos:

* [Controle de acesso de VNET e sub-rede de sua conta do Azure Cosmos](vnet-service-endpoint.md)
* [Como configurar o acesso baseado em rede virtual e sub-rede para sua conta do Azure Cosmos](how-to-configure-vnet-service-endpoint.md)


