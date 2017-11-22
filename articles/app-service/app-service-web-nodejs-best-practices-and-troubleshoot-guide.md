---
title: "Guia de solução de problemas e práticas recomendadas para aplicativos de nó em Aplicativos Web do Azure"
description: "Conheça as práticas recomendadas e as etapas de solução de problemas para aplicativos de nó em Aplicativos Web do Azure."
services: app-service\web
documentationcenter: nodejs
author: ranjithr
manager: wadeh
editor: 
ms.assetid: 387ea217-7910-4468-8987-9a1022a99bef
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/09/2017
ms.author: ranjithr
ms.openlocfilehash: f7f3186ba93670e566a10f97dde86a977101e8fc
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2017
---
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-web-apps"></a>Guia de solução de problemas e práticas recomendadas para aplicativos de nó em Aplicativos Web do Azure
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Neste artigo, você conhecerá as práticas recomendadas e as etapas de solução de problemas para [aplicativos de nó](app-service-web-get-started-nodejs.md) em execução nos Aplicativos Web do Azure (com [iisnode](https://github.com/azure/iisnode)).

> [!WARNING]
> Tenha cuidado ao usar as etapas de solução de problemas no site de produção. A recomendação é solucionar problemas do aplicativo em uma configuração que não seja de produção (por exemplo, o slot de preparo) e, quando o problema for corrigido, trocar o slot de preparo pelo slot de produção.
> 
> 

## <a name="iisnode-configuration"></a>Configuração de IISNOD
Este [arquivo de esquema](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) mostra todas as configurações que podem ser definidas para iisnode. Algumas das configurações que são úteis para o seu aplicativo:

* nodeProcessCountPerApplication
  
    Essa configuração controla o número de processos de nó que são iniciados por aplicativo do IIS. O valor padrão é 1. Para iniciar um número de arquivos node.exe equivalente à contagem de vCPU de VM, defina isso como 0. O valor recomendado é 0 para a maioria dos aplicativos para que você possa utilizar todos os vCPUs do seu computador. Node.exe é de thread único para que um node.exe consuma um máximo de 1 vCPU. Para obter o desempenho máximo do seu aplicativo de nó, você deseja usar todos os vCPUs.
* nodeProcessCommandLine
  
    Essa configuração controla o caminho para node.exe. Você pode definir esse valor para apontar para sua versão de node.exe.
* maxConcurrentRequestsPerProcess
  
    Essa configuração controla o número máximo de solicitações simultâneas enviadas por iisnode a cada node.exe. No Aplicativo Web do Azure, o valor padrão para isso é infinito. Você não precisará se preocupar com essa configuração. Fora Aplicativo Web do Azure, o valor padrão é 1024. Você pode configurar isso dependendo de quantas solicitações o aplicativo obtém e com que rapidez ele processa cada solicitação.
* maxNamedPipeConnectionRetry
  
    Essa configuração controla o número máximo de vezes que iisnode tentará estabelecer uma conexão no pipe nomeado para enviar a solicitação a node.exe. Essa configuração, em combinação com namedPipeConnectionRetryDelay, determina o tempo limite total de cada solicitação em iisnode. O valor padrão é 200 no Aplicativo Web do Azure. Total Timeout em segundos = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay)/1000
* namedPipeConnectionRetryDelay
  
    Essa configuração controla o tempo (em ms) pelo qual iisnode aguardará entre cada tentativa para enviar a solicitação a node.exe no pipe nomeado. O valor padrão é 250 ms.
    Total Timeout em segundos = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay)/1000
  
    Por padrão, o tempo limite total no iisnode no Aplicativo Web do Azure é 200 \* 250 ms = 50 segundos.
* logDirectory
  
    Essa configuração controla o diretório em que o iisnode registrará em log stdout/stderr. O valor padrão é iisnode, que é relativo ao diretório de scripts principal (o diretório em que o server.js principal está presente)
* debuggerExtensionDll
  
    Essa configuração controla qual versão de node-inspector o iisnode usará ao depurar o aplicativo de nó. Atualmente, iisnode-inspector-0.7.3.dll e iisnode-inspector.dll são os dois únicos valores válidos para essa configuração. O valor padrão é iisnode-inspector-0.7.3.dll. A versão do iisnode-inspector-0.7.3.dll usa node-inspector-0.7.3 e usa o WebSocket. Você deve habilitar websockets em seu aplicativo Web do Azure para usar esta versão. Confira <http://www.ranjithr.com/?p=98> para saber mais sobre como configurar o iisnode para usar o novo node-inspector.
