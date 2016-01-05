<properties 
	pageTitle="Definir alertas no Application Insights" 
	description="Receba emails sobre falhas, exceções e alterações de métricas." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/23/2015" 
	ms.author="awills"/>
 
# Definir alertas no Application Insights

O [Application Insights do Visual Studio][start] pode alertá-lo de alterações em métricas de desempenho ou de uso em seu aplicativo.

O Application Insights monitora seu aplicativo ativo em um [ampla variedade de plataformas][platforms] para ajudá-lo a diagnosticar problemas de desempenho e compreender padrões de uso.

Há dois tipos de alertas:
 
* **Testes da Web** informam quando seu site está indisponível na Internet ou está respondendo lentamente. [Saiba mais][availability].
* **Alertas de métrica** informam quando qualquer métrica ultrapassa um valor limite durante determinado período, como contagens de falhas, memória ou exibições de páginas. 

Há um [página separada sobre testes na Web][availability]; portanto, vamos nos concentrar nos alertas de métricas aqui.

## Alertas de métricas

Se você ainda não configurou o Application Insights para seu aplicativo, [faça isso primeiro][start].

Para receber um email quando uma métrica ultrapassar um limite, inicie no Metrics Explorer ou no bloco de Regras de alerta na folha de visão geral.

![Na folha das Regras de alerta, escolha Adicionar Alerta. Defina seu aplicativo como o recurso a ser medido, forneça um nome para o alerta e escolha uma métrica.](./media/app-insights-alerts/01-set-metric.png)

Defina o recurso antes de outras propriedades. **Escolha o recurso "(componentes)"** se desejar definir alertas em métricas de desempenho ou de uso.

Observe as unidades quando você for solicitado para inserir o valor de limite.

O nome dado ao alerta deve ser exclusivo dentro do grupo de recursos (não apenas no seu aplicativo).

*Não vejo o botão Adicionar Alerta.* Você está usando uma conta organizacional? Você poderá definir alertas se tiver acesso de proprietário ou colaborador a esse recurso de aplicativo. Consulte Configurações -> Usuários. [Saiba mais sobre o controle de acesso][roles].

## Ver seus alertas

Você recebe um email quando o estado de um alerta é mudado de inativo para ativo.

O estado atual de cada alerta é mostrado na folha de regras de Alerta.

Há um resumo da atividade recente no menu suspenso de alertas:

![](./media/app-insights-alerts/010-alert-drop.png)

O histórico das alterações de estado está no log de Eventos de operações:

![Na folha Visão geral, próximo à parte inferior, clique em 'Eventos da semana anterior'](./media/app-insights-alerts/09-alerts.png)

*Esses "eventos" estão relacionados a eventos de telemetria ou a eventos personalizados?*

* Não. Esses eventos operacionais são apenas um log de eventos que ocorreram nesse recurso de aplicativo. 


## Como funcionam os alertas

* Um alerta tem dois estados: ("alerta" e "íntegro"). 

* Um email é enviado quando um alerta muda de estado.

* Um alerta é avaliado toda vez que uma métrica chega, mas não o contrário.

* A avaliação agrega a métrica ao longo do período anterior e a compara com o limite para determinar o novo estado.

* O período que você escolhe especifica o intervalo durante o qual as métricas são agregadas. Ele não afeta a frequência com que o alerta é avaliado: isto depende da frequência da chegada das métricas.

* Se nenhum dado chegar para uma determinada métrica por algum tempo, a lacuna tem efeitos diferentes sobre a avaliação do alerta e sobre os gráficos no Metrics Explorer. No Metrics Explorer, se nenhum dado for visto por mais tempo que o intervalo de amostragem do gráfico, o gráfico mostrará um valor de 0. Mas um alerta com base na mesma métrica não será avaliado novamente e o estado do alerta permanecerá inalterado.

    Quando os dados chegam, o gráfico volta para um valor diferente de zero. O alerta será avaliado com base nos dados disponíveis para o período especificado. Se o novo ponto de dados for o único disponível no período, a agregação será baseada apenas nisso.

* Um alerta pode piscar frequentemente entre os estados de alerta e íntegro, mesmo que você defina um longo período. Isso pode acontecer se o valor da métrica estiver em torno do limite. Não há nenhuma histerese no limite: a transição para o alerta acontece com o mesmo valor que a transição para o estado íntegro.



## Alertas de disponibilidade

Você pode configurar testes na Web que testam qualquer site de pontos em todo o mundo. [Saiba mais][availability].

