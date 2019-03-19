---
title: Mover recursos do Azure para uma nova assinatura ou um novo grupo de recursos | Microsoft Docs
description: Use o Azure Resource Manager para mover recursos para um novo grupo de recursos ou uma nova assinatura.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.assetid: ab7d42bd-8434-4026-a892-df4a97b60a9b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: tomfitz
ms.openlocfilehash: 80577b4585a6c9e4ec83a8f21b358b7609d85268
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58081246"
---
# <a name="move-resources-to-new-resource-group-or-subscription"></a>Mover recursos para um novo grupo de recursos ou uma nova assinatura

Este artigo mostra como mover recursos do Azure para outra assinatura do Azure ou outro grupo de recursos na mesma assinatura. Você pode usar o portal do Azure, Azure PowerShell, CLI do Azure ou a API REST para mover recursos. Para percorrer um tutorial, veja [Tutorial: Mover recursos do Azure para outro grupo de recursos ou assinatura](./resource-manager-tutorial-move-resources.md).

O grupo de origem e o grupo de destino ficam bloqueados durante a operação de movimentação. As operações de gravação e exclusão são bloqueadas nos grupos de recursos até que a migração seja concluída. Esse bloqueio significa que você não pode adicionar, atualizar nem excluir os recursos nos grupos de recursos, mas isso não significa que os recursos estão congelados. Por exemplo, se você mover um SQL Server e seu banco de dados para um novo grupo de recursos, um aplicativo que usa o banco de dados não terá nenhuma inatividade. Ele ainda poderá ler e gravar no banco de dados.

Mover um recurso só o move para um novo grupo de recursos. A operação move não pode alterar o local do recurso. O novo grupo de recursos pode ter um local diferente, mas que não altere o local do recurso.