* flushResponse
  
    O comportamento padrão do IIS é que ele armazena em buffer dados de resposta até 4 MB antes de liberá-los ou até o fim da resposta, o que ocorrer primeiro. O iisnode oferece uma configuração para substituir esse comportamento: para liberar um fragmento do corpo da entidade de resposta assim que o iisnode o receba de node.exe, você precisa definir o atributo iisnode/@flushResponse no web.config como 'true':
  
    ```
    <configuration>    
        <system.webServer>    
            <!-- ... -->    
            <iisnode flushResponse="true" />    
        </system.webServer>    
    </configuration>
    ```
  
    Habilitar a liberação de todos os fragmentos do corpo da entidade de resposta aumenta a sobrecarga de desempenho, o que reduz a taxa de transferência do sistema, em cerca de 5% (a partir da v0.1.13). Portanto, é melhor definir o escopo dessa configuração somente para pontos de extremidade que exigem streaming de resposta (por exemplo, usando o elemento <location> no web.config).
  
    Além disso, para aplicativos de streaming, você também precisará definir responseBufferLimit do manipulador do iisnode como 0.
  
    ```
    <handlers>    
        <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>    
    </handlers>
    ```
* watchedFiles
  
    Essa é uma lista de arquivos separados por ponto e vírgula que serão observados para detectar alterações. Uma alteração em um arquivo faz com que o aplicativo seja reciclado. Cada entrada consiste em um nome de diretório opcional, além do nome de arquivo necessário, os quais são relativos ao diretório em que se encontra o ponto de entrada do aplicativo principal. Caracteres curinga são permitidos apenas na parte de nome de arquivo. O valor padrão é “\*.js;web.config”
* recycleSignalEnabled
  
    O valor padrão é falso. Se habilitado, o aplicativo de nó pode se conectar a um pipe nomeado (variável de ambiente IISNODE\_CONTROL\_PIPE) e enviar uma mensagem de "reciclagem". Isso fará com que w3wp seja reciclado normalmente.
