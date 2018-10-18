---
title: Comparar Flow, Aplicativos Lógicos, Functions e WebJobs - Azure
description: 'Compare os serviços em nuvem da Microsoft que são otimizados para tarefas de integração: Flow, Aplicativos Lógicos, Functions e WebJobs.'
services: functions, logic-apps
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: microsoft flow, flow, aplicativos lógicos, azure functions, functions, azure webjobs, webjobs, processamento de eventos, computação dinâmica, arquitetura sem servidor
ms.service: azure-functions
ms.devlang: multiple
ms.topic: overview
ms.date: 04/09/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: e449703dd8d012037af9540397ecd3b7bc0c7e7f
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114627"
---
# <a name="compare-flow-logic-apps-functions-and-webjobs"></a>Comparar Flow, Aplicativos Lógicos, Functions e WebJobs

Este artigo compara os seguintes serviços em nuvem da Microsoft:

* [Microsoft Flow](https://flow.microsoft.com/)
* [Aplicativos Lógicos do Azure](https://azure.microsoft.com/services/logic-apps/)
* [Funções do Azure](https://azure.microsoft.com/services/functions/)
* [WebJobs no Serviço de Aplicativo do Azure](../app-service/web-sites-create-web-jobs.md)

Todos esses serviços podem resolver problemas de integração e automatizar processos empresariais. Todos eles definem entrada e saída, condições e ações. Você pode executar cada um em um cronograma ou gatilho. No entanto, cada serviço tem vantagens exclusivas e este artigo explica as diferenças.

## <a name="compare-microsoft-flow-and-azure-logic-apps"></a>Comparar o Microsoft Flow e os Aplicativos Lógicos do Azure

O Flow e os Aplicativos Lógicos são serviços de integração *primeiro o designer* que podem criar fluxos de trabalho. Ambos os serviços se integram a vários aplicativos SaaS e empresariais. 

O Flow é criado em cima dos Aplicativos Lógicos Eles compartilham o mesmo designer de fluxo de trabalho e os mesmos [conectores](../connectors/apis-list.md). 

O Flow permite que qualquer trabalhador possa fazer integrações simples (por exemplo, um processo de aprovação em uma Biblioteca de Documentos do SharePoint) sem passar por desenvolvedores ou pelo departamento de TI. Por outro lado, Aplicativos Lógicos podem habilitar integrações avançadas (por exemplo, processos de B2B) em que há a necessidade de práticas de segurança e DevOps de nível empresarial. É comum que um fluxo de trabalho de negócios aumente de complexidade ao longo do tempo. Da mesma forma, você pode começar com um fluxo e convertê-lo em um aplicativo lógico conforme a necessidade.

A tabela a seguir ajuda a determinar o que é melhor para determinada integração, o Flow ou os Aplicativos Lógicos.

|  | Flow | Aplicativos Lógicos |
| --- | --- | --- |
| Usuários |Funcionários do escritório, usuários de negócios, administradores do SharePoint |Integradores profissionais e desenvolvedores, profissionais de TI |
| Cenários |Autoatendimento |Integrações avançadas |
| Ferramenta de design |Aplicativo do navegador e móvel, somente interface do usuário |No navegador e no [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md), [Exibição de código](../logic-apps/logic-apps-author-definitions.md) disponível |
| Gerenciamento do Ciclo de Vida do Aplicativo (ALM) |Criar e testar em ambientes de não produção, promover para produção quando estiver pronto. |DevOps: controle de origem, teste, suporte, automação e capacidade de gerenciamento no [Azure Resource Manager](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md) |
| Experiência de administrador |Gerenciar políticas de Ambientes do Flow e de DLP (Prevenção contra Perda de Dados), acompanhar licenciamento [https://admin.flow.microsoft.com](https://admin.flow.microsoft.com) |Gerenciar grupos de recursos, conexões, gerenciamento de acesso e registro em log [https://portal.azure.com](https://portal.azure.com) |
| Segurança |Segurança do Office 365 e logs de auditoria de e conformidade, Prevenção de perda de dados (DLP), [criptografia em repouso](https://wikipedia.org/wiki/Data_at_rest#Encryption) para dados confidenciais, etc. |Garantia de segurança do Azure: [segurança do Azure](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity), [Central de Segurança](https://azure.microsoft.com/services/security-center/), [logs de auditoria](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/) e muito mais. |

## <a name="compare-azure-functions-and-azure-logic-apps"></a>Comparar o Azure Functions e os Aplicativos Lógicos do Azure

O Functions e os Aplicativos Lógicos são os serviços do Azure que permitem as cargas de trabalho sem servidor. O Azure Functions é um serviço de computação sem servidor, enquanto os Aplicativos Lógicos do Azure fornecem fluxos de trabalho sem servidor. *Orquestrações* complexas podem ser criadas para ambos. Uma orquestração é uma coleção de funções ou etapas, chamadas *ações* nos Aplicativos Lógicos, que são executadas para realizar uma tarefa complexa. Por exemplo, para processar um lote de pedidos você pode executar várias instâncias de uma função em paralelo, aguardar a conclusão de todas as instâncias e, em seguida, executar uma função que calcula um resultado na agregação.

Para o Azure Functions, você desenvolve orquestrações escrevendo o código e usando a [extensão Durable Functions](durable-functions-overview.md). Para os Aplicativos Lógicos, você pode criar orquestrações usando uma GUI ou editando arquivos de configuração.

Você pode misturar e fazer a correspondência de serviços quando você compila uma orquestração, chamando funções de aplicativos lógicos e chamando aplicativos lógicos de funções. Escolha como criar cada orquestração com base em recursos dos serviços ou de sua preferência pessoal. A tabela a seguir lista algumas das principais diferenças entre esses serviços:
 
|  | Funções duráveis | Aplicativos Lógicos |
| --- | --- | --- |
| Desenvolvimento | Primeiro o código (obrigatória) | Primeiro o designer (declarativa) |
| Conectividade | [Vários tipos de associação integradas](functions-triggers-bindings.md#supported-bindings), escrever código para associações personalizadas | [Grande coleção de conectores](../connectors/apis-list.md), [Enterprise Integration Pack para cenários de B2B](../logic-apps/logic-apps-enterprise-integration-overview.md), [criar conectores personalizados](../logic-apps/custom-connector-overview.md) |
| Ações | Cada atividade é uma função do Azure. Escrever código para funções de atividade |[Grande coleção de ações predefinidas](../logic-apps/logic-apps-workflow-actions-triggers.md)|
| Monitoramento | [Azure Application Insights](../application-insights/app-insights-overview.md) | [portal do Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md), [Log Analytics](../logic-apps/logic-apps-monitor-your-logic-apps.md)|
| Gerenciamento | [API REST](durable-functions-http-api.md), [Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer) | [Portal do Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md), [API REST](https://docs.microsoft.com/rest/api/logic/), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.logicapp/?view=azurermps-5.6.0), [Visual Studio](https://docs.microsoft.com/azure/logic-apps/manage-logic-apps-with-visual-studio) |
| Contexto de execução | Pode executar [localmente](functions-runtime-overview.md) ou na nuvem. | É executado somente na nuvem.|

<a name="function"></a>

## <a name="compare-functions-and-webjobs"></a>Comparar Functions e WebJobs

Como no Azure Functions, o WebJobs do Serviço de Aplicativo do Azure com o SDK do WebJobs é um serviço de integração *com prioridade de código* que foi projetado para desenvolvedores. Ambos são criados no [Serviço de Aplicativo do Azure](../app-service/app-service-web-overview.md) e dão suporte a recursos como [integração de controle de origem](../app-service/app-service-continuous-deployment.md), [autenticação](../app-service/app-service-authentication-overview.md) e [monitoramento com integração do Application Insights](functions-monitoring.md).

### <a name="webjobs-and-the-webjobs-sdk"></a>WebJobs e SDK do WebJobs

O recurso *WebJobs* do Serviço de Aplicativo permite que você execute um script ou código no contexto de um aplicativo Web do Serviço de Aplicativo. O *SDK do WebJobs* é uma estrutura projetada para WebJobs que simplifica o código que você cria para responder a eventos nos serviços do Azure. Por exemplo, você pode responder à criação de um blob de imagem no Armazenamento do Azure criando uma imagem em miniatura. O SDK do WebJobs é executado como um aplicativo de console do .NET, que você pode implantar em um WebJob. 

O WebJobs e o SDK do WebJobs funcionam melhor em conjunto, mas você pode usar o WebJobs sem o SDK do WebJobs e vice-versa. Um WebJob pode executar qualquer programa ou script que possa ser executado na área restrita do Serviço de Aplicativo. Um aplicativo de console do SDK do WebJobs pode ser executado em qualquer lugar que execute aplicativos de console, como servidores locais.

### <a name="comparison-table"></a>Tabela de comparação

O Azure Functions se baseia no SDK do WebJobs e, portanto, compartilha muitos dos mesmos gatilhos de evento e conexões com outros serviços do Azure. Aqui estão alguns fatores a se considerar ao escolher entre o Azure Functions e o WebJobs com o SDK do WebJobs:

|  | Funções | WebJobs com o SDK do WebJobs |
| --- | --- | --- |
|[Modelo de aplicativo sem servidor](https://azure.microsoft.com/solutions/serverless/) com [dimensionamento automático](functions-scale.md#how-the-consumption-plan-works)|✔||
|[Desenvolver e testar no navegador](functions-create-first-azure-function.md) |✔||
|[Preço de pagamento por uso](functions-scale.md#consumption-plan)|✔||
|[Integração com os Aplicativos Lógicos](functions-twitter-email.md)|✔||
| Eventos de gatilho |[Timer](functions-bindings-timer.md)<br>[Blobs e filas do Armazenamento do Azure](functions-bindings-storage-blob.md)<br>[Filas e tópicos do Barramento de Serviço do Azure](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Hubs de eventos do Azure](functions-bindings-event-hubs.md)<br>[HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md)<br>[Grade de Eventos do Azure](functions-bindings-event-grid.md)|[Timer](functions-bindings-timer.md)<br>[Blobs e filas do Armazenamento do Azure](functions-bindings-storage-blob.md)<br>[Filas e tópicos do Barramento de Serviço do Azure](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Hubs de eventos do Azure](functions-bindings-event-hubs.md)<br>[Sistema de Arquivos](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Files/FileTriggerAttribute.cs)|
| Idiomas com suporte  |C#<br>F#<br>JavaScript<br>Java (versão prévia) |C#<sup>1</sup>|
|Gerenciador de pacotes|NPM e NuGet|NuGet<sup>2</sup>|

<sup>1</sup> WebJobs (sem o SDK do WebJobs) dá suporte a C#, JavaScript, Bash, .cmd, .bat, PowerShell, PHP, TypeScript, Python e muito mais. Isso não é uma lista definitiva; um WebJob pode executar qualquer programa ou script que possa ser executado na área restrita do Serviço de Aplicativo.

<sup>2</sup> O WebJobs (sem o SDK do WebJobs) dá suporte a NPM e NuGet.

### <a name="summary"></a>Resumo

O Azure Functions oferece maior produtividade para o desenvolvedor, mais opções de linguagem de programação, mais opções de ambiente de desenvolvimento, mais opções de integração com serviços do Azure e mais opções de preço. Na maioria dos cenários, é a melhor opção.

Aqui estão dois cenários para os quais o WebJobs pode ser a melhor opção:

* Você precisa de mais controle sobre o código que escuta eventos, o objeto `JobHost`. O Functions oferece um número limitado de maneiras de personalizar o comportamento do `JobHost` no arquivo [host.json](functions-host-json.md). Às vezes, você precisa fazer coisas que não podem ser especificadas por uma cadeia de caracteres em um arquivo JSON. Por exemplo, somente o SDK do WebJobs permite configurar uma política de repetição personalizada para o Armazenamento do Azure.
* Você tem um aplicativo do Serviço de Aplicativo para o qual deseja executar snippets de código e quiser gerenciá-los juntos no mesmo ambiente DevOps.

Para outros cenários em que você deseja executar snippets de código para integrar o Azure ou serviços de terceiros, escolha o Azure Functions em vez do WebJobs com o SDK do WebJobs.

<a name="together"></a>

## <a name="flow-logic-apps-functions-and-webjobs-together"></a>Flow, Aplicativos Lógicos, Functions e WebJobs juntos

Você não precisa escolher apenas um desses serviços; eles se integram entre si e a outros serviços externos.

Um fluxo pode chamar um aplicativo lógico. Um lógica de aplicativo pode chamar uma função e uma função pode chamar um aplicativo lógico. Consulte, por exemplo, [Criar uma função que se integra aos Aplicativos Lógicos do Azure](functions-twitter-email.md).

A integração entre o Flow, o Aplicativo Lógico e o Functions segue melhorando a cada dia. Você pode criar algo em um serviço e usá-lo em outros serviços.

## <a name="next-steps"></a>Próximas etapas

Comece criando seu primeiro fluxo, aplicativo lógico ou aplicativo de funções. Clique em um dos links abaixo:

* [Introdução ao Microsoft Flow](https://flow.microsoft.com/en-us/documentation/getting-started/)
* [Criar um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Criar sua primeira Função do Azure](functions-create-first-azure-function.md)

Ou saiba mais sobre esses serviços de integração com os links abaixo:

* [Aproveitando o Azure Functions e o Serviço de Aplicativo do Azure em cenários de integração, por Christopher Anderson](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
* [Integração simplificada, por Charles Lamanna](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [Webcast ao vivo de Aplicativos Lógicos](http://aka.ms/logicappslive)
* [Perguntas frequentes sobre o Microsoft Flow](https://flow.microsoft.com/documentation/frequently-asked-questions/)
