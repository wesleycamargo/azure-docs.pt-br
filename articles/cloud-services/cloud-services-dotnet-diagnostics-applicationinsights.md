---
title: Solucionar problemas de Serviços de Nuvem usando o Application Insights | Microsoft Docs
description: Saiba como solucionar problemas do serviço de nuvem usando o Application Insights para processar dados do Diagnóstico do Azure.
services: cloud-services
documentationcenter: .net
author: sbtron
manager: timlt
editor: tysonn

ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2015
ms.author: saurabh

---
# Solucionar problemas de Serviços de Nuvem usando o Application Insights
Com o [Azure SDK 2.8](https://azure.microsoft.com/downloads/) e a extensão do Diagnóstico do Azure 1.5, você pode enviar seus dados do Diagnóstico do Azure de seu serviço de nuvem diretamente para o Application Insights. Os vários tipos de logs coletados pelo Diagnóstico do Azure, incluindo logs de aplicativo, logs de eventos do Windows, logs ETW e contadores de desempenho, podem ser enviados ao Application Insights e visualizados na interface do usuário do portal do Application Insights. Quando usado com o SDK do Application Insights, você pode obter informações sobre métricas e logs provenientes de seu aplicativo, bem como dados de sistema e de nível de infraestrutura provenientes do Diagnóstico do Azure.

## Configurar o Diagnóstico do Azure para enviar dados para o Application Insights
Siga estas etapas para configurar seu projeto de serviço de nuvem e enviar os dados do Diagnóstico do Azure para o Application Insights.

1) No Gerenciador de Soluções do Visual Studio, clique em uma função e selecione **propriedades** para abrir o Designer de função

![Propriedades da função Gerenciador de Soluções][1]

2) No designer de função, na seção Diagnóstico, marque a caixa de seleção para **Enviar dados de diagnóstico ao Application Insights**

![O designer de função envia dados de diagnóstico para o application insights][2]

3) Na caixa de diálogo pop-up, selecione o recurso do Application Insights ao qual você gostaria de enviar os dados de diagnóstico do Azure. O diálogo permite que você selecione um recurso do Application Insights existente na sua assinatura ou especifique manualmente uma chave de instrumentação para um recurso do Application Insights. Se você não tiver um recurso existente do Application Insights, pode criá-lo clicando no link **Criar um novo recurso** para abrir uma janela do navegador para o portal clássico do Azure, onde pode criar um recurso do Application Insights. Para saber mais sobre como criar um recurso do Application Insights, confira [Criar um novo recurso do Application Insights](../application-insights/app-insights-create-new-resource.md)

![selecionar recurso do application insights][3]

4) Depois de adicionar o recurso do Application Insights, a chave de instrumentação para esse recurso fica armazenada como uma definição de configuração de serviço com o nome **APPINSIGHTS\_INSTRUMENTATIONKEY**. Você pode alterar essa definição de configuração para cada configuração ou ambiente de serviço selecionando uma configuração diferente na lista suspensa de configuração de serviço e especificando uma nova chave de instrumentação para essa configuração.

![selecionar configuração de serviço][4]

A configuração **APPINSIGHTS\_INSTRUMENTATIONKEY** é usada pelo Visual Studio para configurar a extensão de diagnóstico com as informações apropriadas do recurso do Application Insights durante a publicação. A configuração é uma maneira conveniente de definir chaves de instrumentação diferentes para configurações de serviço diferentes. O Visual Studio irá converter essa configuração e inseri-la na configuração pública da extensão de diagnóstico durante a publicação. Para simplificar o processo de configuração da extensão de diagnóstico com o PowerShell, o pacote de saída do Visual Studio também contém o XML de configuração pública com a chave de instrumentação do Application Insights incluída. Os arquivos de configuração pública são criados na pasta Extensões e seguem o padrão PaaSDiagnostics.<RoleName>.PubConfig.xml. Todas as implantações baseadas em PowerShell podem usar esse padrão para mapear cada configuração para uma função.

5) A habilitação de **Enviar dados de diagnóstico ao Application Insights** configurará automaticamente o Diagnóstico do Azure para enviar todos os contadores de desempenho e logs de nível de erro coletados pelo agente de diagnóstico do Azure ao Application Insights. Se você deseja configurar quais dados são enviados ao Application Insights, precisa editar manualmente o arquivo *diagnostics.wadcfgx* para cada função. Confira [Configurar o Diagnóstico do Azure para enviar dados ao Application Insights](../azure-diagnostics-configure-applicationinsights.md) para saber mais sobre como atualizar a configuração manualmente.

Depois que o Serviço de Nuvem é configurado para enviar dados do Diagnóstico do Azure ao Application Insights, você pode implantá-lo no Azure como faria normalmente, verificando se a extensão de diagnóstico do Azure está habilitada. Confira [Publicando um serviço de nuvem usando o Visual Studio](../vs-azure-tools-publishing-a-cloud-service.md).

## Exibindo dados do Diagnóstico do Azure no Application Insights
A telemetria de diagnóstico do Azure aparecerá no recurso do Application Insights configurado para seu serviço de nuvem.

Abaixo mostramos como os vários tipos de log de diagnóstico do Azure mapeiam para conceitos do Application Insights:

* Contadores de desempenho são exibidos como métricas personalizadas no Application Insights
* Os logs de eventos do Windows são mostrados como Rastreamentos e Eventos Personalizados no Application Insights
* Logs de aplicativo, Logs de ETW e logs de infraestrutura de diagnóstico são mostrados como rastreamentos no Application Insights.

Para exibir dados do Diagnóstico do Azure no Application Insights:

* Use o [Metrics Explorer](../application-insights/app-insights-metrics-explorer.md) para visualizar contadores de desempenho personalizados ou contagens de diferentes tipos de eventos do log de eventos do Windows.

![Métricas personalizadas no Metrics Explorer][5]

* Use [Pesquisa](../application-insights/app-insights-diagnostic-search.md) para pesquisar os vários logs de rastreamento enviados pelo Diagnóstico do Azure. Por exemplo, se você tivesse uma exceção sem tratamento em uma Função que fizesse essa Função parar de funcionar e reciclar, essas informações seriam mostradas no canal *Aplicativo* do *Log de Eventos do Windows*. Você pode usar a funcionalidade Pesquisar para ver o erro do log de eventos do Windows e obter o rastreamento de pilha completo da exceção, permitindo que você encontre a causa principal do problema.

![Pesquisar rastreamentos][6]

## Próximas etapas
* [Adicionar o SDK do Application Insights ao seu serviço de nuvem](../application-insights/app-insights-cloudservices.md) para enviar dados sobre solicitações, exceções, dependências e telemetria personalizada do seu aplicativo. Combinado com os dados do Diagnóstico do Azure, você pode obter uma visão completa do seu aplicativo e do sistema no mesmo recurso do Application Insights.  

<!--Image references-->
[1]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/solution-explorer-properties.png
[2]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-sendtoappinsights.png
[3]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/select-appinsights-resource.png
[4]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-appinsights-serviceconfig.png
[5]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/metrics-explorer-custom-metrics.png
[6]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/search-windowseventlog-error.png

<!---HONumber=AcomDC_0511_2016-->