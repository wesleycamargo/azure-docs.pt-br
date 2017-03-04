---
title: "Exceções de Retransmissão do Microsoft Azure e como corrigi-las | Microsoft Docs"
description: "Lista de exceções de Retransmissão do Azure e ações recomendadas."
services: service-bus-relay
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 5f9dd02c-cce0-43b3-8eb8-744f0c27f38c
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: sethm;jotaub
translationtype: Human Translation
ms.sourcegitcommit: bc71995deef3f9795ebd33d9642c7ffa1995021b
ms.openlocfilehash: 346851226dce8ca8e48d3ae291b97e6e43d1ae03
ms.lasthandoff: 02/02/2017


---
# <a name="relay-exceptions"></a>Exceções de retransmissão

Este artigo lista algumas exceções geradas pelas APIs de retransmissão do Microsoft Azure. Essa referência está sujeita a alterações, então verifique se há atualizações.

## <a name="exception-categories"></a>Categorias de exceções

As APIs de retransmissão geram exceções que podem se enquadrar nas categorias a seguir, junto com a ação associada que pode ser tomada para tentar corrigi-las.

1. Erro de codificação do usuário ([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Ação geral: tentar corrigir o código antes de prosseguir.
2. Erro de instalação/configuração ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Ação geral: examinar sua configuração e alterá-la, se necessário.
3. Exceções temporárias ([Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception.aspx)). Ação geral: repetir a operação ou notificar os usuários.
4. Outras exceções ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception)). Ação geral: específica para o tipo de exceção; consulte a tabela na seção a seguir. 

## <a name="exception-types"></a>Tipos de exceção

A tabela a seguir relaciona os tipos de mensagens de exceção e suas causas e aponta a ação sugerida que você pode tomar.

| **Tipo de exceção** | **Descrição** | **Ação sugerida** | **Observação sobre repetição automática ou imediata** |
| --- | --- | --- | --- |
| [Tempo limite](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |O servidor não respondeu à operação solicitada dentro do tempo especificado que é controlado pela [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout). O servidor pode ter concluído a operação solicitada. Isso pode acontecer devido a rede ou outros atrasos de infraestrutura. |Verifique o estado do sistema para manter a consistência e tente novamente, se necessário. Confira [TimeoutException](#timeoutexception). |Uma nova tentativa pode ajudar em alguns casos; Adicione lógica de repetição ao código. |
| [Operação inválida](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |A operação solicitada pelo usuário não é permitida no servidor ou serviço. Consulte a mensagem de exceção para obter detalhes. Por exemplo, [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete) vai gerar essa exceção se a mensagem foi recebida no modo **ReceiveAndDelete** . |Verifique o código e a documentação. Verifique se a operação solicitada é válida. |Tentar novamente não ajudará. |
| [Operação cancelada](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |É feita uma tentativa para invocar uma operação em um objeto que já foi fechado, anulado ou descartado. Em casos raros, a transação de ambiente já foi descartada. |Verifique o código e certifique-se de que ele não invocará operações em um objeto descartado. |Tentar novamente não ajudará. |
| [Acesso não autorizado](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |O [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) não pôde obter um token, o token é inválido ou não contém as declarações necessárias para executar a operação. |Verifique se o provedor de token for criado com os valores corretos. Verifique a configuração do Serviço de Controle de Acesso. |Uma nova tentativa pode ajudar em alguns casos; Adicione lógica de repetição ao código. |
| [Exceção de argumento](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [Argumento nulo](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[Argumento fora do intervalo](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Um ou mais argumentos fornecidos para o método são inválidos.<br /> O URI fornecido para [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) contém segmento(s) de caminho.<br /> O esquema de URI fornecido para [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [Criar](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) é inválido. <br />O valor da propriedade é maior do que 32 KB. |Verifique o código de chamada e certifique-se de que os argumentos estão corretos. |Tentar novamente não ajudará. |
| [Servidor ocupado](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |O serviço não pode processar a solicitação neste momento. |O cliente pode esperar um período de tempo e repetir a operação. |O cliente pode tentar novamente após um intervalo determinado. Se uma nova tentativa resultar em uma exceção diferente, verifique o comportamento de repetição de tal exceção. |
| [Cota excedida](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |A entidade de mensagens atingiu seu tamanho máximo permitido. |Crie espaço na entidade ao receber mensagens da entidade ou de suas subfilas. Confira [QuotaExceededException](#quotaexceededexception). |Tentar novamente pode ajudar se as mensagens foram removidas nesse meio tempo. |
| [Tamanho da mensagem excedido](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Uma carga de mensagem excede o limite de 256K. Observe que o limite de 256k é o tamanho total de mensagens, que pode incluir propriedades do sistema e qualquer sobrecarga do .NET. |Reduza o tamanho da carga de mensagem e repita a operação. |Tentar novamente não ajudará. |

## <a name="quotaexceededexception"></a>QuotaExceededException

[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) indica que uma cota para uma entidade específica foi excedida.

Para a Retransmissão, essa exceção envolve [System.ServiceModel.QuotaExceededException](https://msdn.microsoft.com/library/system.servicemodel.quotaexceededexception.aspx), indicando que foi excedido o número máximo de ouvintes para esse ponto de extremidade. Isso é indicado no valor **MaximumListenersPerEndpoint** da mensagem de exceção.

## <a name="timeoutexception"></a>TimeoutException
Um [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) indica que uma operação iniciada pelo usuário está demorando mais do que o tempo limite da operação. 

Você deve verificar o valor da propriedade [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit), pois atingir esse limite também pode causar um [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

Para a Retransmissão, você poderá receber exceções de tempo limite ao abrir primeiro uma conexão de remetente de retransmissão. Há duas causas comuns para essa exceção:

1. O valor [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) pode ser muito pequeno (mesmo uma fração de segundo).
2. Os ouvintes de retransmissão local podem não responder (ou talvez tenham problemas de regras de firewall, proibindo os ouvintes de aceitar novas conexões de cliente) e o valor [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) é menor que cerca de 20 segundos.

Por exemplo:

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10. The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>Causas comuns
Há duas causas comuns desse erro: configuração incorreta ou um erro de serviço transitório.

1. **Configuração incorreta**
    O tempo limite da operação pode ser muito pequeno para a condição operacional. O valor padrão para o tempo limite da operação no SDK do cliente é de 60 segundos. Verifique para ver se o código tem o valor definido para algo muito pequeno. Observe que a condição da rede e do uso da CPU pode afetar o tempo necessário para uma determinada operação ser concluída, portanto o tempo limite da operação não deve ser definido com um valor muito pequeno.
2. **Erro de serviço temporário**
    Às vezes, a Retransmissão pode sofrer atrasos no processamento de solicitações; por exemplo, durante os períodos de tráfego intenso. Nesses casos, você pode repetir a operação após um atraso, até que a operação seja bem-sucedida. Se a mesma operação continuar falhando após várias tentativas, visite o [site de status do serviço do Azure](https://azure.microsoft.com/status/) para ver se há qualquer interrupção de serviço conhecida.

## <a name="next-steps"></a>Próximas etapas:
* [Perguntas frequentes sobre retransmissão](relay-faq.md)
* [Criar um namespace](relay-create-namespace-portal.md)
* [Introdução ao .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introdução ao Node](relay-hybrid-connections-node-get-started.md)


