---
title: Exceções de mensagens – Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo fornece uma lista de exceções de mensagens dos Hubs de Eventos do Azure e ações sugeridas.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: a6ebfc86a2489910d23faa96550f34cc979c0435
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56875270"
---
# <a name="event-hubs-messaging-exceptions"></a>Exceções de mensagens dos Hubs de Eventos

Este artigo lista algumas das exceções geradas pela biblioteca de APIs das mensagens do Barramento de Serviço do Azure, que inclui APIs de Hubs de Eventos do .NET Framework. Essa referência está sujeita a alterações, então verifique se há atualizações.

## <a name="exception-categories"></a>Categorias de exceções

As APIs dos Hubs de Eventos geram exceções que podem se enquadrar nas categorias a seguir, junto com a ação associada que pode ser tomada para tentar corrigi-las.

1. Erro de codificação do usuário: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). Ação geral: tentar corrigir o código antes de prosseguir.
2. Erro de instalação/configuração: [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Ação geral: examinar sua configuração e alterá-la, se necessário.
3. Exceções temporárias: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](#serverbusyexception), [Microsoft.Azure.EventHubs.ServerBusyException](#serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). Ação geral: repetir a operação ou notificar os usuários.
4. Outras exceções: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](#timeoutexception), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception). Ação geral: específica ao tipo de exceção; consulte a tabela na seção a seguir. 

## <a name="exception-types"></a>Tipos de exceção
A tabela a seguir relaciona os tipos de mensagens de exceção e suas causas e aponta a ação sugerida que você pode tomar.

| Tipo de exceção | Descrição/Causa/Exemplos | Ação sugerida | Observação sobre repetição automática/imediata |
| -------------- | -------------------------- | ---------------- | --------------------------------- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |O servidor não respondeu à operação solicitada dentro do tempo especificado que é controlado por [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). O servidor pode ter concluído a operação solicitada. Essa exceção pode acontecer devido a atrasos de rede ou outros atrasos de infraestrutura. |Verifique o estado do sistema para manter a consistência e tente novamente, se necessário.<br /> Veja [TimeoutException](#timeoutexception). | Uma nova tentativa pode ajudar em alguns casos; Adicione lógica de repetição ao código. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |A operação solicitada pelo usuário não é permitida no servidor ou serviço. Consulte a mensagem de exceção para obter detalhes. Por exemplo, [Concluir](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) gerará essa exceção se a mensagem tiver sido recebida no modo [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) . | Verifique o código e a documentação. Verifique se a operação solicitada é válida. | Tentar novamente não ajudará. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) | É feita uma tentativa de invocar uma operação em um objeto que já foi fechado, anulado ou descartado. Em casos raros, a transação de ambiente já foi descartada. | Verifique o código e certifique-se de que ele não invocará operações em um objeto descartado. | Tentar novamente não ajudará. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) | O [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) não pôde obter um token, o token é inválido ou não contém as declarações necessárias para executar a operação. | Verifique se o provedor de token for criado com os valores corretos. Verifique a configuração do Serviço de Controle de Acesso. | Uma nova tentativa pode ajudar em alguns casos; Adicione lógica de repetição ao código. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) | Um ou mais argumentos fornecidos para o método são inválidos. O URI fornecido para [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) contém segmento(s) de caminho. O esquema de URI fornecido para [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [Criar](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) é inválido. O valor da propriedade é maior do que 32 KB. | Verifique o código de chamada e certifique-se de que os argumentos estão corretos. | Tentar novamente não ajudará. |
| [Microsoft.ServiceBus.Messaging MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /><br/> [Microsoft.Azure.EventHubs MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) | A entidade associada à operação não existe ou foi excluída. | Certifique-se de que a entidade existe. | Tentar novamente não ajudará. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) | O cliente não pode estabelecer uma conexão com o Hub de Eventos. |Verifique se o nome de host fornecido está correto e se o host está acessível. | Tentar novamente poderá ajudar se houver problemas intermitentes de conectividade. |
| [Microsoft.ServiceBus.Messaging ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> <br/>[Microsoft.Azure.EventHubs ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) | O serviço não pode processar a solicitação neste momento. | O cliente pode esperar um período de tempo e repetir a operação. <br /> Veja [ServerBusyException](#serverbusyexception). | O cliente pode tentar novamente após um intervalo determinado. Se uma nova tentativa resultar em uma exceção diferente, verifique o comportamento de repetição de tal exceção. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) | A exceção de mensagens genéricas pode ser lançada nos seguintes casos: Uma tentativa é feita para criar um [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) usando um nome ou caminho que pertence a um tipo de entidade diferente (por exemplo, um tópico). A tentativa é feita para enviar uma mensagem maior que 1 MB. O servidor ou o serviço encontrou um erro durante o processamento da solicitação. Consulte a mensagem de exceção para obter detalhes. Essa exceção geralmente é uma exceção transitória. | Verifique o código e certifique-se de que somente objetos serializáveis sejam usados para o corpo da mensagem (ou use um serializador personalizado). Verifique a documentação para os tipos de valor com suporte das propriedades e somente os tipos de uso com suporte. Verifique a propriedade [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) . Se for **true**, você pode tentar a operação novamente. | O comportamento de repetição é indefinido e pode não ajudar. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) | Tentativa de criar uma entidade com um nome que já está sendo usado por outra entidade no namespace de serviço. | Exclua a entidade existente ou escolha um nome diferente para a entidade a ser criada. | Tentar novamente não ajudará. |
| [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) | A entidade de mensagens atingiu seu tamanho máximo permitido. Essa exceção pode acontecer se o número máximo de receptores (que é 5) já tiver sido aberto em um nível de grupo por consumidor. | Crie espaço na entidade ao receber mensagens da entidade ou de suas subfilas. <br /> See [QuotaExceededException](#quotaexceededexception) | Tentar novamente pode ajudar se as mensagens foram removidas nesse meio tempo. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) | Solicitação para uma operação de tempo de execução em uma entidade desabilitada. |Ativar a entidade. | Tentar novamente poderá ajudar se a entidade tiver sido ativada durante o processo. |
| [Microsoft.ServiceBus.Messaging MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /><br/> [Microsoft.Azure.EventHubs MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | Uma carga de mensagem excede o limite de 1 MB. Esse limite de 1 MB é para a mensagem total, que pode incluir propriedades do sistema e qualquer sobrecarga do .NET. | Reduza o tamanho da carga de mensagem e repita a operação. |Tentar novamente não ajudará. |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) indica que uma cota para uma entidade específica foi excedida.

Essa exceção pode acontecer se o número máximo de receptores (5) já tiver sido aberto em um nível de grupo por consumidor.

### <a name="event-hubs"></a>Hubs de Eventos
Os Hubs de Eventos têm um limite de 20 grupos de consumidores por Hub de Eventos. Quando você tenta criar mais, recebe [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

## <a name="timeoutexception"></a>TimeoutException
Um [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) indica que uma operação iniciada pelo usuário está demorando mais do que o tempo limite da operação. 

Para os Hubs de Eventos, o tempo limite é especificado como parte da cadeia de conexão ou por meio de [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). A mensagem de erro pode variar, mas ele sempre contém o valor de tempo limite especificado para a operação atual. 

### <a name="common-causes"></a>Causas comuns
Há duas causas comuns desse erro: configuração incorreta ou um erro de serviço transitório.

1. **Configuração incorreta** O tempo limite da operação pode ser muito pequeno para a condição operacional. O valor padrão para o tempo limite da operação no SDK do cliente é de 60 segundos. Verifique para ver se o código tem o valor definido para algo muito pequeno. Observe que a condição da rede e do uso da CPU pode afetar o tempo necessário para uma determinada operação ser concluída, portanto o tempo limite da operação não deve ser definido com um valor pequeno.
2. **Erro de serviço transitória** Às vezes, o serviço de Hubs de Eventos pode sofrer atrasos nas solicitações de processamento; por exemplo, durante os períodos de tráfego intenso. Nesses casos, você pode repetir a operação após um atraso, até que a operação seja bem-sucedida. Se a mesma operação continuar falhando após várias tentativas, visite o [site de status do serviço do Azure](https://azure.microsoft.com/status/) para ver se há qualquer interrupção de serviço conhecida.

## <a name="serverbusyexception"></a>ServerBusyException

A [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) ou [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) indica que um servidor está sobrecarregado. Há dois códigos de erro relevantes para essa exceção.

### <a name="error-code-50002"></a>Código do erro 50002

Esse erro pode ocorrer por um dos seguintes motivos:

1. A carga não é distribuída igualmente entre todas as partições no Hub de Eventos e uma partição atinge a limitação de unidade de produtividade local.
    
    Resolução: Revisar a estratégia de distribuição de partição ou tentando [EventHubClient.Send(eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) pode ajudar.

2. O namespace dos Hubs de Eventos não tem unidades de produtividade suficientes (você pode verificar a tela **Métricas** na janela Namespace de Hubs de Eventos no [portal do Azure](https://portal.azure.com) para confirmar). O portal mostra informações agregadas (1 minuto), mas medimos a taxa de transferência em tempo real – portanto, é apenas uma estimativa.

    Resolução: Aumentar as unidades de produtividade no namespace pode ajudar. É possível fazer essa operação no portal, na janela **Dimensionar** da tela do namespace dos Hubs de Eventos. Ou você pode usar [Inflar automaticamente](event-hubs-auto-inflate.md).

### <a name="error-code-50001"></a>Código do erro 50001

Esse erro deve ocorrer raramente. Isso acontece quando o contêiner executando o código para seu namespace é insuficiente na CPU – não mais do que alguns segundos, antes de o balanceador de carga dos Hubs de Eventos ser iniciado.


## <a name="next-steps"></a>Próximas etapas

Você pode saber mais sobre Hubs de Eventos visitando os links abaixo:

* [Visão geral de Hubs de Eventos](event-hubs-what-is-event-hubs.md)
* [Criar um Hub de Eventos](event-hubs-create.md)
* [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)
