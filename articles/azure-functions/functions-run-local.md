<properties
	pageTitle="Desenvolver e executar funções do Azure localmente | Microsoft Azure"
	description="Saiba como codificar e testar funções do Azure no Visual Studio antes de executá-las no Serviço de Aplicativo do Azure."
	services="functions"
	documentationCenter="na"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="functions"
	ms.workload="na"
	ms.tgt_pltfrm="multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="04/14/2016"
	ms.author="tdykstra"/>

# Como codificar e testar as funções do Azure no Visual Studio

## Visão geral

Este artigo explica como executar [Azure Functions](functions-overview.md) localmente baixando o repositório do GitHub [WebJobs.Script](https://github.com/Azure/azure-webjobs-sdk-script/) e executando a solução do Visual Studio que ele contém.

O tempo de execução para Azure Functions é uma implementação do projeto de software livre WebJobs.Script. Esse projeto, por sua vez, se baseia no [SDK de Trabalhos Web do Azure](../app-service-web/websites-dotnet-webjobs-sdk.md), e ambas as estruturas podem ser executadas localmente. No entanto, você precisa se conectar a uma conta de armazenamento do Azure, pois o SDK de Trabalhos Web usa recursos da conta de armazenamento aos quais o emulador de armazenamento não dá suporte.

As funções são fáceis de codificar e testar no portal do Azure, mas, às vezes, é útil trabalhar com elas localmente antes da execução no Azure. Por exemplo, para algumas das linguagens que dão suporte a Azure Functions, é mais fácil escrever código no Visual Studio, pois ele fornece o [IntelliSense](https://msdn.microsoft.com/library/hcw1s69b.aspx). Enquanto você depura uma função remotamente, pode ser mais rápido e fácil depurá-la localmente. Ao executar localmente, você pode depurar e definir pontos de interrupção no código de função, bem como no código de host de Script de Trabalhos Web.

>[AZURE.NOTE] Atualmente, o recurso Azure Functions está em visualização, e a experiência geral, incluindo as ferramentas, ainda está em desenvolvimento rápido. Os procedimentos descritos neste artigo não refletem a experiência de desenvolvimento local final, e será muito útil se você [fornecer seus comentários](https://feedback.azure.com/forums/355860-azure-functions).

## Pré-requisitos

### Uma conta do Azure com uma função de aplicativo

Este artigo pressupõe que você tenha trabalhado com [Azure Functions](functions-overview.md) no portal e esteja familiarizado com os conceitos de Azure Functions, como [gatilhos, associações e JobHost](functions-reference.md).

Ao executar funções localmente, você obtém uma saída na janela do console, mas também convém usar o painel que é hospedado por um aplicativo de função dinâmica para exibir logs e invocações de função.

### Visual Studio 2015 com o último SDK do Azure para .NET

Se não tiver o Visual Studio 2015 ou o SDK do Azure atual, [baixe o SDK do Azure para Visual Studio 2015](http://go.microsoft.com/fwlink/?linkid=518003). O Visual Studio 2015 será instalado automaticamente com o SDK caso você ainda não o tenha.

### Pré-requisitos condicionais

Alguns recursos do Azure e instalações de software serão necessários apenas se você planejar executar funções que os utilizem; por exemplo:

* Recursos do Azure
	* Barramento de Serviço
	* Tabelas simples
	* Banco de Dados de Documentos
	* Hubs de Evento
	* Hubs de Notificação

* Compiladores e mecanismos de script
	* F#
	* BASH
	* Python
	* PHP

Para obter detalhes sobre esses requisitos, incluindo variáveis de ambiente que você precisa definir, confira as [páginas wiki do repositório WebJobs.Script](https://github.com/Azure/azure-webjobs-sdk-script/wiki/home)

Se o seu objetivo é contribuir para o projeto WebJobs.SDK, todos os pré-requisitos condicionais são necessários para executar testes completos.

## Para executar localmente

1. [Clone](https://github.com/Azure/azure-webjobs-sdk-script/) ou [baixe](https://github.com/Azure/azure-webjobs-sdk-script/archive/master.zip) repositório Webjobs.Script.

2. Defina variáveis de ambiente para cadeias de conexão de armazenamento.

	* AzureWebJobsStorage
	* AzureWebJobsDashboard

	Você pode obter esses valores da folha do portal de **Configurações de Aplicativo** do Serviço de Aplicativo para um aplicativo de função.

	a. Na folha **Aplicativo de função**, clique em **Configurações de aplicativo de função**.

	![Clique em Configurações do Aplicativo de Função](./media/functions-run-local/clickfuncappsettings.png)
 
	b. Na folha **Configurações de aplicativo de função**, clique em **Ir para Configurações do Serviço de Aplicativo**.

	![Clique em Configurações do Serviço de Aplicativo](./media/functions-run-local/clickappsvcsettings.png)
 
	c. Na folha **Configurações**, clique em **Configurações do aplicativo**.

	![Clique em Configurações do Aplicativo](./media/functions-run-local/clickappsettings.png)
 
	d. Na folha **Configurações do aplicativo**, role para baixo até a seção **Configurações do aplicativo** e localize as configurações do SDK WebJobs.

	![Configurações de Trabalhos Web](./media/functions-run-local/wjsettings.png)

	e. Defina uma variável de ambiente com o mesmo nome e valor que a configuração de aplicativo `AzureWebJobsStorage`.

	f. Faça o mesmo para a configuração de aplicativo `AzureWebJobsDashboard`.

2. Crie uma variável de ambiente chamada AzureWebJobsServiceBus e defina-a como a cadeia de conexão do Barramento de Serviço.

	Essa variável de ambiente é necessária para associações do Barramento de Serviço, e é recomendável que você a defina mesmo que não use associações do Barramento de Serviço. Em alguns cenários, talvez você veja exceções se a cadeia de conexão do Barramento de Serviço não estiver definida, independentemente das associações em uso.

3. Verifique se outras variáveis de ambiente de que você necessita estão definidas. (Confira a seção [Pré-requisitos condicionais](#conditional-prerequisites) anterior).

4. Inicie o Visual Studio e abra a solução WebJobs.Script.

6. Defina o projeto de inicialização. Se desejar executar funções que usam disparadores HTTP ou WebHook, escolha **WebJobs.Script.WebHost**; caso contrário, escolha **WebJobs.Script.Host**.

4. Se o projeto de inicialização for WebJobs.Script.Host:

	a. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto WebJobs.Script.Host e clique em **Propriedades**.

	b. Na guia **Depurar** da janela **Propriedades do Projeto**, defina **Argumentos de linha de comando** para `..\..\..\..\sample`.

	![Argumentos de linha de comando](./media/functions-run-local/cmdlineargs.png)

	Esse é um caminho relativo para a pasta *exemplo* no repositório. A pasta *exemplo* contém um arquivo *host.json* com as configurações globais e uma pasta para cada função de exemplo.

	Para começar, é mais fácil usar a pasta *exemplo* fornecida. Posteriormente, você pode adicionar suas próprias funções à pasta *exemplo* ou usar qualquer pasta que contenha um *host.json* e pastas de função.

5. Se o projeto de inicialização for WebJobs.Script.WebHost:

	a. Defina uma variável de ambiente AzureWebJobsScriptRoot para o caminho completo para a pasta `sample`.

	b. Reinicie o Visual Studio para obter o novo valor da variável de ambiente.

	Confira a seção [Chaves de API](#api-keys) para obter informações adicionais sobre como executar funções de gatilho HTTP.

5. Abra o arquivo *exemplo\\host.json* e adicione uma propriedade `functions` para especificar quais funções você deseja executar.

	Por exemplo, o JSON a seguir fará com que o JobHost do SDK de Trabalhos Web procure apenas duas funções.

		{
		  "functions": [ "TimerTrigger-CSharp", "QueueTrigger-CSharp"],
		  "id": "5a709861cab44e68bfed5d2c2fe7fc0c"
		}

	Quando usar sua própria pasta em vez da pasta *exemplo*, inclua nela apenas as funções que você deseja executar. Assim, você pode omitir a propriedade `functions` em *host.json*.
 
6. Compile e execute a solução.

	A janela de console mostra que o JobHost localiza apenas as funções especificadas no arquivo `host.json`.

		Found the following functions:
		Host.Functions.QueueTrigger-CSharp
		Host.Functions.TimerTrigger-CSharp
		Job host started

## Exibição da saída da função

Vá para o painel do aplicativo de função ver as invocações de função e a saída do log para elas.

O painel está na seguinte URL:

	https://{function app name}.scm.azurewebsites.net/azurejobs/#/functions

A página **funções** exibe uma lista de funções que foram executadas e uma lista de invocações de função.

![Detalhe de invocação](./media/functions-run-local/invocationdetail.png)

Clique em uma invocação para ver a página **Detalhes da Invocação**, que indica quando a função foi disparada, o tempo de execução aproximado e a conclusão bem-sucedida. Clique no botão **Alternar Saída** para ver logs gravados pelo código de função.

![Detalhe de invocação](./media/functions-run-local/invocationdetail.png)

## <a id="apikeys"></a> Chaves de API para disparadores HTTP

Para executar uma função HTTP ou WebHook, você precisará de uma chave de API, a menos que inclua `"authLevel": "anonymous"` no arquivo *function.json*.

Por exemplo, se a chave de API for `12345`, você poderá disparar a função *HttpTrigger* com a URL a seguir quando o projeto WebJobs.Script.WebHost estiver em execução.

	http://localhost:28549/api/httptrigger?code=12345

(Como alternativa, você pode colocar a chave de API no cabeçalho HTTP `x-functions-key`.)

As chaves de API são armazenadas em arquivos `.json` na pasta [App\_Data/secrets](https://github.com/Azure/azure-webjobs-sdk-script/tree/master/src/WebJobs.Script.WebHost/App_Data/secrets) no projeto WebJobs.Script.WebHost.

### Chaves de API que se aplicam a todas as funções HTTP e WebHook

O arquivo *host.json* na pasta *App\_Data/secrets* tem duas chaves:

```json
{
  "masterKey": "hyexydhln844f2mb7hyexydhln844f2mb7",
  "functionKey": "7hyexydhn844f2mb7hyexydhln844f2mb7"
}
```

A propriedade `functionKey` armazena uma chave que poderá ser usada para qualquer função HTTP ou WebHook se nenhuma substituição para essa função específica for definida. Esse recurso elimina a necessidade de sempre definir novas chaves de API para todas as funções que você criar.

A propriedade `masterKey` armazena uma chave que é útil em alguns cenários de teste:

* Se você chamar uma função WebHook com uma chave mestra, o SDK de WebJobs ignorará a validação de assinatura do provedor de WebHook.

* Se você chamar uma função HTTP ou WebHook com uma chave mestra, a função será disparada mesmo que esteja desabilitada no arquivo *function.json*. Isso é usado no portal do Azure para fazer com que o botão **Executar** funcione até mesmo para funções desabilitadas.
 
### Chaves de API que se aplicam a funções individuais

Os arquivos denominados *{function name}.json* contêm a chave de API de uma função específica. Por exemplo, o conteúdo JSON a seguir em *App\_Data/secrets/HttpTrigger.json* define a chave de API para a função `HttpTrigger`.

```json
{
  "key": "844f2mdhn844f2mb7hyexydhln844f2mb7"
}
```

## Solucionar problemas

Alterações na variável de ambiente feitas enquanto o Visual Studio está em execução não são obtidas automaticamente. Se você adicionou ou alterou uma variável de ambiente após iniciar o Visual Studio, desligue o Visual Studio e reinicie-o para verificar se ele estiver obtendo os valores atuais.

Ao depurar, você pode obter mais informações sobre exceções selecionando **Exceções do Common Language Runtime** na janela **Configurações de Exceção** (CTRL-ALT-E para abrir a janela).

Outra maneira de obter mais informações de exceção durante a depuração é definir um ponto de interrupção no bloco `catch` do loop principal do host de script. Você encontrará isso no projeto WebJobs.Script, em *Host/ScriptHostManager.cs*, no método `RunAndBlock`.

## Próximas etapas

Para saber mais, consulte os recursos a seguir:

* [Referência do desenvolvedor do Azure Functions](functions-reference.md)
* [Referência do desenvolvedor de C# do Azure Functions](functions-reference-csharp.md)
* [Referência do desenvolvedor de NodeJS do Azure Functions](functions-reference-node.md)
* [Gatilhos e associações de Azure Functions](functions-triggers-bindings.md)

<!---HONumber=AcomDC_0420_2016-->