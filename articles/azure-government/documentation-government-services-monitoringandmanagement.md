---
title: Gerenciamento + monitoramento do Azure Governamental | Microsoft Docs
description: "Este guia oferece uma comparação das funcionalidades e das orientações sobre como desenvolver aplicativos para o Azure Governamental."
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 1/13/2017
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: ec62cc79aeffa21e5d9d772dcd2da6f973c18d81
ms.openlocfilehash: 3d9f2308d20e723da324be7e3aec3106ff5ff846
ms.lasthandoff: 01/18/2017


---
# <a name="azure-government-monitoring--management"></a>Gerenciamento + monitoramento do Azure Governamental
Este artigo descreve as variações e as considerações de serviços para o ambiente do Azure Governamental.

## <a name="automation"></a>Automação
A Automação está geralmente disponível no Azure Governamental.

### <a name="variations"></a>Variações
Atualmente, os seguintes recursos da Automação não estão disponíveis no Azure Governamental.

* Criação de uma credencial de Entidade de Serviço para autenticação

Para saber mais, veja [documentação pública da Automação](../automation/automation-intro.md).

## <a name="backup"></a>Backup
O backup está totalmente disponível no Azure Governamental.

Para saber mais, confira [Backup do Azure Governamental](documentation-government-services-backup.md).

## <a name="site-recovery"></a>Recuperação de Site
O Site Recovery (ASR) está totalmente disponível no Azure Governamental.

Para saber mais, confira [Documentação pública do Site Recovery](../site-recovery/site-recovery-overview.md).

### <a name="variations"></a>Variações
Atualmente, os seguintes recursos do Site Recovery não estão disponíveis no Azure Governamental:

* Cofres de recuperação de site do Azure Resource Manager
* Notificação por email

| Recuperação de Site | Clássico | Gerenciador de Recursos |
| --- | --- | --- |
| VMWare/Físico  | GA | GA |
| Hyper-V | GA | GA |
| Site a Site | GA | GA |

>[!NOTE]
>A tabela se aplica ao Gov. EUA - Virgínia e ao Gov. EUA - Iowa.

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
  * Mapa do Serviço
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

## <a name="next-steps"></a>Próximas etapas
Para obter informações complementares e atualizações, assine o <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog do Microsoft Azure Governamental. </a>

