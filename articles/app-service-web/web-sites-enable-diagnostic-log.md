<properties
	pageTitle="Habilitar o registro em log de diagnóstico para aplicativos Web no Serviço de Aplicativo do Azure"
	description="Saiba como habilitar o log de diagnóstico e adicionar instrumentação ao seu aplicativo, bem como acessar as informações registradas pelo Azure."
	services="app-service\web"
	documentationCenter=".net"
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/29/2015"
	ms.author="cephalin"/>

# Habilitar o registro em log de diagnóstico para aplicativos Web no Serviço de Aplicativo do Azure

## Visão geral

O Azure oferece diagnóstico integrado para ajudar na depuração de um aplicativo Web hospedado em um [Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714). Neste artigo, você saberá como habilitar o registro em log de diagnóstico e adicionar instrumentação ao seu aplicativo, bem como acessar as informações registradas pelo Azure.

> [AZURE.NOTE]Este artigo usa o [portal de visualização do Azure](http://go.microsoft.com/fwlink/?LinkId=529715), o PowerShell do Azure e a interface de linha de comando do Azure (Azure CLI) para trabalhar com logs de diagnóstico. Para saber mais sobre como trabalhar com logs de diagnóstico usando o Visual Studio, confira [Solucionando problemas do Azure no Visual Studio](../troubleshoot-web-sites-in-visual-studio.md).

## <a name="whatisdiag"></a>Diagnóstico de servidor Web e diagnóstico de aplicativos

[Aplicativos Web de Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714) oferecem funcionalidade de diagnóstico para registro em log tanto de informações do servidor Web quanto do aplicativo Web. Estes estão logicamente separados em **diagnóstico de servidor Web** e **diagnóstico de aplicativos**.

### Diagnóstico de servidor Web

Você pode habilitar ou desabilitar os seguintes tipos de logs:

- **Registro em Log Detalhado de Erros** - informações detalhadas de erros para códigos de status HTTP que indiquem uma falha (código de status 400 ou superior). Isto pode conter informações que podem ajudar a determinar por que o servidor retornou o código de erro.
- **Falha no Rastreamento de Solicitação** - informações detalhadas sobre solicitações com falha, incluindo um rastreamento dos componentes IIS usados para processar a solicitação e o tempo levado em cada componente. Isto pode ser útil se você está tentando melhorar o desempenho do site ou isolar o que está causando o retorno de um erro específico de HTTP.
- **Registro em Log de Servidor Web** - informações sobre transações HTTP usando o [formato de arquivo de log estendido W3C](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Este é útil para determinar as métricas gerais do site, como o número de solicitações manipuladas e quantas solicitações existem vindas de um endereço IP específico.

### Diagnóstico de aplicativo

O diagnóstico de aplicativos permite que você capture informações produzidas por um aplicativo Web. Os aplicativos ASP.NET podem usar a classe [Rastreamento.de.Diagnóstico.de.Sistema](http://msdn.microsoft.com/pt-br/library/36hhw2t6.aspx) para registrar informações no log de diagnóstico do aplicativo. Por exemplo:

	System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

O diagnóstico de aplicativo permite que você solucione o seu aplicativo em execução, ao emitir informações quando certas partes do código são usadas. Isto é especialmente útil quando você está tentando determinar por que o código está tomando um caminho específico, geralmente quando este resulta em um erro ou outro comportamento indesejado.

Para saber mais sobre como trabalhar com o Diagnóstico de Aplicativos usando o Visual Studio, confira [Solucionando problemas em Aplicativos Web do Azure no Visual Studio](../troubleshoot-web-sites-in-visual-studio.md).

> [AZURE.NOTE]Diferentemente de alterar o arquivo web.config, habilitar o diagnóstico de aplicativos ou alterar os níveis de log do diagnóstico não recicla o domínio do aplicativo em que este é executado.

Aplicativos Web do Azure também registrar informações de implantação quando você publica o conteúdo em um aplicativo Web. Isto acontece automaticamente e não há definições de configuração para log de implantação. O log de implantação permite que você determine por que uma implantação falhou. Por exemplo, se está usando um script de implantação personalizado, você poderá usar o log de implantação para determinar por que o script está falhando.

## <a name="enablediag"></a>Como habilitar o diagnóstico

Para habilitar o diagnóstico no [Portal de Gerenciamento do Azure](https://portal.azure.com), vá até a folha de seu aplicativo Web e clique em **Todas as Configurações > Logs de Diagnóstico**.

<!-- todo:cleanup dogfood addresses in screenshot -->
![Parte de logs](./media/web-sites-enable-diagnostic-log/logspart.png)

Ao habilitar o **Diagnóstico de Aplicativo**, você deverá também selecionar o **Nível de Log** e escolher se habilitará o log no **sistema de arquivos**, **armazenamento de tabela** ou **armazenamento de blob**. Enquanto todos os três locais de armazenamento fornecem as mesmas informações básicas para eventos registrados, o **armazenamento em tabela** e o **armazenamento de blob** registram informações adicionais como a ID da instância, a ID do thread e um carimbo de data/hora mais granular (formato de escala) do que realizar o log no **sistema de arquivos**.

Ao habilitar o **diagnóstico de site**, você deverá selecionar **armazenamento** ou **sistema de arquivos** para **log de servidor da Web**. Selecionar **armazenamento** permite que você selecione uma conta de armazenamento e, em seguida, um contêiner de blob onde os logs estarão gravados. Todos os outros logs para **diagnóstico de site** serão gravados apenas no sistema de arquivos.

> [AZURE.NOTE]As informações armazenadas em **armazenamento de tabela** ou **armazenamento de blob** apenas podem ser acessadas usando um cliente de armazenamento ou um aplicativo que possa trabalhar diretamente com estes sistemas de armazenamento. Por exemplo, o Visual Studio 2013 contém um Gerenciador de Armazenamento que pode ser usado para explorar armazenamentos de tabela ou de blob e o HDInsight pode acessar dados gravados no armazenamento de blob. Você também pode gravar um aplicativo que acesse o Armazenamento do Azure usando um dos [SDKs do Azure](/downloads/#).

A seguir, estão as configurações disponíveis ao habilitar o **diagnóstico de aplicativo**:

* **Nível de log** - permite que você filtre as informações capturadas como **informativas**, **de aviso** ou **de erro**. Configurar isto para **detalhado** fará o log de toda informação produzida pelo aplicativo. **O nível de log** pode ser configurado de forma diferente para logs de **sistemas de arquivo**, **armazenamento de tabela** e **armazenamento de blob**.
* **Sistema de arquivos** - armazena as informações de diagnóstico de aplicativos no sistema de arquivos do aplicativo Web. Estes arquivos podem ser acessados por FTP ou baixados como um arquivo Zip usando o PowerShell do Azure ou a interface de linha de comando do Azure (CLI do Azure).
* **Armazenamento de tabela** - armazena as informações de diagnóstico de aplicativo na Conta especificada de Armazenamento do Azure e no nome da tabela.
* **Armazenamento de blob** - armazena as informações de diagnóstico de aplicativo na Conta especificada de Armazenamento do Azure e no contêiner de blob.
* **Período de retenção** - por padrão, os logs não são automaticamente excluídos do **armazenamento de blob**. Selecione **definir retenção** e insira o número de dias que os logs serão mantidos se você desejar excluir os logs automaticamente.

> [AZURE.NOTE]Qualquer combinação de sistema de arquivos, armazenamento de tabela ou armazenamento de blob pode ser habilitada ao mesmo tempo e tem configurações de nível de log individuais. Por exemplo, você pode desejar gravar erros e avisos no armazenamento de blob como uma solução de log a longo prazo, habilitando o log de sistema de arquivos em um nível detalhado.

> [AZURE.NOTE]O diagnóstico também pode ser habilitado a partir do PowerShell do Azure usando o cmdlet **Set-AzureWebsite**. Se você não instalou o PowerShell do Azure ou não o configurou para usar sua Assinatura do Azure, consulte [Como usar o PowerShell do Azure](/develop/nodejs/how-to-guides/powershell-cmdlets/).

##<a name="download"></a>Como baixar logs

Informações de diagnóstico armazenadas no sistema de arquivos do aplicativo Web podem ser diretamente acessadas usando FTP. Também podem ser baixadas como um arquivo Zip com o PowerShell do Azure ou a interface de linha de comando do Azure.

A estrutura de diretórios onde os logs estão armazenados é a seguinte:

* **Logs do aplicativo** - /LogFiles/Application/. Essa pasta contém um ou mais arquivos de texto que contêm informações produzidas pelo log do aplicativo.

* **Rastreamento de Solicitação Falha** - /LogFiles/W3SVC#########/. Esta pasta contém um arquivo XSL e um ou mais arquivos XML. Baixe o arquivo XSL no mesmo diretório que o(s) arquivo(s) XML, pois o arquivo XSL fornece funcionalidade para formatar e filtrar o conteúdo do(s) arquivo(s) XML quando visualizado(s) no Internet Explorer.

* **Logs de erro do aplicativo** - /LogFiles/DetailedErrors/. Esta pasta contém um ou mais arquivos .htm que fornecem informações exaustivas para quaisquer erros de HTTP.

* **Logs do Web Server** - /LogFiles/http/RawLogs. Esta pasta contém um ou mais arquivos de texto que foram formatados usando o [formato W3C estendido de arquivo de log](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx).

* **Logs de implantação** - /LogFiles/Git. Esta pasta contém logs gerados pelo processo interno de implantação usado pelos aplicativos Web do Azure, assim como logs para implantações do Git.

### FTP

Para acessar informações de diagnóstico usando FTP, visite o **Painel** do seu aplicativo Web no Portal de Gerenciamento do Azure. Na seção **visão rápida**, use o link **Logs de diagnóstico de FTP** para acessar os arquivos de log usando FTP. A entrada **Implantação/Usuário FTP** lista o nome de usuário que deve ser usado para acessar o site FTP.

> [AZURE.NOTE]Se a entrada **Implantação/Usuário FTP** não estiver definida ou se você esqueceu a senha para este usuário, uma nova senha e nome de usuário poderão ser criados usando o link **Redefinir credenciais de implantação** na seção **visão rápida** do **Painel**.

### Baixar com o PowerShell do Azure

Para baixar os arquivos de log, inicie uma nova instância do PowerShell do Azure e use o seguinte comando:

	Save-AzureWebSiteLog -Name webappname

Isto salvará os logs para o aplicativo Web especificado pelo parâmetro **-Name** em um arquivo chamado **logs.zip** no diretório atual.

> [AZURE.NOTE]Se você não instalou o PowerShell do Azure ou não o configurou para usar sua Assinatura do Azure, consulte [Como usar o PowerShell do Azure](/develop/nodejs/how-to-guides/powershell-cmdlets/).

### Baixar com a interface de linha de comando do Azure

Para baixar os arquivos de log usando a Interface da Linha de Comando do Azure, abra uma nova sessão de prompt de comando, PowerShell, Bash ou Terminal e insira o seguinte comando:

	azure site log download webappname

Isto salvará os logs para o aplicativo Web chamado 'webappname' em um arquivo chamado **diagnostics.zip** no diretório atual.

> [AZURE.NOTE]Se você não instalou a interface de linha de comando do Azure (CLI do Azure) ou não configurou para usar sua assinatura do Azure, consulte [Como usar a CLI do Azure](../xplat-cli.md).

## Como exibir os logs de rastreamento de Java no Application Insights

O Application Insights do Visual Studio fornece ferramentas para filtrar e pesquisar logs e para correlacioná-los com solicitações e outros eventos.

1. Adicionar o SDK do Application Insights ao projeto no Visual Studio.
 * Clique com o botão direito do mouse no projeto no Gerenciador de Soluções e selecione Adicionar Application Insights. Você será guiado pelas etapas que incluem a criação de um recurso do Application Insights. [Saiba mais](../application-insights/app-insights-start-monitoring-app-health-usage.md)
2. Adicionar o pacote do Ouvinte de Rastreamento ao seu projeto.
 * Clique com o botão direito do mouse em seu projeto e escolha Gerenciar Pacotes NuGet. Selecione `Microsoft.ApplicationInsights.TraceListener` [Saiba mais](../application-insights/app-insights-asp-net-trace-logs.md)
3. Carregue seu projeto e execute-o para gerar dados de log.
4. No [Portal de Visualização do Azure](http://portal.azure.com/), navegue até o novo recurso do Application Insights e abra **Pesquisa**. Você verá os dados de log, a solicitação, o uso e outras telemetrias. Algumas telemetrias podem levar alguns minutos para aparecer: clique em Atualizar. [Saiba mais](../application-insights/app-insights-diagnostic-search.md)

[Saiba mais sobre desempenho de rastreamento com o Application Insights](../insights-perf-analytics.md)

##<a name="streamlogs"></a>Como transmitir logs

Ao desenvolver um aplicativo, é sempre útil visualizar informações de registro em log realizado em tempo quase real. Isto pode ser feito ao transmitir informações de log para seu ambiente de desenvolvimento usando o PowerShell do Azure ou a Interface de Linha de Comando do Azure.

> [AZURE.NOTE]Alguns tipos de buffer de log gravam no arquivo de log, o que pode resultar em eventos com problemas na transmissão. Por exemplo, uma entrada para log de aplicativo, que ocorre quando um usuário visita uma página, pode ser exibida durante a transmissão antes da entrada de log HTTP correspondente para a solicitação da página.

> [AZURE.NOTE]O streaming de log também transmitirá informações gravadas em qualquer arquivo de texto armazenado na pasta **D:\home\LogFiles**.

### O streaming realizado com o PowerShell do Azure

Para transmitir informações de log, inicie uma nova instância do PowerShell do Azure e use o seguinte comando:

	Get-AzureWebSiteLog -Name webappname -Tail

Isto faz a conexão ao aplicativo Web especificado pelo parâmetro **-Name** e inicia o streaming de informações para a janela do PowerShell, enquanto eventos de log ocorrem no aplicativo Web. Qualquer informação escrita para os arquivos com terminação .txt ou .htm que sejam armazenadas no diretório /LogFiles (d:/home/LogFiles) será transmitida à console local.

Para filtrar eventos específicos como erros, use o parâmetro **-Mensagem**. Por exemplo:

	Get-AzureWebSiteLog -Name webappname -Tail -Message Error

Para filtrar tipos específicos de log como HTTP, use o parâmetro **-Caminho**. Por exemplo:

	Get-AzureWebSiteLog -Name webappname -Tail -Path http

Para visualizar uma lista de caminhos disponíveis, use o parâmetro -ListPath.

> [AZURE.NOTE]Se você não instalou o PowerShell do Azure ou não o configurou para usar sua Assinatura do Azure, consulte [Como usar o PowerShell do Azure](/develop/nodejs/how-to-guides/powershell-cmdlets/).

### Transmitir com a Interface da linha de comando do Azure

Para transmitir informações de log, abra uma nova sessão de prompt de comando, PowerShell, Bash ou Terminal e insira o seguinte comando:

	azure site log tail webappname

Isto realiza a conexão ao aplicativo Web chamado 'webappname' e inicia o streaming de informações para a janela, enquanto eventos de log ocorrem no aplicativo Web. Qualquer informação escrita para os arquivos com terminação .txt ou .htm que sejam armazenadas no diretório /LogFiles (d:/home/LogFiles) será transmitida à console local.

Para filtrar eventos específicos como erros, use o parâmetro **-Filtro**. Por exemplo:

	azure site log tail webappname --filter Error

Para filtrar tipos específicos de log como HTTP, use o parâmetro **-Caminho**. Por exemplo:

	azure site log tail webappname --path http

> [AZURE.NOTE]Se você não instalou a Interface da Linha de Comando ou não a configurou para usar a sua Assinatura do Azure, consulte [Como usar a Interface da linha de comando do Azure](../xplat-cli.md).

##<a name="understandlogs"></a> Como compreender os logs de diagnóstico

### Logs de diagnóstico de aplicativo

O diagnóstico de aplicativo armazenará informações em um formato específico para aplicativos .NET, dependendo se você armazenar os logs no sistema de arquivos, no armazenamento de tabela ou no armazenamento de blob. O conjunto de dados de base armazenados é o mesmo em todos os três tipos de armazenamento - a data e hora em que o evento ocorreu, a ID do processo que produziu o evento, o tipo de evento (informação, aviso ou erro) e a mensagem do evento.

__Sistema de arquivos__

Cada linha registrada no sistema de arquivos ou recebida via streaming estará no seguinte formato:

	{Date}  PID[{process id}] {event type/level} {message}

Por exemplo, um evento de erro deve aparecer semelhante ao seguinte:

	2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

O registro em sistema de arquivos fornece a informação mais básica dos três métodos disponíveis, fornecendo apenas a hora, o id do processo, o nível de evento e a mensagem.

__Armazenamento de tabela__

Ao registrar no armazenamento de tabela, propriedades adicionais são usadas para facilitar a busca de dados armazenados na tabela e de informações mais granulares sobre o evento. As seguintes propriedades (colunas) são usadas para cada entidade (linha) armazenada na tabela.

<table style="width:100%;border-collapse:collapse">
<thead>
<tr>
<th style="width:45%;border:1px solid black;background-color:#0099dd">Nome da propriedade</th>
<th style="border:1px solid black;vertical-align:top;background-color:#0099dd">Valor/formato</th>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">PartitionKey</td>
<td style="border:1px solid black;vertical-align:top">Data/hora do evento no formato aaaaMMddHH</td>
</tr>
</thead>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">RowKey</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Um valor de GUID que identifica exclusivamente esta entidade</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Timestamp</td>
<td style="border:1px solid black;vertical-align:top">A data e hora em que o evento ocorreu</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">EventTickCount</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">A data e hora em que o evento ocorreu, em formato de escala (maior precisão)</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">ApplicationName</td>
<td style="border:1px solid black;vertical-align:top">O nome do aplicativo Web</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Nível</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Nível de evento (por exemplo, erro, aviso ou informação)</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">EventId</td>
<td style="border:1px solid black;vertical-align:top">A ID de evento deste evento<br>A ID assume o valor 0 como padrão, se nenhum valor for especificado</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">InstanceId</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">A instância do aplicativo Web onde o evento ocorreu</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Pid</td>
<td style="border:1px solid black;vertical-align:top">ID do Processo</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Tid</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">A ID do thread que produziu o evento</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Mensagem</td>
<td style="border:1px solid black;vertical-align:top">A mensagem com detalhes do evento</td>
</tr>
</table>

__Armazenamento de blob__

Ao registrar o log no armazenamento de blob, os dados serão armazenados em um formato de valores separados por vírgulas (CSV). Semelhante ao armazenamento de tabela, campos adicionais são registrados para fornecer informações mais granulares sobre o evento. As seguintes propriedades são usadas para cada linha no CSV:

<table style="width:100%;border-collapse:collapse">
<thead>
<tr>
<th style="width:45%;border:1px solid black;background-color:#0099dd">Nome da propriedade</th>
<th style="border:1px solid black;vertical-align:top;background-color:#0099dd">Valor/formato</th>
</tr>
</thead>
<tr>
<td style="border:1px solid black;vertical-align:top">Data</td>
<td style="border:1px solid black;vertical-align:top">A data e hora em que o evento ocorreu</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Nível</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Nível de evento (por exemplo, erro, aviso ou informação)</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">ApplicationName</td>
<td style="border:1px solid black;vertical-align:top">O nome do aplicativo Web</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">InstanceId</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">A instância do aplicativo Web onde o evento ocorreu</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">EventTickCount</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">A data e hora em que o evento ocorreu, em formato de escala (maior precisão)</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">EventId</td>
<td style="border:1px solid black;vertical-align:top">A ID de evento deste evento<br>A ID assume o valor 0 como padrão, se nenhum valor for especificado</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Pid</td>
<td style="border:1px solid black;vertical-align:top">ID do Processo</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Tid</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">A ID do thread que produziu o evento</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Mensagem</td>
<td style="border:1px solid black;vertical-align:top">A mensagem com detalhes do evento</td>
</tr>
</table>

Os dados armazenados em um blob deverão ser semelhantes ao seguinte:

	date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
	2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [AZURE.NOTE]A primeira linha do log conterá os cabeçalhos da coluna, como representado neste exemplo.

### Rastreamento de solicitação falha

O rastreamento de solicitações falhas é armazenado em arquivos XML chamados __fr######.xml__. Para facilitar a visualização das informações registradas, uma folha de estilos XML chamada __freb.xsl__ é fornecida no mesmo diretório dos arquivos XML. A abertura de um dos arquivos XML no Internet Explorer usará a folha de estilos XSL para fornecer uma exibição de formato da informação rastreada. Isto aparecerá semelhante ao seguinte:

![solicitação falha visualizada no navegador](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

### Logs detalhados de erro

Logs detalhados de erro são documentos HTML que fornecem informações mais detalhadas sobre erros HTTP que tenham ocorrido. Como são simplesmente documentos HTML, eles podem ser visualizados usando um navegador da Web.

### Logs de servidor Web

Os logs do servidor da Web são formatados usando o [formato W3C estendido de arquivo de log](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Esta informação pode ser lida usando um editor de texto ou analisada usando ferramentas como o [Analisador de Log](http://go.microsoft.com/fwlink/?LinkId=246619).

> [AZURE.NOTE]Os logs gerados por aplicativos Web do Azure não dão suporte aos campos __s-computername__, __s-ip__ ou __cs-version__.

>[AZURE.NOTE]Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

##<a name="nextsteps"></a> Próximas etapas

- [Como monitorar aplicativos Web](/pt-br/manage/services/web-sites/how-to-monitor-websites/)
- [Tutorial - Solucionando problemas em aplicativos Web](/pt-br/develop/net/best-practices/troubleshooting-web-sites/)
- [Solucionando problemas de aplicativos Web do Azure no Visual Studio](/pt-br/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/)
- [Analisar logs de aplicativos Web no HDInsight](http://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia sobre a alteração do portal antigo para o novo portal, confira: [Referência para navegar no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)
 

<!---HONumber=62-->