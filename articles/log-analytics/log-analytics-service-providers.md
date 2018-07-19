---
title: Log Analytics para provedores de serviços | Microsoft Docs
description: O Log Analytics pode ajudar provedores de serviços gerenciados (MSPs), grandes empresas, fornecedores de software independentes (ISVs) e provedores de serviço de hospedagem a gerenciar e monitorar servidores no local do cliente ou na infraestrutura de nuvem.
services: log-analytics
documentationcenter: ''
author: MeirMen
manager: jochan
editor: ''
ms.assetid: c07f0b9f-ec37-480d-91ec-d9bcf6786464
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/05/2018
ms.author: meirm
ms.component: na
ms.openlocfilehash: ad0a3b8e0ee5f1114ea1db95cfe2f4176b8e2ddb
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37931983"
---
# <a name="log-analytics-for-service-providers"></a>Log Analytics para provedores de serviços
O Log Analytics pode ajudar provedores de serviços gerenciados (MSPs), grandes empresas, fornecedores de software independentes (ISVs) e provedores de serviço de hospedagem a gerenciar e monitorar servidores no local do cliente ou na infraestrutura de nuvem. 

Grandes empresas compartilham várias semelhanças com provedores de serviços, especialmente quando há uma equipe de TI centralizada que é responsável por gerenciar a TI para muitas unidades de negócios diferentes. Para simplificar, este documento usa o termo *provedor*, mas a mesma funcionalidade também está disponível para empresas e outros clientes.

Para parceiros e provedores de serviços que fazem parte do programa do [Provedor de soluções de nuvem (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview), o Log Analytics é um dos serviços do Azure disponíveis na [assinatura do Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview). 

## <a name="architectures-for-service-providers"></a>Arquiteturas para provedores de serviços

Os espaços de trabalho do Log Analytics fornecem um método para o administrador controlar o fluxo e o isolamento dos logs e criar uma arquitetura de log que atende às suas necessidades de negócios específicas. [Este artigo](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-manage-access) explica as considerações gerais sobre o gerenciamento de espaço de trabalho. Os provedores de serviços têm considerações adicionais.

Há três arquiteturas possíveis para os provedores de serviços em relação aos espaços de trabalho do Log Analytics:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. Distribuído – os logs são armazenados em espaços de trabalho localizados no locatário do cliente 

Nessa arquitetura, o espaço de trabalho é implantado no locatário do cliente que é usado para todos os logs desse cliente. Os administradores do provedor de serviços recebem acesso a esse espaço de trabalho usando [os usuários convidados do Azure Active Directory (B2B)](https://docs.microsoft.com/en-us/azure/active-directory/b2b/what-is-b2b). O administrador do provedor de serviços precisará alternar no portal do Azure para o diretório de seu cliente para conseguir acessar esses espaços de trabalho.

As vantagens dessa arquitetura são:
* O cliente pode gerenciar o acesso aos logs usando seu próprio [acesso baseado em função](https://docs.microsoft.com/en-us/azure/role-based-access-control/overview).
* Cada cliente pode ter configurações diferentes para seu espaço de trabalho, como retenção e limitação de dados.
* Isolamento entre os clientes para fins de regulamentação e conformidade.
* O encargo de cada espaço de trabalho será distribuído para a assinatura do cliente.
* Os logs podem ser coletados de todos os tipos de recursos, não apenas ser baseados em agente. Por exemplo, a Auditoria do Azure.

As desvantagens dessa arquitetura são:
* É mais difícil para o provedor de serviços gerenciar o grande número de locatários de clientes ao mesmo tempo.
* Os administradores do provedor de serviços devem estar provisionados no diretório do cliente.
* O provedor de serviços não pode analisar os dados em seus clientes.

### <a name="2-central---logs-are-stored-in-workspace-located-in-the-service-provider-tenant"></a>2. Central – os logs são armazenados no espaço de trabalho localizado no locatário do provedor de serviços

Nessa arquitetura, os logs não são armazenados nos locatários do cliente, mas apenas em um local central em uma das assinaturas do provedor de serviços. Os agentes que estão instalados nas VMs do cliente são configurados para enviar seus logs para esse espaço de trabalho usando a ID de espaço de trabalho e a chave secreta.

As vantagens dessa arquitetura são:
* É fácil gerenciar um grande número de clientes e integrá-los aos vários sistemas de back-end.
* O provedor de serviços tem propriedade total sobre os logs e os vários artefatos, como funções e consultas salvas.
* O provedor de serviços pode executar a análise em todos os clientes.

As desvantagens dessa arquitetura são:
* Essa arquitetura é aplicável somente a dados de VM baseados em agente; ela não aborda fontes de dados de PaaS, SaaS e de malha do Azure.
* Talvez seja difícil separar os dados entre os clientes quando eles são mesclados em um único espaço de trabalho. O único método satisfatório para fazer isso é usar o FQDN (nome de domínio totalmente qualificado) do computador ou por meio da ID da assinatura do Azure. 
* Todos os dados de todos os clientes serão armazenados na mesma região com uma única fatura e as mesmas definições de retenção e configuração.
* Os serviços de malha do Azure e de PaaS, como o Diagnóstico do Azure e a Auditoria do Azure, exigem que o espaço de trabalho esteja no mesmo locatário do recurso; portanto, eles não podem enviar os logs para o espaço de trabalho central.
* Todos os agentes de VM de todos os clientes serão autenticados no espaço de trabalho central usando a mesma ID de espaço de trabalho e chave. Não há nenhum método para bloquear os logs de um cliente específico sem interromper outros clientes.


### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. Híbrido – os logs são armazenados no espaço de trabalho localizado no locatário do cliente e alguns deles são enviados para um local central.

A terceira combinação de arquitetura entre as duas opções. Ela se baseia na primeira arquitetura distribuída, na qual os logs são locais para cada cliente, mas usando um mecanismo para criar um repositório central de logs. Uma parte dos logs é inserida em um local central para relatórios e análise. Essa parte pode ser um número pequeno de tipos de dados ou um resumo da atividade, como estatística diária.

Há duas opções para implementar o local central no Log Analytics:

1. Espaço de trabalho central: o provedor de serviços pode criar um espaço de trabalho em seu locatário e usar um script que utiliza a [API de Consulta](https://dev.loganalytics.io/) com a [API de Coleta de Dados](log-analytics-data-collector-api.md) para levar os dados de vários espaços de trabalho para esse local central. Outra opção, além do script, é usar um [Aplicativo Lógico do Azure](https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-overview).

2. Power BI como um local central: o Power BI pode servir como o local central quando vários espaços de trabalho exportam dados para ele usando a integração entre o Log Analytics e o [Power BI](log-analytics-powerbi.md). 


## <a name="next-steps"></a>Próximas etapas
* Automatizar a criação e configuração de espaços de trabalho usando [modelos do Gerenciador de recursos](log-analytics-template-workspace-configuration.md)
* Automatizar a criação de espaços de trabalho usando o [PowerShell](log-analytics-powershell-workspace-configuration.md) 
* Use [Alertas](log-analytics-alerts.md) para integrar com sistemas existentes
* Gerar relatórios de resumo usando o [Power BI](log-analytics-powerbi.md)

