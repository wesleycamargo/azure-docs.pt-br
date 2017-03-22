---
title: "Serviços disponíveis do Azure Governamental | Microsoft Docs"
description: "Oferece uma visão geral dos serviços disponíveis no Azure Governamental"
services: azure-government
cloud: gov
documentationcenter: 
author: smichelotti
manager: liki
ms.assetid: a453a23c-bc0f-4203-9075-0f579dea7e23
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: stemi
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 21c2a0faad87b84058093f02c831b374a644b4b6
ms.lasthandoff: 03/06/2017


---
# <a name="available-services-on-azure-government"></a>Serviços disponíveis no Azure Governamental
O Azure Governamental está se expandindo continuamente os serviços disponíveis.  Esses serviços são implantados usando o mesmo código usado no Azure Público.  Esta seção documenta os serviços disponíveis atualmente no Azure Governamental, incluindo dois tipos principais de informação:

* **Variações:** variações que ocorrem devido a funcionalidades que ainda não foram implantadas ou propriedades (por exemplo URLs) exclusivas do ambiente governamental.  
* **Considerações:** detalhes de implementação específicos ao governo para garantir que os dados permanecem no limite de conformidade.

Tudo o mais que você precisa saber sobre esses serviços pode ser encontrado em sua documentação geral.

