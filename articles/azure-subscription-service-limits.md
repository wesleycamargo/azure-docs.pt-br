---
title: Limites e cotas de assinatura do Azure
description: Fornece uma lista de assinaturas comuns do Azure e limites de serviço, cotas e restrições. Este artigo inclui informações sobre como aumentar os limites junto com os valores máximos.
services: multiple
author: rothja
manager: jeffreyg
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: billing
ms.topic: article
ms.date: 04/19/2019
ms.author: byvinyal
ms.openlocfilehash: cb0203e3413ab525d6885f23c5936c64e1bef189
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233695"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Assinatura do Azure e limite de serviços, cotas e restrições
Este documento lista alguns dos limites mais comuns do Microsoft Azure, que também são chamados de cotas. Esse documento não cobre atualmente todos os serviços do Azure. Ao longo do tempo, a lista será expandida e atualizada para abordar mais serviços.

Para saber mais sobre os preços do Azure, consulte [visão geral de preços do Azure](https://azure.microsoft.com/pricing/). Lá, você pode estimar os custos usando o [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/). Você também pode ir para a página de detalhes de preços para um serviço específico, por exemplo, [VMs Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). Para obter dicas sobre como ajudar a gerenciar custos, consulte [Evitar custos inesperados com o gerenciamento de custo e cobrança do Azure](billing/billing-getting-started.md).

> [!NOTE]
> Se você deseja aumentar a cota acima do limite padrão, ou o limite [abrir uma solicitação de suporte do cliente online sem custo](azure-resource-manager/resource-manager-quota-errors.md). Os limites não podem ser aumentados acima do valor de limite máximo mostrado nas tabelas a seguir. Se não houver nenhuma coluna de limite máximo, o recurso não tem limites ajustáveis.
>
> [Assinaturas de avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p) não são elegíveis para aumentos de cota ou limite. Se você tiver uma [assinatura de Avaliação Gratuita](https://azure.microsoft.com/offers/ms-azr-0044p), você poderá atualizar para uma assinatura de [pagamento conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/). Para obter mais informações, consulte [atualizar sua assinatura de avaliação gratuita do Azure para uma assinatura pré-paga](billing/billing-upgrade-azure-subscription.md) e o [perguntas frequentes sobre assinatura de avaliação gratuita](https://azure.microsoft.com/free/free-account-faq).
>

## <a name="limits-and-azure-resource-manager"></a>Limites e o Azure Resource Manager
Agora é possível combinar vários recursos do Azure em um grupo de recursos do Azure único. Quando você usa grupos de recursos, limites que antes eram globais gerenciados em um nível regional com o Azure Resource Manager. Para obter mais informações sobre grupos de recursos do Azure, consulte [visão geral do Azure Resource Manager](azure-resource-manager/resource-group-overview.md).

Na lista de limites a seguir, uma nova tabela reflete quaisquer diferenças nos limites ao usar o Azure Resource Manager. Por exemplo, há um **limites de assinatura** tabela e uma **limites de assinatura – Azure Resource Manager** tabela. Quando um limite se aplica a ambos os cenários, ele é mostrado apenas na primeira tabela. A menos que indicado de outro modo, os limites são globais em todas as regiões.

> [!NOTE]
> As cotas para recursos em grupos de recursos do Azure são por região acessível por sua assinatura, não por assinatura, assim como as cotas de gerenciamento de serviço. Vamos usar cotas vCPU como um exemplo. Para solicitar um aumento de cota com suporte para vCPU, você deve decidir quantos vCPUs deseja usar em quais regiões. Faça uma solicitação específica de cotas de vCPU do grupo de recursos do Azure para as quantidades e regiões desejadas. Se você precisar usar 30 vCPUs na Europa Ocidental para executar seu aplicativo lá, você solicitar especificamente 30 vCPUs na Europa Ocidental. Sua cota de vCPU não é aumentada em qualquer outra região – somente a Europa Ocidental tem a cota de vCPU de 30.
> <!-- -->
> Como resultado, decida quais devem ser suas cotas de grupo de recursos do Azure para sua carga de trabalho em determinada região. Em seguida, solicite essa quantidade em cada região na qual você deseja implantar. Para obter ajuda sobre como determinar suas cotas atuais para regiões específicas, consulte [solucionar problemas de implantação](resource-manager-common-deployment-errors.md).
>
>

## <a name="service-specific-limits"></a>Limites específicos de serviço
* [Active Directory](#active-directory-limits)
* [Gerenciamento da API](#api-management-limits)
* [Serviço de Aplicativo](#app-service-limits)
* [Gateway de Aplicativo](#application-gateway-limits)
* [Application Insights](#application-insights-limits)
* [Automação](#automation-limits)
* [Cache Redis do Azure](#azure-cache-for-redis-limits)
* [Serviços de Nuvem do Azure](#azure-cloud-services-limits)
* [Azure Cosmos DB](#azure-cosmos-db-limits)
* [Banco de Dados do Azure para MySQL](#azure-database-for-mysql)
* [Banco de Dados do Azure para PostgreSQL](#azure-database-for-postgresql)
* [DNS do Azure](#azure-dns-limits)
* [Firewall do Azure](#azure-firewall-limits)
* [Serviço de Kubernetes do Azure](#azure-kubernetes-service-limits)
* [Mapas do Azure](#azure-maps-limits)
* [Azure Monitor](#monitor-limits)
* [Azure Policy](#azure-policy-limits)
* [Azure Search](#azure-search-limits)
* [Serviço Azure SignalR](#azure-signalr-service-limits)
* [Backup](#backup-limits)
* [Batch](#batch-limits)
* [Serviços do BizTalk](#biztalk-services-limits)
* [Instâncias de Contêiner](#container-instances-limits)
* [Registro de Contêiner](#container-registry-limits)
* [Rede de distribuição de conteúdo](#content-delivery-network-limits)
* [Fábrica de dados](#data-factory-limits)
* [Data Lake Analytics](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [Serviço de Migração do Banco de Dados](#database-migration-service-limits)
* [Grade de Eventos](#event-grid-limits)
* [Hubs de Evento](#event-hubs-limits)
* [Serviço de porta da frente](#azure-front-door-service-limits)
* [Identity Manager](#identity-manager-limits)
* [Hub IoT](#iot-hub-limits)
* [Serviço de Provisionamento de Dispositivos no Hub IoT](#iot-hub-device-provisioning-service-limits)
* [Key Vault](#key-vault-limits)
* [Balanceador de Carga](#load-balancer)
* [Log Analytics](#log-analytics-limits)
* [Serviços de Mídia](#media-services-limits)
* [Serviços Móveis](#mobile-services-limits)
* [Autenticação Multifator](#multi-factor-authentication-limits)
* [Rede](#networking-limits)
* [Observador de Rede](#network-watcher-limits)
* [Hubs de Notificação](#notification-hubs-limits)
* [Endereço IP público](#publicip-address)
* [Grupo de recursos](#resource-group-limits)
* [Controle de acesso baseado em função](#role-based-access-control-limits)
* [Agendador](#scheduler-limits)
* [Barramento de Serviço](#service-bus-limits)
* [Recuperação de Site](#site-recovery-limits)
* [Banco de Dados SQL](#sql-database-limits)
* [SQL Data Warehouse](#sql-data-warehouse-limits)
* [Armazenamento](#storage-limits)
* [Sistema StorSimple](#storsimple-system-limits)
* [Análise de fluxo](#stream-analytics-limits)
* [Assinatura](#subscription-limits)
* [Gerenciador de Tráfego](#traffic-manager-limits)
* [Máquinas virtuais](#virtual-machines-limits)
* [Conjuntos de dimensionamento de máquinas virtuais](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>Limites de assinatura
#### <a name="subscription-limits---azure-service-management-classic-deployment-model"></a>Limites de assinatura – gerenciamento de serviços do Azure (modelo de implantação clássico)
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Limites de Assinatura – Azure Resource Manager
Os seguintes limites se aplicam quando você usa o Azure Resource Manager e grupos de recursos do Azure. Limites que não foram alteradas com o Azure Resource Manager não estão listados. Consulte a tabela anterior para obter esses limites.

Para obter informações sobre limites de leitura e gravação da API do Resource Manager, consulte [solicitações do Gerenciador de Recursos de limitação](resource-manager-request-limits.md).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Limites do grupo de recursos
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Limites de Máquinas virtuais
#### <a name="virtual-machines-limits"></a>Limites de Máquinas virtuais
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Limites de Máquinas Virtuais – Gerenciador de Recursos do Azure
Os seguintes limites se aplicam quando você usa o Azure Resource Manager e grupos de recursos do Azure. Limites que não foram alteradas com o Azure Resource Manager não estão listados. Consulte a tabela anterior para obter esses limites.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

#### <a name="shared-image-gallery-limits"></a>Limites de galeria de imagens de compartilhado

Há limites por assinatura, para a implantação de recursos usando Shared galerias de imagens:
- 100 galerias de imagem compartilhada, por assinatura por região
- 1.000 definições de imagem, por assinatura por região
- versões de imagem de 10.000, por assinatura por região

### <a name="virtual-machine-scale-sets-limits"></a>Limites de conjuntos de dimensionamento de máquina virtual
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="container-instances-limits"></a>Limites das Instâncias de Contêiner
[!INCLUDE [container-instances-limits](../includes/container-instances-limits.md)]

### <a name="container-registry-limits"></a>Limites do Registro de Contêiner
A tabela a seguir fornece detalhes sobre os recursos e os limites das [camadas de serviço](./container-registry/container-registry-skus.md) Básico, Standard e Premium.

[!INCLUDE [container-registry-limits](../includes/container-registry-limits.md)]

### <a name="azure-kubernetes-service-limits"></a>Limites de Kubernetes Service do Azure
[!INCLUDE [container-service-limits](../includes/container-service-limits.md)]

### <a name="networking-limits"></a>Limites de rede
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="expressroute-limits"></a>Limites do ExpressRoute
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="application-gateway-limits"></a>Limites do Gateway de Aplicativo

A tabela a seguir se aplica aos SKUs v1, v2, Standard e WAF, salvo indicação em contrário.
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="network-watcher-limits"></a>Limites do Observador de Rede
[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

#### <a name="traffic-manager-limits"></a>Limites do Gerenciador de Tráfego
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="azure-dns-limits"></a>Limites DNS do Azure
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

#### <a name="azure-firewall-limits"></a>Limites de Firewall do Azure
[!INCLUDE [azure-firewall-limits](../includes/firewall-limits.md)]

#### <a name="azure-front-door-service-limits"></a>Limites do Azure Front Door Service
[!INCLUDE [azure-front-door-service-limits](../includes/front-door-limits.md)]

### <a name="storage-limits"></a>Limites de armazenamento
<!--like # storage accts -->
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

Para obter mais informações sobre os limites da conta de armazenamento, consulte [metas de desempenho e escalabilidade do armazenamento do Azure](storage/common/storage-scalability-targets.md).

#### <a name="storage-resource-provider-limits"></a>Limites do provedor de recursos de armazenamento 

[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

#### <a name="azure-blob-storage-limits"></a>Limites de Armazenamento de Blobs do Azure
[!INCLUDE [storage-blob-scale-targets](../includes/storage-blob-scale-targets.md)]

#### <a name="azure-files-limits"></a>Limites de Arquivos do Azure
Para obter mais informações sobre os limites de arquivos do Azure, consulte [metas de desempenho e escalabilidade do arquivos do Azure](storage/files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../includes/storage-files-scale-targets.md)]

#### <a name="azure-file-sync-limits"></a>Limites de Sincronização de Arquivo do Azure
[!INCLUDE [storage-sync-files-scale-targets](../includes/storage-sync-files-scale-targets.md)]

#### <a name="azure-queue-storage-limits"></a>Limites de Armazenamento de Filas do Azure
[!INCLUDE [storage-queues-scale-targets](../includes/storage-queues-scale-targets.md)]

#### <a name="azure-table-storage-limits"></a>Limites de Armazenamento de Tabelas do Azure
[!INCLUDE [storage-tables-scale-targets](../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
#### <a name="virtual-machine-disk-limits"></a>Limites de disco de máquina virtual
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

Para obter mais informações, consulte [tamanhos de máquina Virtual](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

#### <a name="managed-virtual-machine-disks"></a>Discos de máquina virtual gerenciados

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>Discos de máquina virtual não gerenciados

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

### <a name="azure-cloud-services-limits"></a>Limites de serviços de nuvem do Azure
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="app-service-limits"></a>Limites do Serviço de Aplicativo
Os seguintes limites do Serviço de Aplicativo incluem limites para Aplicativos Web, Aplicativos Móveis e Aplicativos de API.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="scheduler-limits"></a>Limites do Agendador
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Limites de lote
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>Limites dos Serviços BizTalk
A tabela a seguir mostra os limites de Serviços BizTalk do Azure.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="azure-cosmos-db-limits"></a>Limites do Azure Cosmos DB
O Azure Cosmos DB é um banco de dados de escala global no qual o armazenamento e a produtividade podem ser dimensionados para atender às necessidades de seu aplicativo. Se você tiver alguma dúvida sobre a escala do Azure Cosmos DB fornece, envie um email para askcosmosdb@microsoft.com.

### <a name="azure-database-for-mysql"></a>Banco de Dados do Azure para MySQL
Para limites do Banco de Dados do Azure para MySQL, consulte [Limitações no Banco de Dados do Azure para MySQL](mysql/concepts-limits.md).

### <a name="azure-database-for-postgresql"></a>Banco de Dados do Azure para PostgreSQL
Para limites do Banco de Dados do Azure para PostgreSQL, consulte [Limitações no Banco de Dados do Azure para PostgreSQL](postgresql/concepts-limits.md).

### <a name="azure-search-limits"></a>Limites de pesquisa do Azure
Os tipos de preço determinam a capacidade e os limites de seu serviço Search. Eles incluem:

* **Livre** serviço multilocatário, compartilhado com outros assinantes do Azure, destina-se para avaliação e projetos de desenvolvimento pequena.
* **Básico** fornece recursos de computação dedicados para cargas de trabalho de produção em uma escala menor, com até três réplicas para cargas de trabalho de consulta altamente disponíveis.
* **Padrão**, que inclui o S1, S2, S3, e S3 de alta densidade, é para cargas de trabalho de produção maiores. Vários níveis existem dentro da camada Standard para que você possa escolher uma configuração de recursos que melhor corresponde ao seu perfil de carga de trabalho.

**Limites por assinatura**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Limites por serviço Search**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

Para saber mais sobre limites em um nível mais granular, como o tamanho do documento, consultas por segundo, chaves, solicitações e respostas, confira [Limites de serviço no Azure Search](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Limites de Serviços de Mídia
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="content-delivery-network-limits"></a>Limites de rede de distribuição de conteúdo
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Limites de Serviços Móveis
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="monitor-limits"></a>Monitorar limites
[!INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

### <a name="notification-hubs-limits"></a>Limites de Hubs de notificação
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Limites de Hubs de Eventos
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Limites de Barramento de Serviço
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>Limites do Hub IoT
[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="iot-hub-device-provisioning-service-limits"></a>Limites do Serviço de Provisionamento de Dispositivos no Hub IoT
[!INCLUDE [azure-iotdps-limits](../includes/iot-dps-limits.md)]

### <a name="data-factory-limits"></a>Limites de fábrica de dados
[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Limites do Data Lake Analytics
[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="data-lake-store-limits"></a>Limites do Data Lake Store
[!INCLUDE [azure-data-lake-store-limits](../includes/azure-data-lake-store-limits.md)]

### <a name="database-migration-service-limits"></a>Limites do Serviço de Migração do Banco de Dados
[!INCLUDE [database-migration-service-limits](../includes/database-migration-service-limits.md)]

### <a name="stream-analytics-limits"></a>Limites do Stream Analytics
[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Limites do Active Directory
[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

### <a name="event-grid-limits"></a>Limita a grade de eventos
[!INCLUDE [event-grid-limits](../includes/event-grid-limits.md)]

### <a name="azure-maps-limits"></a>Limites do Azure Mapas
[!INCLUDE [maps-limits](../includes/maps-limits.md)]

### <a name="azure-policy-limits"></a>Limites do Azure Policy
[!INCLUDE [policy-limits](../includes/azure-policy-limits.md)]

### <a name="storsimple-system-limits"></a>Limites do sistema StorSimple
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="log-analytics-limits"></a>Limites do Log Analytics
[!INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### <a name="backup-limits"></a>Limites do Backup
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="azure-signalr-service-limits"></a>Limites de SignalR Service do Azure
[!INCLUDE [signalr-service-limits](../includes/signalr-service-limits.md)]

### <a name="site-recovery-limits"></a>Limites da Recuperação de Site
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="application-insights-limits"></a>Limites do Application Insights
[!INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### <a name="api-management-limits"></a>Limites de Gerenciamento de API
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-cache-for-redis-limits"></a>Limites do Cache Redis do Azure
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Limites do Cofre da Chave
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication-limits"></a>Limitações da autenticação multifatores
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Limites de automação
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="identity-manager-limits"></a>Limites do Identity Manager
[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

### <a name="role-based-access-control-limits"></a>Limites de controle de acesso baseado em função
[!INCLUDE [role-based-access-control-limits](../includes/role-based-access-control-limits.md)]

### <a name="sql-database-limits"></a>Limites de banco de dados SQL
Para limites de banco de dados SQL, consulte [limites de recursos do banco de dados SQL para bancos de dados individuais](sql-database/sql-database-vcore-resource-limits-single-databases.md), [limites de recursos do banco de dados SQL para pools Elásticos e bancos de dados em pool](sql-database/sql-database-vcore-resource-limits-elastic-pools.md), e [limites de recursos do banco de dados SQL para instâncias gerenciadas](sql-database/sql-database-managed-instance-resource-limits.md).

### <a name="sql-data-warehouse-limits"></a>Limites do SQL Data Warehouse
Para obter os limites do SQL Data Warehouse, consulte [limites de recursos do SQL Data Warehouse](sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="see-also"></a>Consulte também
- [Entender os aumentos e limites do Azure](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
- [Tamanhos de serviço de nuvem e máquina virtual para o Azure](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Tamanhos para serviços de nuvem do Azure](cloud-services/cloud-services-sizes-specs.md)
