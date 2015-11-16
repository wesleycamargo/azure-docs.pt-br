<properties
	pageTitle="Rastrear o fluxo em um Aplicativo de Serviços de Nuvem com o Diagnóstico do Azure | Microsoft Azure"
	description="Adicione mensagens de rastreamento a um aplicativo do Azure para ajudar a depurar, medir o desempenho, monitorar, realizar a análise de tráfego e muito mais."
	services="cloud-services"
	documentationCenter=".net"
	authors="rboucher"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="10/17/2015"
	ms.author="robb"/>



# Rastrear o fluxo de um aplicativo de Serviços de Nuvem com o Diagnóstico do Azure

O rastreamento é uma maneira de você monitorar a execução de seu aplicativo enquanto ele é executado. Você pode usar as classes [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace.aspx), [System.Diagnostics.Debug](https://msdn.microsoft.com/library/system.diagnostics.debug.aspx) e [System.Diagnostics.TraceSource](https://msdn.microsoft.com/library/system.diagnostics.tracesource.aspx) para registrar informações sobre erros e execução do aplicativo em logs, arquivos de texto ou outros dispositivos para análise posterior. Para obter mais informações sobre rastreamento, consulte [Rastreamento e instrumentação de aplicativos](https://msdn.microsoft.com/library/zs6s4h68.aspx).


## Usar instruções e opções de rastreamento

Implemente o rastreamento em seu aplicativo de Serviços de Nuvem adicionando [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) à configuração do aplicativo e fazendo chamadas a System.Diagnostics.Trace ou System.Diagnostics.Debug no código do aplicativo. Use o arquivo de configuração *app.config* para funções de trabalho e *web.config* para funções Web. Quando você cria um novo serviço hospedado usando um modelo do Visual Studio, o Diagnóstico do Azure é adicionado automaticamente ao projeto e DiagnosticMonitorTraceListener é adicionado ao arquivo de configuração apropriado para as funções que você adiciona.

Para obter informações sobre como incluir instruções de rastreamento, consulte [Como: adicionar instruções de rastreamento ao código do aplicativo](https://msdn.microsoft.com/library/zd83saa2.aspx).

Incluindo [Opções de Rastreamento](https://msdn.microsoft.com/library/3at424ac.aspx) em seu código, você pode controlar se o rastreamento ocorre e qual a sua extensão. Isso permite monitorar o status do aplicativo em um ambiente de produção. Isso é particularmente importante em um aplicativo de negócios que usa vários componentes executados em vários computadores. Para obter mais informações, consulte [Como: configurar opções de rastreamento](https://msdn.microsoft.com/library/t06xyy08.aspx).

## Configurar o ouvinte de rastreamento em um aplicativo do Azure

Rastreamento, depuração e TraceSource exigem que você configure "ouvintes" para coletar e registrar as mensagens que são enviadas. Os ouvintes coletam, armazenam e roteiam mensagens de rastreamento. Eles direcionam a saída de rastreamento para um destino apropriado, como um log, uma janela ou um arquivo de texto. O Diagnóstico do Azure usa a classe [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx).

Antes de concluir o procedimento a seguir, você deve inicializar o monitor de diagnóstico do Azure. Para fazer isso, consulte [Habilitando o diagnóstico no Microsoft Azure](cloud-services-dotnet-diagnostics.md).

Observe que, se você usar os modelos fornecidos pelo Visual Studio, a configuração do ouvinte será adicionada automaticamente para você.


### Adicionar um ouvinte de rastreamento

1. Abra o arquivo web.config ou app.config para sua função.
2. Adicione o seguinte código ao arquivo :

	```
	<system.diagnostics>
		<trace>
			<listeners>
				<add type="Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener,
		          Microsoft.WindowsAzure.Diagnostics,
		          Version=1.0.0.0,
		          Culture=neutral,
		          PublicKeyToken=31bf3856ad364e35"
		          name="AzureDiagnostics">
			  	  <filter type="" />
				</add>
			</listeners>
		</trace>
	</system.diagnostics>
	```
3. Salve o arquivo de configuração.

Para obter mais informações sobre ouvintes, consulte [Ouvintes de rastreamento](https://msdn.microsoft.com/library/4y5y10s7.aspx).

Depois de concluir as etapas para adicionar o ouvinte, você pode adicionar instruções de rastreamento ao código.


### Para adicionar a instrução de rastreamento ao código

1. Abra um arquivo de origem para o aplicativo. Por exemplo, o arquivo <RoleName>.cs para a função de trabalho ou Web.
2. Adicione a seguinte instrução using, se ela ainda não tiver sido adicionada: ```
	    using System.Diagnostics;
	```
3. Adicione instruções Trace em que você deseja capturar informações sobre o estado do aplicativo. Você pode usar diversos métodos para formatar a saída da instrução Trace. Para obter mais informações, consulte [Como: adicionar instruções de rastreamento ao código do aplicativo](https://msdn.microsoft.com/library/zd83saa2.aspx).
4. Salve o arquivo de origem.

<!---HONumber=Nov15_HO2-->