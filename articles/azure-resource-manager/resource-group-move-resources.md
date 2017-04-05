---
title: Mover recursos do Azure para uma nova assinatura ou um novo grupo de recursos | Microsoft Docs
description: Use o Azure Resource Manager para mover recursos para um novo grupo de recursos ou uma nova assinatura.
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ab7d42bd-8434-4026-a892-df4a97b60a9b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 892d24199be5065ee54d46863cca2fd958db3236
ms.lasthandoff: 03/30/2017


---
# <a name="move-resources-to-new-resource-group-or-subscription"></a>Mover recursos para um novo grupo de recursos ou uma nova assinatura
Este tópico mostra como mover recursos para uma nova assinatura ou um novo grupo de recursos na mesma assinatura. Você pode usar o portal, PowerShell, CLI do Azure ou a API REST para mover recursos. As operações de movimentação neste tópico estão disponíveis para você sem nenhuma assistência do suporte do Azure.

O grupo de origem e o grupo de destino ficam bloqueados durante a operação de movimentação de recursos. As operações de gravação e exclusão são bloqueadas nos grupos de recursos até que a migração seja concluída. Esse bloqueio significa que você não pode adicionar, atualizar nem excluir os recursos nos grupos de recursos, mas isso não significa que os recursos estão congelados. Por exemplo, se você mover um SQL Server e seu banco de dados para um novo grupo de recursos, um aplicativo que usa o banco de dados não terá nenhuma inatividade. Ele ainda poderá ler e gravar no banco de dados. 

Você não pode alterar o local do recurso. Mover um recurso só o move para um novo grupo de recursos. O novo grupo de recursos pode ter um local diferente, mas que não altere o local do recurso.

> [!NOTE]
> Este artigo descreve como mover recursos dentro de uma oferta de conta existente do Azure. Se você realmente deseja alterar sua oferta de conta do Azure (por exemplo, atualizando do modo pré-pago para pagar antecipadamente), mas quer continuar trabalhando com seus recursos existentes, consulte [Trocar a assinatura do Azure por outra oferta](../billing/billing-how-to-switch-azure-offer.md). 
> 
> 

## <a name="checklist-before-moving-resources"></a>Lista de verificação antes de mover recursos
Há algumas etapas importantes a serem realizadas antes de mover um recurso. Ao verificar essas condições, é possível evitar erros.