## Quais são alguns alertas que é recomendável definir?

Depende de seu aplicativo. Para começar, é melhor não definir um número excessivo de métricas. Passe algum tempo examinando seus gráficos de métricas enquanto seu aplicativo está em execução para ter uma noção de como ele se comporta normalmente. Isso o ajudará a encontrar maneiras de melhorar seu desempenho. Em seguida, configure alertas para informá-lo quando as métricas estiverem fora da zona normal.

Alguns alertas populares são:

* [Testes da web][availability] são importantes se seu aplicativo é um site ou serviço Web que é visível na Internet pública. Eles informam se seu site fica inativo ou responde lentamente, mesmo que haja um problema na operadora, não em seu aplicativo. Porém, eles são testes sintéticos; portanto, não medem a experiência real dos usuários.
* [Métricas de navegador][client], principalmente **tempos de carregamento de páginas** de navegador, são adequados para aplicativos Web. Se sua página tem vários scripts, convém procurar **exceções de navegador**. Para obter essas métricas e alertas, você precisa configurar o [monitoramento de página da Web][client].
* **Tempo de resposta do servidor** e **Solicitações com falha** para o lado do servidor dos aplicativos Web. Além de configurar alertas, confira essas métricas para ver se elas variam de forma desproporcional com altas taxas de solicitação: isso pode indicar que seu aplicativo está ficando sem recursos.
* **Exceções de servidor** - para vê-las, você precisa fazer algumas [configurações adicionais](app-insights-asp-net-exceptions.md).

## Definir alertas usando o PowerShell

Em geral, é suficiente definir alertas manualmente. Mas se você quiser criar alertas de métrica automaticamente, pode fazer isso usando o PowerShell.

#### Configuração única

Se você nunca usou o PowerShell com sua assinatura do Azure:

