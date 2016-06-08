<properties 
	pageTitle="Como registrar eventos em log para Hubs de Eventos do Azure no Gerenciamento de API do Azure | Microsoft Azure" 
	description="Saiba como registrar eventos em log para Hubs de Eventos do Azure no Gerenciamento de API do Azure" 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/25/2016" 
	ms.author="sdanie"/>

# Como registrar eventos em log para Hubs de Eventos do Azure no Gerenciamento de API do Azure

Hub de Eventos do Azure é um serviço de entrada de dados altamente escalonável que pode incluir milhões de eventos por segundo, para que você possa processar e analisar grandes quantidades de dados produzidos por seus aplicativos e dispositivos conectados. Hub de Eventos age como a "porta de entrada” para um pipeline de eventos e depois que os dados são coletados em um hub de eventos, ele pode ser transformado e armazenado usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Hub de Eventos separa a produção de um fluxo de eventos do consumo desses eventos, para que os consumidores de eventos possam acessar os eventos em seu próprio cronograma.

Este artigo é um complemento para o [integrar o Gerenciamento de API do Azure com vídeos dos Hubs de eventos](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) e descreve como registrar eventos de Gerenciamento de API usando Hubs de eventos do Azure.

## Criar um Hub de Eventos do Azure

Para criar um novo Hub de Eventos, entre no [portal clássico do Azure](https://manage.windowsazure.com) e clique em **Novo**->**Serviços de Aplicativos**->**Barramento de Serviço**->**Hub de Eventos**->**Criação Rápida**. Insira um nome de Hub de Eventos, uma região, selecione uma assinatura e selecione um namespace. Se você ainda não tiver criado um namespace, poderá criar um inserindo um nome na caixa de texto **Namespace**. Depois que todas as propriedades estiverem configuradas, clique em **Criar um novo Hub de Eventos** para criar o Hub de Eventos.

![Criar hub de eventos][create-event-hub]

Em seguida, navegue até a guia **Configurar** do novo Hub de Eventos e crie duas **políticas de acesso compartilhado**. Nomeie a primeira como **Envio** e conceda a ela permissões de **Envio**.

![Política de envio][sending-policy]

Nomeie a segunda como **Recebimento**, conceda a ela permissões de **Escuta** e clique em **Salvar**.

![Política de recebimento][receiving-policy]

Cada política de acesso compartilhado permite que aplicativos enviem e recebam eventos de e para o Hub de Eventos. Para acessar as cadeias de conexão dessas políticas, navegue até a guia **Painel** do Hub de Eventos e clique em **Informações de conexão**.

![Cadeia de conexão][event-hub-dashboard]

A cadeia de conexão de **Envio** é usada ao registrar eventos em log e a cadeia de conexão de **Recebimento** é usada ao baixar eventos do Hub de Eventos.

![Cadeia de conexão][event-hub-connection-string]

## Criar um agente de Gerenciamento de API

Agora que você tem um Hub de Eventos, a próxima etapa será configurar um [Agente](https://msdn.microsoft.com/library/azure/mt592020.aspx) no seu serviço de Gerenciamento de API para que ele possa registrar eventos em log para o Hub de Eventos.

Os agentes do Gerenciamento de API são configurados usando a [API REST do Gerenciamento de API](http://aka.ms/smapi). Antes de usar a API REST pela primeira vez, examine os [pré-requisitos](https://msdn.microsoft.com/library/azure/dn776326.aspx#Prerequisites) e verifique se você [habilitou o acesso à API REST](https://msdn.microsoft.com/library/azure/dn776326.aspx#EnableRESTAPI).

Para criar um agente de log, faça uma solicitação HTTP PUT usando o modelo de URL a seguir.

    https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2014-02-14-preview

-	Substitua `{your service}` pelo nome da sua instância do serviço de Gerenciamento de API.
-	Substitua `{new logger name}` pelo nome desejado para o novo agente. Você fará referência a esse nome quando configurar a política [log-to-eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)

Adicione os cabeçalhos a seguir à solicitação.

-	Content-Type : application/json
-	Authorization : SharedAccessSignature uid=...
	-	Para saber mais sobre como gerar a `SharedAccessSignature`, confira [Autenticação da API REST do Gerenciamento de API do Azure](https://msdn.microsoft.com/library/azure/dn798668.aspx).

Especifique o corpo da solicitação usando o modelo a seguir.

    {
      "type" : "AzureEventHub",
      "description" : "Sample logger description",
      "credentials" : {
        "name" : "Name of the Event Hub from the Azure Classic Portal",
        "connectionString" : "Endpoint=Event Hub Sender connection string"
        }
    }

-	`type` deve ser definido como `AzureEventHub`.
-	`description` fornece uma descrição opcional do agente e pode ser uma cadeia de caracteres de comprimento zero, se desejado.
-	`credentials` contém `name` e `connectionString` do seu Hub de Eventos do Azure.

Quando você fizer a solicitação, se o agente for criado, um código de status `201 Created` será retornado

>[AZURE.NOTE] Para outros códigos de retorno possíveis e seus motivos, confira [Criar um agente](https://msdn.microsoft.com/library/azure/mt592020.aspx#PUT). Para saber como executar outras operações, por exemplo, listar, atualizar e excluir, confira a documentação da entidade [Agente](https://msdn.microsoft.com/library/azure/mt592020.aspx).

## Configurar políticas log-to-eventhubs

Depois que o agente de log estiver configurado no Gerenciamento de API, você poderá configurar suas políticas log-to-eventhubs para registrar os eventos desejados em log. A política log-to-eventhubs pode ser usada na seção de política de entrada ou na seção de política de saída.

Para configurar políticas, entre no [portal clássico do Azure](https://manage.windowsazure.com), navegue em seu serviço Gerenciamento de API e clique no **portal do editor** ou em **Gerenciar** para acessar o portal do editor.

![Portal do editor][publisher-portal]

Clique em **Políticas** no menu do Gerenciamento de API à esquerda, selecione o produto e a API desejados e clique em **Adicionar política**. Neste exemplo, estamos adicionando uma política à **API Echo** no produto **Unlimited**.

![Adicionar política][add-policy]

Posicione o cursor na seção da política `inbound` e clique na política **Log para EventHub** para inserir o modelo de instrução da política `log-to-eventhub`.

![Editor de políticas][event-hub-policy]

    <log-to-eventhub logger-id ='logger-id'>
      @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
    </log-to-eventhub>

Substitua `logger-id` pelo nome do agente de Gerenciamento de API que você configurou na etapa anterior.

Você pode usar qualquer expressão que retorne uma cadeia de caracteres como o valor do elemento `log-to-eventhub`. Neste exemplo, é registrada em log uma cadeia de caracteres que com a data e hora, o nome do serviço, a ID da solicitação, o endereço IP da solicitação e o nome da operação.

Clique em **Salvar** para salvar a configuração da política atualizada. Assim que for salva, a política estará ativa e os eventos serão registrados em log para o Hub de Eventos designado.

## Próximas etapas

-	Saiba mais sobre Hubs de Eventos do Azure
	-	[Introdução aos Hubs de Eventos do Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
	-	[Receber mensagens com EventProcessorHost](../event-hubs/event-hubs-csharp-ephcs-getstarted.md#receive-messages-with-eventprocessorhost)
	-	[Guia de programação dos Hubs de Eventos](../event-hubs/event-hubs-programming-guide.md)
-	Saiba mais sobre a integração do Gerenciamento de API e Hubs de eventos
	-	[Referência de entidade do agente](https://msdn.microsoft.com/library/azure/mt592020.aspx)
	-	[referência de política de log ao hub de eventos](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)
	-	[Monitorar suas APIs com o Gerenciamento de API do Azure, Hubs de Eventos e Runscope](api-management-log-to-eventhub-sample.md)	

## Assista a um passo a passo em vídeo

> [AZURE.VIDEO integrate-azure-api-management-with-event-hubs]


[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png

<!---HONumber=AcomDC_0525_2016-->