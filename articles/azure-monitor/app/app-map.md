---
title: Mapa de aplicativos no Azure Application Insights | Microsoft Docs
description: Monitorar topologias complexas de aplicativos com o mapa do aplicativo
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 11f7bb69ed408adf87d62a4af1aa4bd87e70bd6d
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2019
ms.locfileid: "59009188"
---
# <a name="application-map-triage-distributed-applications"></a>Mapa do Aplicativo: Triagem dos Aplicativos Distribuídos

O mapa do aplicativo ajuda você a identificar gargalos de desempenho ou pontos de acesso com falha em todos os componentes dos seus aplicativos distribuídos. Cada nó do mapa representa um componente de aplicativo ou suas dependências e esses nós também têm KPIs de integridade e alertas de status. Você pode clicar em qualquer componente para obter diagnóstico mais detalhado, como eventos do Application Insights. Se seu aplicativo usar os serviços do Azure, você também poderá clicar no diagnóstico do Azure, como nas recomendações do Assistente do Banco de Dados SQL.

## <a name="what-is-a-component"></a>O que é um componente?

Os componentes são partes independentes dos aplicativos de microsserviços/distribuídos. As equipes de operações e desenvolvedores têm acesso ou visibilidade nível de código para telemetria gerada por esses componentes de aplicativos. 

* Os componentes são diferentes das dependências externas "observadas", como SQL, EventHub etc., a qual sua organização/equipe pode não ter acesso (código ou telemetria).
* Os componentes são executados em qualquer número de instâncias de contêiner/função/servidor.
* Os componentes podem ser chaves de instrumentação do Application Insights separadas (mesmo se as assinaturas forem diferentes) ou diferentes funções relatando para uma única chave de instrumentação do Application Insights. A experiência do mapa de visualização mostra os componentes independentemente de como eles estão configurados.

## <a name="composite-application-map"></a>Mapa do aplicativo composto

Você pode ver a topologia do aplicativo completa em vários níveis de componentes de aplicativos relacionados. Os componentes podem ser recursos diferentes do Application Insights ou funções diferentes em um único recurso. O mapa do aplicativo localiza os componentes seguindo qualquer chamada de dependência HTTP feita entre os servidores com o SDK do Application Insights instalado. 

Essa experiência começa com a descoberta progressiva dos componentes. Ao carregar o mapa do aplicativo pela primeira vez, um conjunto de consultas é disparado para descobrir os componentes relacionados a esse componente. Um botão no canto superior esquerdo será atualizado com o número de componentes em seu aplicativo, conforme eles são descobertos. 

Ao clicar em "Atualizar componentes do mapa", o mapa será atualizado com todos os componentes descobertos até aquele momento. Dependendo da complexidade do seu aplicativo, isso pode levar um minuto para carregar.

Se todos os componentes são funções em um único recurso do Application Insights, essa etapa de descoberta não é necessária. A carga inicial para esse tipo de aplicativo terá todos os respectivos componentes.

![Captura de tela do mapa do aplicativo](media/app-map/app-map-001.png)

Um dos principais objetivos com essa experiência é ser capaz de visualizar topologias complexas com centenas de componentes.

Clique em qualquer componente para ver as respectivas informações e acesse a experiência de triagem de desempenho e falha desse componente.

![Submenu](media/app-map/application-map-002.png)

### <a name="investigate-failures"></a>Investigar falhas

Selecione **investigar falhas** para iniciar o painel de falhas.

![Captura de tela do botão Investigar falhas](media/app-map/investigate-failures.png)

![Captura de tela da experiência de falhas](media/app-map/failures.png)

### <a name="investigate-performance"></a>Investigar o desempenho

Para solucionar problemas de desempenho selecione **Investigar o desempenho**.

![Captura de tela do botão Investigar desempenho](media/app-map/investigate-performance.png)

![Captura de tela da experiência de desempenho](media/app-map/performance.png)

### <a name="go-to-details"></a>Acessar detalhes

Selecione **Acessar detalhes** para explorar a experiência de transação de ponta a ponta que pode oferecer exibições feitas no nível da pilha de chamadas.

![Captura de tela do botão Acessar detalhes](media/app-map/go-to-details.png)

![Captura de tela de detalhes da transação de ponta a ponta](media/app-map/end-to-end-transaction.png)

### <a name="view-in-analytics"></a>Exibir na análise

Para consultar e investigar ainda mais os dados do aplicativos, clique em **Exibir na análise**.

![Captura de tela do botão Exibir na análise](media/app-map/view-in-analytics.png)

![Captura de tela da experiência de análise](media/app-map/analytics.png)

### <a name="alerts"></a>Alertas

Para exibir alertas ativos e as regras subjacentes que fazem com que os alertas sejam disparados, selecione **alertas**.

![Captura de tela do botão de alertas](media/app-map/alerts.png)

![Captura de tela da experiência de análise](media/app-map/alerts-view.png)

## <a name="set-cloudrolename"></a>Set cloud_RoleName

O mapa de aplicativo usa a propriedade `cloud_RoleName` para identificar os componentes no mapa. O SDK do Application Insights adiciona automaticamente a `cloud_RoleName` propriedade à telemetria emitida pelos componentes. Por exemplo, o SDK adicionará um nome do site ou nome de função de serviço para a propriedade `cloud_RoleName`. No entanto, há casos em que você talvez queira substituir o valor padrão. Para substituir o cloud_RoleName e alterar o que é exibido no Mapa do Aplicativo:

