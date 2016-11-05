---
title: Documentação do Azure Governamental | Microsoft Docs
description: Este guia fornece uma comparação dos recursos e orientações sobre como desenvolver aplicativos para o Azure Government
services: Azure-Government
cloud: gov
documentationcenter: ''
author: scooxl
manager: zakramer
editor: ''

ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/05/2016
ms.author: scooxl

---
# <a name="azure-government-management-and-security"></a>Segurança e gerenciamento do Azure Governamental
## <a name="key-vault"></a>Cofre da Chave
Para obter detalhes sobre esse serviço e como usá-lo, consulte a <a href="https://azure.microsoft.com/documentation/services/key-vault">documentação pública do Cofre de Chaves do Azure</a>.

### <a name="data-considerations"></a>Considerações de dados
As informações a seguir identificam o limite do Azure Governamental para o Cofre de Chaves do Azure:

| Dados regulamentados/controlados permitidos | Dados regulamentados/controlados não permitidos |
| --- | --- |
| Todos os dados criptografados com uma chave do Cofre de Chaves do Azure podem conter dados regulamentados/controlados. |Metadados do Cofre de Chaves do Azure não são permitidos para conter dados de exportação controlados. Esses metadados incluem todos os dados de configuração inseridos durante a criação e manutenção de seu Cofre de Chaves.  Não insira dados Regulamentados/controlados nos seguintes campos: Nomes de grupo de recursos, Nomes de Cofre de Chaves, Nome de assinatura |

O Cofre de Chaves está disponível no Azure Governamental. Assim como no público, não há qualquer extensão, portanto o Cofre de Chaves só está disponível por meio do PowerShell e da CLI.

## <a name="log-analytics"></a>Log Analytics
O Log Analytics está disponível no Azure Governamental. 

### <a name="differences-from-public-azure"></a>Diferenças do Azure público
Os recursos e as soluções do Log Analytics a seguir não estão disponíveis atualmente no Azure Governamental. Essa lista é atualizada quando o status dos recursos/das soluções muda.

* Soluções que estão na visualização no Azure público, incluindo:
  * Solução de monitoramento de rede
  * Solução de Análise de Rede do Azure
  * Solução do Office 365
  * Solução de Análise de Atualização do Windows 10
  * Monitoramento da Dependência de Aplicativos
  * Application Insights
  * Logs de atividade do Azure
  * Análise de automação do Azure
  * Análise do Cofre de Chaves
* Soluções e recursos que exigem a Automação do Azure, incluindo:
  * Gerenciamento de atualizações
  * Gerenciamento de alterações
  * Alertas que disparam um runbook de automação do Azure
* Soluções e recursos que exigem atualizações de software local, incluindo
  * Integração com o System Center Operations Manager 2016
  * Grupos de computadores do System Center Configuration Manager
  * Solução do Surface Hub
* Recursos que estão na visualização do Azure público, incluindo
  * Exportação de dados para o Power BI
* Integração ao portal do Azure
  * A seleção de contas de armazenamento do Azure para serem monitoradas deve ser feita pelo PowerShell ou por modelos do Resource Manager
  * A seleção de máquinas virtuais para habilitar o agente do Log Analytics deve ser feita pelo PowerShell ou por modelos do Resource Manager
  * Métricas do Azure e Diagnóstico do Azure
* Aplicativos móveis do OMS
* Extensão de VM do agente Linux OMS
* Dados de uso

Os seguintes recursos do Log Analytics têm um comportamento diferente no Azure Governamental:

* O agente do Windows deve ser baixado no portal do [Log Analytics](https://oms.microsoft.us) para o Azure Governamental.
* O upload de dados usando a API do coletor de dados requer o uso da URL do Azure Government, https://*workspaceId*.ods.opinsights.azure.us onde *workspaceId* é a ID do espaço de trabalho do portal do OMS. 
* Para conectar seu servidor de gerenciamento do System Center Operations Manager ao Log Analytics, é preciso baixar e importar pacotes de gerenciamento atualizados.
  1. Baixe e salve os [pacotes de gerenciamento atualizados](http://go.microsoft.com/fwlink/?LinkId=828749)
  2. Descompacte o arquivo que você baixou
  3. Importe os pacotes de gerenciamento no Operations Manager. Para obter informações sobre como importar um pacote de gerenciamento de um disco, consulte o tópico [How to Import an Operations Manager Management Pack](http://technet.microsoft.com/library/hh212691.aspx) no site Microsoft TechNet.
  4. Para conectar o Operations Manager ao Log Analytics, siga as etapas em [Conectar o Operations Manager ao Log Analytics](../log-analytics/log-analytics-om-agents.md) 

### <a name="frequently-asked-questions"></a>Perguntas frequentes
* Posso migrar dados do Log Analytics no Azure público para o Azure Governamental?
  * Não. Não é possível mover os dados ou seu espaço de trabalho do Azure público para o Azure Governamental.
* Posso alternar entre espaços de trabalho do Azure público e do Azure Governamental no portal do Log Analytics do OMS?
  * Não. Os portais do Azure público e do Azure Governamental são separados e não compartilham informações. 

Para saber mais, confira [Documentação pública do Log Analytics](../log-analytics/log-analytics-overview.md).

## <a name="next-steps"></a>Próximas etapas
Para obter informações complementares e atualizações, assine o <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog do Microsoft Azure Governamental. </a>

<!--HONumber=Oct16_HO2-->


