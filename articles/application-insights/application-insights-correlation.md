---
title: Correlação no Azure Application Insights Telemetry | Microsoft Docs
description: Correlação no Application Insights Telemetry
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/09/2018
ms.author: mbullwin
ms.openlocfilehash: 9adecca35524962402d46169c531d135d0772bbd
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
---
# <a name="telemetry-correlation-in-application-insights"></a>Correlação de telemetria no Application Insights

No mundo dos microsserviços, cada operação lógica requer que trabalho seja feito em vários componentes do serviço. Cada um desses componentes pode ser monitorado separadamente pelo [Application Insights](app-insights-overview.md). O componente do aplicativo Web se comunica com o componente de provedor de autenticação para validar as credenciais do usuário e com o componente de API para obter dados para visualização. O componente de API, por sua vez, pode consultar dados de outros serviços e usar componentes de provedor de cache, além de notificar o componente de cobrança sobre essa chamada. O Application Insights dá suporte à correlação de telemetria distribuída. Ele permite detectar qual componente é responsável pela degradação de desempenho ou falha.

Este artigo explica o modelo de dados usado pelo Application Insights para correlacionar a telemetria enviada por vários componentes. Ele aborda os protocolos e as técnicas de propagação de contexto. Ele também aborda a implementação dos conceitos de correlação em plataformas e idiomas diferentes.

## <a name="telemetry-correlation-data-model"></a>Modelo de dados de correlação de telemetria

O Application Insights define um [modelo de dados](application-insights-data-model.md) para correlação de telemetria distribuída. Para associar a telemetria à operação lógica, cada item de telemetria tem o campo de contexto chamado `operation_Id`. Esse identificador é compartilhado por todos os itens de telemetria no rastreamento distribuído. Portanto, mesmo com a perda de telemetria de uma única camada, você ainda pode associar telemetria relatada por outros componentes.

Operação lógica distribuída normalmente consiste de um conjunto de operações menores – solicitações processadas por um dos componentes. Essas operações são definidas por [telemetria de solicitação](application-insights-data-model-request-telemetry.md). Toda telemetria de solicitação tem seu próprio `id` que a identifica de modo exclusivo, globalmente. E toda a telemetria (rastreamentos, exceções, etc.) associada a essa solicitação deve definir o `operation_parentId` para o valor da `id` da solicitação.

Toda operação de saída como http chama outro componente representado por [telemetria de dependência](application-insights-data-model-dependency-telemetry.md). A telemetria de dependência também define sua própria `id`, que é globalmente exclusiva. A telemetria de solicitação, iniciada por essa chamada de dependência, usa-a como `operation_parentId`.

Você pode compilar o modo de exibição de operação lógica distribuída usando `operation_Id`, `operation_parentId` e `request.id` com `dependency.id`. Esses campos também definem a ordem de causalidade das chamadas de telemetria.

No ambiente de microsserviços, rastreamentos de componentes podem ir para os diferentes armazenamentos. Todo componente pode ter sua própria chave de instrumentação no Application Insights. Para obter a telemetria para a operação lógica, você precisa consultar dados de cada armazenamento. Quando o número de armazenamentos é enorme, você precisa ter uma dica sobre onde procurar em seguida.

O modelo de dados do Application Insights define dois campos para resolver esse problema: `request.source` e `dependency.target`. O primeiro campo identifica o componente que iniciou a solicitação de dependência, e o segundo identifica qual componente retornou a resposta da chamada de dependência.


## <a name="example"></a>Exemplo

Vejamos um exemplo de um aplicativo PREÇOS DE AÇÕES mostrando o preço de mercado atual de uma ação usando a API externa chamada API AÇÕES. O aplicativo STOCK PRICES tem uma página `Stock page` aberta pelo navegador da Web cliente usando `GET /Home/Stock`. O aplicativo consulta a STOCK API usando uma chamada HTTP `GET /api/stock/value`.

Você pode analisar telemetria resultante da execução de uma consulta:

