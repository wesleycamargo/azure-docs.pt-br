---
title: Diretrizes para dados pessoais armazenados no Azure Application Insights | Microsoft Docs
description: Este artigo descreve como gerenciar dados pessoais armazenados no Azure Application Insights e os métodos para identificá-los e removê-los.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.reviewer: Evgeny.Ternovsky
ms.author: mbullwin
ms.openlocfilehash: a59b57c546f18a7d91160f2ae7282af82fc42160
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39044706"
---
# <a name="guidance-for-personal-data-stored-in-application-insights"></a>Diretrizes para dados pessoais armazenados no Application Insights

O Application Insights é um armazenamento de dados no qual é possível localizar dados pessoais. Este artigo aborda o local, no Application Insights, em que esses dados normalmente estão localizados, bem como as funcionalidades disponíveis para você manipular esses dados.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>Estratégia para tratamento de dados pessoais

Enquanto couber a você e à sua empresa determinar qual estratégia será adotada para o tratamento de dados privados (se for o caso), algumas abordagens possíveis são apresentadas a seguir. As abordagens estão listadas em ordem de preferência, do ponto de vista técnico, da mais para a menos preferencial:
* Sempre que possível, pare de coletar, ocultar, anonimizar ou ajustar os dados que estão sendo coletados para excluí-lo de ser considerado _privado_. Esse método é a abordagem preferencial, evitando a necessidade de criar uma estratégia de tratamento de dados cara e impactante.
* Quando não for possível, tente normalizar os dados para reduzir o impacto na plataforma e no desempenho dos dados. Por exemplo, em vez de registrar uma ID de usuário explícita, crie um dado de pesquisa que correlacionará o nome de usuário e os respectivos detalhes a uma ID interna que poderá ser registrada em outro lugar. Dessa forma, se um dos usuários solicitar a exclusão das informações pessoais, é possível que apenas excluir a linha na tabela de pesquisa correspondente ao usuário seja suficiente. 
* Por fim, se os dados privados precisarem ser coletados, compile um processo em torno do caminho de API de limpeza e do caminho de API de consulta existente para atender a qualquer obrigação necessária para exportar e excluir dados privados associados a um usuário.

## <a name="where-to-look-for-private-data-in-application-insights"></a>Onde pesquisar dados privados no Application Insights?

O Application Insights é um armazenamento completamente flexível que, enquanto prescreve um esquema aos dados, permite substituir todos os campos por valores personalizados. Assim sendo, é impossível dizer exatamente onde os dados privados estarão localizados no aplicativo específico. No entanto, os locais a seguir são bons pontos iniciais no inventário:

* *Endereços IP*: enquanto o Application Insights ofuscará, por padrão, todos os campos de endereço IP para "0.0.0.0", é um padrão bastante comum substituir esse valor pelo IP do usuário atual para manter as informações de sessão. A consulta de análise abaixo pode ser usada para encontrar qualquer tabela que contém os valores na coluna de endereço IP diferentes de "0.0.0.0" nas últimas 24 horas:
    ```
    search client_IP != "0.0.0.0"
    | where timestamp > ago(1d)
    | summarize numNonObfuscatedIPs_24h = count() by $table
    ```
* *IDs de usuário*: por padrão, o Application Insights usará IDs geradas aleatoriamente para acompanhamento de usuário e de sessão. No entanto, é comum substituir esses campos para armazenar uma ID mais relevante para o aplicativo. Por exemplo: nomes de usuário, GUIDs do AAD etc. Essas IDs são geralmente consideradas no escopo como dados pessoais e, portanto, devem ser tratado apropriadamente. Nossa recomendação é sempre tentar ofuscar essas IDs ou torná-las anônimas. Os campos em que esses valores são geralmente encontrados incluem session_Id, user_Id, user_AuthenticatedId, user_AccountId, bem como customDimensions.
* *Dados personalizados*: o Application Insights permite acrescentar um conjunto de dimensões personalizadas para qualquer tipo de dados. Essas dimensões podem ser *quaisquer* dados. Use a consulta a seguir para identificar quaisquer dimensões personalizadas coletadas nas últimas 24 horas:
    ```
    search * 
    | where isnotempty(customDimensions)
    | where timestamp > ago(1d)
    | project $table, timestamp, name, customDimensions 
    ```
