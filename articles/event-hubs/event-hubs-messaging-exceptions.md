---
title: "Exceções de mensagens dos Hubs de Eventos | Microsoft Docs"
description: "Lista de exceções de mensagens dos Hubs de Eventos e ações sugeridas."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 2c6273de-0106-47e5-b45d-59040e51f2c5
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/14/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c17328ffee6191022fcfd9588b3d30b0cea23148


---
# <a name="event-hubs-messaging-exceptions"></a>Exceções de mensagens dos Hubs de Eventos
Este artigo relaciona algumas exceções geradas pelas APIs de mensagens do Barramento de Serviço do Azure. Este conjunto de API inclui Hubs de Eventos. Essa referência está sujeita a alterações, então verifique se há atualizações.

## <a name="exception-categories"></a>Categorias de exceções
As APIs de mensagens geram exceções que podem se enquadrar nas categorias a seguir, junto com a ação associada que pode ser tomada para tentar corrigi-las. Observe que o significado e as causas de uma exceção podem variar dependendo do tipo de entidade de mensagens (retransmissões, filas/tópicos, Hubs de Eventos):

1. Erro de codificação do usuário ([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Ação geral: tentar corrigir o código antes de prosseguir.
2. Erro de instalação/configuração ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingentitynotfoundexception.aspx), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Ação geral: examinar sua configuração e alterá-la, se necessário.
3. Exceções temporárias ([Microsoft.ServiceBus.Messaging.MessagingException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx), [Microsoft.ServiceBus.Messaging.ServerBusyException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.serverbusyexception.aspx), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingcommunicationexception.aspx)). Ação geral: repetir a operação ou notificar os usuários.
4. Outras exceções ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.ServiceBus.Messaging.MessageLockLostException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagelocklostexception.aspx), [Microsoft.ServiceBus.Messaging.SessionLockLostException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sessionlocklostexception.aspx)). Ação geral: específica para o tipo de exceção; consulte a tabela na seção a seguir. 

## <a name="exception-types"></a>Tipos de exceção
A tabela a seguir relaciona os tipos de mensagens de exceção e suas causas e aponta a ação sugerida que você pode tomar.

| **Tipo de exceção** | **Descrição/Causa/Exemplos** | **Ação sugerida** | **Observação sobre repetição automática/imediata** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |O servidor não respondeu à operação solicitada dentro do tempo especificado que é controlado por [OperationTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx). O servidor pode ter concluído a operação solicitada. Isso pode acontecer devido a rede ou outros atrasos de infraestrutura. |Verifique o estado do sistema para manter a consistência e tente novamente, se necessário. |Uma nova tentativa pode ajudar em alguns casos; Adicione lógica de repetição ao código. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |A operação solicitada pelo usuário não é permitida no servidor ou serviço. Consulte a mensagem de exceção para obter detalhes. Por exemplo, [Concluir](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) gerará essa exceção se a mensagem tiver sido recebida no modo **ReceiveAndDelete** . |Verifique o código e a documentação. Verifique se a operação solicitada é válida. |Tentar novamente não ajudará. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |É feita uma tentativa para invocar uma operação em um objeto que já foi fechado, anulado ou descartado. Em casos raros, a transação de ambiente já foi descartada. |Verifique o código e certifique-se de que ele não invocará operações em um objeto descartado. |Tentar novamente não ajudará. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |O [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) não pôde obter um token, o token é inválido ou não contém as declarações necessárias para executar a operação. |Verifique se o provedor de token for criado com os valores corretos. Verifique a configuração do Serviço de Controle de Acesso. |Uma nova tentativa pode ajudar em alguns casos; Adicione lógica de repetição ao código. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Um ou mais argumentos fornecidos para o método são inválidos. O URI fornecido para [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) ou [Create](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.create.aspx) contém segmento(s) de caminho. O esquema de URI fornecido para [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) ou [Criar](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.create.aspx) é inválido. O valor da propriedade é maior do que 32 KB. |Verifique o código de chamada e certifique-se de que os argumentos estão corretos. |Tentar novamente não ajudará. |
| [MessagingEntityNotFoundException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingentitynotfoundexception.aspx) |A entidade associada à operação não existe ou foi excluída. |Certifique-se de que a entidade existe. |Tentar novamente não ajudará. |
| [MessageNotFoundException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagenotfoundexception.aspx) |Tentativa para receber uma mensagem com um número de sequência específico. Esta mensagem não foi encontrada. |Certifique-se de que a mensagem ainda não foi recebida. Verifique a fila de mensagens mortas para ver se a mensagem foi colocada ali. |Tentar novamente não ajudará. |
| [MessagingCommunicationException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingcommunicationexception.aspx) |O cliente não pode estabelecer uma conexão com o Hub de Eventos. |Verifique se o nome de host fornecido está correto e se o host está acessível. |Tentar novamente poderá ajudar se houver problemas intermitentes de conectividade. |
| [ServerBusyException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.serverbusyexception.aspx) |O serviço não pode processar a solicitação neste momento. |O cliente pode esperar um período de tempo e repetir a operação. |O cliente pode tentar novamente após um intervalo determinado. Se uma nova tentativa resultar em uma exceção diferente, verifique o comportamento de repetição de tal exceção. |
| [MessageLockLostException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagelocklostexception.aspx) |O lock token associado à mensagem expirou ou não foi encontrado. |Descartar a mensagem. |Tentar novamente não ajudará. |
| [SessionLockLostException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sessionlocklostexception.aspx) |O bloqueio associado a esta sessão foi perdido. |Anular o objeto [MessageSession](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.aspx) . |Tentar novamente não ajudará. |
| [MessagingException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx) |A exceção de mensagens genéricas pode ser lançada nos seguintes casos: uma tentativa é feita para criar um [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) usando um nome ou caminho que pertence a um tipo de entidade diferente (por exemplo, um tópico). Uma tentativa de enviar uma mensagem maior que 256 KB foi feita. O servidor ou o serviço encontrou um erro durante o processamento da solicitação. Consulte a mensagem de exceção para obter detalhes. Isso geralmente é uma exceção temporária. |Verifique o código e certifique-se de que somente objetos serializáveis sejam usados para o corpo da mensagem (ou use um serializador personalizado). Verifique a documentação para os tipos de valor com suporte das propriedades e somente os tipos de uso com suporte. Verifique a propriedade [IsTransient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.istransient.aspx) . Se for **true**, você pode tentar a operação novamente. |O comportamento de repetição é indefinido e pode não ajudar. |
| [MessagingEntityAlreadyExistsException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingentityalreadyexistsexception.aspx) |Tentativa de criar uma entidade com um nome que já está sendo usado por outra entidade no namespace de serviço. |Exclua a entidade existente ou escolha um nome diferente para a entidade a ser criada. |Tentar novamente não ajudará. |
| [QuotaExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx) |A entidade de mensagens atingiu seu tamanho máximo permitido. Isso pode acontecer se o número máximo de destinatários (que é 5) já foi aberto em um nível de grupo por consumidor. |Crie espaço na entidade ao receber mensagens da entidade ou de suas subfilas. |Tentar novamente pode ajudar se as mensagens foram removidas nesse meio tempo. |
|  | | | |
| [SessionCannotBeLockedException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sessioncannotbelockedexception.aspx) |Tentativa de aceitar uma sessão com uma ID de sessão específica, mas a sessão está atualmente bloqueada por outro cliente. |Verifique se a sessão foi desbloqueada por outros clientes. |Tentar novamente pode ajudar se a sessão foi liberada nesse meio tempo. |
| [TransactionSizeExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transactionsizeexceededexception_methods.aspx) |Muitas operações fazem parte da transação. |Reduza o número de operações que fazem parte dessa transação. |Tentar novamente não ajudará. |
| [MessagingEntityDisabledException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingentitydisabledexception.aspx) |Solicitação para uma operação de tempo de execução em uma entidade desabilitada. |Ativar a entidade. |Tentar novamente poderá ajudar se a entidade tiver sido ativada durante o processo. |
| [MessageSizeExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesizeexceededexception.aspx) |Uma carga de mensagem excede o limite de 256K. Observe que o limite de 256k é o tamanho total de mensagens, que pode incluir propriedades do sistema e qualquer sobrecarga do .NET. |Reduza o tamanho da carga de mensagem e repita a operação. |Tentar novamente não ajudará. |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |A transação de ambiente (*Transaction.Current*) é inválida. Ela pode ter sido concluída ou anulada. A exceção interna pode fornecer informações adicionais. | |Tentar novamente não ajudará. |
| [TransactionInDoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |Uma operação é tentada em uma transação incerta, ou uma tentativa é feita para confirmar a transação e ela se torna incerta. |Seu aplicativo deve tratar essa exceção (como um caso especial), visto que a transação pode já ter sido confirmada. |- |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx) indica que uma cota para uma entidade específica foi excedida.

