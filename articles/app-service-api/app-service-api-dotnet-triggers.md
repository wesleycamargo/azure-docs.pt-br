---
title: Gatilhos de aplicativo da API do Serviço de Aplicativo | Microsoft Docs
description: Como implementar gatilhos em um aplicativo de API no Serviço de Aplicativo do Azure
services: logic-apps
documentationcenter: .net
author: guangyang
manager: wpickett
editor: jimbe

ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 08/25/2016
ms.author: rachelap

---
# Gatilhos de aplicativo de API do Serviço de Aplicativo do Azure
> [!NOTE]
> Esta versão do artigo se aplica à versão do esquema 2014-12-01-preview de aplicativos de API.
> 
> 

## Visão geral
Este artigo explica como implementar gatilhos de aplicativo de API e consumi-los por meio de um aplicativo lógico.

Todos os trechos de código mostrados neste tópico foram copiados do [exemplo de código do aplicativo de API do FileWatcher](http://go.microsoft.com/fwlink/?LinkId=534802).

Observe que você precisará baixar o seguinte pacote do nuget para que o código neste artigo seja compilado e executado: [http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/).

## O que são gatilhos de aplicativo de API?
É um cenário comum para um aplicativo de API disparar um evento para que os clientes do aplicativo de API possam realizar a ação apropriada em resposta ao evento. O mecanismo baseado em API REST que oferece suporte a esse cenário é chamado de gatilho de aplicativo de API.

Por exemplo, digamos que seu código de cliente está usando o [Aplicativo de API para conexão ao Twitter](../app-service-logic/app-service-logic-connector-twitter.md) e seu código precisa executar uma ação com base em novos tweets que contêm palavras específicas. Nesse caso, você pode definir um gatilho de sondagem ou de envio por push para facilitar essa necessidade.

## Gatilho de sondagem versus gatilho de envio
Atualmente, há suporte para dois tipos de gatilhos:

* Gatilho de sondagem - o cliente faz a sondagem no aplicativo de API buscando notificação de um determinado evento ter sido acionado
* Disparador de envio - o cliente é notificado pelo aplicativo API quando um evento é acionado

### Gatilho de sondagem
Um gatilho de sondagem é implementado como uma API REST regular e espera que seus clientes (por exemplo, um aplicativo lógico) consultem-no para obter notificações. Enquanto o cliente pode manter o estado, o gatilho de sondagem em si é sem estado.

As informações sobre os pacotes de solicitação e resposta a seguir ilustram alguns aspectos fundamentais do contrato de gatilho de sondagem.

* Solicitação
  * Método HTTP: GET
  * Parâmetros
    * triggerState - esse parâmetro opcional permite que os clientes especifiquem seu estado para que o gatilho de sondagem possa decidir corretamente se deseja retornar ou não a notificação com base no estado especificado.
    * Parâmetros específicos de API
* Resposta
  * Código de status **200** - a solicitação é válida e há uma notificação do gatilho. O conteúdo da notificação será o corpo da resposta. Um cabeçalho de "Tente novamente após" na resposta indica que os dados de notificação adicionais devem ser recuperados com uma chamada de solicitação subsequente.
  * Código de status **202** - a solicitação é válida, mas não há uma nova notificação do gatilho.
  * Código de status **4xx** - a solicitação não é válida. O cliente não deve tentar solicitar novamente.
  * Código de status **5xx** - a solicitação resultou em um erro interno do servidor e/ou em um problema temporário. O cliente deve tentar solicitar novamente.

O trecho de código a seguir é um exemplo de como implementar um gatilho de sondagem.

    // Implement a poll trigger.
    [HttpGet]
    [Route("api/files/poll/TouchedFiles")]
    public HttpResponseMessage TouchedFilesPollTrigger(
        // triggerState is a UTC timestamp
        string triggerState,
        // Additional parameters
        string searchPattern = "*")
    {
        // Check to see whether there is any file touched after the timestamp.
        var lastTriggerTimeUtc = DateTime.Parse(triggerState).ToUniversalTime();
        var touchedFiles = Directory.EnumerateFiles(rootPath, searchPattern, SearchOption.AllDirectories)
            .Select(f => FileInfoWrapper.FromFileInfo(new FileInfo(f)))
            .Where(fi => fi.LastAccessTimeUtc > lastTriggerTimeUtc);

        // If there are files touched after the timestamp, return their information.
        if (touchedFiles != null && touchedFiles.Count() != 0)
        {
            // Extension method provided by the AppService service SDK.
            return this.Request.EventTriggered(new { files = touchedFiles });
        }
        // If there are no files touched after the timestamp, tell the caller to poll again after 1 mintue.
        else
        {
            // Extension method provided by the AppService service SDK.
            return this.Request.EventWaitPoll(new TimeSpan(0, 1, 0));
        }
    }

Para testar esse gatilho de sondagem, siga estas etapas:

1. Implante o Aplicativo de API com uma configuração de autenticação **Público (anônimo)**.
2. Chame a operação **touch** para tocar em um arquivo. A imagem a seguir mostra um exemplo de solicitação via Postman. ![Operação de toque de chamada via Postman](./media/app-service-api-dotnet-triggers/calltouchfilefrompostman.PNG)
3. Chame o gatilho de sondagem com o parâmetro **triggerState** definido como um carimbo de data/hora antes da etapa 2. A imagem a seguir mostra o exemplo de solicitação via Postman. ![Gatilho de sondagem de chamada via Postman](./media/app-service-api-dotnet-triggers/callpolltriggerfrompostman.PNG)

### Gatilho de push
Um gatilho de push é implementado como uma API REST regular que envia notificações para clientes que se inscreveram para serem notificados quando eventos específicos forem disparados.

As informações a seguir sobre os pacotes de solicitação e resposta ilustram alguns aspectos fundamentais do contrato de gatilho de push:

* Solicitação
  * Método HTTP: PUT
  * Parâmetros
    * triggerId: requerida - cadeia de caracteres opaca (como uma GUID) que representa o registro de um gatilho de push.
    * callbackUrl: requerida - URL do retorno de chamada a ser invocado quando o evento for acionado. A chamada é uma chamada simples do HTTP POST.
    * Parâmetros específicos de API
* Resposta
  * Código de status **200** - solicitação para registrar o cliente bem-sucedida.
  * Código de status **4xx** - a solicitação não é válida. O cliente não deve tentar solicitar novamente.
  * Código de status **5xx** - a solicitação resultou em um erro interno do servidor e/ou em um problema temporário. O cliente deve tentar solicitar novamente.
* Callback
  * Método HTTP: POST
  * Corpo da solicitação: conteúdo da notificação.

O trecho de código a seguir é um exemplo de como implementar um gatilho de push:

    // Implement a push trigger.
    [HttpPut]
    [Route("api/files/push/TouchedFiles/{triggerId}")]
    public HttpResponseMessage TouchedFilesPushTrigger(
        // triggerId is an opaque string.
        string triggerId,
        // A helper class provided by the AppService service SDK.
        // Here it defines the input of the push trigger is a string and the output to the callback is a FileInfoWrapper object.
        [FromBody]TriggerInput<string, FileInfoWrapper> triggerInput)
    {
        // Register the trigger to some trigger store.
        triggerStore.RegisterTrigger(triggerId, rootPath, triggerInput);

        // Extension method provided by the AppService service SDK indicating the registration is completed.
        return this.Request.PushTriggerRegistered(triggerInput.GetCallback());
    }

    // A simple in-memory trigger store.
    public class InMemoryTriggerStore
    {
        private static InMemoryTriggerStore instance;

        private IDictionary<string, FileSystemWatcher> _store;

        private InMemoryTriggerStore()
        {
            _store = new Dictionary<string, FileSystemWatcher>();
        }

        public static InMemoryTriggerStore Instance
        {
            get
            {
                if (instance == null)
                {
                    instance = new InMemoryTriggerStore();
                }
                return instance;
            }
        }

        // Register a push trigger.
        public void RegisterTrigger(string triggerId, string rootPath,
            TriggerInput<string, FileInfoWrapper> triggerInput)
        {
            // Use FileSystemWatcher to listen to file change event.
            var filter = string.IsNullOrEmpty(triggerInput.inputs) ? "*" : triggerInput.inputs;
            var watcher = new FileSystemWatcher(rootPath, filter);
            watcher.IncludeSubdirectories = true;
            watcher.EnableRaisingEvents = true;
            watcher.NotifyFilter = NotifyFilters.LastAccess;

            // When some file is changed, fire the push trigger.
            watcher.Changed +=
                (sender, e) => watcher_Changed(sender, e,
                    Runtime.FromAppSettings(),
                    triggerInput.GetCallback());

            // Assoicate the FileSystemWatcher object with the triggerId.
            _store[triggerId] = watcher;

        }

        // Fire the assoicated push trigger when some file is changed.
        void watcher_Changed(object sender, FileSystemEventArgs e,
            // AppService runtime object needed to invoke the callback.
            Runtime runtime,
            // The callback to invoke.
            ClientTriggerCallback<FileInfoWrapper> callback)
        {
            // Helper method provided by AppService service SDK to invoke a push trigger callback.
            callback.InvokeAsync(runtime, FileInfoWrapper.FromFileInfo(new FileInfo(e.FullPath)));
        }
    }

Para testar esse gatilho de sondagem, siga estas etapas:

1. Implante o Aplicativo de API com uma configuração de autenticação **Público (anônimo)**.
2. Navegue até [http://requestb.in/](http://requestb.in/) para criar um RequestBin que servirá como sua URL de retorno de chamada.
3. Chame o disparador de envio com uma GUID como **triggerId** e a URL RequestBin como **callbackUrl**. ![Gatilho de push de chamada via Postman](./media/app-service-api-dotnet-triggers/callpushtriggerfrompostman.PNG)
4. Chame a operação **touch** para tocar em um arquivo. A imagem a seguir mostra um exemplo de solicitação via Postman. ![Operação de toque de chamada via Postman](./media/app-service-api-dotnet-triggers/calltouchfilefrompostman.PNG)
5. Verifique o RequestBin para confirmar que o retorno de chamada do gatilho de push é invocado com a saída da propriedade. ![Gatilho de sondagem de chamada via Postman](./media/app-service-api-dotnet-triggers/pushtriggercallbackinrequestbin.PNG)

### Descrever gatilhos na definição de API
Depois de implementar os gatilhos e implantar seu aplicativo de API no Azure, navegue até a folha **definição API** no portal de visualização do Azure e você verá que os gatilhos são reconhecidos automaticamente na interface do usuário, que é orientada pela definição de API Swagger 2.0 do aplicativo de API.

![Folha Definição de API](./media/app-service-api-dotnet-triggers/apidefinitionblade.PNG)

Se você clicar no botão **Baixar Swagger** e abrir o arquivo JSON, você verá resultados semelhantes ao seguinte:

    "/api/files/poll/TouchedFiles": {
      "get": {
        "operationId": "Files_TouchedFilesPollTrigger",
        ...
        "x-ms-scheduler-trigger": "poll"
      }
    },
    "/api/files/push/TouchedFiles/{triggerId}": {
      "put": {
        "operationId": "Files_TouchedFilesPushTrigger",
        ...
        "x-ms-scheduler-trigger": "push"
      }
    }

A propriedade de extensão **x-ms-schedular-trigger** é como os gatilhos são descritos na definição de API, e é adicionada automaticamente pelo gateway de aplicativo de API quando você solicita a definição de API por meio do gateway se a solicitação corresponde a um dos critérios a seguir. (Você pode também adicionar essa propriedade manualmente.)

* Gatilho de sondagem
  * Se o método HTTP é **GET**.
  * Se a propriedade **operationId** contém a cadeia de caracteres **trigger**.
  * Se a propriedade **parameters** inclui um parâmetro com uma propriedade **name** definida como **triggerState**.
* Gatilho de push
  * Se o método HTTP é **PUT**.
  * Se a propriedade **operationId** contém a cadeia de caracteres **trigger**.
  * Se a propriedade **parameters** inclui um parâmetro com uma propriedade **name** definida como **triggerId**.

## Usar gatilhos de aplicativo de API em aplicativos lógicos
### Listar e configurar gatilhos de aplicativo de API no designer de aplicativos lógicos
Se você criar um aplicativo lógico no mesmo grupo de recursos que o aplicativo de API, você poderá adicioná-la à tela do designer simplesmente clicando nele. As imagens a seguir ilustram isso:

![Gatilhos no Designer de Aplicativos Lógicos](./media/app-service-api-dotnet-triggers/triggersinlogicappdesigner.PNG)

![Configurar o Gatilho de Sondagem no Designer de Aplicativos Lógicos](./media/app-service-api-dotnet-triggers/configurepolltriggerinlogicappdesigner.PNG)

![Configurar o Gatilho de Push no Designer de Aplicativos Lógicos](./media/app-service-api-dotnet-triggers/configurepushtriggerinlogicappdesigner.PNG)

## Otimizar gatilhos de aplicativo de API para aplicativos lógicos
Depois de adicionar gatilhos a um aplicativo de API, há algumas coisas que você pode fazer para melhorar a experiência ao usar o aplicativo de API em um aplicativo lógico.

Por exemplo, o parâmetro **triggerState** para gatilhos sondagem deve ser definido como a expressão a seguir no aplicativo lógico. Essa expressão deve avaliar a última chamada do gatilho pelo aplicativo lógico e retornar esse valor.

    @coalesce(triggers()?.outputs?.body?['triggerState'], '')

OBSERVAÇÃO: Para obter uma explicação das funções usadas na expressão acima, consulte a documentação sobre [Linguagem de definição de fluxo de trabalho de aplicativos lógicos](https://msdn.microsoft.com/library/azure/dn948512.aspx).

Os usuários de aplicativos lógicos precisariam fornecer a expressão acima para o parâmetro **triggerState** ao usar o gatilho. É possível fazer com que esse valor seja predefinido pelo designer do aplicativo lógico por meio da propriedade de extensão **x-ms-scheduler-recommendation**. A propriedade de extensão **x-ms-visibility** pode ser definida com o um valor *internal* para que o parâmetro em si não seja exibido no designer. O trecho de código a seguir ilustra isso.

    "/api/Messages/poll": {
      "get": {
        "operationId": "Messages_NewMessageTrigger",
        "parameters": [
          {
            "name": "triggerState",
            "in": "query",
            "required": true,
            "x-ms-visibility": "internal",
            "x-ms-scheduler-recommendation": "@coalesce(triggers()?.outputs?.body?['triggerState'], '')",
            "type": "string"
          }
        ]
        ...
        "x-ms-scheduler-trigger": "poll"
      }
    }

Para gatilhos de envio, o parâmetro **triggerId** deve identificar o aplicativo lógico de modo único. Uma prática recomendada é definir essa propriedade como o nome do fluxo de trabalho, usando a seguinte expressão:

    @workflow().name

Usando as propriedades de extensão **x-ms-scheduler-recommendation** e **x-ms-visibility** em sua definição de API, o aplicativo de API pode transmitir ao designer de aplicativos lógicos uma instrução para definir automaticamente essa expressão para o usuário.

        "parameters":[  
          {  
            "name":"triggerId",
            "in":"path",
            "required":true,
            "x-ms-visibility":"internal",
            "x-ms-scheduler-recommendation":"@workflow().name",
            "type":"string"
          },


### Adicionar propriedades de extensão na definição de API
Informações adicionais de metadados - como as propriedades de extensão **x-ms-scheduler-recommendation** e **x-ms-visibility** -podem ser adicionadas à definição de API de dois modos: estáticos ou dinâmicos.

Para metadados estáticos, você pode editar diretamente o arquivo */metadata/apiDefinition.swagger.json* em seu projeto e adicionar as propriedades manualmente.

Para aplicativos de API usando metadados dinâmicos, você pode editar o arquivo SwaggerConfig.cs para adicionar um filtro de operação que possa adicionar essas extensões.

    GlobalConfiguration.Configuration 
        .EnableSwagger(c =>
            {
                ...
                c.OperationFilter<TriggerStateFilter>();
                ...
            }


Este é um exemplo de como essa classe pode ser implementada para facilitar o cenário de metadados dinâmicos.

    // Add extension properties on the triggerState parameter
    public class TriggerStateFilter : IOperationFilter
    {

        public void Apply(Operation operation, SchemaRegistry schemaRegistry, System.Web.Http.Description.ApiDescription apiDescription)
        {
            if (operation.operationId.IndexOf("Trigger", StringComparison.InvariantCultureIgnoreCase) >= 0)
            {
                // this is a possible trigger
                var triggerStateParam = operation.parameters.FirstOrDefault(x => x.name.Equals("triggerState"));
                if (triggerStateParam != null)
                {
                    if (triggerStateParam.vendorExtensions == null)
                    {
                        triggerStateParam.vendorExtensions = new Dictionary<string, object>();
                    }

                    // add 2 vendor extensions
                    // x-ms-visibility: set to 'internal' to signify this is an internal field
                    // x-ms-scheduler-recommendation: set to a value that logic app can use
                    triggerStateParam.vendorExtensions.Add("x-ms-visibility", "internal");
                    triggerStateParam.vendorExtensions.Add("x-ms-scheduler-recommendation",
                                                           "@coalesce(triggers()?.outputs?.body?['triggerState'], '')");
                }
            }
        }
    }


<!---HONumber=AcomDC_0831_2016-->