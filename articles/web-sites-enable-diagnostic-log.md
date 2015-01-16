<properties urlDisplayName="Enable diagnostic logging" pageTitle="Habilitar o log de diagnóstico - sites do Azure" metaKeywords="sites de diagnóstico do Azure, diagnóstico do Portal de Gerenciamento do Azure, diagnóstico do Azure, diagnóstico de site, depuração de site" description="Saiba como habilitar o log de diagnóstico e adicionar instrumentação ao seu aplicativo, bem como acessar as informações registradas pelo Azure." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Enable diagnostic logging for Azure Websites" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />





#Habilitar registro em log do diagnóstico para sites do Azure

O Azure fornece diagnóstico interno para ajudar na depuração de um aplicativo hospedado em sites do Azure. Neste artigo, você saberá como habilitar o registro em log de diagnóstico e adicionar instrumentação ao seu aplicativo, bem como acessar as informações registradas pelo Azure.

> [WACOM.NOTE] Este artigo descreve o uso do Portal de Gerenciamento do Azure, o PowerShell do Azure e a Interface de Linha de Comando entre Plataformas do Azure para trabalhar com logs de diagnóstico. Para obter informações sobre como trabalhar com logs de diagnóstico usando o Visual Studio, consulte [Solucionando problemas em sites do Azure no Visual Studio](/pt-br/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

##Sumário##

- [O que é: Diagnóstico de site?](#whatisdiag)
- [Como: Habilitar diagnósticos](#enablediag)
- [Como: Baixe logs](#download)
- [Como: Logs de streaming](#streamlogs)
- [Como: Compreendendo os logs de diagnóstico](#understandlogs)
- [Próximas etapas](#nextsteps)

<a name="whatisdiag"></a><h2>O que é o diagnóstico de site?</h2>

Os sites do Azure fornecem funcionalidade de diagnóstico para informações de registro em log tanto para o servidor Web, como para o aplicativo Web. Eles estão logicamente separados em **diagnóstico de site** e **diagnóstico de aplicativos**.

###Diagnóstico de site

O diagnóstico de site permite que você habilite ou desabilite o seguinte:

- **Log de Erro Detalhado** - informações detalhadas de erro de logs para códigos de status HTTP que indiquem uma falha (código de status 400 ou superior). Isto pode conter informações que podem ajudar a determinar por que o servidor retornou o código de erro.
- **Falha no Rastreamento de Solicitação** - informações detalhadas de logs sobre solicitações com falha, incluindo um rastreamento dos componentes IIS usados para processar a solicitação e o tempo levado em cada componente. Isto pode ser útil se você está tentando melhorar o desempenho do site ou isolar o que está causando o retorno de um erro específico de HTTP.
- **Log de Servidor da Web** - registra todas as transações HTTP em um site, usando o [formato W3C estendido de arquivo de log](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Este relatório é útil para determinar as métricas gerais do site, como o número de solicitações manipuladas ou quantas solicitações existem vindas de um endereço IP específico

###Diagnóstico de aplicativo

O diagnóstico de aplicativos permite que você capture informações produzidas por um aplicativo Web. Os aplicativos ASP.NET podem usar a classe [Rastreamento.de.Diagnóstico.de.Sistema](http://msdn.microsoft.com/pt-br/library/36hhw2t6.aspx) para registrar informações no log de diagnóstico do aplicativo. Por exemplo:

	System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

O diagnóstico de aplicativos permite que você solucione o seu aplicativo em execução, ao emitir informações quando certas partes do código são usadas. Isto é especialmente útil quando você está tentando determinar por que o código está tomando um caminho específico, geralmente quando este resulta em um erro ou outro comportamento indesejado.

Para obter informações sobre como trabalhar com Diagnóstico de Aplicativos usando o Visual Studio, consulte [Solucionando problemas em sites do Azure no Visual Studio](http://www.windowsazure.com/pt-br/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

> [WACOM.NOTE] Diferentemente de alterar o arquivo web.config, habilitar o diagnóstico de aplicativos ou alterar os níveis de log do diagnóstico não recicla o domínio do aplicativo em que este é executado.

Os sites do Azure também armazenam em log as informações de implantação quando você publicar um aplicativo em um site. Isto acontece automaticamente e não há definições de configuração para log de implantação. O log de implantação permite que você determine por que uma implantação falhou. Por exemplo, se está usando um script de implantação personalizado, você poderá usar o log de implantação para determinar por que o script está falhando.

<a name="enablediag"></a><h2>Como: Habilitar diagnósticos</h2>

O diagnóstico pode ser habilitado ao acessar a página **Configurar** no site do Azure no [Portal de Gerenciamento do Azure](https://manage.microsoft.com). Na página **Configurar**, use as seções **diagnóstico de aplicativos** e **diagnóstico de site** para habilitar o registro em log.

Ao habilitar o **diagnóstico de aplicativos**, você deverá também selecionar o **nível de log** e escolher se habilitará o registro em log em **sistema de arquivos**, **armazenamento de tabela** ou **armazenamento de blob**. Enquanto todos os três locais de armazenamento fornecem as mesmas informações básicas para eventos registrados, o **armazenamento em tabela** e o **armazenamento de blob** registram informações adicionais como a ID da instância, a ID do thread e um carimbo de data/hora mais granular (formato de escala) do que realizar o registro em log no **sistema de arquivos**.

Ao habilitar o **diagnóstico de site**, você deverá selecionar **armazenamento** ou **sistema de arquivos** para **registro em log de servidor Web**. Selecionar **armazenamento** permite que você selecione uma conta de armazenamento e, em seguida, um contêiner de blob onde os logs estarão gravados. Todos os outros logs para **diagnóstico de site** serão gravados apenas no sistema de arquivos.

> [WACOM.NOTE]As informações armazenadas em **armazenamento de tabela** ou **armazenamento de blob** apenas podem ser acessadas usando um cliente de armazenamento ou um aplicativo que possa trabalhar diretamente com estes sistemas de armazenamento. Por exemplo, o Visual Studio 2013 contém um Gerenciador de Armazenamento que pode ser usado para explorar armazenamentos de tabela ou de blob e o HDInsight pode acessar dados gravados no armazenamento de blob. Você também pode gravar um aplicativo que acesse o Armazenamento do Azure usando um dos [SDKs do Azure](http://www.windowsazure.com/pt-br/downloads/#).

A seguir, estão as configurações disponíveis ao habilitar o **diagnóstico de aplicativos**:

* **Nível de log** - permite que você filtre as informações capturadas como **informativas**, **de aviso** ou **de erro**. Configurar isto para **detalhado** fará o log de toda informação produzida pelo aplicativo. **O nível de registro em log** pode ser configurado de forma diferente para registro em log de **sistema de arquivos**, **armazenamento de tabela** e **armazenamento de blob**.
* **Sistema de arquivos** - armazena as informações de diagnóstico de aplicativo no sistema de arquivos do site. Estes arquivos podem ser acessados por FTP ou baixados como um arquivo Zip usando o PowerShell do Azure ou as Ferramentas de Linha de Comando do Azure.
* **Armazenamento de tabela** - armazena as informações de diagnóstico de aplicativos na conta de Armazenamento do Azure especificada  e no nome da tabela.
* **Armazenamento de blob** - armazena as informações de diagnóstico de aplicativos na conta de Armazenamento do Azure especificada e no contêiner de blob.
* **Período de retenção** - por padrão, os logs não são automaticamente excluídos do **armazenamento de blob**. Selecione **definir retenção** e insira o número de dias que os logs serão mantidos se você desejar excluir os logs automaticamente.

> [WACOM.NOTE]Qualquer combinação de sistema de arquivos, armazenamento de tabela ou armazenamento de blob pode ser habilitada ao mesmo tempo e tem configurações de nível de log individuais. Por exemplo, você pode desejar gravar erros e avisos no armazenamento de blob como uma solução de registro em log a longo prazo, habilitando o registro em log de sistema de arquivos em um nível detalhado.

> [WACOM.NOTE] O diagnóstico também pode ser habilitado no PowerShell do Azure usando o cmdlet **Set-AzureWebsite**. Se você não instalou o PowerShell do Azure ou não o configurou para usar sua Assinatura do Azure, consulte [Como usar o PowerShell do Azure](http://www.windowsazure.com/pt-br/develop/nodejs/how-to-guides/powershell-cmdlets/).

<a name="download"></a><h2>Como: Baixe logs</h2>

Informações de diagnóstico armazenadas no sistema de arquivos do site podem ser diretamente acessadas usando FTP. Também podem ser baixadas como um arquivo Zip com o PowerShell do Azure ou as Ferramentas de Linha de Comando do Azure.

A estrutura de diretórios onde os logs estão armazenados é a seguinte:

* **Logs do aplicativo** - /LogFiles/Application/. Essa pasta contém um ou mais arquivos de texto que contêm informações produzidas pelo registro em log do aplicativo.

* **Rastreamento de Solicitação Falha** - /LogFiles/W3SVC#########/. Esta pasta contém um arquivo XSL e um ou mais arquivos XML. Baixe o arquivo XSL no mesmo diretório que o(s) arquivo(s) XML, pois o arquivo XSL fornece funcionalidade para formatar e filtrar o conteúdo do(s) arquivo(s) XML quando visualizado(s) no Internet Explorer.

* **Logs de erro do aplicativo** - /LogFiles/DetailedErrors/. Esta pasta contém um ou mais arquivos .htm que fornecem informações exaustivas para quaisquer erros de HTTP. 

* **Logs do Web Server** - /LogFiles/http/RawLogs. Esta pasta contém um ou mais arquivos de texto que foram formatados usando o [formato W3C estendido de arquivo de log](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). 

* **Logs de implantação** - /LogFiles/Git. Esta pasta contém logs gerados pelo processo interno de implantação usado pelos sites do Azure, assim como logs para implantações do Git.

###FTP

Para acessar informações de diagnóstico usando FTP, acesse o **Painel** do seu site no Portal de Gerenciamento do Azure. Na seção **visão rápida**, use o link **Logs de diagnóstico de FTP** para acessar os arquivos de log usando FTP. A entrada **Implantação/Usuário FTP** lista o nome de usuário que deve ser usado para acessar o site FTP.

> [WACOM.NOTE] Se a entrada **Implantação/Usuário FTP** não estiver definida ou se você esqueceu a senha para este usuário, uma nova senha e nome de usuário poderão ser criados usando o link **Redefinir credenciais de implantação** na seção **visão rápida** do **Painel**.

###Baixar com o PowerShell do Azure

Para baixar os arquivos de log, inicie uma nova instância do PowerShell do Azure e use o seguinte comando:

	Save-AzureWebSiteLog -Name websitename

Isto salvará os logs para o site especificado pelo parâmetro **-Name** em um arquivo chamado **logs.zip** no diretório atual.

> [WACOM.NOTE] Se você não instalou o PowerShell do Azure ou não o configurou para usar sua Assinatura do Azure, consulte [Como usar o PowerShell do Azure](http://www.windowsazure.com/pt-br/develop/nodejs/how-to-guides/powershell-cmdlets/).

###Baixar com as ferramentas da linha de comando do Azure

Para baixar os arquivos de log usando as Ferramentas da Linha de Comando do Azure, abra uma nova sessão de prompt de comando, PowerShell, Bash ou Terminal e insira o seguinte comando:

	azure site log download websitename

Isto salvará os logs para o site nomeado 'websitename' em um arquivo nomeado **diagnostics.zip** no diretório atual.

> [WACOM.NOTE] Se você não instalou as Ferramentas da Linha de Comando ou não configurou-as para usar a sua Assinatura do Azure, consulte [Como usar as ferramentas da linha de comando do Azure](http://www.windowsazure.com/pt-br/develop/nodejs/how-to-guides/command-line-tools/).

<a name="streamlogs"></a><h2>Como: Logs de streaming</h2>

Ao desenvolver um aplicativo, é sempre útil visualizar informações de registro em log em tempo quase real. Isto pode ser feito ao transmitir informações de registro em log para seu ambiente de desenvolvimento usando o PowerShell do Azure ou as Ferramentas de Linha de Comando do Azure.

> [WACOM.NOTE]Alguns tipos de buffer de log gravam no arquivo de log, o que pode resultar em eventos com problemas na transmissão. Por exemplo, uma entrada para log de aplicativo, que ocorre quando um usuário visita uma página, pode ser exibida durante a transmissão antes da entrada de log HTTP correspondente para a solicitação da página.

> [WACOM.NOTE] O streaming de log também transmitirá informações gravadas em qualquer arquivo de texto armazenado na pasta **D:\\home\\LogFiles\\**.

###O streaming realizado com o PowerShell do Azure

Para transmitir informações de log, inicie uma nova instância do PowerShell do Azure e use o seguinte comando:

	Get-AzureWebSiteLog -Name websitename -Tail

Isto fará a conexão ao site especificado pelo parâmetro **-Name** e iniciará o streaming de informações para a janela do PowerShell, enquanto eventos de log ocorrem no site. Qualquer informação escrita para os arquivos com terminação .txt, .log ou .htm que seja armazenada no diretório /LogFiles (d:/home/LogFiles) será transmitida ao console local.

Para filtrar eventos específicos, como erros, use o parâmetro **-Message**. Por exemplo:

	Get-AzureWebSiteLog -Name websitename -Tail -Message Error

Para filtrar tipos específicos de log como HTTP, use o parâmetro **-Path**. Por exemplo:

	Get-AzureWebSiteLog -Name websitename -Tail -Path http

Para visualizar uma lista de caminhos disponíveis, use o parâmetro -ListPath.

> [WACOM.NOTE] Se você não instalou o PowerShell do Azure ou não o configurou para usar sua Assinatura do Azure, consulte [Como usar o PowerShell do Azure](http://www.windowsazure.com/pt-br/develop/nodejs/how-to-guides/powershell-cmdlets/).

###Transmitir com as ferramentas da linha de comando do Azure

Para transmitir informações de log, abra uma nova sessão de prompt de comando, PowerShell, Bash ou Terminal e insira o seguinte comando:

	azure site log tail websitename

Isto conectará ao site chamado 'websitename' e iniciará o streaming de informações para a janela, enquanto eventos de log ocorrem no site. Qualquer informação escrita para os arquivos com terminação .txt, .log ou .htm que seja armazenada no diretório /LogFiles (d:/home/LogFiles) será transmitida ao console local.

Para filtrar eventos específicos, como erros, use o parâmetro **--Filter**. Por exemplo:

	azure site log tail websitename --filter Error

Para filtrar tipos específicos de log como HTTP, use o parâmetro **--Path**. Por exemplo:

	azure site log tail websitename --path http

> [WACOM.NOTE] Se você não instalou as Ferramentas da Linha de Comando ou não configurou-as para usar a sua Assinatura do Azure, consulte [Como usar as ferramentas da linha de comando do Azure](http://www.windowsazure.com/pt-br/develop/nodejs/how-to-guides/command-line-tools/).

<a name="understandlogs"></a><h2>Como: Compreendendo os logs de diagnóstico</h2>

###Logs de diagnóstico de aplicativo

O diagnóstico de aplicativos armazenará informações em um formato específico para aplicativos .NET, dependendo se você armazenar os logs no sistema de arquivos, no armazenamento de tabela ou no armazenamento de blob. O conjunto de dados de base armazenado é o mesmo em todos os três tipos de armazenamento - a data e hora em que o evento ocorreu, a ID do processo que produziu o evento, o tipo de evento (informação, aviso ou erro) e a mensagem do evento.

__Sistema de arquivos__

Cada linha registrada no sistema de arquivos ou recebida via streaming estará no seguinte formato:

	{Date}  PID[{process id}] {event type/level} {message}

Por exemplo, um evento de erro deve aparecer semelhante ao seguinte:

	2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

O registro em sistema de arquivos fornece a informação mais básica dos três métodos disponíveis, fornecendo apenas a hora, o id do processo, o nível de evento e a mensagem.

__Table storage__

Ao registrar no armazenamento de tabela, propriedades adicionais são usadas para facilitar a busca de dados armazenados na tabela e de informações mais granulares sobre o evento. As seguintes propriedades (colunas) são usadas para cada entidade (linha) armazenada na tabela.

<table style="width:100%;border-collapse:collapse">
<thead>
<tr>
<th style="width:45%;border:1px solid black;background-color:#0099dd">Property name</th>
<th style="border:1px solid black;vertical-align:top;background-color:#0099dd">Value/format</th>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">PartitionKey</td>
<td style="border:1px solid black;vertical-align:top">Data/hora do evento no formato aaaaMMddHH</td>
</tr>
</thead>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">RowKey</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">A GUID value that uniquely identifies this entity</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Timestamp</td>
<td style="border:1px solid black;vertical-align:top">A data e hora em que o evento ocorreu</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">EventTickCount</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">The date and time that the event occurred, in Tick format (greater precision)</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">ApplicationName</td>
<td style="border:1px solid black;vertical-align:top">O nome do site</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Level</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Event level (e.g. error, warning, information)</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">EventId</td>
<td style="border:1px solid black;vertical-align:top">A ID deste evento<br>é padronizada como 0, caso nada seja especificado</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">InstanceId</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Instance of the website that the even occurred on</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Pid</td>
<td style="border:1px solid black;vertical-align:top">ID do Processo</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Tid</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">The thread ID of the thread that produced the event</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Mensagem</td>
<td style="border:1px solid black;vertical-align:top">A mensagem com detalhes do evento</td>
</tr>
</table>

__Blob storage__

Ao registrar em log no armazenamento de blob, os dados serão armazenados em um formato de valores separados por vírgulas (CSV). Semelhante ao armazenamento de tabela, campos adicionais são registrados para fornecer informações mais granulares sobre o evento. As seguintes propriedades são usadas para cada linha no CSV:

<table style="width:100%;border-collapse:collapse">
<thead>
<tr>
<th style="width:45%;border:1px solid black;background-color:#0099dd">Property name</th>
<th style="border:1px solid black;vertical-align:top;background-color:#0099dd">Value/format</th>
</tr>
</thead>
<tr>
<td style="border:1px solid black;vertical-align:top">Data</td>
<td style="border:1px solid black;vertical-align:top">A data e hora em que o evento ocorreu</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Level</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Event level (e.g. error, warning, information)</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">ApplicationName</td>
<td style="border:1px solid black;vertical-align:top">O nome do site</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">InstanceId</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Instance of the website that the even occurred on</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">EventTickCount</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">The date and time that the event occurred, in Tick format (greater precision)</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">EventId</td>
<td style="border:1px solid black;vertical-align:top">A ID deste evento<br>é padronizada como 0, caso nada seja especificado</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Pid</td>
<td style="border:1px solid black;vertical-align:top">ID do Processo</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Tid</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">The thread ID of the thread that produced the event</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Mensagem</td>
<td style="border:1px solid black;vertical-align:top">A mensagem com detalhes do evento</td>
</tr>
</table>

Os dados armazenados em um blob deverão ser semelhantes ao seguinte:

	date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
	2014-01-30T16:36:52,Error,mywebsite,6ee38a,635266966128818593,0,3096,9,An error occurred

> [WACOM.NOTE] A primeira linha do log conterá os cabeçalhos da coluna, como representado neste exemplo.

###Rastreamento de solicitação falha

O rastreamento de solicitações falhas é armazenado em arquivos XML chamados __fr######.xml__. Para facilitar a exibição das informações registradas, uma folha de estilos XSL chamada __freb.xsl__ é fornecida no mesmo diretório dos arquivos XML. A abertura de um dos arquivos XML no Internet Explorer usará a folha de estilos XSL para fornecer uma exibição de formato da informação rastreada. Isto aparecerá de modo semelhante ao seguinte:

![failed request viewed in the browser](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

###Logs detalhados de erro

Logs detalhados de erro são documentos HTML que fornecem informações mais detalhadas sobre erros HTTP que tenham ocorrido. Como são simplesmente documentos HTML, eles podem ser visualizados usando um navegador da Web.

###Logs de servidor Web

Os logs do servidor Web são formatados usando o [formato W3C estendido de arquivo de log](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Esta informação pode ser lida usando um editor de texto ou analisada usando ferramentas como o [Analisador de Log](http://go.microsoft.com/fwlink/?LinkId=246619).

> [WACOM.NOTE] Os logs produzidos pelos sites do Azure não dão suporte a campos de __s-computername__, __s-ip__ ou __cs-version__.

<a name="nextsteps"></a><h2>Próximas etapas</h2>

- [Como monitorar Sites](/pt-br/manage/services/web-sites/how-to-monitor-websites/)
- [Tutorial - solucionando problemas com sites](/pt-br/develop/net/best-practices/troubleshooting-web-sites/)
- [Solucionando problemas de sites do Azure no Visual Studio](/pt-br/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/)
- [Analisar logs de sites em HDInsight](http://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)

