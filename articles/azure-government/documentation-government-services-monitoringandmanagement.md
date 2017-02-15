---
title: Gerenciamento + monitoramento do Azure Governamental | Microsoft Docs
description: "Este guia oferece uma comparação das funcionalidades e das orientações sobre como desenvolver aplicativos para o Azure Governamental."
services: Azure-Government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
editor: 
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/31/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 3e460dd2aaf51ef2ba9840513d236ce313320c80
ms.openlocfilehash: 39a87bdbef7c532f9e50f1e71e9c12797bc9a33c


---
# <a name="azure-government-monitoring--management"></a>Gerenciamento + monitoramento do Azure Governamental
Este artigo descreve as variações e as considerações de serviços para o ambiente do Azure Governamental.

## <a name="automation"></a>Automação
A Automação está geralmente disponível no Azure Governamental.

### <a name="variations"></a>Variações
Atualmente, os seguintes recursos da Automação não estão disponíveis no Azure Governamental.

* Criação de uma credencial de Princípio de Serviço para autenticação

Para saber mais, veja [documentação pública da Automação](../automation/automation-intro.md).

## <a name="backup"></a>Backup
O backup está totalmente disponível no Azure Governamental.

Para saber mais, confira [Backup do Azure Governamental](documentation-government-services-backup.md).

### <a name="variations"></a>Variações
Atualmente, os seguintes recursos de Backup não estão disponíveis no Azure Governamental:

* Cofres do Azure Resource Manager
* Gerenciamento usando o Portal do Azure (há suporte para o Portal Clássico do Azure)

As URLs do Backup são diferentes no Azure Governamental:

## <a name="site-recovery"></a>Site Recovery
O Site Recovery (ASR) está totalmente disponível no Azure Governamental.

Para saber mais, confira [Documentação pública do Site Recovery](../site-recovery/site-recovery-overview.md).

### <a name="variations"></a>Variações
Atualmente, os seguintes recursos do Site Recovery não estão disponíveis no Azure Governamental:

* Cofres de recuperação de site do Azure Resource Manager

| Site Recovery | Clássico | Gerenciador de Recursos | 
| --- | --- | --- |
| VMWare/Físico  | GA | Planejado |
| Hyper-V | GA | Planejado |
| Site a Site | GA | Planejado |

Observações: a Tabela se aplica a VA e Iowa. 

As seguintes URLs do ASR são diferentes no Azure Governamental:

| Público do Azure | Azure Government | Observações |
| --- | --- | --- |
| *.hypervrecoverymanager.windowsazure.com | *.hypervrecoverymanager.windowsazure.us | Acesso ao serviço Site Recovery |
| *. backup.windowsazure.com  | *.backup.windowsazure.us | Acesso ao serviço de proteção |
| *.blob.core.windows.net | *.blob.core.usgovcloudapi.net | Para armazenar instantâneos da VM |
| http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi | http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi | Para baixar o MySQL |



## <a name="log-analytics"></a>Log Analytics
O Log Analytics está disponível no Azure Governamental.

### <a name="variations"></a>Variações
Os recursos e as soluções do Log Analytics a seguir não estão disponíveis atualmente no Azure Governamental.

* As soluções que estão em visualização no Microsoft Azure, incluindo:
  * Solução de monitoramento de rede
  * Solução de Monitoramento da Dependência de Aplicativos
  * Solução do Office 365
  * Solução de Análise de Atualização do Windows 10
  * Solução do Application Insights
  * Solução de Análise de Rede do Azure
  * Solução da Análise da Automação do Azure
  * Solução da Análise do Cofre de Chaves
* Soluções e funcionalidades que exigem atualizações de software local, incluindo:
  * Integração com o System Center Operations Manager 2016 (há suporte para versões anteriores do Operations Manager)
  * Grupos de computadores do System Center Configuration Manager
  * Solução do Surface Hub
* Funcionalidades que estão em visualização do Azure público, incluindo:
  * Exportação de dados para o Power BI
* Métricas do Azure e Diagnóstico do Azure
* Aplicativos móveis do Operations Management Suite

As URLs do Log Analytics são diferentes no Azure Governamental:

| Público do Azure | Azure Government | Observações |
| --- | --- | --- |
| mms.microsoft.com |oms.microsoft.us |Portal do Log Analytics |
| *workspaceId*.ods.opinsights.azure.com |*workspaceId*.ods.opinsights.azure.us |[API do coletor de dados](../log-analytics/log-analytics-data-collector-api.md) |
| \*.ods.opinsights.azure.com |\*.ods.opinsights.azure.us |Comunicação do agente - [definindo as configurações de firewall](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.oms.opinsights.azure.com |\*.oms.opinsights.azure.us |Comunicação do agente - [definindo as configurações de firewall](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.blob.core.windows.net |\*.blob.core.usgovcloudapi.net |Comunicação do agente - [definindo as configurações de firewall](../log-analytics/log-analytics-proxy-firewall.md) |

As seguintes funcionalidades do Log Analytics se têm um comportamento diferente no Azure Governamental:

* O Agente do Windows deve ser baixado do [portal do Log Analytics](https://oms.microsoft.us) para o Azure Governamental.
* Para conectar seu servidor de gerenciamento do System Center Operations Manager ao Log Analytics, é preciso baixar e importar pacotes de gerenciamento atualizados.
  1. Baixe e salve os [pacotes de gerenciamento atualizados](http://go.microsoft.com/fwlink/?LinkId=828749).
  2. Descompacte o arquivo que você baixou.
  3. Importe os pacotes de gerenciamento no Operations Manager. Para saber mais sobre como importar um pacote de gerenciamento de um disco, veja o tópico [Como importar um pacote de gerenciamento do Operations Manager](http://technet.microsoft.com/library/hh212691.aspx) no site do Microsoft TechNet.
  4. Para conectar o Operations Manager ao Log Analytics, siga as etapas em [Conectar o Operations Manager ao Log Analytics](../log-analytics/log-analytics-om-agents.md).

### <a name="frequently-asked-questions"></a>Perguntas frequentes
* Posso migrar dados do Log Analytics no Microsoft Azure para o Azure Governamental?
  * Não. Não é possível mover os dados ou seu espaço de trabalho do Microsoft Azure para o Azure Governamental.
* Posso alternar entre os espaços de trabalho do Microsoft Azure e do Azure Governamental no portal do Log Analytics do Operations Management Suite?
  * Não. Os portais do Microsoft Azure e do Azure Governamental são separados e não compartilham informações.

Para saber mais, confira [Documentação pública do Log Analytics](../log-analytics/log-analytics-overview.md).

## <a name="site-recovery"></a>Recuperação de Site
O Site Recovery está totalmente disponível no Azure Governamental.

Para saber mais, confira [Documentação pública do Site Recovery](../site-recovery/site-recovery-overview.md).

### <a name="variations"></a>Variações
Atualmente, os seguintes recursos do Site Recovery não estão disponíveis no Azure Governamental:

* Cofres de recuperação de site do Azure Resource Manager

## <a name="next-steps"></a>Próximas etapas
Para obter informações complementares e atualizações, assine o <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog do Microsoft Azure Governamental. </a>



<!--HONumber=Dec16_HO2-->


