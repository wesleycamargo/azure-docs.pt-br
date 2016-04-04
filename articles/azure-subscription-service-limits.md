<properties
	pageTitle="Assinatura do Microsoft Azure e limites de serviços, cotas e restrições"
	description="Fornece uma lista de assinaturas comuns do Azure e limites de serviço, cotas e restrições. Isso inclui informações sobre como aumentar os limites junto com os valores máximos."
	services=""
	documentationCenter=""
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/07/2016"
	ms.author="jroth"/>

# Assinatura do Azure e limite de serviços, cotas e restrições

## Visão geral

Este documento especifica alguns dos limites mais comuns do Microsoft Azure. Observe que isso não cobre atualmente todos os serviços do Azure. Com o tempo, esses limites serão ampliados e atualizados para uma maior cobertura da plataforma.

> [AZURE.NOTE] Se você deseja aumentar o limite acima do **Limite Padrão**, você pode [abrir uma solicitação no suporte ao cliente online sem custo](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). Os limites não podem ser aumentados acima do valor de **Limite máximo** nas tabelas abaixo. Se não houver nenhuma coluna **Limite máximo**, o recurso especificado não tem limites ajustáveis.

## Limites e o Gerenciador de Recursos do Azure

Agora é possível combinar vários recursos do Azure em um único Grupo de Recursos do Azure. Ao usar os Grupos de Recursos, limites que antes eram globais passam a ser gerenciados em nível regional com o Gerenciador de Recursos do Azure. Para obter mais informações sobre os Grupos de Recursos do Azure, consulte [Usando grupos de recursos para gerenciar recursos do Azure](azure-portal/resource-group-portal.md).

Nos limites abaixo, uma nova tabela foi adicionada para refletir quaisquer diferenças nos limites ao usar o Gerenciador de Recursos do Azure. Por exemplo, há uma tabela de **Limites de Assinatura** e uma tabela de **Limites de Assinatura – Gerenciador de Recursos do Azure**. Quando um limite se aplica a ambos os cenários, ele é mostrado apenas na primeira tabela. A menos que indicado de outro modo, os limites são globais em todas as regiões.