Isso pode acontecer se o número máximo de destinatários (5) já foi aberto em um nível de grupo por consumidor.

### <a name="event-hubs"></a>Hubs de Eventos
Os Hubs de Eventos têm um limite de 20 grupos de consumidores por Hub de Eventos. Quando você tenta criar mais, recebe [QuotaExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx). 

## <a name="timeoutexception"></a>TimeoutException
Um [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) indica que uma operação iniciada pelo usuário está demorando mais do que o tempo limite da operação. 

### <a name="event-hubs"></a>Hubs de Eventos
Para os Hubs de Eventos, o tempo limite é especificado como parte da cadeia de conexão ou por meio de [ServiceBusConnectionStringBuilder](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.aspx). A mensagem de erro pode variar, mas ele sempre contém o valor de tempo limite especificado para a operação atual. 

### <a name="common-causes"></a>Causas comuns
Há duas causas comuns desse erro: configuração incorreta ou um erro de serviço transitório.

1. **Configuração incorreta**
    O tempo limite da operação pode ser muito pequeno para a condição operacional. O valor padrão para o tempo limite da operação no SDK do cliente é de 60 segundos. Verifique para ver se o código tem o valor definido para algo muito pequeno. Observe que a condição da rede e do uso da CPU pode afetar o tempo necessário para uma determinada operação ser concluída, portanto o tempo limite da operação não deve ser definido com um valor muito pequeno.
2. **Erro de serviço temporário**
    Às vezes, o serviço de Hubs de Eventos pode sofrer atrasos nas solicitações de processamento; por exemplo, durante os períodos de tráfego intenso. Nesses casos, você pode repetir a operação após um atraso, até que a operação seja bem-sucedida. Se a mesma operação continuar falhando após várias tentativas, visite o [site de status do serviço do Azure](https://azure.microsoft.com/status/) para ver se há qualquer interrupção de serviço conhecida.

## <a name="next-steps"></a>Próximas etapas
Para obter a referência completa de Barramento de Serviço e a API do .NET de Hubs de Eventos, consulte a [Referência do Azure sobre o MSDN](https://msdn.microsoft.com/library/azure/mt419900.aspx).




<!--HONumber=Nov16_HO3-->


