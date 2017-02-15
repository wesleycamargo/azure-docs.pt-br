---
title: "Visão geral de exemplos de retransmissão do Barramento de Serviço| Microsoft Docs"
description: "Categoriza e descreve exemplos de retransmissão do Barramento de Serviço com links para cada um."
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 9237a9a2-f126-4d3f-9f9b-604ee6b32153
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0b2206fae964c55e961505b9d0e2ad71fd0b2102


---
# <a name="service-bus-relay-samples"></a>Exemplos de retransmissão do Barramento de Serviço
Os exemplos de retransmissão do Barramento de Serviço demonstram os recursos principais da [retransmissão do Barramento de Serviço](https://azure.microsoft.com/services/service-bus/). Este tópico categoriza e descreve os exemplos disponíveis, com links para cada um.

> [!NOTE]
> Os exemplos do Barramento de Serviço não são instalados com o SDK. Para obter os exemplos, visite a [página de exemplos do SDK do Azure](https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=5).
> 
> Além disso, há um conjunto atualizado de exemplos de retransmissão do Barramento de Serviço [aqui](https://github.com/Azure-Samples/azure-servicebus-relay-samples) (até o fechamento deste artigo, elas não foram descritas).  
> 
> 

Para obter exemplos do sistema de mensagens, consulte [Amostras do sistema de mensagens do Barramento de Serviço](../service-bus-messaging/service-bus-samples.md).

## <a name="service-bus-relay"></a>Retransmissão do Barramento de Serviço
Os exemplos a seguir ilustram como gravar aplicativos que usam o sistema de retransmissão do Barramento de Serviço.

Observe que os exemplos de retransmissão exigem uma cadeia de caracteres de conexão para acessar o namespace do Barramento de Serviço.

### <a name="to-obtain-a-connection-string-for-azure-service-bus"></a>Para obter uma cadeia de conexão do Barramento de Serviço do Azure
1. Faça logon no [portal do Azure](http://portal.azure.com).
2. Na coluna à esquerda, clique em **Barramento de Serviço**.
3. Clique no nome do seu namespace na lista.
4. Na folha namespace, clique em **Políticas de acesso compartilhado**.
5. Na folha **Políticas de acesso compartilhado**, clique em **RootManageSharedAccessKey**.
6. Copie a cadeia de conexão para a área de transferência.

### <a name="to-obtain-a-connection-string-for-service-bus-for-windows-server"></a>Para obter uma cadeia de conexão do Barramento de Serviço do Windows Server
1. Execute o seguinte cmdlet do PowerShell:
   
    ```
    get-sbClientConfiguration
    ```
2. Cole a cadeia de conexão no arquivo App.config do exemplo.

## <a name="service-bus-relay"></a>Retransmissão do Barramento de Serviço
Exemplos que demonstram a retransmissão do Barramento de Serviço.

### <a name="getting-started"></a>Introdução
| Nome do exemplo | Descrição | Versão mínima do SDK | Disponibilidade |
| --- | --- | --- | --- |
| [Sistema de mensagens de retransmissão do WCF: Azure](http://code.msdn.microsoft.com/Relayed-Messaging-Windows-0d2cede3) |Demonstra como executar um serviço e um cliente do Barramento de Serviço no Azure. Este exemplo configura o Barramento de Serviço programaticamente. Somente as informações sobre ambiente e segurança são armazenadas nos arquivos de configuração. |1.8 |Barramento de Serviço do Microsoft Azure |
| [Autenticação do sistema de mensagens de retransmissão do WCF: segredo compartilhado](http://code.msdn.microsoft.com/Relayed-Messaging-92b04c02) |Demonstra como usar um nome de emissor e o segredo do emissor para autenticar usando o Barramento de Serviço. |1.8 |Barramento de Serviço do Microsoft Azure |
| [Autenticação do sistema de mensagens de retransmissão do WCF: WebNoAuth](http://code.msdn.microsoft.com/Relayed-Messaging-a4f0b831) |Demonstra como expor um serviço HTTP que não exija autenticação de usuário do cliente. |1.8 |Barramento de Serviço do Microsoft Azure |
| [Associações do sistema de mensagens de retransmissão do WCF: WebHttp](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-a6477ba0) |Demonstra como usar a associação **WebHttpRelayBinding** para retornar dados binários usando o modelo de programação da Web. |1.8 |Barramento de Serviço do Microsoft Azure |
| [Associações do sistema de mensagens de retransmissão do WCF: NetTcp retransmitido](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-2dec7692) |Demonstra como usar a associação **NetTcpRelayBinding** . |1.8 |Barramento de Serviço do Microsoft Azure |

### <a name="exploring-features"></a>Explorando os recursos
Exemplos que demonstram os recursos de retransmissão do Barramento de Serviço.

| Nome do exemplo | Descrição | Versão mínima do SDK | Disponibilidade |
| --- | --- | --- | --- |
| [Autenticação do sistema de mensagens de retransmissão do WCF: WebToken simples](http://code.msdn.microsoft.com/Relayed-Messaging-32c74392) |Demonstra como usar uma credencial de token da Web simples para autenticar no Barramento de Serviço. O exemplo é semelhante ao exemplo de Eco, com algumas alterações. Especificamente, este exemplo adiciona um comportamento aos aplicativos ServiceHost (serviço) e ChannelFactory (cliente). |1.8 |Barramento de Serviço do Microsoft Azure |
| [Sistema de mensagens de retransmissão do WCF: balanceamento de carga](http://code.msdn.microsoft.com/Relayed-Messaging-Load-bd76a9f8) |Demonstra como usar o Barramento de Serviço do Microsoft Azure para rotear mensagens para vários destinatários. Mostra várias instâncias de um serviço simples comunicando-se com um cliente por meio da associação **NetTcpRelayBinding** |1.8 |Barramento de Serviço do Microsoft Azure |
| [Associações do sistema de mensagens de retransmissão do WCF: evento de rede](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-c0176977) |Demonstra como usar a associação **NetEventRelayBinding** no Barramento de Serviço do Microsoft Azure. |1.8 |Barramento de Serviço do Microsoft Azure |
| [Associações do sistema de mensagens de retransmissão do WCF: sessão WS2007Http](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ef1f1fcb) |Demonstra como usar a associação **WS2007HttpRelayBinding** com sessões confiáveis habilitadas. Também mostra como especificar credenciais do Barramento de Serviço no arquivo de configuração em vez de programaticamente. |1.8 |Barramento de Serviço do Microsoft Azure |
| [Associações do sistema de mensagens de retransmissão do WCF: WS2007Http MsgSecCertificate](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-f29c9da5) |Demonstra como usar a associação **WS2007HttpRelayBinding** com segurança de mensagem para proteger mensagens de ponta a ponta, ainda exigindo que os clientes se autentiquem no Barramento de Serviço. |1.8 |Barramento de Serviço do Microsoft Azure |
| [Sistema de mensagens de retransmissão do WCF: troca de metadados](http://code.msdn.microsoft.com/Relayed-Messaging-Metadata-f122312e) |Demonstra como expor um ponto de extremidade de metadados que usa a associação de retransmissão. Há suporte para **MetadataExchange** nas seguintes associações de retransmissão: **NetTcpRelayBinding**, **NetOnewayRelayBinding**, **BasicHttpRelayBinding** e **WS2007HttpRelayBinding**. |1.8 |Barramento de Serviço do Microsoft Azure |
| [Associações do sistema de mensagens de retransmissão do WCF: NetTcp direto](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ca039161) |Demonstra como configurar a associação **NetTcpRelayBinding** para dar suporte ao modo de conexão **Híbrido** que primeiro estabelece uma conexão retransmitida e, se possível, alterna automaticamente para uma conexão direta entre um cliente e um serviço. |1.8 |Barramento de Serviço do Microsoft Azure |
| [Associações do sistema de mensagens de retransmissão do WCF: NetTcp MsgSec UserName](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-30542392) |Demonstra como usar a associação **NetTcpRelayBinding** com a segurança de mensagem. |1.8 |Barramento de Serviço do Microsoft Azure |
| [Associações do sistema de mensagens de retransmissão do WCF: Net Oneway](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-bb5b813a) |Demonstra como expor e consumir um ponto de extremidade de serviço usando a associação **NetOnewayRelayBinding** . |1.8 |Barramento de Serviço do Microsoft Azure |
| [Associações do sistema de mensagens de retransmissão do WCF: WS2007Http simples](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-aa4b793a) |Demonstra como usar a associação **WS2007HttpRelayBinding** . Demonstra um serviço simples que não usa opções de segurança e que não exige que os clientes se autentiquem. |1.8 |Barramento de Serviço do Microsoft Azure |

## <a name="next-steps"></a>Próximas etapas
Confira os tópicos a seguir para obter visões gerais conceituais do Barramento de Serviço.

* [Visão geral da retransmissão do Barramento de Serviço](service-bus-relay-overview.md)
* [Arquitetura do Barramento de Serviço](../service-bus-messaging/service-bus-architecture.md)
* [Conceitos fundamentais do barramento de serviço](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)




<!--HONumber=Nov16_HO3-->


