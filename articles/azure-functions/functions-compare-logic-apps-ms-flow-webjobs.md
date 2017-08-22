---
title: "Escolha entre o Flow, os Aplicativos Lógicos, o Functions e o WebJobs | Microsoft Docs"
description: "Compare e contraste os serviços de integração na nuvem da Microsoft e decida quais deles você deve usar."
services: functions,app-service\logic
documentationcenter: na
author: cephalin
manager: wpickett
tags: 
keywords: "microsoft flow, flow, aplicativos lógicos, azure functions, functions, azure webjobs, webjobs, processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.assetid: e9ccf7ad-efc4-41af-b9d3-584957b1515d
ms.service: functions
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/03/2017
ms.author: glenga
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 256bc04684ba337fe8a403d977079c96b63ca61d
ms.contentlocale: pt-br
ms.lasthandoff: 08/04/2017

---
# <a name="choose-between-flow-logic-apps-functions-and-webjobs"></a>Escolha entre o Flow, os Aplicativos Lógicos, o Functions e o WebJobs
Este artigo compara e contrasta os seguintes serviços na nuvem da Microsoft, que podem todos solucionar problemas de integração e automação de processos de negócios:

* [Microsoft Flow](https://flow.microsoft.com/)
* [Aplicativos Lógicos do Azure](https://azure.microsoft.com/services/logic-apps/)
* [Funções do Azure](https://azure.microsoft.com/services/functions/)
* [WebJobs no Serviço de Aplicativo do Azure](../app-service-web/web-sites-create-web-jobs.md)

Todos esses serviços são úteis para "unir" sistemas diferentes. Todos eles definem entrada e saída, condições e ações. Você pode executar cada um em um cronograma ou gatilho. No entanto, cada serviço adiciona um conjunto exclusivo de valores, e compará-los não é uma questão de "qual serviço é o melhor?", mas de "qual serviço é o mais adequado para essa situação?" Geralmente, uma combinação desses serviços é a melhor maneira de criar rapidamente uma solução escalonável de integração completa.

<a name="flow"></a>

## <a name="flow-vs-logic-apps"></a>Flow vs. Aplicativos Lógicos
Podemos discutir o Microsoft Flow e os Aplicativos Lógicos do Azure juntos porque são ambos integrações de *configuration-first*, o que facilita a criação de processos e fluxos de trabalho e a integração com diversos aplicativos SaaS e corporativos. 

* O Flow é criado em cima de Aplicativos Lógicos
* Eles têm o mesmo designer de fluxo de trabalho
* [Conectores](../connectors/apis-list.md) que funcionam em um também podem funcionar no outro

O Flow permite que qualquer funcionário possa fazer integrações simples (por exemplo, obter SMS para emails importantes) sem passar por desenvolvedores ou pelo departamento de TI. Por outro lado, os Aplicativos Lógicos podem habilitar integrações avançadas ou essenciais (por exemplo, processos de B2B) onde há a necessidade de práticas de segurança e DevOps de nível empresarial. É comum que um fluxo de trabalho de negócios aumente de complexidade ao longo do tempo. Da mesma forma, você pode começar com um fluxo e convertê-lo em um aplicativo lógico conforme a necessidade.

A tabela a seguir ajuda a determinar o que é melhor para determinada integração, o Flow ou os Aplicativos Lógicos.

|  | Flow | Aplicativos Lógicos |
| --- | --- | --- |
| Público-alvo |funcionários do escritório, usuários de negócios |profissionais de TI, desenvolvedores |
| Cenários |Autoatendimento |Essenciais |
| Ferramenta de design |Aplicativo do navegador e móvel, somente interface do usuário |No navegador e no [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md), [Exibição de código](../logic-apps/logic-apps-author-definitions.md) disponível |
| DevOps |Ad-hoc, desenvolver em produção |controle de origem, teste, suporte, automação e capacidade de gerenciamento no [Azure Resource Manager](../logic-apps/logic-apps-arm-provision.md) |
| Experiência de administrador |[https://flow.microsoft.com](https://flow.microsoft.com) |[https://portal.azure.com](https://portal.azure.com) |
| Segurança |Práticas padrão: [soberania de dados](https://wikipedia.org/wiki/Technological_Sovereignty), [criptografia em repouso](https://wikipedia.org/wiki/Data_at_rest#Encryption) para dados confidenciais etc. |Garantia de segurança do Azure: [segurança do Azure](https://www.microsoft.com/trustcenter/Security/AzureSecurity), [Central de Segurança](https://azure.microsoft.com/services/security-center/), [logs de auditoria](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/) e muito mais. |

<a name="function"></a>

## <a name="functions-vs-webjobs"></a>Functions vs. Trabalhos Web
Podemos discutir o Azure Functions e os WebJobs do Serviço de Aplicativo do Azure juntos porque eles são serviços de integração de *code-first* e projetados para desenvolvedores. Eles permitem que você execute um script ou um trecho de código em resposta a vários eventos, como [novos blobs de armazenamento](functions-bindings-storage.md) ou [uma solicitação WebHook](functions-bindings-http-webhook.md). Eis as semelhanças: 

* Ambos são criados no [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md) e têm recursos como [controle do código-fonte](../app-service-web/app-service-continuous-deployment.md), [autenticação](../app-service/app-service-authentication-overview.md) e [monitoramento](../app-service-web/web-sites-monitor.md).
* Ambos são serviços voltados para desenvolvedores.
* Ambos dão suporte a scripts e linguagens de programação padrão.
* Ambos têm suporte NuGet e NPM.

O Functions é a evolução natural do WebJobs no sentido de ter o melhor do WebJob, só que aprimorado. As melhorias incluem: 

* Desenvolvimento simplificado, teste e execução de código diretamente no navegador.
* Integração interna com outros serviços do Azure e serviços de terceiros como o [GitHub WebHooks](https://developer.github.com/webhooks/creating/).
* Pagamento por uso. Não é preciso pagar um [Plano do Serviço de Aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
* Automático, [dimensionamento dinâmico](functions-scale.md).
* Para os clientes existentes do Serviço de Aplicativo, a execução no Plano do Serviço de Aplicativo ainda possível (para tirar proveito dos recursos subutilizados).
* Integração com os Aplicativos Lógicos.

A tabela a seguir resume as diferenças entre o Functions e o WebJobs:

|  | Funções | Trabalhos Web |
| --- | --- | --- |
| Dimensionamento |Dimensionamento sem configuração |dimensionar com Plano do Serviço de Aplicativo |
| Preços |Pagamento por uso ou parte de Plano do Serviço de Aplicativo |Parte do Plano do Serviço de Aplicativo |
| Executar-tipo |disparado, agendado (pelo gatilho de temporizador) |acionado, contínuo, agendado |
| Eventos de gatilho |[temporizador](functions-bindings-timer.md), [Banco de Dados Cosmos do Azure](functions-bindings-documentdb.md), [Hubs de Eventos do Azure](functions-bindings-event-hubs.md), [HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md), [Aplicativos Móveis do Serviço de Aplicativo do Azure](functions-bindings-mobile-apps.md), [Hubs de Notificação do Azure](functions-bindings-notification-hubs.md), [Barramento de Serviço do Azure](functions-bindings-service-bus.md), [Armazenamento do Azure](functions-bindings-storage.md) |[Armazenamento do Azure](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md), [Barramento de Serviço do Azure](../app-service-web/websites-dotnet-webjobs-sdk-service-bus.md) |
| Desenvolvimento no navegador |com suporte | sem suporte |
| Script de janela |experimental |com suporte |
| PowerShell |experimental |com suporte |
| C# |com suporte |com suporte |
| F# |com suporte |sem suporte |
| Bash |experimental |com suporte |
| PHP |experimental |com suporte |
| Python |experimental |com suporte |
| JavaScript |com suporte |com suporte |

O uso do Functions ou do WebJobs depende do que você já está fazendo com o Serviço de Aplicativo. Se você tiver um aplicativo do Serviço de Aplicativo para o qual deseja executar trechos de código e quiser gerenciá-los juntos no mesmo ambiente DevOps, deverá usar o WebJobs. Se você desejar executar trechos de código para outros serviços ou aplicativos de terceiros, ou se quiser gerenciar trechos de código de integração separadamente dos aplicativos do Serviço de Aplicativo, ou se desejar chamar trechos de código em um Aplicativo lógico, aproveite todas as melhorias do Functions.  

<a name="together"></a>

## <a name="flow-logic-apps-and-functions-together"></a>Flow, Aplicativos Lógicos e Functions juntos
Conforme mencionado anteriormente, o serviço mais adequado para você depende da situação. 

* Para a otimização de negócios simples, use o Flow.
* Se seu cenário de integração é muito avançado para o Flow, ou se você precisa de recursos de DevOps e conformidades de segurança, use os Aplicativos Lógicos.
* Se uma etapa no seu cenário de integração requer transformação altamente personalizada ou código especializado, crie um aplicativo de funções e dispare uma função como ação em seu aplicativo lógico.

Você pode chamar um aplicativo lógico em um fluxo. Você também pode chamar uma função em um aplicativo lógico e um aplicativo lógico em uma função. A integração entre o Flow, os Aplicativos Lógicos e o Functions vai melhorando a cada dia. Você pode criar algo em um serviço e usá-lo em outros serviços. Portanto, todo o investimento feito nessas três tecnologias vale a pena.

## <a name="next-steps"></a>Próximas etapas
Comece a usar cada um dos serviços criando seu primeiro fluxo, aplicativo lógico, aplicativo de funções ou trabalho Web. Clique em um dos links abaixo:

* [Introdução ao Microsoft Flow](https://flow.microsoft.com/en-us/documentation/getting-started/)
* [Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md)
* [Criar sua primeira Função do Azure](functions-create-first-azure-function.md)
* [Implantar Trabalhos Web usando o Visual Studio](../app-service-web/websites-dotnet-deploy-webjobs.md)

Ou saiba mais sobre esses serviços de integração com os links abaixo:

* [Aproveitando o Azure Functions e o Serviço de Aplicativo do Azure em cenários de integração, por Christopher Anderson](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
* [Integração simplificada, por Charles Lamanna](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [Webcast ao vivo de Aplicativos Lógicos](http://aka.ms/logicappslive)
* [Perguntas frequentes sobre o Microsoft Flow](https://flow.microsoft.com/documentation/frequently-asked-questions/)
* [Recursos de documentação do Azure Webjobs](../app-service-web/websites-webjobs-resources.md)


