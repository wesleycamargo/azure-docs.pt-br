<properties 
	pageTitle="Modelo de dados do Application Insights" 
	description="Descreve as propriedades exportadas de exportação contínua em JSON e usados como filtros." 
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
	ms.date="08/12/2015" 
	ms.author="awills"/>

# Modelo de dados de exportação do Application Insights

Esta tabela lista as propriedades de telemetria enviadas dos SDKs do [Application Insights](app-insights-overview.md) para o portal. Você verá essas propriedades na saída de dados de [Exportação Contínua](app-insights-export-telemetry.md). Elas também aparecerão em filtros de propriedade em [Gerenciador de Métricas](app-insights-metrics-explorer.md) e em [Pesquisa de Diagnóstico](app-insights-diagnostic-search.md).

Existem vários [exemplos](app-insights-export-telemetry.md#code-samples) que ilustram como usá-los.

O "& lt; telemetryType & gt;" da primeira seção é um espaço reservado para qualquer um dos nomes de tipo de telemetria: exibição, solicitação e assim por diante.


## &lt;telemetryType&gt;

**<measurement>**

    KVPs <string, double> <telemetryType>.measurement      Max: 100
* 
    Um recipiente de propriedades de KVP (pares chave-valor) que fornece extensibilidade para itens de telemetria do AppInsights, para adicionar métricas personalizadas. 

    *Derivação:* os nomes das medidas têm o tamanho máximo de 100

    *Padrão:* se existir a chave, mas o valor estiver ausente, count = 1, value = 0, min/max = 0

**<property>**

    KVPs <string, string> <telemetryType>.properties      Max: 100
* 
    Um recipiente de propriedades de KVP (pares chave-valor) que fornece extensibilidade para itens de telemetria do AppInsights, para adicionar propriedades personalizadas. O desenvolvedor é capaz de fornecer uma lista de KVPs associada a um item de telemetria. Cada chave é monitorada e no máximo 200 chaves exclusivas podem ser fornecidas pelo AppInsights ikey (aplicativo). Uma chave pode ter um comprimento máximo de 100 caracteres. Todos os valores são tratados como cadeias de caracteres e um tamanho máximo de 1000 caracteres pode ser fornecido. Cada propriedade é classificada, inicialmente, como uma dimensão, habilitando recursos de segmentação com base no conjunto de valores de cada propriedade. Cada conjunto de valores é monitorado segundo a chave de propriedade quanto à sua cardinalidade. Quando a cardinalidade de uma chave ultrapassa 100 valores exclusivos, a propriedade é classificada como um atributo. Um atributo pode ser pesquisado, mas não pode ser alvo de segmentação (agregação ou agrupar por). 

    *Derivação:* os nomes das propriedades têm um tamanho máximo de 100; os valores das propriedades têm um tamanho máximo de 1024

    *Padrão:* se existir a chave, mas o valor estiver ausente, o valor é null

**count**

    long <telemetryType>.count      
* 
    A contagem do item de telemetria.   

    *Derivação:* se for null, count = 1

**duration**

    simpleMetric <telemetryType>.duration   ticks   
* 
    A duração do item de telemetria. Para uma solicitação, esse é o tempo de execução da solicitação. 

    *Padrão:* R1: para o modo de exibição, este campo é opcional

**message**

    string <telemetrytype>.message      Max: 10240
* 
    Uma mensagem de texto no tipo de telemetria. Essa cadeia de caracteres está disponível para pesquisa de texto completo. 

**name**

    string <telemetrytype>.name      Max: 250
* 
    O nome do item de telemetria. Este é um nome não exclusivo entre instâncias e representa um agrupamento de tipos de telemetria. Para exibições, é padronizado como a URLData.base. Para eventos, este é um rótulo fornecido pelo desenvolvedor. Para solicitações, essa é uma forma legível da solicitação, como o controlador\\ação. 

    *Exemplos*<br/>Nomes de exibições:<br/>Exame 70-486, Pergunta 1<br/>Sobre - Meu aplicativo ASP.NET<br/><br/>Exemplos de nomes de solicitação:<br/>POST /Components/WebHandlers/ItemCompare.ashx<br/>GET /signalr/poll<br/>GET /signalr/negotiate

**severity**

    string <telemetryType>.severity      Max: 100
* 
    A severidade do item de telemetria. É válido para itens de telemetria de Rastreamento e Exceção 

**url**

    string <telemetrytype>.url      Max: 2048
* 
    A URL da exibição de página, evento, solicitação ou RDD. A URL completa tem suporte para pesquisa de texto completo e exportação. Este campo pode ter uma cardinalidade muito alta e é um atributo. Ele é analisado em um conjunto de itens de dados urlData que pode ser usado para agregações no Gerenciador de Métricas. 

    *Padrão:* R2: em remotedepencyType, se dependencyType = HTTP, este campo é obrigatório<br/> em clientperformanceType, este campo é obrigatório

    *Exemplos*<br/> https://icecream.contoso.com/main.aspx?etc=3&extraqs=%3fetc%3d3%26formid%3dc40d07a7-1cf1-4e1d-b00e-e61876d1284e&pagemode=iframe&pagetype=entityrecord<br/>http://fabrikam-oats.azurewebsites.net/index.htm

**urlData.base**

    string <telemetrytype>.urldata.base      Max: 2048
* 
    Uma parte do item de dados da URL excluindo parâmetros de consulta e host. É o URI raiz. Esse valor pode ser usado para segmentação/agregação. 

    *Derivação:* consulte no apêndice a transformação de URL

    *Exemplos*<br/>/main.aspx?etc=3&extraqs=%3fetc%3d3%26formid%3dc40d07a7-1cf1-4e1d-b00e-e61876d1284e&pagemode=iframe&pagetype=entityrecord<br/>/default.aspx<br/>/Patients/Search/<br/>

**urldata.hashTag**

    string <telemetrytype>.urldata.hashtag      Max: 100
* 
    O texto da hashtag do item de dados da URL 

    *Derivação:* consulte no apêndice a transformação de URL

**urlData.host**

    string <telemetrytype>.urldata.host      Max: 200
* 
    O host do item de dados da URL. Se o item de dados da URL for um URI local, ele é representado como vazio 

    *Derivação:* consulte no apêndice a transformação de URL

    *Exemplos*<br/>www.fabrikam.com<br/>www.contoso.com<br/>bretwpc711.azurewebsites.net<br/>

**urlData.port**

    string <telemetrytype>.urldata.port      Max: 100
* 
    A porta do item de dados da URL, se ela for representada na URL completa. Caso contrário, será vazio. 

    *Derivação:* consulte no apêndice a transformação de URL

    *Exemplos*<br/> 80<br/>443

**urlData.protocol**

    string <telemetrytype>.urldata.protocol      Max: 100
* 
    O protocolo (HTTP, FTP etc.) do item de dados da URL 

    *Derivação:* consulte no apêndice a transformação de URL

    *Exemplos*<br/> http<br/>https

**urlData.queryParameters.parameter**

    string <telemetrytype>.urldata.queryparameters.parameter      Max: 100
* 
    Uma matriz dos nomes de parâmetro de consulta do item de dados da URL 

    *Derivação:* consulte no apêndice a transformação de URL

    *Exemplos*<br/> etc<br/>extraqs<br/>pagemode<br/>pagetype

**urlData.queryParameters.value**

    string <telemetrytype>.urldata.queryparameters.value      Max: 100
* 
    Uma matriz de valores de parâmetros de consulta analisados do item de dados da URL 

    *Derivação:* consulte no apêndice a transformação de URL


## disponibilidade

**disponibilidade**

    simpleMetric availability.availability      
* 
    Um indicador do sucesso do teste de disponibilidade 

**dataSize**

    simpleMetric availability.datasize      
* 
    O tamanho da mensagem de texto &lt;telemetry&gt;.message 

**result**

    enum (pass/fail) availability.result      
* 
    Um ponteiro (chamada HTTP) para recuperar as informações detalhadas do teste de disponibilidade da Web 

**runLocation**

    string availability.runlocation      Max: 100
* 
    O local de execução do teste de disponibilidade 

**testName**

    string availability.testname      Max: 1024
* 
    O nome do teste de disponibilidade 

**testRunId**

    string availability.testrunid      Max: 100
* 
    Uma ID exclusiva para a instância da execução do teste de disponibilidade 

**testTimeStamp**

    datetime availability.testtimestamp      
* 
    O carimbo de data/hora do início da instância de execução de teste de disponibilidade 


## basicexception

**assembly**

    string basicexception.assembly      Max: 100
**exceptionGroup**

    string basicException.exceptionGroup      
**exceptionType**

    string basicexception.exceptiontype      Max: 100
**failedUserCodeAssembly**

    string basicException.failedUserCodeAssembly      
**failedUserCodeMethod**

    string basicException.failedUserCodeMethod      
**handledAt**

    string basicexception.handledat      Max: 100
**innerMostExceptionMessage**

    string basicException.innermostExceptionMessage      
**innerMostExceptionThrownAtAssembly**

    string basicException.innermostExceptionThrownAtAssembly      
**innerMostExceptionThrownAtMethod**

    string basicException.innermostExceptionThrownAtMethod      
**innerMostExceptionType**

    string basicException.innermostExceptionType      
**estático**

    string basicexception.method      Max: 100
**outerMostExceptionMessage**

    string basicException.outerExceptionMessage      
**outerMostExceptionThrownAtAssembly**

    string basicException.outerExceptionThrownAtAssembly      
**outerMostExceptionTrownAtMethod**

    string basicException.outerExceptionThrownAtMethod      
**outerMostExceptionType**

    string basicException.outerExceptionType      
**problemid**

    string basicexception.problemid      Max: 100
* 
    *Derivação:* consulte Apêndice para análise de pilha de chamadas 

**Exceptions.Assembly**

    string basicexception.exceptions.assembly      Max: 100
**Exceptions.fileName**

    string basicexception.exceptions.filename      Max: 100
**Exceptions.hasFullStack**

    boolean basicexception.exceptions.hasfullstack      
**Exceptions.Level**

    string basicexception.exceptions.level      Max: 100
**Exceptions.Line**

    long basicexception.exceptions.line      
**exceptions.message**

    string basicexception.exceptions.message      Max: 10240
**Exceptions.Method**

    string basicexception.exceptions.method      Max: 100
**Exceptions.outerId**

    long basicexception.exceptions.outerid      
**Exceptions.parsedStack**

    List<StackFrame> basicexception.exceptions.parsedstack      
**Exceptions.Stack**

    string basicexception.exceptions.stack      Max: 10240
**Exceptions.typeName**

    string basicexception.exceptions.typename      Max: 100
**parsedStackAssembly**

    string basicException.parsedStack.assembly      
**parsedStackFilename**

    string basicException.parsedStack.fileName      
**parsedStackLevel**

    string basicException.parsedStack.level      
**parsedStackLine**

    string basicException.parsedStack.line      
**paseStackMethod**

    string basicException.parsedStack.method      

## clientperformance

**domProcessing**

    simpleMetric clientperformance.domprocessing   ms   
* 
    Uma parte do tempo perTotal. Essa parte representa o tempo que o aplicativo levou para processar a resposta. Para um cliente da Web, este é o tempo de processamento do DOM (Document Object Model). Esse tempo é capturado usando a API de perfTiming do navegador moderno. 

**networkConnect**

    simpleMetric clientperformance.networkconnect   ms   
* 
    Uma parte do tempo perTotal. Essa parte representa o tempo que o aplicativo levou para fazer a conexão de rede. Esse tempo é capturado usando a API de perfTiming do navegador moderno. 

**perfTotal**

    simpleMetric clientperformance.perftotal   ms   
* 
    O tempo total para carregar a exibição. Para clientes da Web, é equivalente ao "" tempo de carregamento de página"". Esse tempo é capturado usando a API de perfTiming do navegador moderno. 

**receiveResponse**

    simpleMetric clientperformance.receiveresponse   ms   
* 
    Uma parte do tempo perTotal. Essa parte representa o tempo que o aplicativo levou para receber a resposta da solicitação. Esse tempo é capturado usando a API de perfTiming do navegador moderno. 

**sendRequest**

    simpleMetric clientperformance.sendrequest   ms   
* 
    Uma parte do tempo perTotal. Essa parte representa o tempo que o aplicativo levou para enviar a solicitação para o servidor. Esse tempo é capturado usando a API de perfTiming do navegador moderno. 


## context

**applicationBuild**

    string context.application.build      Max: 100
* 
    O número da versão do aplicativo 

**applicationVersion**

    string context.application.version      Max: 100
* 
    A versão do aplicativo cliente 

    *Exemplos*<br/>2015.5.21.3<br/>NokiaMailBye\_CD\_20150227.4

**telemetryType**

    string context.billing.telemetrytype      Max: 100
* 
    Representa o tipo de telemetria para um registro de cobrança e é usado internamente como uma segmentação de itens de telemetria faturáveis para um aplicativo 

**dataId**

    string context.data.id      Max: 100
* 
    Um identificador exclusivo do item de telemetria. Atribuído no ponto de extremidade de coleta de dados. 

    *Derivação:* UUID4 gerado

    *Exemplos*<br/>edc6eaf3-3459-46a0-bb81-bedc24913864

**eventTime**

    datetime context.data.eventtime      
* 
    A hora do evento de telemetria registrado em UTC. Normalmente, é preenchido no cliente. Se este campo estiver ausente, ele será preenchido no ponto de extremidade de coleta de dados. O formato do campo AAAA-MM-DDTHH:MM:SS.sssZ.    

    *Exemplos*<br/> 2015-05-20T04:00:46.8338283Z

**samplingRate**

    string context.data.samplerate      Max: 100
* 
    A taxa de amostragem de produtor de dados (SDK). Um valor diferente de 1 significa que as métricas associadas a este item de telemetria representam valores de amostra. Portanto, uma taxa de amostra de 0,05 resultaria em qualquer item de telemetria representando uma contagem de 20. 

**iPhone**

    string context.device.browser      Max: 100
* 
    O navegador do cliente 

    *Padrão:* se null, é definido com base no processamento do agente do usuário. Consulte no apêndice a análise do agente do usuário

    *Exemplos*<br/>Opera<br/>Mobile Safari<br/>Ovi Browser<br/>Chrome<br/>Firefox<br/>Internet Explorer

**browserVersion**

    string context.device.browserversion      Max: 100
* 
    A versão do navegador do cliente 

    *Padrão:* se null, é definido com base no processamento do agente do usuário. Consulte no apêndice a análise do agente do usuário

    *Exemplos*<br/> Opera 12.17<br/>Mobile Safari 8.0<br/>Ovi Browser 5.5<br/>Chrome 37.0<br/>Firefox 21.0<br/>Internet Explorer 7.0

**deploymentId**

    string context.device.deploymentid      Max: 100
* 
    A ID de implantação do servidor 

**deviceId**

    string context.device.id      Max: 100
* 
    Uma identificação exclusiva do cliente. Uma ID gerada que deve ser armazenada localmente no dispositivo e não deve ser PII, como o endereço MAC ou uma ID imutável semelhante.   

**deviceModel**

    string context.device.devicemodel      Max: 100
* 
    O devicemodel para o cliente de hardware móvel 

    *Exemplos*<br/> Outros<br/>iPad<br/>Nokia 503s

**deviceName**

    string context.device.name      Max: 100
* 
    O nome do dispositivo em que o aplicativo está em execução 

**deviceType**

    string context.device.type      Max: 100
* 
    O tipo de dispositivo de hardware do cliente 

    *Exemplos*<br/> PC<br/>Celular<br/>Tablet

**idioma**

    string context.device.language      Max: 100
* 
    O idioma do aplicativo no cliente. Se não for fornecida explicitamente no item de telemetria, ele será originado pelo processamento do campo de agente do usuário. 

**locale**

    string context.device.locale      Max: 100
* 
    O local do aplicativo no cliente. Se não for fornecida explicitamente no item de telemetria, ele é originado pelo processamento do campo de agente do usuário. 

    *Exemplos*<br/> ru<br/>pt-BR<br/>de-DE<br/>desconhecido

**machineName**

    string context.device.vmname      Max: 100
* 
    O nome da máquina do servidor. Para a computação virtualizada, este item de dados é equivalente ao host subjacente. Para a computação dedicada, esse é o nome da máquina. 

**networkType**

    string context.device.network      Max: 100
* 
    O tipo de rede do cliente 

**oemName**

    string context.device.oemname      Max: 100
* 
    O nome de OEM para o cliente de hardware móvel 

**operatingSystem**

    string context.device.os      Max: 100
* 
    O sistema operacional do cliente 

    *Padrão:* se null, é definido com base no processamento do agente do usuário. Consulte no apêndice a análise do agente do usuário

    *Exemplos*<br/> Windows<br/>iPad iOS<br/>Nokia

**operatingSystemVersion**

    string context.device.osversion      Max: 100
* 
    A versão do sistema operacional do cliente 

    *Padrão:* se null, é definido com base no processamento do agente do usuário. Consulte no apêndice a análise do agente do usuário

    *Exemplos*<br/> Windows XP<br/>iOS 8.3<br/>Nokia Series 40<br/>Windows 7<br/>Windows 8

**roleInstance**

    string context.device.roleinstance      Max: 100
* 
    A instância de computação de servidor. Em um cenário de nuvem/virtualizado, é o nome virtual da instância de computação. Em uma instância de computação dedicada, esse é o nome da máquina. Para os Serviços de Nuvem do Azure, o padrão deve ser o nome de instância de função de PaaS ou o nome da máquina virtual IaaS  

**roleName**

    string context.device.rolename      Max: 100
* 
    Um namespace lógico para instâncias de computação do servidor de grupo. Para serviços hospedados do Azure, as funções de PaaS devem ter como padrão o nome da função de PaaS. Funções de IaaS devem ter como padrão o nome da máquina virtual  

**screenHeight**

    string context.device.screenresolution.height      Max: 100
* 
    A altura da tela do aplicativo no hardware do cliente no momento em que o item de telemetria é registrado. Se não for fornecido explicitamente, é originado por uma transformação do item de dados screenresolution. 

    *Derivação:* analisado de context.device.screenresolution se estiver presente

    *Exemplos*<br/> 360<br/>1280<br/>1920

**screenResolution**

    string context.device.screenresolution      Max: 100
* 
    A resolução da tela no momento em que o item de telemetria foi capturado pelo aplicativo. Pode alternar entre retrato e paisagem no decorrer de uma sessão. Quando esse atributo é executado no nível da sessão, a resolução da primeira tela capturada representa a sessão completa. 

    *Exemplos*<br/> Largura Altura da resolução de tela<br/>360X640<br/>1280X800<br/>1920X1080

**screenWidth**

    string context.device.screenresolution.width      Max: 100
* 
    A largura da tela do aplicativo no hardware do cliente no momento em que o item de telemetria é registrado. Se não for fornecido explicitamente, é originado por uma transformação do item de dados screenresolution. 

    *Derivação:* analisado de context.device.screenresolution se estiver presente

    *Exemplos*<br/> 640<br/>800<br/>1080

**userAgentString**

    string context.device.useragent      Max: 1000
* 
    O useragent do navegador do cliente 

    *Padrão:* se for null, é definido como o agente do usuário HTTP capturado no ponto de extremidade de coleta de dados

    *Exemplos*<br/> Opera/9.80 (Windows NT 5.1) Presto/2.12.388 Versão/12.17<br/>Mozilla/5.0 (iPad; CPU OS 8\_3 como Mac OS X) AppleWebKit/600.1.4 (KHTML, como Gecko) Version/8.0 Mobile/12F69 Safari/600.1.4<br/>Chrome/37.0.2062.124 Safari/537.36<br/>Mozilla/4.0 (compatível; MSIE 7.0; Windows NT 6.1; Trident/7.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; Media Center PC 6.0; .NET4.0C; .NET4.0E)<br/>Safari/537.36<br/>+S89

**aiAgentVersion**

    string context.internal.agentversion      Max: 100
* 
    O item de dados interno representa a versão do agente SDK que produziu o item de telemetria 

**city**

    string context.location.city      Max: 100
* 
    A cidade da sessão do aplicativo. Pode ser fornecido diretamente no item de telemetria. Se não estiver presente, ele é preenchido com base no IPv4 no item de telemetria. Se não nenhum IPv4 for fornecido, o campo é deixado em branco. 

    *Exemplos*<br/> Minsk<br/>Haarlem

**clientIpAddress**

    ipv4 context.location.clientip      
* 
    O endereço IPv4 do cliente no formato xxx.xxx.xxx.xxx.   

    *Padrão:* se for null, é definido como o IP HTTP capturado no ponto de extremidade de coleta de dados

    *Exemplos*<br/> 0.123.63.143<br/>123.203.131.197

**continent**

    string context.location.continent      Max: 100
* 
    Continente da sessão do aplicativo. Pode ser fornecido diretamente no item de telemetria. Se não estiver presente, ele é preenchido com base no IPv4 no item de telemetria. Se não nenhum IPv4 for fornecido, o campo é deixado em branco. 

    *Exemplos*<br/> Europa<br/>América do Norte

**country**

    string context.location.country      Max: 100
* 
    O país da sessão do aplicativo. Pode ser fornecido diretamente no item de telemetria. Se não estiver presente, ele é preenchido com base no IPv4 no item de telemetria. Se não nenhum IPv4 for fornecido, o campo é deixado em branco. 

    *Exemplos*<br/>Belarus<br/>Países Baixos<br/>Alemanha

**latitude**

    long context.location.point.lat      
* 
    *Exemplos*<br/> 53.9<br/>45.7788 

**longitude**

    long context.location.point.lon      
* 
    *Exemplos*<br/> 27.5667<br/>-119.529 

**state**

    string context.location.province      Max: 100
* 
    O estado da sessão do aplicativo. Pode ser fornecido diretamente no item de telemetria. Se não estiver presente, ele é preenchido com base no IPv4 no item de telemetria. Se não nenhum IPv4 for fornecido, o campo é deixado em branco. 

    *Exemplos*<br/>Minsk<br/>Oregon<br/>Sérvia Central<br/>Provincia di Oristano

**operationId**

    string context.operation.id      Max: 100
* 
    O operation.id é uma ID de correlação que pode ser usada em itens de telemetria. Por exemplo, uma ID de solicitação pode ser preenchida no operation.id de todos os itens de telemetria relacionados, permitindo uma correlação entre itens de telemetria como rdd, exceções, eventos etc.  

**operationName**

    string context.operation.name      Max: 100
* 
    Um nome de operação legível por humanos. Veja a ID da operação. Permite um agrupamento de ids de operação semelhantes, como Compra Completa.   

**operationParentId**

     context.operation.parentid      
* 
    Uma ID pai para operation.id, permitindo um aninhamento de ids para correlação de telemetria.   

**syntheticSource**

    string context.data.syntheticsource      Max: 100
* 
    Se issynthetic = true, este item de dados representa a fonte dos dados sintéticos. 

    *Padrão:* se null, o agente do usuário é inspecionado quanto a fontes sintéticas conhecidas (rastreadores da Web, etc.) e, com base nisso, a fonte pode ser definida.

**syntheticTransaction**

    boolean context.data.issynthetic      
* 
    Um indicador de que o item de telemetria foi gerado por testes sintéticos, e não atividades reais de usuários. 

    *Padrão:* se null, o agente do usuário é inspecionado em relação a uma lista de agentes sintéticos conhecidos. Se uma correspondência for encontrada, o valor é definido como true.<br/>Se o agente do usuário for null, é definido como false

**session.Id**

    String context.session.id      Max: 100
* 
    Um identificador exclusivo de uma interação entre usuários reais e um aplicativo. Essa interação é uma "" sessão"". Toda a telemetria que é gerada pelo aplicativo sob o mesmo iKey deve conter esse identificador exclusivo. <br/><br/>Uma sessão é definida por eventos consecutivos dentro da mesma interação do usuário. Um período de mais de 30 minutos sem um evento de telemetria sinaliza o término de uma sessão.   

    *Padrão:* não é válido em MetricType, BillingType

    *Exemplos*<br/> CFFC8B21-9828-4F56-AD7C-B6B5AC26B133

**accountAcquisitionDate**

    datetime context.user.accountAcquisitionDate      
**anonUserId**

    string context.user.anonId      Max: 100
* 
    Um identificador exclusivo que define um usuário anônimo no aplicativo. Quando é usado um SDK, é gerado automaticamente e persiste no cliente. Embora não diferencie usuários reais quando um dispositivo é compartilhado sob o mesmo logon, diferencia entre usuários reais quando eles usam logons diferentes e o sistema operacional dá suporte a perfis. É o melhor proxy para usuários exclusivos quando não houver nenhuma experiência autenticada. 

    *Exemplos*<br/> f23854a1b01c4b1fa79fa2d9a5768526

**anonymousUserAcquisitionDate**

    datetime context.user.anonAcquisitionDate      
**authenticatedUserAcquisitionDate**

    datetime context.user.authAcquisitionDate      
**authUserId**

    string context.user.authId      Max: 100
* 
    Um identificador exclusivo que define um usuário autenticado no aplicativo. É fornecido pelo desenvolvedor. O benefício de um usuário autenticado é que o identificador pode se mover entre dispositivos dentro do aplicativo e ainda manter sua exclusividade. 

**storeRegion**

    string context.user.storeregion      Max: 100
* 
    A região do armazenamento de que o aplicativo se originou. 

**userAcountId**

    string context.user.accountId      Max: 100
* 
    Um identificador exclusivo que define uma conta no aplicativo. É fornecido pelo desenvolvedor. 

### Métricas personalizadas

    context.custom.metrics.<metric-name>

      double value
      double count
      double min
      double max
      double stdDev
      double sampledValue
      double sum


## remotedependency

**async**

    boolean remotedependency.async      
* 
    Um indicador se a chamada de dependência remota tiver sido assíncrona 

**commandName**

    string remotedependency.commandname      Max: 2048
* 
    O nome do comando SQL da dependência remota, se a dependência remota for SQL 

**dependencyTypeName**

    string remotedependency.dependencytypename      Max: 2048
* 
    O nome do tipo da dependência remoto 

**remoteDependencyName**

    string remotedependency.remotedependencyname      Max: 2048
* 
    O nome nf da dependência remota 

    *Derivação:* padronizar para &lt;telemetryType.name&gt;

**remoteDependencyType**

    string remotedependency.remotedependencytype      Max: 100
* 
    O tipo de dependência remota. Os tipos com suporte são SQL, os Recursos do AZURE (armazenamento, filas, etc.) e HTTP. 

**sucesso**

    boolean remotedependency.success      
* 
    Um indicador se a chamada de dependência remota tiver sido bem-sucedida ou com falha. 


## solicitação

**hasdetaileddata**

    boolean request.hasdetaileddata      
* 
    Um indicador que identifica se houver itens de telemetria relacionados com base na operation.id 

**httpMethod**

    string request.httpmethod      Max: 100
* 
    O método HTTP usado na solicitação.   

**ID**

    string request.id      Max: 100
* 
    Um identificador exclusivo de uma solicitação, gerado pelo SDK. Essa ID pode ser preenchida adicionalmente para a propriedade de ID de operação para correlacionar os itens de telemetria que resultam da mesma solicitação. 

**responseCode**

    long request.responsecode      
* 
    O código de resposta de uma solicitação 

**sucesso**

    boolean request.success      
* 
    Indica se a solicitação foi bem-sucedida. Um código de resposta na faixa de 200 é considerado bem-sucedido. 

    *Padrão:* se for null, é definido como true


## sessionmetric

**anonymousUserDurationSinceLastVisit**

    Long sessionmetric.anonymoususerdurationsincelastvisit      
* 
    O tempo decorrido desde a última visita por esse identificador de usuário anônimo. Na primeira visita, esse valor está vazio. Em cada visita subsequente, é o tempo entre visitas representado com a granularidade de dias. Um valor de 3 significa que se passaram 3 dias da instância de sessão anterior a essa instância de sessão 

**anonymousUserSessionCount**

    Long sessionmetric.anonymoususersessioncount      
* 
    A contagem de visitas do usuário anônimo. É um contador incremental do total de sessões históricas para esse identificador exclusivo de usuário anônimo. Cada sessão com este identificador incrementa o contador. Esse contador será zerado se o identificador de usuário não for visto em um período de 30 dias, quando o contador será redefinido e a próxima visita do identificador de usuário será considerada um novo usuário. 

**authenticatedAccountDurationSinceLastVisit**

    Long sessionmetric.authenticatedaccountdurationsincelastvisit      
* 
    O tempo decorrido desde a última visita por esse identificador de conta. Na primeira visita, esse valor está vazio. Em cada visita subsequente, é o tempo entre visitas representado com a granularidade de dias. Um valor de 3 significa que se passaram 3 dias da instância de sessão anterior a essa instância de sessão 

**authenticatedAccountSessionCount**

    Long sessionmetric.authenticatedaccountsessioncount      
* 
    A contagem de visitas para o identificador de conta autenticado. É um contador incremental do total de sessões históricas para esse identificador de conta exclusivo. Cada sessão com este identificador incrementa o contador. Esse contador será zerado se o identificador de usuário não for visto em um período de 30 dias, quando o contador será redefinido e a próxima visita do identificador de usuário será considerada um novo usuário. 

**authenticatedUserDurationSinceLastVisit**

    Long sessionmetric.authenticateduserdurationsincelastvisit      
* 
    O tempo decorrido desde a última visita por esse identificador de usuário autenticado. Na primeira visita, esse valor está vazio. Em cada visita subsequente, é o tempo entre visitas representado com a granularidade de dias. Um valor de 3 significa que se passaram 3 dias da instância de sessão anterior a essa instância de sessão 

**authenticatedUserSessionCount**

    Long sessionmetric.authenticatedusersessioncount      
* 
    A contagem de visitas para o identificador de usuário autenticado. É um contador incremental do total de sessões históricas para esse identificador exclusivo de usuário autenticado. Cada sessão com este identificador incrementa o contador. Esse contador será zerado se o identificador de usuário não for visto em um período de 30 dias, quando o contador será redefinido e a próxima visita do identificador de usuário será considerada um novo usuário. 

**crashCount**

    Long sessionmetric.crashcount      
* 
    A contagem de falhas ocorridas durante esta instância de sessão. 

**duration**

    timespan sessionmetric.duration      
* 
    A duração de uma instância de sessão 

**entryEvent**

    string sessionmetric.entryevent      Max: 200
* 
    O primeiro evento da sessão. É originado de event.name e está disponível como uma segmentação/agregação de métricas de sessionMetric 

    *Derivação:* originado de event.name

**entryUrl**

    string sessionmetric.entryurl      Max: 2048
* 
    A primeira URL da sessão. É originado de urlData.base e está disponível como uma segmentação/agregação de métricas de sessionMetric 

    *Derivação:* originado de &lt;telemetryType&gt;.Url

**eventCount**

    Long sessionmetric.eventcount      
* 
    A contagem de eventos ocorridos durante esta instância de sessão. 

**exceptionCount**

    Long sessionmetric.exceptioncount      
* 
    A contagem de exceções ocorridas durante esta instância de sessão. 

**exitEvent**

    string sessionmetric.exitevent      Max: 200
* 
    O último evento da sessão. É originado de event.name e está disponível como uma segmentação/agregação de métricas de sessionMetric 

    *Derivação:* originado de event.name

**exitUrl**

    string sessionmetric.exiturl      Max: 2048
* 
    A última URL da sessão. É originado de urlData.base e está disponível como uma segmentação/agregação de métricas de sessionMetric 

    *Derivação:* originado de &lt;telemetryType&gt;.Url

**pageBounceCount**

    boolean sessionmetric.pagebouncecount      
* 
    A contagem de sessões de salto que este item de telemetria sessionMetric representa. Uma sessão de salto é uma sessão que é criada com base em um único item de telemetria de exibição. 

    *Derivação:* se sessionMetric.viewCount + sessionMetric.requestCount = 1, então 1 else 0

**pageCount**

    Long sessionmetric.pagecount      
* 
    A contagem de exibições ocorridas durante esta instância de sessão 

**requestCount**

    Long sessionmetric.requestcount      
* 
    A contagem de solicitações ocorridas durante esta instância de sessão 

**sessionCount**

    Long sessionmetric.sessioncount      
* 
    Uma contagem de sessões que esta instância de telemetria sessionMetric representa. 


## trace

**context**

    string trace.context      Max: 100
* 
    O contexto do capp no momento do rastreamento/exceção 

**exception**

    string trace.exception      Max: 10240
* 
    A exceção associada ao item de telemetria de rastreamento 

**excerpt**

    string trace.excerpt      Max: 100
* 
    Uma breve mensagem de texto de um item de telemetria de rastreamento 

**formatMessage**

    string trace.formatmessage      Max: 100
* 
    A mensagem de formato para o item de telemetria de rastreamento 

**formatProvider**

    string trace.formatprovider      Max: 100
* 
    O provedor de formato para o item de telemetria de rastreamento 

**hasStackTrace**

    boolean trace.hasstacktrace      
* 
    O indicador se o item de telemetria de rastreamento incluir um rastreamento de pilha 

**level**

    string trace.level      Max: 100
* 
    O nível da mensagem de rastreamento 

**loggerName**

    string trace.loggername      Max: 100
* 
    O nome do agente de rastreamento 

**loggerShortName**

    string trace.loggershortname      Max: 100
* 
    O nome curto do agente 

**message**

    string trace.message      Max: 10240
* 
    A mensagem de texto completa de um item de telemetria de rastreamento 

**messageId**

    string trace.messageId      Max: 100
* 
    Um identificador exclusivo do item de telemetria de rastreamento. 

**parâmetros**

    string trace.parameters      Max: 100
* 
    Estes são os parâmetros fornecidos para o gravador de rastreamento para o item de telemetria do rastreamento 

**processId**

    string trace.processId      Max: 100
* 
    A ID de processo do aplicativo na gravação do item de telemetria 

**sourceType**

    string trace.sourceType      Max: 100
* 
    O sourcetype de um item de telemetria de rastreamento 

**sqquenceId**

    string trace.sequenceid      Max: 100
* 
    Um identificador de sequência que um provedor de rastreamento pode usar para gravar a sequência dos itens de telemetria de rastreamento 

**stacktrace**

    string trace.stacktrace      Max: 100
* 
    Um rastreamento de pilha capturado para o item de telemetria de rastreamento 

**threadId**

    string trace.threadId      Max: 100
* 
    O threadid do aplicativo no momento do registro do item de telemetria 

**userStackframe**

    string trace.userstackframe      Max: 100
* 
    O registro de ativação do usuário para o item de telemetria de rastreamento 

**userStackframNum**

    string trace.userstackframenum      Max: 100
* 
    O número do registro de ativação do usuário para o item de telemetria de rastreamento 


## exibição

**referrerDataUrl**

    string view.referralurl      Max: 2048
* 
    A URL de referência da exibição de página. A URL completa tem suporte para pesquisa de texto completo e exportação. Este campo pode ter uma cardinalidade muito alta e é um atributo. Ele é analisado em um conjunto de itens de dados referralData que pode ser usado para agregações no Gerenciador de Métricas. 

**referrerData.base**

    string view.referrerdata.base      Max: 2048
* 
    Uma parte da URL de referência, excluindo parâmetros de consulta e host. É o URI raiz. Esse valor pode ser usado para segmentação/agregação. 

    *Derivação:* consulte no apêndice a transformação de URL

**referrerData.hashTag**

    string view.referrerdata.hashtag      Max: 100
* 
    O texto da hashtag da URL de referência 

    *Derivação:* consulte no apêndice a transformação de URL

**referrerData.host**

    string view.referrerdata.host      Max: 200
* 
    O host da URL de referência. Se a URL for um URI local, ele é representado como vazio 

    *Derivação:* consulte no apêndice a transformação de URL

**referrerData.port**

    string view.referrerdata.port      Max: 100
* 
    A porta da URL de referência, se ela for representada na URL completa. Caso contrário, será vazio. 

    *Derivação:* consulte no apêndice a transformação de URL

**referrerData.protocol**

    string view.referrerdata.protocol      Max: 100
* 
    O protocolo (HTTP, FTP etc.) da URL de referência 

    *Derivação:* consulte no apêndice a transformação de URL

    *Exemplos*<br/> http<br/>https

**referrerData.queryParameters.parameter**

    string view.referrerdata.queryparameters.parameter      Max: 100
* 
    Uma matriz dos nomes de parâmetro de consulta da URL de referência 

    *Derivação:* consulte no apêndice a transformação de URL

**referrerData.queryParameters.value**

    string view.referrerdata.queryparameters.value      Max: 100
* 
    Uma matriz de valores de parâmetros de consulta analisados da URL de referringData. 

    *Derivação:* consulte no apêndice a transformação de URL



## Consulte também

* [Application Insights](app-insights-overview.md) 
* [Exportação Contínua](app-insights-export-telemetry.md)
* [Exemplos de código](app-insights-export-telemetry.md#code-samples)

<!---HONumber=Oct15_HO3-->