1. As assinaturas de origem e de destino devem existir no mesmo [locatário do Active Directory](../active-directory/active-directory-howto-tenant.md). Para verificar se as duas assinaturas têm a mesma ID de locatário, use o Azure PowerShell ou a CLI do Azure.

  Para o Azure PowerShell, use:

  ```powershell
  (Get-AzureRmSubscription -SubscriptionName "Example Subscription").TenantId
  ```

  Para a CLI 2.0 do Azure, use:

  ```azurecli
  az account show --subscription "Example Subscription" --query tenantId
  ```

  Se as IDs do locatário para as assinaturas de origem e de destino não forem iguais, tente alterar o diretório da assinatura. No entanto, essa opção só está disponível para Administradores de Serviço conectados a uma conta da Microsoft (e não uma conta corporativa). Para tentar alterar o diretório, faça logon no [portal clássico](https://manage.windowsazure.com/) e selecione **Configurações** para selecionar a assinatura. Se o ícone **Editar Diretório** estiver disponível, selecione-o para alterar o Active Directory associado. 

  ![editar diretório](./media/resource-group-move-resources/edit-directory.png) 

  Se esse ícone não estiver disponível, será necessário entrar em contato com o suporte para mover os recursos para um novo locatário.

2. O serviço deve permitir a movimentação de recursos. Este tópico lista quais serviços permitem mover os recursos e quais serviços não habilitam a movimentação dos recursos.
3. A assinatura de destino deve estar registrada para que o provedor de recursos do recurso seja movido. Se não estiver, você receberá um erro afirmando que a **assinatura não está registrada para um tipo de recurso**. Você pode encontrar esse problema ao mover um recurso para uma nova assinatura que nunca tenha sido usada com esse tipo de recurso. Para saber como verificar o status do registro e registrar provedores de recursos, consulte [Provedores e tipos de recursos](resource-manager-supported-services.md#resource-providers-and-types).

## <a name="when-to-call-support"></a>Quando telefonar para o suporte
Você pode mover a maioria dos recursos por meio de operações de autoatendimento mostradas neste tópico. Use as operações de autoatendimento para:

* Mova os recursos do Resource Manager.
* Mover recursos clássicos de acordo com as [Limitações da implantação clássica](#classic-deployment-limitations). 

Telefonar para o suporte quando você precisa:

* Mover os recursos para uma nova conta do Azure (e locatário do Active Directory).
* Mover recursos clássicos, mas está tendo problemas com as limitações.

## <a name="services-that-enable-move"></a>Serviços que permitem mover
Por enquanto, os serviços que permitem mover para um novo grupo de recursos e uma nova assinatura são:

* Gerenciamento da API
* Aplicativos do Serviço de Aplicativo (aplicativos Web) - consulte [Limitações do Serviço de Aplicativo](#app-service-limitations)
* Automação
* Batch
* Bing Mapas
* CDN
* Serviços de Nuvem - veja [Limitações da implantação clássica](#classic-deployment-limitations)
* Serviços Cognitivos
* Content Moderator
* Catálogo de Dados
* Data Factory
* Análises Data Lake
* Repositório Data Lake
* DNS
* Banco de Dados de Documentos
* Hubs de Eventos
* Clusters HDInsight – veja [Limitações do HDInsight](#hdinsight-limitations)
* Hubs IoT
* Cofre da Chave 
* Balanceadores de Carga
* Aplicativos Lógicos
* Machine Learning
* Serviços de mídia
* Mobile Engagement
* Hubs de Notificação
* Insights Operacionais
* Gerenciamento de Operações
* Power BI
* Cache Redis
* Agendador
* Pesquisar
* Gerenciamento do Servidor
* Barramento de Serviço
* Service Fabric
* Armazenamento
* Armazenamento (clássico) - consulte [Limitações da implantação clássica](#classic-deployment-limitations)
* Stream Analytics
* Servidor do Banco de Dados SQL - O banco de dados e o servidor devem residir no mesmo grupo de recursos. Quando você move um SQL Server, todos os seus bancos de dados também são movidos.
* Gerenciador de Tráfego
* Máquinas Virtuais – Não oferecem suporte para mudar para uma nova assinatura quando seus certificados estão armazenados em um Key Vault
* Máquinas virtuais (clássicas) - consulte [Limitações da implantação clássica](#classic-deployment-limitations)
* Redes virtuais — atualmente, uma Rede Virtual emparelhada não pode ser movida até que o emparelhamento da rede virtual tenha sido desabilitado. Depois de desabilitado, a Rede Virtual pode ser movida com êxito e o emparelhamento VNet pode ser habilitado.
* Gateway de VPN 

 
## <a name="services-that-do-not-enable-move"></a>Serviços que não permitem mover
Os serviços que atualmente não permitem mover um recurso são:

* Serviço de Integridade Híbrida do AD
* Gateway de Aplicativo
* Application Insights
* Serviços do BizTalk
* Serviço de Contêiner
* Rota Expressa
* Laboratórios DevTest – Habilita a mudança para um novo grupo de recursos na mesma assinatura, mas desabilita a troca entre assinaturas.
* Dynamics LCS
* Cofre de Serviços de Recuperação – não mova os recursos de Computação, Rede e Armazenamento associados ao cofre dos Serviços de Recuperação. Consulte [Limitações dos Serviços de Recuperação](#recovery-services-limitations).
* Segurança
* Máquinas Virtuais com certificado armazenado no Cofre de Chaves
* Máquinas virtuais com o Managed Disks
* Conjuntos de disponibilidade com Máquinas Virtuais com o Managed Disks
* Conjuntos de Dimensionamento de Máquina Virtual com Managed Disks
* Managed Disks
* Imagens criadas no Managed Disks
* Instantâneos criados no Managed Disks
* Conjuntos de Escala de Máquinas Virtuais
* Redes Virtuais (clássicas) - consulte [Limitações da implantação clássica](#classic-deployment-limitations)
* Máquinas Virtuais criadas em recursos do Marketplace — não podem ser movidas entre assinaturas. O recurso precisa ser desprovisionado na assinatura atual e implantado novamente na nova assinatura

## <a name="app-service-limitations"></a>Limitações do Serviço de Aplicativo
Ao trabalhar com aplicativos do Serviço de Aplicativo, você não pode mover um plano de Serviço de Aplicativo. Para mover os Aplicativos do Serviço de Aplicativo, as opções são:

* Mova o plano do Serviço de Aplicativo e todos os outros recursos do Serviço de Aplicativo nesse grupo de recursos para um novo grupo de recursos que ainda não têm os recursos do Serviço de Aplicativo. Esse requisito significa que você precisa mover até mesmo os recursos do Serviço de Aplicativo que não estão associados ao plano do Serviço de Aplicativo. 
* Mova os aplicativos para um grupo de recursos diferente, mas mantenha todos os planos do Serviço de Aplicativo no grupo de recursos original.

Se o grupo de recursos original também incluir um recurso do Application Insights, não será possível mover esse recurso, pois, atualmente, o Application Insights não permite a operação de movimentação. Se você incluir o recurso Application Insights ao mover aplicativos do Serviço de Aplicativo, toda a operação de movimentação falhará. No entanto, para que o aplicativo funcione corretamente, o Application Insights e o plano do Serviço de Aplicativo não precisam residir no mesmo grupo de recursos que o aplicativo.

Por exemplo, se o grupo de recursos contém:

* **web-a**, que está associado a **plan-a** e **app-insights-a**
* **web-b**, que está associado a **plan-b** e **app-insights-b**

Suas opções são:

* Mover **web-a**, **plan-a**, **web-b** e **plan-b**
* Mover **web-a** e **web-b**
* Mover **web-a**
* Mover **web-b**

Todas as outras combinações envolvem a movimentação de um tipo de recurso que não pode ser movido (Application Insights) ou deixar para trás um tipo de recurso que não pode ser deixado para trás ao mover um plano do Serviço de Aplicativo (qualquer tipo de recurso do Serviço de Aplicativo).

Caso o aplicativo Web resida em um grupo de recursos diferente de seu plano do Serviço de Aplicativo, mas você desejar mover ambos para um novo grupo de recursos, será necessário realizar a movimentação em duas etapas. Por exemplo:

* **web-a** reside em **web-group**
* **plan-a** reside em **plan-group**
* Você quer que **web-a** e **plan-a** residam em **combined-group**

Para realizar essa movimentação, execute duas operações de movimentação separadas na sequência a seguir:

1. Mover **web-a** para **plan-group**
2. Mover **web-a** e **plan-a** para **combined-group**.

Você pode mover um Certificado do Serviço de Aplicativo para um novo grupo de recursos ou assinatura sem problemas. No entanto, se seu aplicativo Web incluir um certificado SSL que você tiver comprado externamente e carregado para o aplicativo, você deverá excluir o certificado antes de mover o aplicativo Web. Por exemplo, você pode executar as seguintes etapas:

1. Excluir o certificado carregado do aplicativo Web
2. Mover o aplicativo Web
3. Carregar o certificado no aplicativo Web

## <a name="recovery-services-limitations"></a>Limitações dos Serviços de Recuperação
A movimentação dos recursos de Armazenamento, Rede ou Computação usados para configurar a recuperação de desastres com o Azure Site Recovery não está habilitada. 

Por exemplo, suponha que você configurou a replicação das máquinas locais para uma conta de armazenamento (Storage1) e queira que a máquina protegida venha, após o failover, para o Azure como uma máquina virtual (VM1) conectada a uma rede virtual (Network1). Você não pode mover nenhum desses recursos do Azure – Storage1, VM1 e Network1 – entre grupos de recursos dentro da mesma assinatura ou em assinaturas diferentes.

## <a name="hdinsight-limitations"></a>Limitações do HDInsight

Você pode mover os clusters HDInsight para uma nova assinatura ou grupo de recursos. No entanto, não é possível mover os recursos de rede vinculados ao cluster HDInsight (por exemplo, a rede virtual, NIC ou balanceador de carga) entre assinaturas. Além disso, não é possível mover uma para um novo grupo de recursos uma NIC que está conectada a uma máquina virtual para o cluster.

Ao mover um cluster HDInsight para uma nova assinatura, mova primeiro os outros recursos (como a conta de armazenamento). Em seguida, mova apenas o cluster HDInsight.

## <a name="classic-deployment-limitations"></a>Limitações da implantação clássica
As opções de movimentação dos recursos implantados por meio do modelo clássico apesentam diferenças, dependendo se você estiver movendo os recursos em uma assinatura ou para uma nova assinatura. 

### <a name="same-subscription"></a>Mesma assinatura
Ao mover recursos de um grupo de recursos para outro na mesma assinatura, as seguintes restrições se aplicarão:

* Redes virtuais (clássicas) não podem ser movidas.
* Máquinas virtuais (clássicas) devem ser movidas com o serviço de nuvem. 
* Um serviço de nuvem pode ser movido apenas quando a movimentação incluir todas as suas máquinas virtuais.
* Apenas um serviço de nuvem pode ser movido por vez.
* Apenas uma conta de armazenamento (clássica) pode ser movida por vez.
* Uma conta de armazenamento (clássica) não pode ser movida na mesma operação com uma máquina virtual ou um serviço de nuvem.

Para mover recursos clássicos para um novo grupo de recursos dentro da mesma assinatura, use o [portal](#use-portal), o [Azure PowerShell](#use-powershell), a [CLI do Azure](#use-azure-cli) ou a [API REST](#use-rest-api). Use as mesmas operações como você usa para mover os recursos do Resource Manager.

### <a name="new-subscription"></a>Nova assinatura
Ao mover recursos para uma nova assinatura, as seguintes restrições se aplicarão:

* Todos os recursos clássicos na assinatura devem ser movidos na mesma operação.
* A assinatura de destino não deve conter nenhum outro recurso clássico.
* A movimentação pode ser solicitada apenas por meio de uma API REST separada para movimentações clássicas. Os comandos de movimentação padrão do Gerenciador de Recursos não funcionam quando há uma movimentação dos recursos clássicos para uma nova assinatura.

Para mover recursos clássicos para uma nova assinatura, use operações REST ou do portal que sejam específicas para recursos clássicos. Para saber mais sobre como mover recursos clássicos por meio do portal, confira [Usar portal](#use-portal). Para usar REST, execute as seguintes etapas:

1. Verifique se a assinatura de origem pode participar de uma movimentação entre assinaturas. Use a operação a seguir:

  ```HTTP   
  POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
  ```
   
     No corpo da solicitação, inclua:

  ```json 
  {
    "role": "source"
  }
  ```
  
     A resposta para a operação de validação é no seguinte formato:

  ```json 
  {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
  }
  ```

2. Verifique se a assinatura de destino pode participar de uma movimentação entre assinaturas. Use a operação a seguir:

  ```HTTP 
  POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
  ```

     No corpo da solicitação, inclua:

  ```json 
  {
    "role": "target"
  }
  ```
   
     A resposta está no mesmo formato que a validação de assinatura de origem.
3. Se ambas as assinaturas forem aprovadas na validação, mova todos os recursos clássicos de uma assinatura para outra, use a seguinte operação:

  ```HTTP 
  POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
  ```

    No corpo da solicitação, inclua:

  ```json 
  {
    "target": "/subscriptions/{target-subscription-id}"
  }
  ```

A operação pode executar por vários minutos. 

## <a name="use-portal"></a>Usar o portal
Para mover recursos, selecione o grupo de recursos que os contém e, em seguida, o botão **Mover**.

![Mover recursos](./media/resource-group-move-resources/select-move.png)

Selecione se você está movendo os recursos para um novo grupo de recursos ou para uma nova assinatura.

Selecione os recursos a serem movidos e o grupo de recursos de destino. Confirme que você precisa atualizar scripts para esses recursos e selecione **OK**. Se tiver selecionado o ícone de edição da assinatura na etapa anterior, você também precisará selecionar a assinatura de destino.

![selecionar destino](./media/resource-group-move-resources/select-destination.png)

Em **Notificações**, você verá que a operação de movimentação está em execução.

![mostrar status da movimentação](./media/resource-group-move-resources/show-status.png)

Quando for concluída, você será notificado sobre o resultado.

![mostrar resultado da movimentação](./media/resource-group-move-resources/show-result.png)

## <a name="use-powershell"></a>Usar o PowerShell
Para mover recursos existentes para outro grupo de recursos ou assinatura, use o comando `Move-AzureRmResource`.

O primeiro exemplo mostra como mover um recurso para um novo grupo de recursos.

```powershell
$resource = Get-AzureRmResource -ResourceName ExampleApp -ResourceGroupName OldRG
Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $resource.ResourceId
```

O segundo exemplo mostra como mover vários recursos para um novo grupo de recursos.

```powershell
$webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Para mover para uma nova assinatura, inclua um valor para o parâmetro `DestinationSubscriptionId`.

Será solicitado que você confirme que deseja mover os recursos especificados.

```powershell
Confirm
Are you sure you want to move these resources to the resource group
'/subscriptions/{guid}/resourceGroups/newRG' the resources:

/subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/serverFarms/exampleplan
/subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/sites/examplesite
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
```

## <a name="use-azure-cli-20"></a>Usar a CLI 2.0 do Azure
Para mover recursos existentes para outro grupo de recursos ou assinatura, use o comando `az resource move`. Forneça as IDs dos recursos a mover. Você pode obter as IDs dos recursos com o seguinte comando:

```azurecli
az resource show -g sourceGroup -n storagedemo --resource-type "Microsoft.Storage/storageAccounts" --query id
```

O exemplo a seguir mostra como mover uma conta de armazenamento para um novo grupo de recursos. No parâmetro `--ids`, forneça uma lista separada por espaços das IDs do recurso que deseja mover.

```azurecli
az resource move --destination-group newgroup --ids "/subscriptions/{guid}/resourceGroups/sourceGroup/providers/Microsoft.Storage/storageAccounts/storagedemo"
```

Para mover para uma nova assinatura, forneça o parâmetro `--destination-subscription-id`.

## <a name="use-azure-cli-10"></a>Usar a CLI do Azure 1.0
Para mover recursos existentes para outro grupo de recursos ou assinatura, use o comando `azure resource move`. Forneça as IDs dos recursos a mover. Você pode obter as IDs dos recursos com o seguinte comando:

```azurecli
azure resource list -g sourceGroup --json
```

Isso retorna o seguinte formato:

```azurecli
[
  {
    "id": "/subscriptions/{guid}/resourceGroups/sourceGroup/providers/Microsoft.Storage/storageAccounts/storagedemo",
    "name": "storagedemo",
    "type": "Microsoft.Storage/storageAccounts",
    "location": "southcentralus",
    "tags": {},
    "kind": "Storage",
    "sku": {
      "name": "Standard_RAGRS",
      "tier": "Standard"
    }
  }
]
```

O exemplo a seguir mostra como mover uma conta de armazenamento para um novo grupo de recursos. No parâmetro `-i`, forneça uma lista separada por vírgulas das IDs do recurso que deseja mover.

```azurecli
azure resource move -i "/subscriptions/{guid}/resourceGroups/sourceGroup/providers/Microsoft.Storage/storageAccounts/storagedemo" -d "destinationGroup"
```

Será solicitado que você confirme que deseja mover o recurso especificado.

## <a name="use-rest-api"></a>Usar a API REST
Para mover recursos existentes para outro grupo de recursos ou outra assinatura, execute:

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 
```

No corpo da solicitação, especifique o grupo de recursos de destino e os recursos para mover. Para obter mais informações sobre a operação de movimentação REST, consulte [Mover recursos](https://msdn.microsoft.com/library/azure/mt218710.aspx).

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre os cmdlets do PowerShell para gerenciar sua assinatura, veja [Como usar o Azure PowerShell com o Resource Manager](powershell-azure-resource-manager.md).
* Para saber mais sobre os comandos da CLI do Azure para gerenciar sua assinatura, veja [Como usar a CLI do Azure com o Resource Manager](xplat-cli-azure-resource-manager.md).
* Para saber mais sobre os recursos do portal para gerenciar sua assinatura, veja [Usar o Portal do Azure para gerenciar recursos](resource-group-portal.md).
* Para saber mais sobre como aplicar uma organização lógica aos seus recursos, veja [Usando marcações para organizar seus recursos](resource-group-using-tags.md).


