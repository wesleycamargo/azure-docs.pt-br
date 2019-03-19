---
title: Monitorar desempenho dos serviços de aplicativos do Azure | Microsoft Docs
description: Monitoramento do desempenho de aplicativos para serviços de aplicativos do Azure. Tempo de resposta e de carga, informações de dependência e alertas definidos sobre o desempenho do gráfico.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0b2deb30-6ea8-4bc4-8ed0-26765b85149f
ms.service: application-insights
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: mbullwin
ms.openlocfilehash: 92a7c1a45655f8804aa1f81b1a77ebf7cd5197e8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58122158"
---
# <a name="monitor-azure-app-service-performance"></a>Monitorar o desempenho do Serviço de Aplicativo do Azure
No [portal do Azure](https://portal.azure.com), você pode configurar o monitoramento de desempenho dos aplicativos Web, dos back-ends móveis e dos aplicativos de API no [Serviço de Aplicativo do Azure](../../app-service/overview.md). O [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) equipa seu aplicativo para enviar a telemetria sobre suas atividades para o serviço Application Insights, onde ela é armazenada e analisada. Lá, os gráficos de métricas e as ferramentas de pesquisa podem ser usados para ajudar a diagnosticar problemas, melhorar o desempenho e avaliar o uso.

## <a name="runtime-or-build-time"></a>Tempo de compilação ou de tempo de execução
Você pode configurar o monitoramento por meio da instrumentação do aplicativo de duas maneiras:

* **Tempo de execução** -você pode selecionar um desempenho extensão de monitoramento quando seu serviço de aplicativo já estiver ativo. Não é necessário recompilar ou reinstalar o aplicativo. Obtenha um conjunto padrão de pacotes que monitoram os tempos de resposta, taxas de êxito, exceções, dependências e assim por diante.

* **Tempo de compilação** - você pode instalar um pacote em seu aplicativo em desenvolvimento. Essa opção é mais versátil. Além dos mesmos pacotes padrão, você pode escrever código para personalizar a telemetria ou para enviar sua própria telemetria. Você pode registrar eventos de registro de acordo com a semântica do seu domínio de aplicativo ou de atividades específicas. Isso também lhe permite testar a versão mais recente do SDK do Application Insights, como você pode optar por avaliar o beta SDKs, enquanto o monitoramento de tempo de execução é restrito para a versão estável mais recente.

## <a name="runtime-instrumentation-with-application-insights"></a>Instrumentação de tempo de execução com o Application Insights
Se você estiver executando um serviço de aplicativo no Azure, você obtém um monitoramento: taxas de solicitação e de erro por padrão. Adicione o Application Insights para obter mais, como tempos de resposta, monitoramento de chamadas para dependências, detecção inteligente e o acesso à poderosa linguagem de consulta do Kusto. 

1. **Selecione o Application Insights** no painel de controle do Azure para seu serviço de aplicativo.

    ![Em Configurações, escolha Insights do aplicativo](./media/azure-web-apps/settings-app-insights.png)

   * Opte por criar um novo recurso, a menos que você já tenha configurado um recurso do Application Insights para este aplicativo. 

     > [!NOTE]
     > Ao clicar em **OK** para criar o novo recurso, você será solicitado a **Aplicar configurações de monitoramento**. Se você selecionar **Continuar**, vinculará seu novo recurso do Application Insights ao seu serviço de aplicativo. Fazer isso também **disparará uma reinicialização do seu serviço de aplicativo**. 

     ![Instrumentar seu aplicativo Web](./media/azure-web-apps/create-resource.png)

2. Depois de especificar qual recurso usar, você pode escolher como deseja que o Application Insights colete dados por plataforma para seu aplicativo. Monitoramento de aplicativo do ASP.NET é ativado por padrão com dois níveis diferentes de coleção.

    ![Escolha as opções por plataforma](./media/azure-web-apps/choose-options-new.png)

   * O nível **Coleção básica** do .NET oferece recursos essenciais de APM de instância única.
    
   * O nível **Coleção recomendada** do .NET:
       * Adiciona as tendências de uso de CPU, memória e E/S.
       * Correlaciona microsserviços entre limites de solicitação/dependência.
       * Coleta as tendências de uso e permite a correlação entre resultados de disponibilidade e transações.
       * Coleta as exceções não tratadas pelo processo de host.
       * Aumenta a precisão de métricas de APM com carga quando a amostragem é usada.
    
     O .NET core oferece **recomendado coleta** ou **desabilitado** para .NET Core 2.0 e 2.1.

3. **Instrumente seu serviço de aplicativo** após a instalação do Application Insights.

   **Habilite o monitoramento do lado do cliente** para telemetria de usuário e exibição de página.

    (Isso está habilitado por padrão em aplicativos .NET Core com **Coleção recomendada**, independentemente de a configuração do aplicativo 'APPINSIGHTS_JAVASCRIPT_ENABLED' estar presente. O suporte granular baseado em interface do usuário para desabilitar o monitoramento do lado do cliente não está disponível atualmente para o .NET Core.)
    
   * Selecione Configurações > Configurações do Aplicativo
   * Em configurações do aplicativo, adicione um novo par de chave/valor:

     Chave: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Valor: `true`
   * **Salve** as configurações e **Reinicie** seu aplicativo.

4. Explore os dados de monitoramento de seu aplicativo selecionando **Configurações** >  **Insights de aplicativos** > **Veja mais em Insights de aplicativos**.

Posteriormente, você poderá compilar o aplicativo com o Application Insights, se desejar.

*Como remover o Application Insights ou alternar para envio para outro recurso?*

* No Azure, abra a folha de controle de aplicativo Web e, em Ferramentas de Desenvolvimento, abra **Application Insights**. Você pode desativar o Application Insights clicando em **Desabilitar** na parte superior, ou selecione um novo recurso na seção **Alterar o recurso**.

## <a name="build-the-app-with-application-insights"></a>Compilar seu aplicativo com o Application Insights
O Application Insights pode fornecer dados de telemetria mais detalhados instalando um SDK em seu aplicativo. Em particular, você pode coletar logs de rastreamento, [escrever telemetria personalizada](../../azure-monitor/app/api-custom-events-metrics.md) e obter relatórios de exceção mais detalhados.

1. **No Visual Studio** (2013 atualização 2 ou posterior), adicione o Application Insights ao seu projeto.

    Clique com o botão direito do mouse no projeto da Web e selecione **Add> Application Insights** ou **Project** > **Application Insights** > **Configure Application Insights**.

    ![Clique com o botão direito do mouse no projeto da Web e escolha Adicionar ou Configurar o Application Insights](./media/azure-web-apps/03-add.png)

    Se receber uma solicitação para se conectar, use as credenciais da conta do Azure.

    A operação tem dois efeitos:

   1. Cria um recurso do Application Insights no Azure, onde a telemetria é armazenada, analisada e exibida.
   2. Adiciona o pacote NuGet do Application Insights ao seu código (se ainda não estiver lá) e o configura para enviar telemetria ao recurso do Azure.
2. **Teste a telemetria** executando o aplicativo no computador de desenvolvimento (F5).
3. **Publique o aplicativo** no Azure como de costume. 

*Como eu mudo para enviar dados para um recurso diferente do Application Insights?*

* No Visual Studio, clique com o botão direito do mouse no projeto, escolha **Configurar o Application Insights** e escolha o recurso desejado. Você obtém a opção para criar um novo recurso. Recompilar e reimplantar.

## <a name="automate-monitoring"></a>Automatizar o monitoramento

Para habilitar a coleta de telemetria com o Application Insights somente as configurações do aplicativo precisam ser definido:

   ![Configurações do aplicativo de serviço de aplicativo com as configurações disponíveis do Application Insights](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Definições de configurações do aplicativo

|Nome da configuração do aplicativo |  Definição | Valor |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Extensão principal, que controla o tempo de execução de monitoramento. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  Em default recursos essenciais, apenas em modo são habilitados para garantir um desempenho ideal. | `default` ou `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Controla se o mecanismo de regravação de binário `InstrumentationEngine` será ativado. Essa configuração tem implicações de desempenho e afeta o tempo de início/inicialização a frio. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Controla se o texto de tabela do SQL e o Azure será capturada junto com as chamadas de dependência. Aviso de desempenho: isso requer o `InstrumentationEngine`. | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Configurações do aplicativo de serviço de aplicativo com o Azure Resource Manager

Configurações de aplicativo para serviços de aplicativos podem ser gerenciadas e configuradas com [modelos do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Esse método pode ser usado durante a implantação de novos recursos de serviço de aplicativo com a automação do Azure Resource Manager ou para modificar as configurações dos recursos existentes.

A estrutura básica de configurações do aplicativo JSON para um serviço de aplicativo está abaixo:

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]

```

Para obter um exemplo de um Gerenciador de recursos do Azure modelo com configurações de aplicativo configurado para o Application Insights isso [modelo](https://github.com/Andrew-MSFT/BasicImageGallery) podem ser úteis, especificamente a seção começando na [linha 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Automatize a criação de um recurso do Application Insights e um link para seu serviço de aplicativo recém-criado.

Para criar um modelo do Azure Resource Manager com todas as configurações de Insights de aplicativo padrão configuradas, inicie o processo de como se você fosse criar um novo aplicativo Web com o Application Insights habilitado.

Selecione **opções de automação**

   ![Menu de criação de aplicativo do serviço de aplicativo web](./media/azure-web-apps/create-web-app.png)

Isso gera o modelo mais recente do Azure Resource Manager com todas as configurações necessárias.

  ![Modelo de aplicativo do serviço de aplicativo web](./media/azure-web-apps/arm-template.png)

> [!NOTE]
> O modelo gerará as configurações do aplicativo no modo de "default". Esse modo é o desempenho otimizado, embora você pode modificar o modelo para ativar qualquer recursos que você preferir.

## <a name="more-telemetry"></a>Mais telemetria

* [Carregar dados da página da Web](../../azure-monitor/app/javascript.md)
* [Telemetria personalizada](../../azure-monitor/app/api-custom-events-metrics.md)

## <a name="troubleshooting"></a>solução de problemas

### <a name="do-i-still-need-to-go-to-extensions---add---application-insights-extension-for-new-app-service-apps"></a>Eu ainda preciso ir para extensões - Adicionar - extensão do Application Insights para novos aplicativos de serviço de aplicativo?

Não, você não precisa adicionar a extensão manualmente. Habilitar o Application Insights por meio da folha de configurações, você adicionará todas as configurações de aplicativo necessárias para habilitar o monitoramento. Isso agora é possível, porque os arquivos adicionados anteriormente pela extensão agora estão [pré-instalado](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) como parte da imagem do serviço de aplicativo. Os arquivos estão localizados em `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent`.

### <a name="if-runtime-and-build-time-monitoring-are-both-enabled-do-i-end-up-with-duplicate-data"></a>Se o tempo de execução e monitoramento de tempo de compilação estão habilitados, terminou com dados duplicados?

Não, por padrão se o monitoramento de tempo de compilação for detectado através da extensão de monitoramento do tempo de execução interromperá o envio de dados e apenas o tempo de compilação que configuração de monitoramento será respeitado. A determinação de desabilitar o monitoramento de tempo de execução ou não se baseia na detecção de qualquer um desses três arquivos:

* Microsoft.ApplicationInsights dll
* Dll de Microsoft.ASP.NET.TelemetryCorrelation
* Dll de diagnosticsource

É importante ter em mente que em muitas versões do Visual Studio, alguns ou todos esses arquivos são adicionados por padrão para os arquivos de modelo do ASP.NET e ASP.NET Core Visual Studio. Se seu projeto foi criado com base em um dos modelos, mesmo se você ainda não tiver habilitado o Application Insights explicitamente, a presença da dependência do arquivo impediria de monitoramento de tempo de execução da ativação.

### <a name="appinsightsjavascriptenabled-causes-incomplete-html-response-in-net-core-web-applications"></a>APPINSIGHTS_JAVASCRIPT_ENABLED gera resposta HTML incompleta em aplicativos Web NET CORE.

Habilitar o Javascript por meio de Serviços de Aplicativos pode fazer respostas html serem cortadas.

* Solução alternativa 1: definir a Configuração de Aplicativo APPINSIGHTS_JAVASCRIPT_ENABLED como false ou removê-la completamente e reiniciar
* Solução alternativa 2: adicionar o sdk por meio de código e remover a extensão (Profiler e depurador de instantâneos não funcionarão com essa configuração)

Para acompanhar esse problema, vá para [extensão do Azure, fazendo com que a resposta incompleta do HTML](https://github.com/Microsoft/ApplicationInsights-Home/issues/277).

Os itens abaixo **não têm suporte** para .NET Core atualmente:

* Implantação autossuficiente.
* Aplicativos destinados ao .NET Framework.
* Aplicativos .NET Core 2.2.

> [!NOTE]
> Há suporte para .NET Core 2.0 e .NET Core 2.1. Este artigo será atualizado quando o suporte do .NET Core 2.2 for adicionado.

## <a name="next-steps"></a>Próximas etapas
* [Executar o criador de perfil em seu aplicativo ativo](../../azure-monitor/app/profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) – monitorar o Azure Functions com o Application Insights
* [Permita que o diagnóstico do Azure](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md) seja enviado ao Application Insights.
* [Monitore as métricas de integridade do serviço](../../azure-monitor/platform/data-collection.md) para se certificar de que o serviço esteja disponível e responsivo.
* [Receba notificações de alerta](../../azure-monitor/platform/alerts-overview.md) sempre que ocorrerem eventos operacionais ou métricas ultrapassarem um limite.
* Use o [Application Insights para aplicativos JavaScript e páginas Web](../../azure-monitor/app/javascript.md) para obter a telemetria do cliente dos navegadores que visitam uma página da Web.
* [Configure testes de disponibilidade da Web](../../azure-monitor/app/monitor-web-app-availability.md) para ser alertado se o seu site for desativado.