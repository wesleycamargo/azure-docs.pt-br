---
title: "Visão geral de exemplos do sistema de mensagens do Barramento de Serviço | Microsoft Docs"
description: "Categoriza e descreve exemplos de mensagens do Barramento de Serviço com links para cada um."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 0b420343-2d2a-4c65-98f1-ee0e39ef55c8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 719403ca2ef90cdcf5f76fe15f7a33095e71a74c


---
# <a name="service-bus-messaging-samples"></a>Exemplos de mensagens do Barramento de Serviço
Os exemplos de mensagens do Barramento de Serviço demonstram os principais recursos de [mensagens do Barramento de Serviço](https://azure.microsoft.com/services/service-bus/) (serviço de nuvem) e do [Barramento de Serviço para o Windows Server](https://msdn.microsoft.com/library/dn282144.aspx). Este tópico categoriza e descreve os exemplos disponíveis, com links para cada um.

> [!NOTE]
> Os exemplos do Barramento de Serviço não são instalados com o SDK. Para obter os exemplos, visite a [página de exemplos do SDK do Azure](https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=5).
> 
> Além disso, há um conjunto atualizado de amostras de mensagens do Barramento de Serviço [aqui](https://github.com/Azure-Samples/azure-servicebus-messaging-samples) (até o fechamento deste artigo, elas não são descritas).  
> 
> 

Para obter exemplos de retransmissão, consulte [Service Bus relay samples (Exemplos de Retransmissão do Barramento de Serviço)](../service-bus-relay/service-bus-relay-samples.md).

## <a name="service-bus-messaging"></a>Mensagens do Barramento de Serviço
Os exemplos a seguir ilustram como gravar aplicativos que usam o sistema de mensagens do Barramento de Serviço.

Observe que os exemplos do sistema de mensagens exigem uma cadeia de caracteres de conexão para acessar o namespace do Barramento de Serviço.

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

### <a name="getting-started-samples"></a>Exemplos de introdução
Estes exemplos descrevem a funcionalidade básica do sistema de mensagens.

| Nome do exemplo | Descrição | Versão mínima do SDK | Disponibilidade |
| --- | --- | --- | --- |
| [Introdução: Sistema de mensagens com filas](http://code.msdn.microsoft.com/Getting-Started-Brokered-aa7a0ac3) |Demonstra como usar o Barramento de Serviço do Microsoft Azure para enviar e receber mensagens de uma fila. |1.8 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Introdução: Sistema de mensagens com tópicos](http://code.msdn.microsoft.com/Getting-Started-Brokered-614d42e5) |Demonstra como usar o Barramento de Serviço do Microsoft Azure para enviar e receber mensagens de um tópico com várias assinaturas. |1.8 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |

### <a name="exploring-features"></a>Explorando os recursos
Os exemplos a seguir demonstram diversos recursos do Barramento de Serviço.

| Nome do exemplo | Descrição | Versão mínima do SDK | Disponibilidade |
| --- | --- | --- | --- |
| [Provedores de token HTTP](http://code.msdn.microsoft.com/Service-Bus-HTTP-Token-38f2cfc5) |Demonstra as maneiras diferentes de autenticar um cliente HTTP/REST com o Barramento de Serviço. |2,1 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Cliente HTTP do Barramento de Serviço](http://code.msdn.microsoft.com/Service-Bus-HTTP-client-fe7da74a) |Demonstra como enviar e receber mensagens do Barramento de Serviço via HTTP/REST. |2.3 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Encaminhamento automático do Barramento de Serviço](http://code.msdn.microsoft.com/Service-Bus-Autoforwarding-b9df470b) |Demonstra como encaminhar automaticamente as mensagens de uma fila, de uma assinatura ou de uma fila de mensagens mortas para outra fila ou tópico. Isso também demonstra como enviar uma mensagem para uma fila ou tópico por meio de uma fila de transferência. |2.3 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Sistema de Mensagens Agenciado: exemplo de sessão de canal WCF](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-0a526451) |Demonstra como usar o Barramento de Serviço do Microsoft Azure usando canais do Windows Communication Foundation (WCF). O exemplo mostra o uso de canais do WCF para enviar e receber mensagens por meio de uma fila do Barramento de Serviço. O exemplo mostra a comunicação de sessão e sem sessão no Barramento de Serviço. |1.8 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Sistema de Mensagens Agenciado: Transações](http://code.msdn.microsoft.com/Brokered-Messaging-8cd41d1e) |Demonstra como usar os recursos do sistema de mensagens do Barramento de Serviço do Microsoft Azure em um escopo de transação para garantir que lotes de operações do sistema de mensagens sejam atomicamente confirmados. |1.8 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Sistema de Mensagens Agenciado: Operações de gerenciamento usando REST](http://code.msdn.microsoft.com/Brokered-Messaging-569cff88) |Demonstra como realizar operações de gerenciamento no Barramento de Serviço usando REST. |1.8 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [APIs REST do Provedor de Recursos](http://code.msdn.microsoft.com/Service-Bus-Resource-5d887203) |Demonstra como usar as novas APIs REST do RDFE do Barramento de Serviço para gerenciar namespaces e entidades do sistema de mensagens. |1.8 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Sistema de Mensagens Agenciado: Exemplo de sessão do serviço WCF](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-db4262c2) |Demonstra como usar o Barramento de Serviço do Microsoft Azure usando o modelo de serviço WCF. O exemplo mostra o uso do modelo de serviço WCF para realizar a comunicação baseada em sessão por meio de uma fila do Barramento de Serviço. |1.8 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Sistema de Mensagens Agenciado: Resposta de solicitação](http://code.msdn.microsoft.com/Brokered-Messaging-Request-2b4ff5d8) |Demonstra como usar o Barramento de Serviço do Microsoft Azure e a funcionalidade de solicitação/resposta. O exemplo mostra clientes e servidores simples se comunicando por meio de uma fila do Barramento de Serviço. |1.8 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Sistema de Mensagens Agenciado: Fila de mensagens mortas](http://code.msdn.microsoft.com/Brokered-Messaging-Dead-22536dd8) |Demonstra como usar o Barramento de Serviço do Microsoft Azure e a funcionalidade “fila de mensagens mortas” do sistema de mensagens. O exemplo mostra uma comunicação simples entre remetente e destinatário por meio de uma fila do Barramento de Serviço. |1.8 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Sistema de Mensagens Agenciado: Mensagens adiadas](http://code.msdn.microsoft.com/Brokered-Messaging-ccc4f879) |Demonstra como usar o recurso de adiamento de mensagens do Barramento de Serviço do Microsoft Azure. O exemplo mostra uma comunicação simples entre remetente e destinatário por meio de uma fila do Barramento de Serviço. |1.8 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Sistema de Mensagens Agenciado: Mensagens de sessão](http://code.msdn.microsoft.com/Brokered-Messaging-Session-41c43fb4) |Demonstra como usar o Barramento de Serviço do Microsoft Azure e a funcionalidade Sessão do Sistema de Mensagens. O exemplo mostra uma comunicação simples entre remetentes e destinatários por meio de uma fila do Barramento de Serviço. |1.8 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Sistema de Mensagens Agenciado: Tópico de resposta de solicitação](http://code.msdn.microsoft.com/Brokered-Messaging-Request-6759a36e) |Demonstra como implementar o padrão de solicitação/resposta usando tópicos e assinaturas do Barramento de Serviço do Microsoft Azure. O exemplo mostra clientes e servidores simples se comunicando por meio de um tópico do Barramento de Serviço. |1.8 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Sistema de Mensagens Agenciado: Fila de resposta de solicitação](http://code.msdn.microsoft.com/Brokered-Messaging-Request-0ce8fcaf) |Demonstra como usar o Barramento de Serviço do Microsoft Azure e a funcionalidade de solicitação/resposta. O exemplo mostra clientes e servidores simples se comunicando por meio de duas filas do Barramento de Serviço. |1.8 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Sistema de Mensagens Agenciado: Detecção de duplicidades](http://code.msdn.microsoft.com/Brokered-Messaging-c0acea25) |Demonstra como usar a detecção de mensagens duplicadas do Barramento de Serviço do Microsoft Azure com filas. Ele cria duas filas, uma com a detecção de duplicidades habilitada e outra sem a detecção de duplicidades. |1.8 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Sistema de Mensagens Agenciado: Sistema de mensagens assíncrono](http://code.msdn.microsoft.com/Brokered-Messaging-Async-211c1e74) |Demonstra como usar o Barramento de Serviço do Microsoft Azure para enviar e receber mensagens assincronamente de uma fila. A fila fornece comunicação separada e assíncrona entre um remetente e qualquer número de destinatários (aqui, um único destinatário). |1.8 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Sistema de Mensagens Agenciado: Filtros Avançados](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749) |Demonstra como usar os filtros avançados de publicação/assinatura do Barramento de Serviço do Microsoft Azure. Ele cria um tópico e 3 assinaturas com diferentes definições de filtro, envia mensagens para o tópico e recebe todas as mensagens de assinaturas. |1.8 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Sistema de Mensagens Agenciado: Pré-busca de mensagens](http://code.msdn.microsoft.com/Brokered-Messaging-be2dac1d) |Demonstra como usar o recurso de pré-busca de mensagens do Barramento de Serviço do Microsoft Azure. Demonstra como usar o recurso de pré-busca de mensagens após o recebimento. |1.8 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |

## <a name="service-bus-reference-tools"></a>Ferramentas de referência do Barramento de Serviço
Os exemplos a seguir demonstram vários outros recursos do serviço.

| Nome do exemplo | Descrição | Versão mínima do SDK | Disponibilidade |
| --- | --- | --- | --- |
| [Service Bus Explorer](http://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a) |O Gerenciador do Barramento de Serviço permite que usuários se conectem a um namespace de serviço do Barramento de Serviço e gerenciem entidades do sistema de mensagens de uma maneira fácil. A ferramenta fornece recursos avançados, como a funcionalidade de importação/exportação, e a capacidade de testar entidades do sistema de mensagens e os serviços de retransmissão. |1.8 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Autorização: SBAzTool](http://code.msdn.microsoft.com/Authorization-SBAzTool-6fd76d93) |Este exemplo demonstra como criar e gerenciar identidades de serviço no Controle de Acesso do Active Directory do Microsoft Azure (também conhecido como Serviço de Controle de Acesso ou ACS) a ser usado com o Barramento de Serviço. |N/D |Barramento de Serviço do Microsoft Azure |

## <a name="next-steps"></a>Próximas etapas
Confira os tópicos a seguir para obter visões gerais conceituais do Barramento de Serviço.

* [Visão geral de mensagens do Barramento de Serviço](service-bus-messaging-overview.md)
* [Arquitetura do Barramento de Serviço](service-bus-architecture.md)
* [Conceitos fundamentais do barramento de serviço](service-bus-fundamentals-hybrid-solutions.md)




<!--HONumber=Nov16_HO3-->