Instale o módulo do Azure Powershell no computador em que você deseja executar os scripts.

 * Instale o [Microsoft Web Platform Installer (v5 ou superior)](http://www.microsoft.com/web/downloads/platform.aspx).
 * Use-o para instalar o Microsoft Azure PowerShell


#### Conecte-se ao Azure

Inicie o Azure PowerShell e [conecte-se à sua assinatura](powershell-install-configure.md):

```PowerShell

    Add-AzureAccount
    Switch-AzureMode AzureResourceManager
```


#### Obter alertas

    Get-AlertRule -ResourceGroup "Fabrikam" [-Name "My rule"] [-DetailedOutput]

#### Adicionar alerta


    Add-AlertRule  -Name "{ALERT NAME}" -Description "{TEXT}" `
     -ResourceGroup "{GROUP NAME}" `
     -ResourceId "/subscriptions/{SUBSCRIPTION ID}/resourcegroups/{GROUP NAME}/providers/microsoft.insights/components/{APP RESOURCE NAME}" `
     -MetricName "{METRIC NAME}" `
     -Operator GreaterThan  `
     -Threshold {NUMBER}   `
     -WindowSize {HH:MM:SS}  `
     [-SendEmailToServiceOwners] `
     [-CustomEmails "EMAIL1@X.COM","EMAIL2@Y.COM" ] `
     -Location "East US"
     -RuleType Metric



#### Exemplo 1

Enviar email se a resposta do servidor às solicitações HTTP, em média no prazo de 5 minutos, demorar mais de 1 segundo. Meu recurso Application Insights é chamado IceCreamWebApp e está no grupo de recursos Fabrikam. Sou o proprietário da assinatura do Azure.

O GUID é a ID da assinatura (não a chave de instrumentação do aplicativo).

    Add-AlertRule -Name "slow responses" `
     -Description "email me if the server responds slowly" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "request.duration" `
     -Operator GreaterThan `
     -Threshold 1 `
     -WindowSize 00:05:00 `
     -SendEmailToServiceOwners `
     -Location "East US" -RuleType Metric

#### Exemplo 2

Tenho um aplicativo em que uso o [TrackMetric()](app-insights-api-custom-events-metrics.md#track-metric) para relatar uma métrica chamada "salesPerHour". Envie um email para meus colegas se "salesPerHour" cair abaixo de 100, em média no prazo de 24 horas.

    Add-AlertRule -Name "poor sales" `
     -Description "slow sales alert" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "salesPerHour" `
     -Operator LessThan `
     -Threshold 100 `
     -WindowSize 24:00:00 `
     -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
     -Location "East US" -RuleType Metric

A mesma regra pode ser usada para a métrica relatada usando o [parâmetro de medida](app-insights-api-custom-events-metrics.md#properties) de outra chamada de controle, como TrackEvent ou trackPageView.

#### Nomes de métrica

Nome da métrica | Nome da tela | Descrição
---|---|---
`basicExceptionBrowser.count`|Exceções de navegador|Contagem de exceções não identificadas lançadas no navegador.
`basicExceptionServer.count`|Exceções do servidor|Contagem de exceções sem tratamento lançadas pelo aplicativo
`clientPerformance.clientProcess.value`|Tempo de processamento do cliente|Tempo entre o recebimento do último byte de um documento até que o DOM seja carregado. As solicitações assíncronas ainda podem estar sendo processadas.
`clientPerformance.networkConnection.value`|Tempo de conexão de rede de carregamento de página| Tempo que leva para o navegador se conectar à rede. Pode ser 0 se armazenado em cache.
`clientPerformance.receiveRequest.value`|Tempo de resposta de recebimento| Tempo entre o envio da solicitação do navegador e o início do recebimento de resposta.
`clientPerformance.sendRequest.value`|Tempo de solicitação de envio| Tempo gasto pelo navegador para enviar a solicitação.
`clientPerformance.total.value`|Tempo de carregamento de página do navegador|Tempo de solicitação do usuário até que o DOM, as imagens, os scripts e as folhas de estilo sejam carregados.
`performanceCounter.available_bytes.value`|Memória disponível|Memória física disponível imediatamente para um processo ou para uso do sistema.
`performanceCounter.io_data_bytes_per_sec.value`|Taxa de processamento de IO|Total de bytes por segundo lidos e gravados em arquivos, rede e dispositivos.
`performanceCounter.number_of_exceps_thrown_per_sec`|taxa de exceção|Exceções geradas por segundo.
`performanceCounter.percentage_processor_time.value`|CPU do processo|A porcentagem de tempo decorrido em todos os threads do processo usados pelo processador para executar instruções do processo dos aplicativos.
`performanceCounter.percentage_processor_total.value`|Tempo do processador|A porcentagem de tempo que o processador gasta em threads ocupados.
`performanceCounter.process_private_bytes.value`|Processar bytes particulares|Memória atribuída exclusivamente aos processos do aplicativo monitorado.
`performanceCounter.request_execution_time.value`|Tempo de execução de solicitação do ASP.NET|Tempo de execução da solicitação mais recente.
`performanceCounter.requests_in_application_queue.value`|Solicitações do ASP.NET na fila de execução|Comprimento da fila de solicitação de aplicativo.
`performanceCounter.requests_per_sec`|Taxa de solicitação do ASP.NET|Taxa de todas as solicitações para o aplicativo por segundo do ASP.NET.
`remoteDependencyFailed.durationMetric.count`|Falhas de dependência|Contagem de falhas de chamadas feitas pelo aplicativo servidor para recursos externos.
`request.duration`|Tempo de resposta do servidor|Tempo entre o recebimento de uma solicitação HTTP e a finalização do envio da resposta.
`request.rate`|Taxa de solicitação|Taxa de todas as solicitações para o aplicativo por segundo.
`requestFailed.count`|Solicitações falhas|Contagem de solicitações HTTP que resultaram em um código de resposta >= 400 
`view.count`|Visualizações de página|Contagem de solicitações de usuário cliente para uma página da Web. O tráfego sintético é filtrado.
{o nome de métrica personalizada}|{O nome da métrica}|O valor de métrica relatado pelo [TrackMetric](app-insights-api-custom-events-metrics.md#track-metric) ou o [parâmetro de medidas de uma chamada de controle](app-insights-api-custom-events-metrics.md#properties).

As métricas são enviadas por diferentes módulos de telemetria:

Grupo de métricas | Módulo de coletor
---|---
basicExceptionBrowser,<br/>clientPerformance,<br/>view | [JavaScript do navegador](app-insights-javascript.md)
performanceCounter | [Desempenho](app-insights-configuration-with-applicationinsights-config.md#nuget-package-3)
remoteDependencyFailed| [Dependência](app-insights-configuration-with-applicationinsights-config.md#nuget-package-1)
request,<br/>requestFailed|[Solicitação do servidor](app-insights-configuration-with-applicationinsights-config.md#nuget-package-2)


## Consulte também


* [Criar recursos de teste da Web e do Application Insights por meio de modelos](app-insights-powershell.md)

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[platforms]: app-insights-platforms.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md

 

<!---HONumber=AcomDC_1125_2015-->