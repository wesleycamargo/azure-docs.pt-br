<properties
   pageTitle="Cenário do Aplicativo Lógico: Gatilho do Barramento de Serviço da Função do Azure | Microsoft Azure"
   description="Saiba como usar as Azure Functions como um gatilho do barramento de serviço para Aplicativos Lógicos"
   services="app-service\logic,functions"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/23/2016"
   ms.author="jehollan"/>
   
# Cenário do Aplicativo Lógico: Gatilho do Barramento de Serviço da Função do Azure

As Azure Functions podem ser utilizadas como um gatilho para os Aplicativos Lógicos sempre que os ouvintes ou tarefas demoradas precisam ser implantadas. Por exemplo, você pode criar uma Função do Azure que escutaria em uma fila e acionaria imediatamente um Aplicativo Lógico como um gatilho de envio.

## Compilando o Aplicativo Lógico

Neste exemplo, você terá uma função em execução para cada Aplicativo Lógico que precisa ser disparado. Primeiro, você precisa criar um Aplicativo Lógico com um gatilho de Solicitação HTTP. A Função do Azure chamará esse ponto de extremidade sempre que uma mensagem da fila for recebida.

1. Abra um novo Aplicativo Lógico e escolha o gatilho **Manual - Quando uma Solicitação HTTP for Recebida**.  
    * Opcionalmente, você pode especificar um Esquema JSON que a mensagem da fila terá por meio de uma ferramenta como [jsonschema.net](http://jsonschema.net) e colar no gatilho. Isso permitirá que o designer compreenda a forma dos dados e envie facilmente as propriedades pelo fluxo de trabalho.
1. Adicione as etapas que você deseja realizar após uma mensagem da fila ser recebida. Por exemplo, você pode enviar uma mensagem de email pelo Office 365.  
1. Salve o Aplicativo Lógico para gerar a URL de callback do gatilho para esse Aplicativo Lógico. A URL aparecerá no cartão do gatilho.

![][1]

## Compilando a Função do Azure

Em seguida, você precisa criar uma Função do Azure que atuará como o gatilho e ouvirá a fila.

1. Abra o [Portal das Azure Functions](https://functions.azure.com/signin) e escolha **Nova Função** com um modelo **ServiceBusQueueTrigger - C#**.

    ![][2]

2. Configure a conexão com a Fila do Barramento de Serviço (que usará o ouvinte de `OnMessageReceive()` do SDK do Barramento de Serviço)
3. Grave uma função simples para chamar o ponto de extremidade do Aplicativo Lógico (acima) com a mensagem da fila. Veja um exemplo completo de uma função abaixo com um tipo de conteúdo de mensagem de `application/json`, mas isso pode ser alterado quando necessário.

   ```
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;
   
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/.........";
   
   public static void Run(string myQueueItem, TraceWriter log)
   {
       
       log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
       using (var client = new HttpClient())
       {
           var response = client.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
       }
   }
   ```

Você pode testar adicionando uma mensagem da fila por meio de uma ferramenta como o [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) e ver o Aplicativo Lógico disparar imediatamente após a Função receber a mensagem.

<!-- Image References -->
[1]: ./media/app-service-logic-scenario-function-sb-trigger/manualTrigger.PNG
[2]: ./media/app-service-logic-scenario-function-sb-trigger/newQueueTriggerFunction.PNG

<!---HONumber=AcomDC_0601_2016-->