```
(requests | union dependencies | union pageViews) 
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Na exibição resultante, observe que todos os itens de telemetria compartilham a raiz `operation_Id`. Quando a chamada AJAX é feita da página – a nova ID exclusiva `qJSXU` é atribuída à telemetria de dependência e a ID de pageView é usada como `operation_ParentId`. A solicitação do servidor, por sua vez, usa a ID do AJAX como `operation_ParentId`, etc.

| itemType   | Nome                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Página de ações                |              | STYz               | STYz         |
| dependência | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| solicitação    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| dependência | GET /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

Agora, quando a chamada `GET /api/stock/value` é feita para um serviço externo, você deseja saber a identidade desse servidor. Assim, você pode definir o campo `dependency.target` adequadamente. Quando o serviço externo não dá suporte a monitoramento – `target` é definido como o nome do host do serviço como `stock-prices-api.com`. No entanto, se esse serviço se identifica retornando um cabeçalho HTTP predefinido – `target` contém a identidade do serviço que permite que o Application Insights crie o rastreamento distribuído consultando a telemetria desse serviço. 

## <a name="correlation-headers"></a>Cabeçalhos de correlação

Estamos trabalhando na proposta de RFC para o [protocolo HTTP de correlação](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v1.md). Essa proposta define dois cabeçalhos:

- `Request-Id` – portar a ID globalmente exclusiva da chamada
- `Correlation-Context` – portar a coleção de pares nome/valor das propriedades de rastreamento distribuído

O padrão também define dois esquemas de geração de `Request-Id` – simples e hierárquico. Com o esquema simples, há uma chave `Id` conhecida definida para a coleção `Correlation-Context`.

O Application Insights define a [extensão](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) para o protocolo HTTP de correlação. Ele usa pares de nome/valor de `Request-Context` para propagar a coleção de propriedades usadas pelo computador chamado ou chamador imediato. O SDK do Application Insights usa esse cabeçalho para definir os campos `dependency.target` e `request.source`.

## <a name="open-tracing-and-application-insights"></a>Rastreamento aberto e Application Insights

Aparência dos modelos de dados de [Rastreamento Aberto](http://opentracing.io/) e do Application Insights 

- `request`, `pageView` mapeia para **Span** com `span.kind = server`
- `dependency` mapeia para **Span** com `span.kind = client`
- `id` de um `request` e `dependency` mapeia para **Span.Id**
- `operation_Id` mapeia para **TraceId**
- `operation_ParentId` mapeia para **Reference** do tipo `ChildOf`

Consulte [modelo de dados](application-insights-data-model.md) para modelo de dados e tipos do Application Insights.

Consulte [especificação](https://github.com/opentracing/specification/blob/master/specification.md) e [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md) para obter definições dos conceitos de rastreamento aberto.


## <a name="telemetry-correlation-in-net"></a>Correlação de telemetria em .NET

Ao longo do tempo, o .NET definiu várias maneiras de correlacionar os logs de diagnóstico e de telemetria. Há `System.Diagnostics.CorrelationManager` permitindo rastrear [LogicalOperationStack e ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx). `System.Diagnostics.Tracing.EventSource` e o ETW (Rastreamento de Eventos para Windows) definem o método [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx). `ILogger` usa [Escopos de log](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes). WCF e HTTP conectam a propagação de contexto "atual".

No entanto, esses métodos não habilitam o suporte a rastreamento distribuído automático. `DiagnosticsSource` é uma maneira de dar suporte automático à correlação entre computadores. Bibliotecas .NET dão suporte à origem de diagnóstico e permitem propagação automática entre computadores do contexto de correlação por meio de transporte como HTTP.

O [Guia para atividades](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) em Origem de Diagnóstico explica as noções básicas do acompanhamento de atividades. 

O ASP.NET Core 2.0 dá suporte à extração de cabeçalhos HTTP e inicia a nova atividade. 

A versão inicial `4.1.0` de `System.Net.HttpClient` dá suporte à injeção automática de cabeçalhos HTTP de correlação e ao acompanhamento da chamada HTTP como uma atividade.

Há um novo módulo HTTP [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/) para o ASP.NET Clássico. Este módulo implementa a correlação de telemetria usando DiagnosticsSource. Ele inicia a atividade com base nos cabeçalhos de solicitação de entrada. Ele correlaciona telemetria de diferentes estágios do processamento da solicitação. Isso se dá mesmo nos casos em que todos os estágios do processamento de IIS são executados em um thread de gerenciamento diferente.

A versão inicial `2.4.0-beta1` do SDK do Application Insights usa DiagnosticsSource e Atividade para coletar a telemetria e associá-la à atividade atual. 

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Correlação de telemetria no SDK do Java
O [SDK do Java do Application Insights](app-insights-java-get-started.md) dá suporte para correlação automática de telemetria a partir da versão `2.0.0`. Ele preenche automaticamente `operation_id` para toda a telemetria (traços, exceções, eventos personalizados e etc.) emitida dentro do escopo de uma solicitação. Ele também cuida da propagação dos cabeçalhos de correlação (descritos acima) para o serviço de chamadas de serviço via HTTP se o [Agendo do SDK do Java](app-insights-java-agent.md) estiver configurado. Observação: somente chamadas feitas via Cliente Apache HTTP têm suporte para o recurso de correlação. Se você estiver usando o Spring Rest Template ou o Feign, ambos podem ser usados com o Cliente Apache HTTP.

Atualmente, a propagação automática de contexto entre tecnologias de mensagens (por exemplo, Kafka, RabbitMQ, Barramento de Serviço do Microsoft Azure) não tem suporte. No entanto, é possível codificar manualmente esses cenários, usando as APIs `trackDependency` e `trackRequest` em que uma telemetria de dependência representa uma mensagem sendo enfileirada por um produtor e a solicitação representa uma mensagem sendo processada por um consumidor. Neste caso, tanto `operation_id` como `operation_parentId` devem ser propagados nas propriedades da mensagem.

<a name="java-role-name"></a>
### <a name="role-name"></a>Nome da função
Às vezes, convém personalizar a maneira como os nomes de componentes são exibidos no [Mapa do Aplicativo](app-insights-app-map.md). Para fazer isso, você pode definir manualmente `cloud_roleName`seguindo um destes procedimentos:

Via um inicializador de telemetria (todos os itens de telemetria são marcados)
```Java
public class CloudRoleNameInitializer extends WebTelemetryInitializerBase {

    @Override
    protected void onInitializeTelemetry(Telemetry telemetry) {
        telemetry.getContext().getTags().put(ContextTagKeys.getKeys().getDeviceRoleName(), "My Component Name");
    }
  }
```
Via a [classe de contexto de dispositivo](https://docs.microsoft.com/et-ee/java/api/com.microsoft.applicationinsights.extensibility.context._device_context) (apenas esse item de telemetria é marcado)
```Java
telemetry.getContext().getDevice().setRoleName("My Component Name");
```

## <a name="next-steps"></a>Próximas etapas

- [Escrever telemetria personalizada](app-insights-api-custom-events-metrics.md)
- Integrar todos os componentes do seu microsserviço no Application Insights. Confira as [plataformas com suporte](app-insights-platforms.md).
- Consulte [modelo de dados](application-insights-data-model.md) para modelo de dados e tipos do Application Insights.
- Saiba como [estender e filtrar a telemetria](app-insights-api-filtering-sampling.md).