> [AZURE.NOTE] É importante enfatizar que as cotas para recursos nos Grupos de Recursos do Azure são acessíveis de acordo com a região pela assinatura e não de acordo com a assinatura, assim como acontece com as cotas de gerenciamento de serviço. Vamos usar cotas de núcleos como exemplo. Se você precisar solicitar um aumento de cota com suporte para núcleos, você precisa decidir quantos núcleos deseja usar em quais regiões e, em seguida, fazer uma solicitação específica de cotas de núcleos do Grupo de Recursos do Azure para as quantidades e regiões desejadas. Portanto, se precisar usar 30 núcleos na Europa Ocidental para executar seu aplicativo lá, você deve solicitar especificamente 30 núcleos na Europa Ocidental. Mas você não terá um aumento na cota de núcleos em nenhuma outra região – somente a Europa Ocidental terá a cota de 30 núcleos.
<!-- -->
Como resultado, pode ser útil pensar em decidir quais devem ser as cotas do Grupo de Recursos do Azure para a carga de trabalho em determinada região e solicitar essa quantidade em cada região na qual esteja considerando a possibilidade de implantação. Consulte [Solucionando problemas de implantação](resource-group-deploy-debug.md##authentication-subscription-role-and-quota-issues) para obter mais ajuda ao descobrir suas cotas atuais para regiões específicas.


## Limites específicos de serviço

- [Active Directory](#active-directory-limits)
- [Gerenciamento da API](#api-management-limits)
- [Serviço de Aplicativo](#app-service-limits)
- [Application Insights](#application-insights-limits)
- [Cache Redis do Azure](#azure-redis-cache-limits)
- [RemoteApp do Azure](#azure-remoteapp-limits)
- [Backup](#backup-limits)
- [Batch](#batch-limits)
- [Serviços do BizTalk](#biztalk-services-limits)
- [CDN](#cdn-limits)
- [Serviços de Nuvem](#cloud-services-limits)
- [Fábrica de dados](#data-factory-limits)
- [Análises Data Lake](#data-lake-analytics-limits)
- [DNS](#dns-limits)
- [Banco de Dados de Documentos](#documentdb-limits)
- [Hub IoT](#iot-hub-limits)
- [Cofre da Chave](#key-vault-limits)
- [Serviços de Mídia](#media-services-limits)
- [Mobile Engagement](#mobile-engagement-limits)
- [Serviços Móveis](#mobile-services-limits)
- [Multi-Factor Authentication](#multi-factor-authentication)
- [Rede](#networking-limits)
- [Serviço de hub de notificação](#notification-hub-service-limits)
- [Insights Operacionais](#operational-insights-limits)
- [Grupo de recursos](#resource-group-limits)
- [Agendador](#scheduler-limits)
- [Pesquisar](#search-limits)
- [Barramento de Serviço](#service-bus-limits)
- [Recuperação de Site](#site-recovery-limits)
- [Banco de Dados SQL](#sql-database-limits)
- [Armazenamento](#storage-limits)
- [Sistema StorSimple](#storsimple-system-limits)
- [Análise de fluxo](#stream-analytics-limits)
- [Assinatura](#subscription-limits)
- [Gerenciador de Tráfego](#traffic-manager-limits)
- [Máquinas virtuais](#virtual-machines-limits)


### Limites de assinatura
#### Limites de assinatura
[AZURE.INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### Limites de Assinatura – Gerenciador de Recursos do Azure

Os limites a seguir se aplicam ao usar o Gerenciador de Recursos do Azure e os Grupos de Recursos do Azure. Limites que não foram alterados com o Gerenciador de Recursos do Azure não estão listados abaixo. Consulte a tabela anterior para obter esses limites.

[AZURE.INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]


### Limites do Grupo de Recursos

[AZURE.INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]


### Limites de Máquinas virtuais
#### Limites de Máquina virtual
[AZURE.INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]


#### Limites de Máquinas Virtuais – Gerenciador de Recursos do Azure

Os limites a seguir se aplicam ao usar o Gerenciador de Recursos do Azure e os Grupos de Recursos do Azure. Limites que não foram alterados com o Gerenciador de Recursos do Azure não estão listados abaixo. Consulte a tabela anterior para obter esses limites.

[AZURE.INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]


### Limites de rede

[AZURE.INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### Limites de rede
[AZURE.INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### Limites do Gerenciador de Tráfego

[AZURE.INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### Limites de DNS

[AZURE.INCLUDE [dns-limits](../includes/dns-limits.md)]

### Limites de armazenamento

Para obter mais detalhes sobre os limites da conta de armazenamento, veja [Metas de desempenho e escalabilidade do Armazenamento do Azure](../articles/storage/storage-scalability-targets.md).

#### Limites de Serviço de Armazenamento

[AZURE.INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

#### Limites de Disco de Máquina Virtual

[AZURE.INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

Consulte [Tamanhos de máquina virtual](../articles/virtual-machines/virtual-machines-linux-sizes.md) para saber mais detalhes.

**Contas de armazenamento padrão**

[AZURE.INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

**Contas de armazenamento Premium**

[AZURE.INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

#### Limites de Provedor de Recursos de Armazenamento

[AZURE.INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]


### Limites de Serviços de Nuvem

[AZURE.INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]


### Limites do Serviço de Aplicativo
Os limites de Serviço de Aplicativo a seguir incluem limites para Aplicativos Web, Aplicativos Móveis, Aplicativos de API e Aplicativos Lógicos.

[AZURE.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### Limites do Agendador

[AZURE.INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### Limites de lote

[AZURE.INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

###Limites dos Serviços BizTalk
A tabela a seguir mostra os limites para os serviços Biztalk do Azure.

[AZURE.INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]


### Limites do Banco de Dados de Documentos

[AZURE.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]


### Limites do Mobile Engagement

[AZURE.INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]


### Limites de pesquisa

Os tipos de preço determinam a capacidade e os limites de seu serviço de pesquisa. Eles incluem:

- **Gratuito** serviço multilocatário, compartilhado com outros assinantes do Azure, destinado à avaliação e a pequenos projetos de desenvolvimento.
- **Básico (Preview)** fornece recursos de computação dedicados para cargas de trabalho de produção em uma escala menor. Atualmente, esse tipo está em Preview e é oferecido por uma taxa reduzida.
- **Standard (S1 e S2)** destina-se a cargas de trabalho de produção. Uma versão com capacidade maior (**S2**) está disponível mediante solicitação (envie um email para azuresearch_contact@microsoft.com).

[AZURE.INCLUDE [azure-search-limits-all](../includes/azure-search-limits-all.md)]

Para saber mais sobre outros limites, como tamanho do documento, chaves, solicitações e respostas, veja [Limites de serviço na Pesquisa do Azure](search/search-limits-quotas-capacity.md).

### Limites de Serviços de Mídia

[AZURE.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### Limites de CDN

[AZURE.INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### Limites de Serviços Móveis

[AZURE.INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### Limites de serviço de hub de notificação

[AZURE.INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]


### Limites de Barramento de Serviço

[AZURE.INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### Limites do Hub IoT

[AZURE.INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### Limites de fábrica de dados

[AZURE.INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### Limites da Análise Data Lake
[AZURE.INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### Limites do Stream Analytics

[AZURE.INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### Limites do Active Directory

[AZURE.INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]


### Limites do Azure RemoteApp

[AZURE.INCLUDE [azure-remoteapp-limits](../includes/azure-remoteapp-limits.md)]

### Limites do sistema StorSimple

[AZURE.INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]


### Limites dos Insights Operacionais

[AZURE.INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### Limites do Backup

[AZURE.INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### Limites da Recuperação de Site

[AZURE.INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### Limites do Application Insights

[AZURE.INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### Limites de Gerenciamento de API

[AZURE.INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### Limites do Cache Redis do Azure

[AZURE.INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### Limites do Cofre da Chave

[AZURE.INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### Multi-Factor Authentication
[AZURE.INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### Limites de banco de dados SQL

Para obter os limites do Banco de Dados SQL, veja [Limites de recurso de Banco de Dados SQL](sql-database/sql-database-resource-limits.md).

## Consulte também

[Entendendo os limites e aumentos do Azure](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Tamanhos de máquinas virtuais e serviços de nuvem do Azure](virtual-machines/virtual-machines-linux-sizes.md)

[Tamanhos dos serviços de nuvem](cloud-services/cloud-services-sizes-specs.md)

<!---HONumber=AcomDC_0323_2016-->