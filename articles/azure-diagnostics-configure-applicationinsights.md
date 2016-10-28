<properties
   pageTitle="Configurar o Diagnóstico do Azure para enviar dados ao Application Insights | Microsoft Azure"
   description="Atualize a configuração pública do Diagnóstico do Azure para enviar dados ao Application Insights."
   services="multiple"
   documentationCenter=".net"
   authors="sbtron"
   manager=""
   editor="" />
<tags
   ms.service="application-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/15/2015"
   ms.author="saurabh" />

# Configurar o Diagnóstico do Azure para enviar dados para o Application Insights

O Diagnóstico do Azure armazena dados em tabelas do Armazenamento do Azure. No entanto, você pode redirecionar todos ou um subconjunto dos dados para o Application Insights configurando "coletores" e "canais" durante o uso da extensão do Diagnóstico do Azure 1.5 ou posterior.

Este artigo descreve como criar a configuração pública da extensão do Diagnóstico do Azure de forma que fique configurada para enviar dados ao Application Insights.

## Configurando o Application Insights como um coletor

A extensão do Diagnóstico do Azure 1.5 introduz o elemento **<SinksConfig>** na configuração pública. Isso define o *coletor* adicional para onde os dados do Diagnóstico do Azure podem ser enviados. Você pode especificar os detalhes do recurso do Application Insights para onde deseja enviar os dados do Diagnóstico do Azure como parte desta **<SinksConfig>**. Uma **SinksConfig** se parece com esta:

	<SinksConfig>
        <Sink name="ApplicationInsights">
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
          <Channels>
            <Channel logLevel="Error" name="MyTopDiagData"  />
            <Channel logLevel="Verbose" name="MyLogData"  />
          </Channels>
        </Sink>
      </SinksConfig>

Para o elemento **Sink**, o atributo *name* especifica um valor de cadeia de caracteres que será usado para se referir exclusivamente ao coletor. O elemento **ApplicationInsights** especifica a chave de instrumentação do recurso do Application Insights para onde os dados do Diagnóstico do Azure serão enviados. Se você não tiver um recurso existente do Application Insights, confira [Criar um novo recurso do Application Insights](./application-insights/app-insights-create-new-resource.md) para saber mais sobre como criar um recurso e obter a chave de instrumentação.

Se você estiver desenvolvendo um projeto de serviço de nuvem com o SDK 2.8 do Azure, essa chave de instrumentação é populada automaticamente na configuração pública baseada na configuração de serviço **APPINSIGHTS\_INSTRUMENTATIONKEY** durante o empacotamento do projeto do serviço de nuvem. Confira [Usar o Application Insights com o Diagnóstico do Azure para solucionar problemas do serviço de nuvem](./cloud-services/cloud-services-dotnet-diagnostics-applicationinsights.md).

O elemento **Channels** elemento permite que você defina um ou mais elementos **Channel** para os dados que serão enviados ao coletor. O canal funciona como um filtro e permite que você selecione os níveis de log específicos que deseja enviar ao coletor. Por exemplo, você pode coletar logs detalhados e enviá-los para armazenamento, mas poderia optar por definir um canal com um nível de log de Erro e, ao enviar logs por aquele canal, somente logs de erro seriam enviados ao coletor. Em um **Channel**, o atributo *name* é usado para se referir exclusivamente a esse canal. O atributo *loglevel* permite que você especifique o nível de log permitido para o canal. Os níveis de log disponíveis na ordem de mais para menos informações
 - Detalhado
 - Informações
 - Aviso
 - Erro
 - Crítico

## Enviar dados para o coletor do Application Insights
Depois que o coletor do Application Insights tiver sido definido, você pode enviar dados para esse coletor adicionando o atributo *sink* aos elementos no nó **DiagnosticMonitorConfiguration**. A adição do elemento *sinks* a cada nó especifica que você deseja que os dados coletados daquele nó e de seus subnós sejam enviados ao coletor especificado.

Por exemplo, se você deseja enviar todos os dados que estão sendo coletados pelo Diagnóstico do Azure, pode adicionar o atributo *sink* diretamente ao nó **DiagnosticMonitorConfiguration**. Defina o valor dos *coletores* como o nome do coletor que foi especificado no **SinkConfig**.

	<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">

Se você quisesse somente enviar logs de erros para o coletor do Application Insights, poderia definir o valor *sinks* como o nome do coletor seguido do nome do canal separado por um ponto ("."). Por exemplo, para enviar somente logs de erros para o coletor do Application Insights, use o canal MyTopDiagdata que foi definido no SinksConfig acima.

	<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">

Se você quisesse somente enviar logs detalhados de aplicativo para o Application Insights, deveria adicionar o atributo *sinks* ao nó **Logs**.

	<Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>

Você também pode incluir vários coletores na configuração em diferentes níveis na hierarquia. Nesse caso, o coletor especificado no nível superior da hierarquia atua como uma configuração global e o especificado no elemento individual atua como uma substituição daquela configuração global.

Eis um exemplo completo do arquivo de configuração pública que envia todos os erros ao Application Insights (especificado no nó **DiagnosticMonitorConfiguration**) e também logs de nível detalhado aos logs de aplicativo (especificados no nó **Logs**).

    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096"
           sinks="ApplicationInsights.MyTopDiagData"> <!-- All info below sent to this channel -->
        <DiagnosticInfrastructureLogs />
        <PerformanceCounters>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="ApplicationInsights.MyLogData/>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
        </PerformanceCounters>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*" />
        </WindowsEventLog>
        <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose"
                sinks="ApplicationInsights.MyLogData"/> <!-- This specific info sent to this channel -->
      </DiagnosticMonitorConfiguration>

    <SinksConfig>
        <Sink name="ApplicationInsights">
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
          <Channels>
            <Channel logLevel="Error" name="MyTopDiagData"  />
            <Channel logLevel="Verbose" name="MyLogData"  />
          </Channels>
        </Sink>
      </SinksConfig>
    </WadCfg>

![Configuração pública do Diagnóstico](./media/azure-diagnostics-configure-applicationinsights/diagnostics-publicconfig.png)

É preciso ter atenção a algumas limitações dessa funcionalidade

- Os canais devem funcionar somente com o tipo de log, e não com os contadores de desempenho. Se você especificar um canal com um elemento contador de desempenho, ele será ignorado.
- O nível de log para um canal não pode exceder o nível de log do que está sendo coletado pelo Diagnóstico do Azure. Por exemplo: não é possível coletar erros no Log de Aplicativo no elemento Logs e tentar enviar logs detalhados ao coletor do Application Insights. O atributo *scheduledTransferLogLevelFilter* sempre deve coletar uma quantidade igual ou maior de logs que a quantidade de logs que você está tentando enviar para um coletor.
- Você não pode enviar dados de blob coletados pela extensão do Diagnóstico do Azure ao Application Insights. Por exemplo, nada especificado no nó *Directories*. No caso de despejos de memória, o despejo de memória real ainda será enviado para o armazenamento de blobs e somente uma notificação da geração do despejo será enviada ao Application Insights.


## Próximas etapas

- Use o [PowerShell](./cloud-services/cloud-services-diagnostics-powershell.md) para habilitar a extensão do Diagnóstico do Azure para seu aplicativo. 
- Use o [Visual Studio](vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) para habilitar a extensão do Diagnóstico do Azure para seu aplicativo

<!---HONumber=AcomDC_0413_2016-->