<properties
    pageTitle="Enviar logs do Diagnóstico do Azure ao Application Insights"
    description="Configure os detalhes dos logs de diagnóstico dos Serviços de Nuvem do Azure que são enviados ao portal do Application Insights."
    services="application-insights"
    documentationCenter=".net"
    authors="sbtron"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
	ms.date="11/17/2015"
    ms.author="awills"/>

# Configurar o registro em log do Diagnóstico do Azure no Application Insights

Quando você configura um projeto de Serviços de Nuvem ou uma máquina virtual no Microsoft Azure, [o Azure pode gerar um log de diagnóstico](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md). Você pode enviar esse log ao Application Insights para que possa analisá-lo junto com a telemetria de diagnóstico e de uso enviada do aplicativo pelo SDK do Application Insights. O log do Azure inclui eventos do gerenciamento do aplicativo, por exemplo, iniciar, parar, falhas e contadores de desempenho. O log também inclui chamadas no aplicativo para System.Diagnostics.Trace.

Este artigo descreve detalhadamente a configuração da captura do diagnóstico.

Você precisa do SDK 2.8 do Azure instalado no Visual Studio.

## Obter um recurso do Application Insights

Para obter a melhor experiência, [adicione o SDK do Application Insights para cada função de seu aplicativo de Serviços de Nuvem](app-insights-cloudservices.md), ou [para qualquer aplicativo que será executado em sua VM](app-insights-get-started.md). Você pode enviar os dados de diagnóstico para análise e exibição ao mesmo recurso do Application Insights.

Como alternativa, se você não quiser usar o SDK, por exemplo, se o aplicativo já estiver funcionando, você pode simplesmente [criar um novo recurso do Application Insights](app-insights-create-new-resource.md) no Portal do Azure. Escolha **Diagnóstico do Azure** como o tipo de aplicativo.


## Enviar o diagnóstico do Azure ao Application Insights

Se você conseguir atualizar seu projeto de aplicativo, selecione, no Visual Studio, cada função, escolha suas propriedades e na guia Configuração, selecione **Enviar diagnósticos ao Application Insights**.

Se o seu aplicativo já estiver funcionando, use o Gerenciador de Servidores do Visual Studio ou os Serviços de Nuvem para abrir as propriedades do aplicativo. Selecione **Enviar diagnósticos ao Application Insights**.

Em cada caso, você receberá uma solicitação pelos detalhes do recurso do Application Insights que você criou.

[Saiba mais sobre como configurar o Application Insights para um aplicativo de Serviços de Nuvem](app-insights-cloudservices.md).

## Configurando o adaptador de diagnóstico do Azure

Continue a leitura somente se você quiser selecionar as partes do log que envia ao Application Insights. Por padrão, tudo é enviado, incluindo: eventos do Microsoft Azure; contadores de desempenho; chamadas de rastreamento do aplicativo para System.Diagnostics.Trace.

O Diagnóstico do Azure armazena dados em tabelas do Armazenamento do Azure. No entanto, você pode redirecionar todos ou um subconjunto dos dados para o Application Insights configurando "coletores" e "canais" durante o uso da extensão do Diagnóstico do Azure 1.5 ou posterior.

### Configurando o Application Insights como um coletor

Quando você usa as propriedades de função para definir "Enviar dados ao Application Insights", o SDK do Azure (2.8 ou posterior) adiciona um elemento `<SinksConfig>` ao [arquivo de configuração público do Diagnóstico do Azure](https://msdn.microsoft.com/library/azure/dn782207.aspx) da função.

`<SinksConfig>` define o coletor adicional no qual os dados do diagnóstico do Azure podem ser enviados. Um `SinksConfig` de exemplo tem esta aparência:

```xml

    <SinksConfig>
     <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
     </Sink>
    </SinksConfig>

```

O elemento `ApplicationInsights` especifica a chave de instrumentação que identifica o recurso do Application Insights para onde os dados do Diagnóstico do Azure serão enviados. Quando você seleciona o recurso, ele é automaticamente preenchido com base na configuração do serviço do `APPINSIGHTS_INSTRUMENTATIONKEY`. (Se você quiser defini-lo manualmente, obtenha a chave na lista suspensa Essentials do recurso.)

`Channels` define os dados que serão enviados ao coletor. O canal funciona como um filtro. O atributo `loglevel` permite que você especifique o nível de log que o canal enviará. Os valores disponíveis são: `{Verbose, Information, Warning, Error, Critical}`.

### Enviar dados ao coletor

Enviar dados ao coletor do Application Insights adicionando o atributo sinks ao nó DiagnosticMonitorConfiguration. A adição do elemento sinks a cada nó especifica que você deseja que os dados coletados daquele nó e de seus subnós sejam enviados ao coletor especificado.

Por exemplo, o padrão criado pelo SDK do Azure é enviar todos os dados de diagnóstico do Azure:

```xml

    <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```

Mas se você quiser enviar apenas os logs de erro, qualifique o nome do coletor com um nome de canal:

```xml

    <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```

Observe que estamos usando o nome do Coletor que definimos, junto com o nome de um canal definido acima.

Se você quisesse somente enviar logs detalhados de aplicativo para o Application Insights, deveria adicionar o atributo sinks ao nó `Logs`.

```xml

    <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>
```

Você também pode incluir vários coletores na configuração em diferentes níveis na hierarquia. Nesse caso, o coletor especificado no nível superior da hierarquia atua como uma configuração global e o especificado no elemento individual atua como uma substituição daquela configuração global.

Este é um exemplo completo do arquivo de configuração pública que envia todos os erros ao Application Insights (especificado no nó `DiagnosticMonitorConfiguration`) e também logs de nível detalhado aos logs de aplicativo (especificados no nó `Logs`).

```xml

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
            sinks="ApplicationInsights.MyLogData"/>
       <!-- This specific info sent to this channel -->
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
```

![](./media/app-insights-azure-diagnostics/diagnostics-publicconfig.png)

É preciso ter atenção a algumas limitações dessa funcionalidade

* Os canais devem funcionar somente com o tipo de log, e não com os contadores de desempenho. Se você especificar um canal com um elemento contador de desempenho, ele será ignorado.
* O nível de log para um canal não pode exceder o nível de log do que está sendo coletado pelo Diagnóstico do Azure. Por exemplo: você não pode coletar erros no log de aplicativo no elemento Logs e tentar enviar logs detalhados para a sincronização do Application Insights. O atributo scheduledTransferLogLevelFilter sempre deve coletar uma quantidade igual ou maior de logs que a quantidade de logs que você está tentando enviar para um coletor.
* Você não pode enviar dados de blob coletados pela extensão do Diagnóstico do Azure ao Application Insights. Por exemplo, nada especificado sob o nó Directories. No caso de despejos de memória, o despejo de memória real ainda será enviado para o armazenamento de blobs e somente uma notificação da geração do despejo será enviada ao Application Insights.

## Tópicos relacionados

* [Monitorar os Serviços de Nuvem do Azure com o Application Insights](app-insights-cloudservices.md)
* [Usando o PowerShell para enviar diagnósticos do Azure ao Application Insights](app-insights-powershell-azure-diagnostics.md)
* [Arquivo de configuração de Diagnóstico do Azure](https://msdn.microsoft.com/library/azure/dn782207.aspx)

<!---HONumber=AcomDC_0504_2016-->