Para obter a lista mais atual dos serviços, consulte [Produtos por região](https://azure.microsoft.com/regions/services/). 

Nas tabelas a seguir, os serviços especificados como habilitados o Resource Manager têm provedores de recursos e podem ser gerenciados com o PowerShell. Para obter informações detalhadas sobre provedores do Resource Manager, versões de API e esquemas, clique [aqui](../azure-resource-manager/resource-manager-supported-services.md). Os serviços especificados como disponíveis no Portal, podem ser gerenciados no [Portal do Azure Governamental](https://portal.azure.us/). 


## <a name="computedocumentation-government-computemd"></a>[Computação](documentation-government-compute.md)

| O Barramento de | Gerenciador de Recursos habilitado | Portal |
| --- | --- | --- |
| [Máquinas virtuais](documentation-government-compute.md#virtual-machines) | Sim | Sim |
| Batch | Sim | Sim |
| Serviços de Nuvem | Sim | Sim |
| Service Fabric | Sim | Sim |
| Conjuntos de Dimensionamento da VM | Sim | Sim |


## <a name="networkingdocumentation-government-networkingmd"></a>[Rede](documentation-government-networking.md)

| O Barramento de | Gerenciador de Recursos habilitado | Portal |
| --- | --- | --- |
| [ExpressRoute](documentation-government-networking.md#expressroute-private-connectivity) | Sim | Sim |
| Rede Virtual | Sim | Sim |
| [Balanceador de carga](documentation-government-networking.md#support-for-load-balancer) | Sim | Sim |
| [Gerenciador de Tráfego](documentation-government-networking.md#support-for-traffic-manger) | Sim | Sim |
| [Gateway de VPN](documentation-government-networking.md#support-for-vpn-gateway) | Sim | Sim |
| Application Gateway | Sim | Sim |
| ExpressRoute | Sim | Sim |



## <a name="storagedocumentation-government-services-storagemd"></a>[Armazenamento](documentation-government-services-storage.md)

| O Barramento de | Gerenciador de Recursos habilitado | Portal |
| --- | --- | --- |
| [Armazenamento - Blobs](documentation-government-services-storage.md#azure-storage) | Sim | Sim |
| [Armazenamento - Tabelas](documentation-government-services-storage.md#azure-storage) | Sim | Sim |
| [Armazenamento - Filas](documentation-government-services-storage.md#azure-storage) | Sim | Sim |
| [Armazenamento - Arquivos](documentation-government-services-storage.md#azure-storage) | Sim | Sim |
| [Armazenamento - Discos](documentation-government-services-storage.md#azure-storage) | Sim | Sim |
| [StorSimple](documentation-government-services-storage.md) | Sim | Sim |
| [Backup](documentation-government-services-storage.md#azure-storage) | Sim | Sim |
| [Recuperação de Site](documentation-government-services-storage.md#azure-storage) | Sim | Sim |
| [Importar/exportar](documentation-government-services-storage.md#azure-storage) | Sim | Não |



## <a name="web--mobiledocumentation-government-services-webandmobilemd"></a>[Web + Celular](documentation-government-services-webandmobile.md)

| O Barramento de | Gerenciador de Recursos habilitado | Portal |
| --- | --- | --- |
| [Serviço de Aplicativo - Aplicativos Web](documentation-government-services-webandmobile.md#app-services) | Sim | Sim |
| [Serviço de Aplicativo - Aplicativo de API](documentation-government-services-webandmobile.md#app-services) | Sim | Sim |
| [Serviço de Aplicativo - Aplicativos Móveis](documentation-government-services-webandmobile.md#app-services) | Sim | Sim |
| Serviços de mídia | Sim | Sim |


## <a name="databasesdocumentation-government-services-databasemd"></a>[Bancos de dados](documentation-government-services-database.md)

| O Barramento de | Gerenciador de Recursos habilitado | Portal |
| --- | --- | --- |
| [Banco de Dados SQL](documentation-government-services-database.md#sql-database) | Sim | Sim |
| SQL Data Warehouse | Sim | Sim |
| SQL Server Stretch Database | Sim | Sim |
| [Cache Redis](documentation-government-services-database.md#azure-redis-cache) | Sim | Sim |


## <a name="intelligence--analyticsdocumentation-government-services-intelligenceandanalyticsmd"></a>[Inteligência + Análise](documentation-government-services-intelligenceandanalytics.md)

| O Barramento de | Gerenciador de Recursos habilitado | Portal |
| --- | --- | --- |
| [HDInsights](documentation-government-services-intelligenceandanalytics.md#hdinsight) | Sim | Sim |
| [Power BI Pro](documentation-government-services-intelligenceandanalytics.md#power-bi) | Não | Não (Portal de administração do Office 365) |


## <a name="internet-of-things-iot"></a>Internet das coisas (IoT)

| O Barramento de | Gerenciador de Recursos habilitado | Portal |
| --- | --- | --- |
| Hubs de evento | Sim | Sim |
| Hubs de Notificação | Não | Não (acesse [Portal herdado](https://manage.windowsazure.us/)) |


## <a name="enterprise-integration"></a>Integração Empresarial

| O Barramento de | Gerenciador de Recursos habilitado | Portal |
| --- | --- | --- |
| Barramento de Serviço | Sim | Sim |
| [StorSimple](documentation-government-services-storage.md) | Sim | Sim |
| SQL Server Stretch Database | Sim | Sim |



## <a name="security--identitydocumentation-government-services-securityandidentitymd"></a>[Segurança + Identidade](documentation-government-services-securityandidentity.md)

| O Barramento de | Gerenciador de Recursos habilitado | Portal |
| --- | --- | --- |
| Azure Active Directory | Sim | Sim |
| [Cofre de Chaves](documentation-government-services-securityandidentity.md#key-vault) | Sim | Não (em breve) |
| Autenticação Multifator | Sim | Sim |


## <a name="intelligence--analytics"></a>Inteligência + Análises

| O Barramento de | Gerenciador de Recursos habilitado | Portal |
| --- | --- | --- |
| Power BI | Sim | Não |
| HDInsight | Sim | Sim |



## <a name="monitoring--managementdocumentation-government-services-monitoringandmanagementmd"></a>[Monitoramento + Gerenciamento](documentation-government-services-monitoringandmanagement.md)

| O Barramento de | Gerenciador de Recursos habilitado | Portal |
| --- | --- | --- |
| [Automação](documentation-government-services-monitoringandmanagement.md#automation) | Sim | Sim |
| [Backup](documentation-government-services-backup.md) | Sim | Sim |
| [Log Analytics](documentation-government-services-monitoringandmanagement.md#log-analytics) | Sim | Sim |
| [Recuperação de Site](documentation-government-services-monitoringandmanagement.md#site-recovery) | Sim | Sim |
| Agendador | Sim | Não |
| Monitoramento e diagnóstico | Sim | Sim |




## <a name="next-steps"></a>Próximas etapas
Para obter informações complementares e atualizações, assine o [Blog do Microsoft Azure Governamental](https://blogs.msdn.microsoft.com/azuregov/).


