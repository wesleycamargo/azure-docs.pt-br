---
title: Log Analytics para provedores de serviços | Microsoft Docs
description: O Log Analytics pode ajudar Provedores de Serviços Gerenciados (MSPs), grandes empresas, Fornecedores Independentes de Software (ISVs) e provedores de serviço de hospedagem a gerenciar e monitorar servidores na infraestrutura de nuvem ou no local do cliente.
services: log-analytics
documentationcenter: ''
author: MeirMen
manager: jochan
editor: ''
ms.assetid: c07f0b9f-ec37-480d-91ec-d9bcf6786464
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: meirm
ms.openlocfilehash: ac34cc467cc8ff7d231551683da130c0f7565800
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53340531"
---
# <a name="log-analytics-for-service-providers"></a>Log Analytics para provedores de serviços
O Log Analytics pode ajudar provedores de serviços gerenciados (MSPs), grandes empresas, fornecedores de software independentes (ISVs) e provedores de serviço de hospedagem a gerenciar e monitorar servidores no local do cliente ou na infraestrutura de nuvem. 

Grandes empresas compartilham várias semelhanças com provedores de serviços, especialmente quando há uma equipe de TI centralizada que é responsável por gerenciar a TI para muitas unidades de negócios diferentes. Para simplificar, este documento usa o termo *provedor*, mas a mesma funcionalidade também está disponível para empresas e outros clientes.

Para parceiros e provedores de serviços que fazem parte do programa [Provedor de Soluções na Nuvem (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview), o Log Analytics é um dos serviços do Azure disponíveis nas [assinaturas do Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview). 

## <a name="architectures-for-service-providers"></a>Arquiteturas para provedores de serviços

Os workspaces do Log Analytics fornecem um método para o administrador controlar o fluxo e o isolamento dos logs e criar uma arquitetura de log que atende às suas necessidades de negócios específicas. [Este artigo](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access) explica as considerações gerais sobre o gerenciamento de workspace. Os provedores de serviços têm considerações adicionais.

Há três arquiteturas possíveis para os provedores de serviços em relação aos workspaces do Log Analytics:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. Distribuído – os logs são armazenados em workspaces localizados no locatário do cliente 

Nesta arquitetura, um espaço de trabalho é implantado no locatário do cliente que é usado para todos os logs desse cliente. Os administradores do provedor de serviços recebem acesso a esse workspace usando [os usuários convidados do Azure Active Directory (B2B)](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b). Os administradores do provedor de serviços precisarão alternar para o diretório do cliente no portal do Azure para conseguir acessar esses espaços de trabalho.

As vantagens dessa arquitetura são:
* O cliente pode gerenciar o acesso aos logs usando seu próprio [acesso baseado em função](https://docs.microsoft.com/azure/role-based-access-control/overview).
* Cada cliente pode ter configurações diferentes para seu workspace, como retenção e limitação de dados.
* Isolamento entre os clientes para fins de regulamentação e conformidade.
* O encargo de cada workspace será distribuído para a assinatura do cliente.
* Os logs podem ser coletados de todos os tipos de recursos, não apenas ser baseados em agente. Por exemplo, os Logs de Auditoria do Azure.

As desvantagens dessa arquitetura são:
* É mais difícil para o provedor de serviços gerenciar um grande número de locatários de clientes ao mesmo tempo.
* Os administradores do provedor de serviços devem estar provisionados no diretório do cliente.
* O provedor de serviços não pode analisar os dados em seus clientes.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. Central – os logs são armazenados em um espaço de trabalho localizado no locatário do provedor de serviços

Nessa arquitetura, os logs não são armazenados nos locatários do cliente, mas apenas em um local central em uma das assinaturas do provedor de serviços. Os agentes que estão instalados nas VMs do cliente são configurados para enviar seus logs para esse workspace usando a ID de workspace e a chave secreta.

As vantagens dessa arquitetura são:
* É fácil gerenciar um grande número de clientes e integrá-los aos vários sistemas de back-end.
* O provedor de serviços tem propriedade total sobre os logs e os vários artefatos, como funções e consultas salvas.
* O provedor de serviços pode executar a análise em todos os clientes.

As desvantagens dessa arquitetura são:
* Essa arquitetura é aplicável somente a dados de VM baseados em agente; ela não aborda fontes de dados de PaaS, SaaS e de malha do Azure.
* Talvez seja difícil separar os dados entre os clientes quando eles são mesclados em um único workspace. O único método satisfatório para fazer isso é usar o FQDN (nome de domínio totalmente qualificado) do computador ou por meio da ID da assinatura do Azure. 
* Todos os dados de todos os clientes serão armazenados na mesma região com uma única fatura e as mesmas definições de retenção e configuração.
* Os serviços de PaaS e de malha do Azure, como o Diagnóstico do Azure e os Logs de Auditoria do Azure, exigem que o espaço de trabalho esteja no mesmo locatário do recurso; portanto, eles não podem enviar os logs para o espaço de trabalho central.
* Todos os agentes de VM de todos os clientes serão autenticados no workspace central usando a mesma ID de workspace e chave. Não há nenhum método para bloquear os logs de um cliente específico sem interromper outros clientes.


### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. Híbrido – os logs são armazenados no workspace localizado no locatário do cliente e alguns deles são enviados para um local central.

A terceira combinação de arquitetura entre as duas opções. Ela se baseia na primeira arquitetura distribuída, na qual os logs são locais para cada cliente, mas usando um mecanismo para criar um repositório central de logs. Uma parte dos logs é inserida em um local central para relatórios e análise. Essa parte pode ser uma pequena quantidade de tipos de dados ou um resumo da atividade, como estatísticas diárias.

Há duas opções para implementar o local central no Log Analytics:

1. Workspace central: O provedor de serviços pode criar um workspace em seu locatário e usar um script que utiliza a [API de Consulta](https://dev.loganalytics.io/) com a [API de Coleta de Dados](../../azure-monitor/platform/data-collector-api.md) para levar os dados de vários workspaces para essa localização central. Outra opção, além de um script, é usar os [Aplicativos Lógicos do Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).

2. Power BI como uma localização central: O Power BI pode servir como a localização central quando vários workspaces exportam dados para ele usando a integração entre o Log Analytics e o [Power BI](../../azure-monitor/platform/powerbi.md). 


## <a name="next-steps"></a>Próximas etapas
* Automatizar a criação e configuração de workspaces usando [modelos do Gerenciador de recursos](template-workspace-configuration.md)
* Automatizar a criação de workspaces usando o [PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md) 
* Use [Alertas](../../azure-monitor/platform/alerts-overview.md) para integrar com sistemas existentes
* Gerar relatórios de resumo usando o [Power BI](../../azure-monitor/platform/powerbi.md)
* Examine o processo de [Configuração do Log Analytics e do Power BI para monitoramento de vários clientes CSP](https://docs.microsoft.com/azure/cloud-solution-provider/support/monitor-multiple-customers)