* idlePageOutTimePeriod
  
    O valor padrão é 0, o que significa que esse recurso está desabilitado. Quando definido como um valor maior que 0, iisnode fará uma remoção de página de todos os seus processos filho a cada 'idlePageOutTimePeriod' em milissegundos. Confira a [documentação](https://msdn.microsoft.com/library/windows/desktop/ms682606.aspx) para compreender o que a remoção de página significa. Essa configuração será útil para aplicativos que consomem muita memória e querem executar uma remoção de página de memória para o disco ocasionalmente para liberar memória RAM.

> [!WARNING]
> Tenha cuidado ao habilitar as definições de configuração a seguir em aplicativos de produção. A recomendação é não as habilitar em aplicativos de produção ativos.
> 
> 

* debugHeaderEnabled
  
    O valor padrão é falso. Se definido como verdadeiro, iisnode adicionará um cabeçalho de resposta HTTP iisnode-debug a todas as respostas HTTP que enviar ao cabeçalho iisnode-debug cujo valor é uma URL. Informações de diagnóstico individuais podem ser obtidas, observando-se o fragmento de URL, mas uma visualização muito melhor é obtida abrindo-se a URL no navegador.
* loggingEnabled
  
    Essa configuração controla o registro em log de stdout e stderr por iisnode. Iisnode capturará stdout/stderr dos processos de nó que ele iniciar e gravará no diretório especificado na configuração 'logDirectory'. Depois que isso for habilitado, o aplicativo gravará logs no sistema de arquivos e, dependendo da quantidade de logs realizados pelo aplicativo, poderá haver implicações de desempenho.
* devErrorsEnabled
  
   O valor padrão é falso. Quando definido como true, iisnode exibirá o código de status HTTP e o código de erro Win32 no navegador. O código win32 será útil na depuração de certos tipos de problemas.
* debuggingEnabled (não habilitar em sites de produção ativos)
  
    Essa configuração controla o recurso de depuração. Iisnode é integrado a node-inspector. Habilitando essa configuração, você habilita a depuração do aplicativo de nó. Quando essa configuração estiver habilitada, iisnode definirá o layout dos arquivos de node-inspector necessários no diretório 'debuggerVirtualDir' na primeira solicitação de depuração ao aplicativo de nó. Você pode carregar o node-inspector ao enviar uma solicitação para http://seusite/server.js/debug. Você pode controlar o segmento de URL de depuração com a configuração 'debuggerPathSegment'. Por padrão, debuggerPathSegment='debug'. Você pode definir isso em um GUID, por exemplo, para que seja mais difícil de ser descoberto por outros.
  
    Confira este [link](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html) para obter mais detalhes sobre a depuração.

## <a name="scenarios-and-recommendationstroubleshooting"></a>Cenários e recomendações/solução de problemas
### <a name="my-node-application-is-making-too-many-outbound-calls"></a>O aplicativo de nó está fazendo muitas chamadas de saída.
Muitos aplicativos desejam fazer conexões de saída como parte de suas operações normais. Por exemplo, quando uma solicitação chega, o aplicativo de nó deseja contatar uma API REST em outro lugar e obter algumas informações para processar a solicitação. Convém usar um agente keep alive ao fazer chamadas http ou https. Por exemplo, você pode usar o módulo agentkeepalive como seu agente keep alive ao fazer essas chamadas de saída. Isso garante que os soquetes sejam reutilizados na máquina virtual do Aplicativo Web do Azure, reduzindo a sobrecarga de criação de novos soquetes para cada solicitação de saída. Além disso, garante que você use o menor número de soquetes para fazer muitas solicitações de saída e, assim, não exceda os maxSockets que são alocados por VM. A recomendação no Aplicativo Web Azure é definir o valor de agentKeepAlive maxSockets como um total de 160 soquetes por VM. Isso significa que se há quatro node.exe em execução na VM, convém definir agentKeepAlive maxSockets como 40 por node.exe, que é um total de 160 por VM.

Exemplo de configuração [agentKeepALive](https://www.npmjs.com/package/agentkeepalive):

```
var keepaliveAgent = new Agent({    
    maxSockets: 40,    
    maxFreeSockets: 10,    
    timeout: 60000,    
    keepAliveTimeout: 300000    
});
```

Este exemplo pressupõe que haja quatro node.exe em execução na VM. Se houver um número diferente de node.exe em execução na VM, você precisará modificar a configuração maxSockets de forma adequada.

### <a name="my-node-application-is-consuming-too-much-cpu"></a>Meu aplicativo de nó está consumindo muita CPU.
Você provavelmente obterá uma recomendação do Aplicativo Web do Azure no portal sobre o alto consumo de cpu. Você também pode configurar monitores para observar determinadas [métricas](web-sites-monitor.md). Ao conferir o uso de CPU no [Painel do Portal do Azure](../application-insights/app-insights-web-monitor-performance.md), verifique os valores máximo de CPU para que você não deixe de ver os valores de pico.
Nos casos em que achar que o aplicativo está consumindo muita CPU e não conseguir determinar o motivo, você pode analisar o seu aplicativo de nó para descobrir.

### 
#### <a name="profiling-your-node-application-on-azure-web-apps-with-v8-profiler"></a>Criar o perfil do aplicativo de nó no Aplicativo Web do Azure com o V8-Profiler
Por exemplo, digamos que você tenha um aplicativo hello world para o qual deseje criar o perfil, conforme mostrado abaixo:

```
var http = require('http');    
function WriteConsoleLog() {    
    for(var i=0;i<99999;++i) {    
        console.log('hello world');    
    }    
}

function HandleRequest() {    
    WriteConsoleLog();    
}

http.createServer(function (req, res) {    
    res.writeHead(200, {'Content-Type': 'text/html'});    
    HandleRequest();    
    res.end('Hello world!');    
}).listen(process.env.PORT);
```

Vá para o site do scm https://seusite.scm.azurewebsites.net/DebugConsole

Você verá um prompt de comando, conforme mostrado abaixo. Vá para o diretório site/wwwroot.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

Execute o comando "npm install v8-profiler".

Isso deve instalar o v8-profiler no diretório node\_modules e todas as suas dependências.
Agora, edite o server.js para criar o perfil do aplicativo.

```
var http = require('http');    
var profiler = require('v8-profiler');    
var fs = require('fs');

function WriteConsoleLog() {    
    for(var i=0;i<99999;++i) {    
        console.log('hello world');    
    }    
}

function HandleRequest() {    
    profiler.startProfiling('HandleRequest');    
    WriteConsoleLog();    
    fs.writeFileSync('profile.cpuprofile', JSON.stringify(profiler.stopProfiling('HandleRequest')));    
}

http.createServer(function (req, res) {    
    res.writeHead(200, {'Content-Type': 'text/html'});    
    HandleRequest();    
    res.end('Hello world!');    
}).listen(process.env.PORT);
```

As alterações anteriores criarão o perfil da função WriteConsoleLog e gravarão a saída do perfil no arquivo 'profile.cpuprofile' no site wwwroot. Envie uma solicitação para o aplicativo. Você verá um arquivo 'profile.cpuprofile' criado no site wwwroot.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

Fazer o download do arquivo e abri-lo com as ferramentas do Chrome F12. Pressione F12 no Chrome e clique na guia **Perfis**. Clique no botão **Carregar**. Selecione o arquivo profile.cpuprofile que você baixou. Clique no perfil que você acabou de carregar.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

Você verá que 95% do tempo foi consumido pela função WriteConsoleLog. Isso também mostra os números de linha e os arquivos de origem exatos que causaram o problema.

### <a name="my-node-application-is-consuming-too-much-memory"></a>Meu aplicativo de nó está consumindo muita memória
Se o seu aplicativo estiver consumindo muita memória, você verá um aviso dos Aplicativos Web do Azure em seu portal sobre o alto consumo de memória. Você pode configurar monitores para observar determinadas [métricas](web-sites-monitor.md). Ao conferir o uso de CPU no [Painel do Portal do Azure](../application-insights/app-insights-web-monitor-performance.md), verifique os valores máximo de CPU para a memória para que você não deixe de ver os valores de pico.

#### <a name="leak-detection-and-heap-diffing-for-nodejs"></a>Detecção de perda e Comparação de Heap para node.js
Você pode usar [node-memwatch](https://github.com/lloyd/node-memwatch) para ajudá-lo a identificar perdas de memória.
Você pode instalar memwatch da mesma forma como v8-profiler e editar o código para capturar e comparar heaps a fim de identificar perdas de memória no aplicativo.

### <a name="my-nodeexes-are-getting-killed-randomly"></a>Os arquivos node.exe estão sendo encerrados aleatoriamente
Há alguns motivos pelos quais isso pode ocorrer:

1. Seu aplicativo está lançando exceções não identificadas – confira o arquivo d:\\home\\LogFiles\\Application\\logging-errors.txt para obter detalhes sobre a exceção lançada. Esse arquivo tem o rastreamento de pilha para que você possa corrigir o aplicativo com base nisso.
2. O aplicativo está consumindo muita memória, o que está impedindo outros processos de serem iniciados. Se a memória total da VM está próxima de 100%, o node.exe pode ter sido encerrado pelo gerenciador de processos para deixar que outros processos possam funcionar. Para corrigir isso, verifique se o aplicativo não está perdendo memória OU se o aplicativo precisa usar muita memória, escale verticalmente para uma máquina virtual maior com muito mais RAM.

### <a name="my-node-application-does-not-start"></a>O aplicativo de nó não é iniciado
Se o aplicativo estiver retornando Erros 500 na inicialização, pode haver alguns motivos:

1. Node.exe não está presente no local correto. Verifique a configuração nodeProcessCommandLine.
2. O arquivo de script principal não está presente no local correto. Verifique web.config e confira se o nome do arquivo de script principal na seção de manipuladores corresponde ao arquivo de script principal.
3. A configuração de Web.config não está correta. Verifique os nomes/valores das configurações.
4. Inicialização a Frio – o aplicativo está demorando muito para ser inicializado. Se o aplicativo demorar mais do que (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay)/1000 segundos, iisnode retornará um erro 500. Aumente os valores dessas configurações para corresponder à hora de início do aplicativo e impedir que iisnode atinja o tempo limite e retorne o erro 500.

### <a name="my-node-application-crashed"></a>Meu aplicativo de nó falhou
Seu aplicativo está lançando exceções não identificadas – confira o arquivo d:\\home\\LogFiles\\Application\\logging-errors.txt para obter detalhes sobre a exceção lançada. Esse arquivo tem o rastreamento de pilha para que você possa corrigir o aplicativo com base nisso.

### <a name="my-node-application-takes-too-much-time-to-start-cold-start"></a>O aplicativo de nó demora muito para ser inicializado (Inicialização a Frio)
O motivo mais comum para um aplicativo demorar muito para iniciar é um grande número de arquivos no nó\_módulos. O aplicativo tenta carregar a maioria desses arquivos ao iniciar. Por padrão, como os arquivos residem no compartilhamento de rede no Aplicativo Web do Azure, o carregamento de tantos arquivos pode levar tempo.
Algumas soluções para tornar esse processo mais rápido são:

1. Verifique se você tem uma estrutura de dependência simples e nenhuma dependência duplicada usando npm3 para instalar os módulos.
2. Tente carregar lentamente os node\_modules e não carregue todos os módulos na inicialização do aplicativo. Isso significa que a chamada para exigir ('module') deverá ser feita quando você realmente precisar dela na função ao tentar usar o módulo.
3. O Aplicativo Web do Azure oferece um recurso chamado cache local. Esse recurso copia o conteúdo do compartilhamento de rede para o disco local na VM. Como os arquivos são locais, o tempo de carregamento do nó\_módulos é muito mais rápido.

## <a name="iisnode-http-status-and-substatus"></a>Substatus e status http de IISNODE
Esse [arquivo de origem](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) lista todas as combinações possíveis de status/substatus que o iisnode pode retornar em caso de um erro.

Habilite FREB para que o aplicativo veja o código de erro win32 (habilite FREB apenas em sites que não sejam produção, por motivos de desempenho).

| Status Http | SubStatus Http | Razão Possível? |
| --- | --- | --- |
| 500 |1000 |Houve algum problema ao expedir a solicitação para IISNODE – Verifique se node.exe foi iniciado. Node.exe pode ter falhado ao inicializar. Verifique se há erros na configuração de web.config. |
| 500 |1001 |- Win32Error 0x2 - o aplicativo não está respondendo à URL. Verifique as regras de regravação de URL ou se o aplicativo expresso tem as rotas corretas definidas. -Win32Error 0x6d – o pipe nomeado está ocupado – Node.exe não está aceitando solicitações porque o pipe está ocupado. Verifique o alto uso da cpu. - Outros erros – verifique se node.exe falhou. |
| 500 |1002 |Falha de node.exe – confira d:\\home\\LogFiles\\logging-errors.txt para o rastreamento de pilha. |
| 500 |1003 |Problema de configuração de pipe – isso nunca deverá ocorrer, mas, caso ocorra, a configuração de pipe nomeado está incorreta. |
| 500 |1004-1018 |Erro ao enviar a solicitação ou ao processar a resposta de/para node.exe. Verifique se node.exe falhou. verifique d:\\home\\LogFiles\\logging-errors.txt para rastrear a pilha. |
| 503 |1000 |Não há memória suficiente para alocar mais conexões de pipe nomeado. Verifique por que o aplicativo está consumindo tanta memória. Verifique o valor da configuração maxConcurrentRequestsPerProcess. Se não estiver definido como infinito e houver muitas solicitações, aumente o valor para evitar o erro. |
| 503 |1001 |Não foi possível expedir a solicitação para node.exe porque o aplicativo está sendo reciclado. Depois que o aplicativo for reciclado, as solicitações deverão ser atendidas normalmente. |
| 503 |1002 |Verifique o código de erro win32 para obter o motivo real – não foi possível distribuir a solicitação para um node.exe. |
| 503 |1003 |O pipe nomeado está muito ocupado – verifique se o nó está consumindo muita CPU |

Há uma configuração no NODE.exe chamada NODE\_PENDING\_PIPE\_INSTANCES. Por padrão, fora do Aplicativo Web do Azure, esse valor é 4. Isso significa que node.exe só pode aceitar quatro solicitações por vez no pipe nomeado. Em aplicativos Web do Azure, esse valor é definido como 5000. Esse valor deve ser suficiente para a maioria dos aplicativos de nó em execução no Aplicativo Web Azure. Você não deve ver 503.1003 no Aplicativo Web do Azure, porque temos um valor alto para NODE\_PENDING\_PIPE\_INSTANCES.  |

## <a name="more-resources"></a>Mais recursos
Siga estes links para saber mais sobre aplicativos do node.js no Serviço de Aplicativo do Azure.

* [Introdução aos aplicativos Web do Node.js no Serviço de Aplicativo do Azure](app-service-web-get-started-nodejs.md)
* [Como depurar um aplicativo Web Node.js no Serviço de Aplicativo do Azure](app-service-web-tutorial-nodejs-mongodb-app.md)
* [Usando Módulos no Node.js com aplicativos do Microsoft Azure](../nodejs-use-node-modules-azure-apps.md)
* [Aplicativos Web do Serviço de Aplicativo do Azure: Node.js](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Centro de Desenvolvedores do Node.js](../nodejs-use-node-modules-azure-apps.md)
* [Explorar o Console de Depuração Super Secret Kudu](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)