* *Dados na memória e em trânsito*: o Application Insights acompanhará solicitações, exceções, chamadas de dependência e rastreamentos. Dados privados podem frequentemente ser coletados no código e no nível de chamada HTTP. Examine as exceções, solicitações, dependências e tabelas de rastreamento para identificar quaisquer dados desse tipo. Use [inicializadores de telemetria](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) sempre que possível para ofuscar esses dados.
* *Capturas do Depurador de Instantâneos*: o recurso [Depurador de Instantâneos](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) do Application Insights permite coletar instantâneos de depuração, sempre que uma exceção é detectada na instância de produção do seu aplicativo. Instantâneos vão expor o rastreamento de pilha completo levando às exceções, bem como os valores de variáveis locais em cada etapa na pilha. Infelizmente, esse recurso não permite a exclusão seletiva de pontos de alinhamento, nem o acesso programático aos dados no instantâneo. Portanto, se a taxa de retenção de instantâneo padrão não atende a seus requisitos de conformidade, a recomendação é desativar o recurso.

## <a name="how-to-export-and-delete-private-data"></a>Como exportar e excluir dados privados

É __altamente__ recomendável, quando possível, reestruturar a política de coleta de dados para desabilitar a coleta de dados privados, ofuscando ou anonimizando-os, ou modificando-os de alguma outra forma, para evitar que sejam considerados "privados". Tratar os dados após eles serem coletados resultará em custos para você e sua equipe para definir e automatizar uma estratégia, compilar uma interface para seus clientes interagirem com os dados, e custos de manutenção contínuos. Além disso, é computacionalmente oneroso para o Application Insights, e um grande volume de consultas simultâneas ou chamadas à API de limpeza têm o potencial de impactar negativamente todas as outras interações com a funcionalidade do Application Insights. Dito isso, há de fato alguns cenários válidos em que dados privados devem ser coletados. Para esses casos, os dados devem ser tratados conforme descrito nesta seção.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Exibir e exportar

Para solicitações de visualização e exportação de dados, poderá ser usada uma [API de consulta](https://dev.applicationinsights.io/quickstart). A lógica para converter a forma dos dados em uma forma apropriada para entregar aos usuários será de sua responsabilidade. O [Azure Functions](https://azure.microsoft.com/services/functions/) é um ótimo local para hospedar essa lógica.

### <a name="delete"></a>Excluir

> [!WARNING]
> As exclusões no Application Insights são destrutivas e irreversíveis! Tenha extremo cuidado na execução delas.

Disponibilizamos um caminho de API de "limpeza" como parte de uma história de tratamento de privacidade. Esse caminho deve ser utilizado com moderação devido ao risco associado, o potencial impacto no desempenho e o potencial para distorcer agregações, medidas e outros aspectos dos dados do Application Insights. Consulte a seção [Estratégia para tratamento de dados pessoais](#strategy-for-personal-data-handling) acima para obter abordagens alternativas para tratar dados privados.

A limpeza é uma operação altamente privilegiada que nenhum aplicativo ou usuário no Azure (incluindo até mesmo o proprietário do recurso) terá permissões para executar sem que seja concedida explicitamente uma função no Azure Resource Manager. Essa função é _Limpador de Dados_ e deve ser cuidadosamente delegada devido ao potencial de perda de dados.

Depois que a função do Azure Resource Manager for atribuída, dois novos caminhos de API estarão disponíveis, documentação completa do desenvolvedor e forma da chamada vinculadas:

* [Limpeza POST](https://docs.microsoft.com/rest/api/application-insights/components/purge) – obtém um objeto especificando parâmetros de dados para excluir e retorna um GUID de referência
* Status de limpeza GET – a chamada de limpeza POST retornará um cabeçalho 'x-ms-status-location' que incluirá uma URL que você poderá chamar para determinar o status da API de limpeza. Por exemplo: 
   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

> [!IMPORTANT]
>  Embora a grande maioria das operações de limpeza possa ser concluída muito mais rapidamente do que o SLA, devido a seu grande impacto na plataforma de dados usada pelo Application Insights,  **o SLA formal para a conclusão das operações de limpeza é definido como 30 dias**.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como os dados são coletados, processados e protegidos, consulte [Segurança de dados do Application Insights](app-insights-data-retention-privacy.md).