### <a name="net"></a>.NET

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here
                telemetry.Context.Cloud.RoleName = "Custom RoleName";
                telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance"
            }
        }
    }
}
```

**Carregue seu inicializador**

Em ApplicationInsights.config:

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

Como alternativa, você pode instanciar o inicializador no código, por exemplo em Global.aspx.cs:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
    }
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();
appInsights.defaultClient.context.tags["ai.cloud.role"] = "your role name";
appInsights.defaultClient.context.tags["ai.cloud.roleInstance"] = "your role instance";
```

### <a name="alternate-method-for-nodejs"></a>Método alternativo para o Node. js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();

appInsights.defaultClient.addTelemetryProcessor(envelope => {
    envelope.tags["ai.cloud.role"] = "your role name";
    envelope.tags["ai.cloud.roleInstance"] = "your role instance"
});
```

### <a name="java"></a>Java

Se você usar o Spring Boot com o iniciador do Spring Boot do Application Insights, a única alteração necessária é definir seu nome personalizado para o aplicativo no arquivo application.properties.

`spring.application.name=<name-of-app>`

O iniciador do Spring Boot atribuirá automaticamente cloudRoleName para o valor inserido para a propriedade spring.application.name.

Para obter mais informações sobre a correlação de Java e como configurar cloudRoleName para check-out de aplicativos não-SpringBoot verifique essa [seção](https://docs.microsoft.com/azure/application-insights/application-insights-correlation#role-name) na correlação.

### <a name="clientbrowser-side-javascript"></a>JavaScript do lado do cliente/navegador

```javascript
appInsights.queue.push(() => {
appInsights.context.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

### <a name="understanding-cloudrolename-within-the-context-of-the-application-map"></a>Noções básicas sobre Cloud.RoleName dentro do contexto do mapa do aplicativo

Tão distante como pensar sobre Cloud.RoleName pode ser útil examinar um mapa de aplicativos que tem vários Cloud.RoleNames presente:

![Captura de tela do mapa do aplicativo](media/app-map/cloud-rolename.png)

No mapa de aplicativo acima de cada um dos nomes na caixas verdes são Cloud.RoleName/role valores diferentes aspectos deste aplicativo distribuído específico. Portanto, para este aplicativo suas funções consistem em: `Authentication`, `acmefrontend`, `Inventory Management`, um `Payment Processing Worker Role`. 

No caso desse aplicativo cada desses `Cloud.RoleNames` também representa um recurso exclusivo diferente do Application Insights com suas próprias chaves de instrumentação. Como o proprietário do aplicativo tem acesso a cada um desses quatro recursos diferentes do Application Insights, mapa do aplicativo é capaz de reunir um mapa das relações subjacentes.

Para o [definições oficiais](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93):

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

Como alternativa, pode ser útil para cenários em que você informa o problema está em algum lugar no seu front-end da web, mas talvez você esteja executando o front-end da web em vários servidores com balanceamento de carga isso ser capaz de fazer uma busca de uma camada mais profunda Cloud.RoleName Cloud.RoleInstance por meio de consultas do Kusto e saber se o problema está afetando todas as instâncias da web front-end servidores/ou apenas um deles pode ser extremamente importante.

Um cenário em que você talvez queira substituir o valor para Cloud.RoleInstance seria se o aplicativo é executado em um ambiente em contêineres em que apenas saber o servidor individual pode não ser informações suficientes para localizar um determinado problema.

Para obter mais informações sobre como substituir a propriedade cloud_RoleName com inicializadores de telemetria, consulte [adicionar propriedades: ITelemetryInitializer](api-filtering-sampling.md#add-properties-itelemetryinitializer).

## <a name="troubleshooting"></a>solução de problemas

Se você estiver tendo dificuldades para obter o Mapa do aplicativo para trabalhar conforme esperado, tente essas etapas:

1. Certifique-se que você está usando um SDK com suporte oficial. SDKs de comunidade/sem suporte podem não dar suporte à correlação.

    Consulte este [artigo](https://docs.microsoft.com/azure/application-insights/app-insights-platforms) para obter uma lista dos SDKs com suporte.

2. Atualize todos os componentes para a versão mais recente do SDK.

3. Se você estiver usando o Azure Functions com C#, atualize para o [Functions V2](https://docs.microsoft.com/azure/azure-functions/functions-versions).

4. Confirme se [cloud_RoleName](#set-cloud_rolename) está configurado corretamente.

5. Se estiver faltando uma dependência, verifique se ele está na lista de [dependências coletadas automaticamente](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies). Se não, você ainda poderá acompanhá-lo manualmente com uma chamada [acompanhar dependência](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

## <a name="portal-feedback"></a>Comentários do Portal

Para fornecer feedback, use a opção de feedback.

![Imagem de MapLink-1](./media/app-map/14-updated.png)

## <a name="next-steps"></a>Próximas etapas

* [Noções básicas sobre correlação](https://docs.microsoft.com/azure/application-insights/application-insights-correlation)