> [!NOTE]
> Este artigo descreve como mover recursos entre assinaturas existentes do Azure. Se você realmente quiser atualizar sua assinatura do Azure (por exemplo, mudando de gratuita para pré-paga), você precisará converter sua assinatura.
> * Para atualizar uma avaliação gratuita, consulte [Faça o upgrade da sua avaliação gratuita ou da assinatura do Microsoft Imagine Azure para o Pague conforme o uso](..//billing/billing-upgrade-azure-subscription.md).
> * Para alterar uma conta de pagamento conforme o uso, consulte [Alterar sua assinatura do Azure Pay-As-You-Go para uma oferta diferente](../billing/billing-how-to-switch-azure-offer.md).
> * Se você não conseguir converter a assinatura, [crie uma solicitação de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md). Selecione **Subscription Management** para o tipo de problema.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="when-to-call-azure-support"></a>Quando telefonar para o suporte do Azure

Você pode mover a maioria dos recursos por meio de operações de autoatendimento mostradas neste artigo. Use as operações de autoatendimento para:

* Mova os recursos do Resource Manager.
* Mover recursos clássicos de acordo com as [Limitações da implantação clássica](#classic-deployment-limitations).

Entre em contato com o [suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) quando você precisar:

* Mover os recursos para uma nova conta do Azure (e o locatário do Azure Active Directory) e precisar de ajuda com as instruções na seção anterior.
* Mover recursos clássicos, mas está tendo problemas com as limitações.

## <a name="services-that-can-be-moved"></a>Serviços que podem ser movidos

A lista a seguir fornece um resumo geral dos serviços do Azure que podem ser movidos para um novo grupo de recursos e uma nova assinatura. Para obter mais detalhes, confira [Move operation support for resources](move-support-resources.md) (Suporte para a operação de movimentação de recursos).

* Serviços de análise
* Gerenciamento de API
* Aplicativos do Serviço de Aplicativo (aplicativos Web) - consulte [Limitações do Serviço de Aplicativo](#app-service-limitations)
* Certificados do Serviço de Aplicativo – veja [Limitações de Certificado de Serviço de Aplicativo](#app-service-certificate-limitations)
* Automação: os runbooks devem existir no mesmo grupo de recursos que a conta de automação.
* Azure Active Directory B2C
* Cache do Azure para Redis – se o Cache do Azure para instância do Redis estiver configurado com uma rede virtual, a instância não poderá ser movida para uma assinatura diferente. Confira [Limitações de redes virtuais](#virtual-networks-limitations).
* Azure Cosmos DB
* Azure Data Explorer
* Banco de Dados do Azure para MariaDB
* Banco de Dados do Azure para MySQL
* Banco de Dados do Azure para PostgreSQL
* Azure DevOps – as organizações do Azure DevOps com compras de extensão que não são da Microsoft precisam [cancelar suas compras](https://go.microsoft.com/fwlink/?linkid=871160) para que possam mover a conta entre assinaturas.
* Mapas do Azure
* Logs do Azure Monitor
* Retransmissão do Azure
* Azure Stack - registros
* Lote
* Serviços do BizTalk
* Serviço de Bot
* CDN
* Serviços de Nuvem - veja [Limitações da implantação clássica](#classic-deployment-limitations)
* Serviços Cognitivos
* Registro de Contêiner - um registro de contêiner não pode ser movido quando a replicação geográfica estiver habilitada.
* Content Moderator
* Gerenciamento de Custos
* Customer Insights
* Catálogo de Dados
* Data Factory
* Data Lake Analytics
* Data Lake Store
* DNS
* Grade de Eventos
* Hubs de Eventos
* Front Door
* Clusters HDInsight – veja [Limitações do HDInsight](#hdinsight-limitations)
* Central de IoT
* Hubs IoT
* Key Vault - é usado para criptografia de disco e não pode ser movido para grupos de recursos na mesma assinatura ou entre assinaturas.
* Load Balancer - SKU básica pode ser movido. O balanceador de carga de SKU padrão não pode ser movido.
* Aplicativos Lógicos
* Aprendizado de Máquina - os serviços Web do Machine Learning Studio podem ser movidos para um grupo de recursos na mesma assinatura, mas não uma assinatura diferente. Outros recursos de Microsoft Machine Learning podem ser movidos entre assinaturas.
* Managed Disks - Managed Disks em zonas de disponibilidade não podem ser movidos para uma assinatura diferente
* Identidade gerenciada - atribuída pelo usuário
* Serviços de mídia
* Monitor - verifique se mover para nova assinatura não excede as [cotas de assinatura](../azure-subscription-service-limits.md#monitor-limits)
* Hubs de Notificação
* Insights Operacionais
* Gerenciamento de Operações
* Painéis do portal do Azure
* Power BI - tanto o Power BI inserido Embedded como a coleção de workspaces do BI
* IP público - SKU básica pode ser movido. O IP Público SKU padrão não pode ser movido.
* Cofre dos Serviços de Recuperação – registrar em uma [versão prévia](#recovery-services-limitations).
* Agendador
* Pesquisa - não é possível mover vários recursos de Pesquisa em regiões diferentes em uma operação. Em vez disso, mova-os em operações separadas.
* Barramento de Serviço
* Service Fabric
* Malha do Service Fabric
* Serviço SignalR
* Armazenamento – as contas de armazenamento em regiões diferentes não podem ser movidas na mesma operação. Nesse caso, use operações separadas para cada região.
* Armazenamento (clássico) - consulte [Limitações da implantação clássica](#classic-deployment-limitations)
* Stream Analytics – os trabalhos do Stream Analytics não podem ser movidos durante o estado de execução.
* Servidor de Banco de Dados SQL – o banco de dados e o servidor devem estar no mesmo grupo de recursos. Quando você move um SQL Server, todos os seus bancos de dados também são movidos. Este comportamento se aplica ao Banco de Dados SQL do Azure e ao banco de dados SQL Data Warehouse do Azure.
* Time Series Insights
* Gerenciador de Tráfego
* Máquinas virtuais – consulte [limitações das máquinas virtuais](#virtual-machines-limitations)
* Máquinas virtuais (clássicas) - consulte [Limitações da implantação clássica](#classic-deployment-limitations)
* Conjuntos de Dimensionamento de Máquinas Virtuais – consulte [Limitações de máquinas virtuais](#virtual-machines-limitations)
* Redes virtuais – consulte [Limitações de redes virtuais](#virtual-networks-limitations)
* Gateway de VPN

### <a name="services-that-cannot-be-moved"></a>Serviços que não podem ser movidos

A lista a seguir fornece um resumo geral dos serviços do Azure que não podem ser movidos para um novo grupo de recursos e uma nova assinatura. Para obter mais detalhes, confira [Move operation support for resources](move-support-resources.md) (Suporte para a operação de movimentação de recursos).

* AD Domain Services
* Serviço de Integridade Híbrida do AD
* Gateway de Aplicativo
* Migração de banco de dados do Azure
* Azure Databricks
* Firewall do Azure
* Migrações para Azure
* Azure NetApp Files
* Certificados - Os certificados do Serviço de Aplicativo podem ser movidos, mas os certificados carregados têm [limitações](#app-service-limitations).
* Aplicativos clássicos
* Instâncias de Contêiner
* Serviço de Contêiner
* Data Box
* Espaços de Desenvolvimento
* Dynamics LCS
* ExpressRoute
* Serviço do Kubernetes
* Serviços de Laboratórios – mover para um novo grupo de recursos na mesma assinatura está habilitado, mas a troca entre assinaturas está desabilitado.
* Aplicativos gerenciados
* Microsoft Genomics
* SAP HANA no Azure
* Segurança
* Site Recovery
* Gerenciador de Dispositivos StorSimple
* Redes Virtuais (clássicas) - consulte [Limitações da implantação clássica](#classic-deployment-limitations)

## <a name="limitations"></a>Limitações

A seção fornece descrições de como lidar com cenários complicados para mover os recursos. As limitações são:

* [Limitações das máquinas virtuais](#virtual-machines-limitations)
* [Limitações das redes virtuais](#virtual-networks-limitations)
* [Limitações do Serviço de Aplicativo](#app-service-limitations)
* [Limitações de Certificado do Serviço de Aplicativo](#app-service-certificate-limitations)
* [Limitações da implantação clássica](#classic-deployment-limitations)
* [Limitações dos Serviços de Recuperação](#recovery-services-limitations)
* [Limitações do HDInsight](#hdinsight-limitations)

### <a name="virtual-machines-limitations"></a>Limitações das máquinas virtuais

Você pode mover máquinas virtuais, com os discos gerenciados, imagens gerenciadas, os instantâneos gerenciados e conjuntos de disponibilidade com máquinas virtuais que usam discos gerenciados. Discos gerenciados em zonas de disponibilidade não podem ser movidos para uma assinatura diferente.

Ainda não há suporte para os cenários a seguir:

* Máquinas Virtuais com certificado armazenado no Key Vault podem ser movidas para um novo grupo de recursos na mesma assinatura, mas não entre assinaturas.
* Conjuntos de dimensionamento de máquina virtual com o balanceador de carga de SKU Standard ou IP público do SKU Standard não pode ser movidos.
* As máquinas virtuais criadas a partir dos recursos do Marketplace com os planos anexados não podem ser movidas entre grupos de recursos ou assinaturas. Desprovisione a máquina virtual na assinatura atual e implante-a novamente na nova assinatura.

Para mover máquinas virtuais configuradas com o Backup do Azure, use a seguinte solução alternativa:

* Localize o local de sua máquina virtual.
* Localize um grupo de recursos com o seguinte padrão de nomenclatura: `AzureBackupRG_<location of your VM>_1` por exemplo, AzureBackupRG_westus2_1
* Se estiver no portal do Azure, marque "Mostrar tipos ocultos"
* Se estiver no PowerShell, use o cmdlet `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1`
* Se estiver na CLI, use o `az resource list -g AzureBackupRG_<location of your VM>_1`
* Localize o recurso com o tipo `Microsoft.Compute/restorePointCollections` que tem o padrão de nomenclatura `AzureBackup_<name of your VM that you're trying to move>_###########`
* Exclua este recurso. Esta operação exclui somente os pontos de recuperação instantânea, não os dados de backup no cofre.
* Após a conclusão da exclusão, você poderá mover sua Máquina Virtual. Você pode mover a máquina virtual e o cofre para a assinatura de destino. Após a movimentação, você poderá continuar backups sem perda de dados.
* Para saber mais sobre como mover cofres do Serviço de Recuperação para o backup, veja [Limitações dos serviços de recuperação](#recovery-services-limitations).

### <a name="virtual-networks-limitations"></a>Limitações das redes virtuais

Ao mover uma rede virtual, você também deve mover os recursos dependentes. Para Gateways de VPN, você deve mover endereços IP, gateways de rede virtual e todos os recursos de conexão associados. Os gateways de rede local podem estar em um grupo de recursos diferentes.

Para mover uma máquina virtual com uma placa de interface de rede, você deve mover todos os recursos dependentes. Você deve mover a rede virtual para a placa de interface de rede, todas as outras placas de interface de rede para a rede virtual e os gateways de VPN.

Para mover uma rede virtual emparelhada, primeiro é necessário desabilitar o emparelhamento de rede virtual. Quando desabilitado, você pode mover a rede virtual. Após a movimentação, reabilite o emparelhamento de rede virtual.

Você não pode mover uma rede virtual para uma assinatura diferente caso a rede virtual contenha uma sub-rede com links de navegação de recurso. Por exemplo, se um recurso de Cache do Azure para Redis estiver implantado em uma sub-rede, essa sub-rede terá um link de navegação do recurso.

### <a name="app-service-limitations"></a>Limitações do Serviço de Aplicativo

As limitações para mover recursos do Serviço de Aplicativo diferem se você está movendo os recursos dentro de uma assinatura ou para uma nova assinatura. Se seu aplicativo Web usa um Certificado do Serviço de Aplicativo, veja [Limitações de Certificado de Serviço de Aplicativo](#app-service-certificate-limitations)

#### <a name="moving-within-the-same-subscription"></a>Movendo dentro da mesma assinatura

Ao mover um aplicativo Web _dentro da mesma assinatura_, você não pode mover os certificados SSL de terceiros. No entanto, você pode mover um aplicativo Web para o novo grupo de recursos sem mover o respectivo certificado SSL de terceiros, mantendo a funcionalidade SSL do aplicativo.

Se você deseja mover o certificado SSL com o aplicativo Web, siga estas etapas:

1. Excluir o certificado de terceiros do aplicativo Web, mas manter uma cópia do seu certificado
2. Mova o aplicativo Web.
3. Carregue o certificado de terceiros para o aplicativo Web movido.

#### <a name="moving-across-subscriptions"></a>Movendo entre assinaturas

Ao mover um aplicativo Web _entre assinaturas_, as seguintes limitações se aplicam:

- O grupo de recursos de destino não pode ter nenhum recurso de Serviço de Aplicativo existente. Os recursos de Serviço de Aplicativo incluem:
    - Aplicativos Web
    - Planos do Serviço de Aplicativo
    - Certificados SSL importados ou carregados
    - Ambientes de Serviço de Aplicativo
- Todos os recursos de Serviço de Aplicativo no grupo de recursos devem ser movidos juntos.
- Recursos do Serviço de Aplicativo podem ser movidos apenas no grupo de recursos no qual eles foram originalmente criados. Se um recurso do Serviço de Aplicativo não estiver mais em seu grupo de recursos original, ele deverá ser movido de volta para esse grupo de recursos original primeiro e, em seguida, poderá ser movido entre assinaturas.

### <a name="app-service-certificate-limitations"></a>Limitações de Certificado do Serviço de Aplicativo

Você pode mover seu Certificado do Serviço de Aplicativo para um novo grupo de recursos ou assinatura. Se seu Certificado do Serviço de Aplicativo estiver associado a um aplicativo Web, você deverá executar algumas etapas antes de mover os recursos para uma nova assinatura. Exclua a associação de SSL e o certificado particular do aplicativo Web antes de mover os recursos. O Certificado do Serviço de Aplicativo não precisa ser excluído, apenas o certificado particular no aplicativo Web.

### <a name="classic-deployment-limitations"></a>Limitações da implantação clássica

As limitações para mover recursos do Serviço de Aplicativo diferem se você está movendo os recursos dentro de uma assinatura ou para uma nova assinatura.

#### <a name="same-subscription"></a>Mesma assinatura

Ao mover recursos de um grupo de recursos para outro na mesma assinatura, as seguintes restrições se aplicarão:

* Redes virtuais (clássicas) não podem ser movidas.
* Máquinas virtuais (clássicas) devem ser movidas com o serviço de nuvem.
* Um serviço de nuvem pode ser movido apenas quando a movimentação incluir todas as suas máquinas virtuais.
* Apenas um serviço de nuvem pode ser movido por vez.
* Apenas uma conta de armazenamento (clássica) pode ser movida por vez.
* Uma conta de armazenamento (clássica) não pode ser movida na mesma operação com uma máquina virtual ou um serviço de nuvem.

Para mover recursos clássicos para um novo grupo de recursos dentro da mesma assinatura, use o [portal](#use-portal), o Azure PowerShell, a CLI do Azure ou a API REST. Use as mesmas operações como você usa para mover os recursos do Resource Manager.

#### <a name="new-subscription"></a>Nova assinatura

Ao mover recursos para uma nova assinatura, as seguintes restrições se aplicarão:

* Todos os recursos clássicos na assinatura devem ser movidos na mesma operação.
* A assinatura de destino não deve ter nenhum outro recurso clássico.
* A movimentação pode ser solicitada apenas por meio de uma API REST separada para movimentações clássicas. Os comandos de movimentação padrão do Gerenciador de Recursos não funcionam quando há uma movimentação dos recursos clássicos para uma nova assinatura.

Para mover recursos clássicos para uma nova assinatura, use operações REST específicas para recursos clássicos. Para usar REST, execute as seguintes etapas:

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

### <a name="recovery-services-limitations"></a>Limitações dos Serviços de Recuperação

 Para mover um cofre dos Serviços de Recuperação, você deve se registrar em uma [versão prévia pública limitada](../backup/backup-azure-move-recovery-services-vault.md).

No momento, você pode mover um cofre dos Serviços de Recuperação por região por vez. Não é possível mover cofres que fazem backup de Azure Files, Sincronização de Arquivos do Azure ou SQL em máquinas virtuais IaaS.

Se uma máquina virtual não for movida com o cofre, os pontos de recuperação de máquina virtual atual permanecerão no cofre até expirarem. Independentemente de a máquina virtual ser movida com o cofre, você pode restaurá-la do histórico de backup no cofre.

O cofre dos Serviços de Recuperação não oferece suporte a backups de assinatura cruzada. Se você mover um cofre contendo dados de backup de máquinas virtuais entre assinaturas, deverá mover as máquinas virtuais para a mesma assinatura e usar o mesmo grupo de recursos de destino para continuar com os backups.

Políticas de backup definidas para o cofre são mantidas depois de o cofre ser movido. Relatório e monitoramento devem ser configurados novamente para o cofre após a movimentação.

Para mover uma máquina virtual para uma nova assinatura sem mover o cofre dos Serviços de Recuperação:

 1. Interromper temporariamente o backup
 1. [Exclua o ponto de restauração](#virtual-machines-limitations). Esta operação exclui somente os pontos de recuperação instantânea, não os dados de backup no cofre.
 1. Mover as máquinas virtuais para a nova assinatura
 1. Proteja-o novamente em um novo cofre naquela assinatura

A movimentação dos recursos de Armazenamento, Rede ou Computação usados para configurar a recuperação de desastres com o Azure Site Recovery não está habilitada. Por exemplo, suponha que você configurou a replicação das máquinas locais para uma conta de armazenamento (Storage1) e queira que a máquina protegida venha, após o failover, para o Azure como uma máquina virtual (VM1) conectada a uma rede virtual (Network1). Você não pode mover nenhum desses recursos do Microsoft Azure – Storage1, VM1 e Network1 – entre grupos de recursos dentro da mesma assinatura ou em assinaturas diferentes.

### <a name="hdinsight-limitations"></a>Limitações do HDInsight

Você pode mover os clusters HDInsight para uma nova assinatura ou grupo de recursos. No entanto, não é possível mover os recursos de rede vinculados ao cluster HDInsight (por exemplo, a rede virtual, NIC ou balanceador de carga) entre assinaturas. Além disso, não é possível mover uma para um novo grupo de recursos uma NIC que está conectada a uma máquina virtual para o cluster.

Ao mover um cluster HDInsight para uma nova assinatura, mova primeiro os outros recursos (como a conta de armazenamento). Em seguida, mova apenas o cluster HDInsight.

## <a name="checklist-before-moving-resources"></a>Lista de verificação antes de mover recursos

Há algumas etapas importantes a serem executadas antes de mover um recurso. Ao verificar essas condições, é possível evitar erros.

1. As assinaturas de origem e de destino devem estar ativas. Se você tiver problemas para habilitar uma conta que tenha sido desabilitada [crie uma solicitação de Suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md). Selecione **Subscription Management** para o tipo de problema.

1. As assinaturas de origem e de destino devem existir no mesmo [locatário do Azure Active Directory](../active-directory/develop/quickstart-create-new-tenant.md). Para verificar se as duas assinaturas têm a mesma ID de locatário, use o Azure PowerShell ou a CLI do Azure.

   Para o Azure PowerShell, use:

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   Para a CLI do Azure, use:

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   Se as IDs de locatário para as assinaturas de origem e de destino não forem iguais, use os métodos a seguir para reconciliá-las:

   * [Transferir a propriedade de uma assinatura do Azure para outra conta](../billing/billing-subscription-transfer.md)
   * [Como associar ou adicionar uma assinatura do Azure ao Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. A assinatura de destino deve estar registrada para que o provedor de recursos do recurso seja movido. Se não estiver, você receberá um erro afirmando que a **assinatura não está registrada para um tipo de recurso**. Você pode ver esse erro ao mover um recurso para uma nova assinatura que nunca tenha sido usada com esse tipo de recurso.

   Para o PowerShell, use os seguintes comandos para obter o status do registro:

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   Para registrar um provedor de recursos, use:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   Para a CLI do Azure, use os seguintes comandos para obter o status do registro:

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   Para registrar um provedor de recursos, use:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. A conta de movimentação de recursos deve ter pelo menos as seguintes permissões:

   * **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action** no grupo de recursos de origem.
   * **Microsoft.Resources/subscriptions/resourceGroups/write** no grupo de recursos de destino.

1. Antes de mover os recursos, verifique as cotas de assinatura da assinatura para a qual você está movendo os recursos. Se mover os recursos significa que a assinatura excederá seus limites, será necessário verificar se você pode solicitar um aumento na cota. Para obter uma lista de limites e como solicitar um aumento, consulte [Limites, cotas e restrições em assinaturas e serviços do Azure](../azure-subscription-service-limits.md).

1. Quando possível, quebre grandes movimentações em operações de movimentação separadas. O Resource Manager imediatamente retornará um erro quando houver mais de 800 recursos em uma única operação. No entanto, mover menos de 800 recursos também pode falhar por tempo limite.

1. O serviço deve permitir a movimentação de recursos. Para determinar se a jogada será bem-sucedida, [valide sua solicitação de movimentação](#validate-move). Consulte as seções abaixo neste artigo [serviços permitem mover os recursos](#services-that-can-be-moved) e quais [services não permitem mover os recursos](#services-that-cannot-be-moved).

## <a name="validate-move"></a>Validar movimentação

A operação [validate move](/rest/api/resources/resources/validatemoveresources) permite que você teste seu cenário de movimentação sem realmente mover os recursos. Use essa operação para determinar se a movimentação será bem-sucedida. Para executar essa operação, você precisa de:

* nome do grupo de recursos de origem
* ID do recurso do grupo de recursos de destino
* ID do recurso de cada recurso para mover
* o [token de acesso](/rest/api/azure/#acquire-an-access-token) para sua conta

Envie a seguinte solicitação:

```
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2018-02-01
Authorization: Bearer <access-token>
Content-type: application/json
```

Com um corpo de solicitação:

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Se a solicitação estiver formatada corretamente, a operação retornará:

```
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

O código de status 202 indica que a solicitação de validação foi aceita, mas ainda não determinou se a operação de movimentação será bem-sucedida. O valor `location` contém um URL que você usa para verificar o status da operação de longa duração.  

Para verificar o status, envie a seguinte solicitação:

```
GET <location-url>
Authorization: Bearer <access-token>
```

Enquanto a operação ainda está em execução, você continua recebendo o código de status 202. Aguarde o número de segundos indicado no valor `retry-after` antes de tentar novamente. Se a operação de movimentação é validado com êxito, você receberá o código de 204 status. Se a validação da movimentação falhar, você receberá uma mensagem de erro, como:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="move-resources"></a>Mover recursos

### <a name="a-nameuse-portal-by-using-azure-portal"></a><a name="use-portal" />Usando o portal do Azure

Para mover recursos, selecione o grupo de recursos que os contém e, em seguida, selecione o botão **Mover**.

![Mover recursos](./media/resource-group-move-resources/select-move.png)

Selecione se você está movendo os recursos para um novo grupo de recursos ou uma nova assinatura.

Selecione os recursos a serem movidos e o grupo de recursos de destino. Confirme que você precisa atualizar scripts para esses recursos e selecione **OK**. Se tiver selecionado o ícone de edição da assinatura na etapa anterior, você também precisará selecionar a assinatura de destino.

![selecionar destino](./media/resource-group-move-resources/select-destination.png)

Em **Notificações**, você verá que a operação de movimentação está em execução.

![mostrar status da movimentação](./media/resource-group-move-resources/show-status.png)

Quando for concluída, você será notificado sobre o resultado.

![mostrar resultado da movimentação](./media/resource-group-move-resources/show-result.png)

### <a name="by-using-azure-powershell"></a>Usando o Azure PowerShell

Para mover os recursos existentes para outro grupo de recursos ou assinatura, use o comando [Move-AzResource](/powershell/module/az.resources/move-azresource). O exemplo a seguir mostra como mover diversos recursos para um novo grupo de recursos.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Para mover para uma nova assinatura, inclua um valor para o parâmetro `DestinationSubscriptionId`.

### <a name="by-using-azure-cli"></a>Usando a CLI do Azure

Para mover os recursos existentes para outro grupo de recursos ou assinatura, use o comando [az resource move](/cli/azure/resource?view=azure-cli-latest#az-resource-move). Forneça as IDs dos recursos a mover. O exemplo a seguir mostra como mover diversos recursos para um novo grupo de recursos. No parâmetro `--ids`, forneça uma lista separada por espaços das IDs do recurso que deseja mover.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Para mover para uma nova assinatura, forneça o parâmetro `--destination-subscription-id`.

### <a name="by-using-rest-api"></a>Usando API REST

Para mover recursos existentes para outro grupo de recursos ou outra assinatura, execute:

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

No corpo da solicitação, especifique o grupo de recursos de destino e os recursos para mover. Para obter mais informações sobre a operação de movimentação REST, consulte [Mover recursos](/rest/api/resources/Resources/MoveResources).

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre os cmdlets do PowerShell para gerenciar os recursos, consulte [usando o Azure PowerShell com o Resource Manager](manage-resources-powershell.md).
* Para saber mais sobre os comandos da CLI do Azure para gerenciar os recursos, consulte [usando a CLI do Azure com o Resource Manager](manage-resources-cli.md).
* Para saber mais sobre os recursos do portal para gerenciar sua assinatura, veja [Usar o Portal do Azure para gerenciar recursos](resource-group-portal.md).
* Para saber mais sobre como aplicar uma organização lógica aos seus recursos, veja [Usando marcações para organizar seus recursos](resource-group-using-tags.md).
