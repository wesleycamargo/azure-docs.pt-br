---
title: Visão geral de exemplos do Barramento de Serviço | Microsoft Docs
description: Categoriza e descreve exemplos do Barramento de Serviço com links para cada um.
services: service-bus
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''

ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2016
ms.author: sethm

---
# Exemplos do Barramento de Serviço
Os exemplos do Barramento de Serviço demonstram os principais recursos do [Barramento de Serviço](https://azure.microsoft.com/services/service-bus/) (serviço de nuvem) e do [Barramento de Serviço para o Windows Server](https://msdn.microsoft.com/library/dn282144.aspx). Este tópico categoriza e descreve os exemplos disponíveis, com links para cada um.

> [!NOTE]
> Os exemplos do Barramento de Serviço não são instalados com o SDK. Para obter os exemplos, visite a [página de exemplos do SDK do Azure](https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=5).
> 
> Além disso, há um conjunto atualizado de amostras de mensagens do Barramento de Serviço [aqui](https://github.com/Azure-Samples/azure-servicebus-messaging-samples) (até o fechamento deste artigo, elas não são descritas). Amostras de retransmissão podem ser encontradas [aqui](https://github.com/Azure-Samples/azure-servicebus-relay-samples).
> 
> 

## Sistema de mensagens agenciado do Barramento de Serviço
Os exemplos a seguir ilustram como gravar aplicativos que usam o Barramento de Serviço.

Observe que os exemplos do sistema de mensagens agenciado exigem uma cadeia de conexão para acessar o namespace do Barramento de Serviço.

### Para obter uma cadeia de conexão do Barramento de Serviço do Azure
1. Faça logon no [portal clássico do Azure](http://manage.windowsazure.com).
2. Na coluna à esquerda, clique em **Barramento de Serviço**.
3. Clique no nome do seu namespace de serviço na lista.
4. Clique em **Informações de Conexão**. Na caixa de diálogo **Informações de Conexão de Acesso**, copie a cadeia de conexão para a área de transferência.
5. Cole a cadeia de conexão no arquivo App.config do exemplo.

### Para obter uma cadeia de conexão do Barramento de Serviço do Windows Server
1. Execute o seguinte cmdlet do PowerShell:
   
    ```
    get-sbClientConfiguration
    ```
2. Cole a cadeia de conexão no arquivo App.config do exemplo.

### Exemplos de introdução
Estes exemplos descrevem a funcionalidade básica da retransmissão e do sistema de mensagens.

| Nome do exemplo | Descrição | Versão mínima do SDK | Disponibilidade |
| --- | --- | --- | --- |
| [Introdução: Sistema de mensagens com filas](http://code.msdn.microsoft.com/Getting-Started-Brokered-aa7a0ac3) |Demonstra como usar o Barramento de Serviço do Microsoft Azure para enviar e receber mensagens de uma fila. |1\.8 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Introdução: Sistema de mensagens com tópicos](http://code.msdn.microsoft.com/Getting-Started-Brokered-614d42e5) |Demonstra como usar o Barramento de Serviço do Microsoft Azure para enviar e receber mensagens de um tópico com várias assinaturas. |1\.8 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Introdução aos Hubs de Eventos](http://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097) |Demonstra os recursos básicos dos Hubs de Eventos, como a criação de um Hub de Eventos, o envio de eventos para um Hub de Eventos, o consumo de eventos usando o Processador de Eventos. |2\.4 |Barramento de Serviço do Microsoft Azure |

### Explorando os recursos
Os exemplos a seguir demonstram diversos recursos do Barramento de Serviço.

| Nome do exemplo | Descrição | Versão mínima do SDK | Disponibilidade |
| --- | --- | --- | --- |
| [Provedores de token HTTP](http://code.msdn.microsoft.com/Service-Bus-HTTP-Token-38f2cfc5) |Demonstra as maneiras diferentes de autenticar um cliente HTTP/REST com o Barramento de Serviço. |2,1 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Cliente HTTP do Barramento de Serviço](http://code.msdn.microsoft.com/Service-Bus-HTTP-client-fe7da74a) |Demonstra como enviar e receber mensagens do Barramento de Serviço via HTTP/REST. |2\.3 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Encaminhamento automático do Barramento de Serviço](http://code.msdn.microsoft.com/Service-Bus-Autoforwarding-b9df470b) |Demonstra como encaminhar automaticamente as mensagens de uma fila, de uma assinatura ou de uma fila de mensagens mortas para outra fila ou tópico. Isso também demonstra como enviar uma mensagem para uma fila ou tópico por meio de uma fila de transferência. |2\.3 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Sistema de Mensagens Agenciado: exemplo de sessão de canal WCF](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-0a526451) |Demonstra como usar o Barramento de Serviço do Microsoft Azure usando canais do Windows Communication Foundation (WCF). O exemplo mostra o uso de canais do WCF para enviar e receber mensagens por meio de uma fila do Barramento de Serviço. O exemplo mostra a comunicação de sessão e sem sessão no Barramento de Serviço. |1\.8 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Sistema de Mensagens Agenciado: Transações](http://code.msdn.microsoft.com/Brokered-Messaging-8cd41d1e) |Demonstra como usar os recursos do sistema de mensagens do Barramento de Serviço do Microsoft Azure em um escopo de transação para garantir que lotes de operações do sistema de mensagens sejam atomicamente confirmados. |1\.8 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Sistema de Mensagens Agenciado: Operações de gerenciamento usando REST](http://code.msdn.microsoft.com/Brokered-Messaging-569cff88) |Demonstra como realizar operações de gerenciamento no Barramento de Serviço usando REST. |1\.8 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [APIs REST do Provedor de Recursos](http://code.msdn.microsoft.com/Service-Bus-Resource-5d887203) |Demonstra como usar as novas APIs REST do RDFE do Barramento de Serviço para gerenciar namespaces e entidades do sistema de mensagens. |1\.8 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Sistema de Mensagens Agenciado: Exemplo de sessão do serviço WCF](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-db4262c2) |Demonstra como usar o Barramento de Serviço do Microsoft Azure usando o modelo de serviço WCF. O exemplo mostra o uso do modelo de serviço WCF para realizar a comunicação baseada em sessão por meio de uma fila do Barramento de Serviço. |1\.8 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Sistema de Mensagens Agenciado: Resposta de solicitação](http://code.msdn.microsoft.com/Brokered-Messaging-Request-2b4ff5d8) |Demonstra como usar o Barramento de Serviço do Microsoft Azure e a funcionalidade de solicitação/resposta. O exemplo mostra clientes e servidores simples se comunicando por meio de uma fila do Barramento de Serviço. |1\.8 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Sistema de Mensagens Agenciado: Fila de mensagens mortas](http://code.msdn.microsoft.com/Brokered-Messaging-Dead-22536dd8) |Demonstra como usar o Barramento de Serviço do Microsoft Azure e a funcionalidade “fila de mensagens mortas” do sistema de mensagens. O exemplo mostra uma comunicação simples entre remetente e destinatário por meio de uma fila do Barramento de Serviço. |1\.8 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Sistema de Mensagens Agenciado: Mensagens adiadas](http://code.msdn.microsoft.com/Brokered-Messaging-ccc4f879) |Demonstra como usar o recurso de adiamento de mensagens do Barramento de Serviço do Microsoft Azure. O exemplo mostra uma comunicação simples entre remetente e destinatário por meio de uma fila do Barramento de Serviço. |1\.8 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Sistema de Mensagens Agenciado: Mensagens de sessão](http://code.msdn.microsoft.com/Brokered-Messaging-Session-41c43fb4) |Demonstra como usar o Barramento de Serviço do Microsoft Azure e a funcionalidade Sessão do Sistema de Mensagens. O exemplo mostra uma comunicação simples entre remetentes e destinatários por meio de uma fila do Barramento de Serviço. |1\.8 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Sistema de Mensagens Agenciado: Tópico de resposta de solicitação](http://code.msdn.microsoft.com/Brokered-Messaging-Request-6759a36e) |Demonstra como implementar o padrão de solicitação/resposta usando tópicos e assinaturas do Barramento de Serviço do Microsoft Azure. O exemplo mostra clientes e servidores simples se comunicando por meio de um tópico do Barramento de Serviço. |1\.8 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Sistema de Mensagens Agenciado: Fila de resposta de solicitação](http://code.msdn.microsoft.com/Brokered-Messaging-Request-0ce8fcaf) |Demonstra como usar o Barramento de Serviço do Microsoft Azure e a funcionalidade de solicitação/resposta. O exemplo mostra clientes e servidores simples se comunicando por meio de duas filas do Barramento de Serviço. |1\.8 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Sistema de Mensagens Agenciado: Detecção de duplicidades](http://code.msdn.microsoft.com/Brokered-Messaging-c0acea25) |Demonstra como usar a detecção de mensagens duplicadas do Barramento de Serviço do Microsoft Azure com filas. Ele cria duas filas, uma com a detecção de duplicidades habilitada e outra sem a detecção de duplicidades. |1\.8 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Sistema de Mensagens Agenciado: Sistema de mensagens assíncrono](http://code.msdn.microsoft.com/Brokered-Messaging-Async-211c1e74) |Demonstra como usar o Barramento de Serviço do Microsoft Azure para enviar e receber mensagens assincronamente de uma fila. A fila fornece comunicação separada e assíncrona entre um remetente e qualquer número de destinatários (aqui, um único destinatário). |1\.8 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Sistema de Mensagens Agenciado: Filtros Avançados](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749) |Demonstra como usar os filtros avançados de publicação/assinatura do Barramento de Serviço do Microsoft Azure. Ele cria um tópico e 3 assinaturas com diferentes definições de filtro, envia mensagens para o tópico e recebe todas as mensagens de assinaturas. |1\.8 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Sistema de Mensagens Agenciado: Pré-busca de mensagens](http://code.msdn.microsoft.com/Brokered-Messaging-be2dac1d) |Demonstra como usar o recurso de pré-busca de mensagens do Barramento de Serviço do Microsoft Azure. Demonstra como usar o recurso de pré-busca de mensagens após o recebimento. |1\.8 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |

## Retransmissão do Barramento de Serviço
Exemplos que demonstram a retransmissão do Barramento de Serviço.

### Introdução
| Nome do exemplo | Descrição | Versão mínima do SDK | Disponibilidade |
| --- | --- | --- | --- |
| [Sistema de mensagens de retransmissão: Azure](http://code.msdn.microsoft.com/Relayed-Messaging-Windows-0d2cede3) |Demonstra como executar um serviço e um cliente do Barramento de Serviço no Azure. Este exemplo configura o Barramento de Serviço programaticamente. Somente as informações sobre ambiente e segurança são armazenadas nos arquivos de configuração. |1\.8 |Barramento de Serviço do Microsoft Azure |
| [Autenticação do sistema de mensagens de retransmissão: Segredo Compartilhado](http://code.msdn.microsoft.com/Relayed-Messaging-92b04c02) |Demonstra como usar um nome de emissor e o segredo do emissor para autenticar usando o Barramento de Serviço. |1\.8 |Barramento de Serviço do Microsoft Azure |
| [Autenticação do sistema de mensagens de retransmissão: WebNoAuth](http://code.msdn.microsoft.com/Relayed-Messaging-a4f0b831) |Demonstra como expor um serviço HTTP que não exija autenticação de usuário do cliente. |1\.8 |Barramento de Serviço do Microsoft Azure |
| [Associações do sistema de mensagens de retransmissão: WebHttp](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-a6477ba0) |Demonstra como usar a associação **WebHttpRelayBinding** para retornar dados binários usando o modelo de programação da Web. |1\.8 |Barramento de Serviço do Microsoft Azure |
| [Associações do sistema de mensagens de retransmissão: NetTcp retransmitido](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-2dec7692) |Demonstra como usar a associação **NetTcpRelayBinding**. |1\.8 |Barramento de Serviço do Microsoft Azure |

### Explorando os recursos
Exemplos que demonstram os recursos de retransmissão do Barramento de Serviço.

| Nome do exemplo | Descrição | Versão mínima do SDK | Disponibilidade |
| --- | --- | --- | --- |
| [Autenticação do sistema de mensagens de retransmissão: WebToken simples](http://code.msdn.microsoft.com/Relayed-Messaging-32c74392) |Demonstra como usar uma credencial de token da Web simples para autenticar no Barramento de Serviço. O exemplo é semelhante ao exemplo de Eco, com algumas alterações. Especificamente, este exemplo adiciona um comportamento aos aplicativos ServiceHost (serviço) e ChannelFactory (cliente). |1\.8 |Barramento de Serviço do Microsoft Azure |
| [Sistema de mensagens de retransmissão: Balanceamento de carga](http://code.msdn.microsoft.com/Relayed-Messaging-Load-bd76a9f8) |Demonstra como usar o Barramento de Serviço do Microsoft Azure para rotear mensagens para vários destinatários. Mostra várias instâncias de um serviço simples comunicando-se com um cliente por meio da associação **NetTcpRelayBinding** |1\.8 |Barramento de Serviço do Microsoft Azure |
| [Associações do sistema de mensagens de retransmissão: Evento de rede](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-c0176977) |Demonstra como usar a associação **NetEventRelayBinding** no Barramento de Serviço do Microsoft Azure. |1\.8 |Barramento de Serviço do Microsoft Azure |
| [Associações de Sistema de Mensagens de Retransmissão: Sessão WS2007Http](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ef1f1fcb) |Demonstra como usar a associação **WS2007HttpRelayBinding** com sessões confiáveis habilitadas. Também mostra como especificar credenciais do Barramento de Serviço no arquivo de configuração em vez de programaticamente. |1\.8 |Barramento de Serviço do Microsoft Azure |
| [Associações do sistema de mensagens de retransmissão: WS2007Http MsgSecCertificate](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-f29c9da5) |Demonstra como usar a associação **WS2007HttpRelayBinding** com segurança de mensagem para proteger mensagens de ponta a ponta, ainda exigindo que os clientes se autentiquem no Barramento de Serviço. |1\.8 |Barramento de Serviço do Microsoft Azure |
| [Sistema de mensagens de retransmissão: Troca de metadados](http://code.msdn.microsoft.com/Relayed-Messaging-Metadata-f122312e) |Demonstra como expor um ponto de extremidade de metadados que usa a associação de retransmissão. **MetadataExchange** tem suporte nas seguintes associações de retransmissão: **NetTcpRelayBinding**, **NetOnewayRelayBinding**, **BasicHttpRelayBinding** e **WS2007HttpRelayBinding**. |1\.8 |Barramento de Serviço do Microsoft Azure |
| [Associações do sistema de mensagens de retransmissão: NetTcp direto](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ca039161) |Demonstra como configurar a associação **NetTcpRelayBinding** para oferecer suporte ao modo de conexão **Híbrido** que primeiro estabelece uma conexão retransmitida e, se possível, alterna automaticamente para uma conexão direta entre um cliente e um serviço. |1\.8 |Barramento de Serviço do Microsoft Azure |
| [Sistema de mensagens de retransmissão: NetTcp MsgSec UserName](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-30542392) |Demonstra como usar a associação **NetTcpRelayBinding** com a segurança de mensagem. |1\.8 |Barramento de Serviço do Microsoft Azure |
| [Associações do sistema de mensagens de retransmissão: Net Oneway](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-bb5b813a) |Demonstra como expor e consumir um ponto de extremidade de serviço usando a associação **NetOnewayRelayBinding**. |1\.8 |Barramento de Serviço do Microsoft Azure |
| [Associações do sistema de mensagens de retransmissão: WS2007Http simples](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-aa4b793a) |Demonstra como usar a associação **WS2007HttpRelayBinding**. Demonstra um serviço simples que não usa opções de segurança e que não exige que os clientes se autentiquem. |1\.8 |Barramento de Serviço do Microsoft Azure |

## Ferramentas de referência do Barramento de Serviço
Os exemplos a seguir demonstram vários outros recursos do serviço.

| Nome do exemplo | Descrição | Versão mínima do SDK | Disponibilidade |
| --- | --- | --- | --- |
| [Service Bus Explorer](http://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a) |O Gerenciador do Barramento de Serviço permite que usuários se conectem a um namespace de serviço do Barramento de Serviço e gerenciem entidades do sistema de mensagens de uma maneira fácil. A ferramenta fornece recursos avançados, como a funcionalidade de importação/exportação, e a capacidade de testar entidades do sistema de mensagens e os serviços de retransmissão. |1\.8 |Barramento de Serviço do Microsoft Azure; Barramento de Serviço do Windows Server |
| [Autorização: SBAzTool](http://code.msdn.microsoft.com/Authorization-SBAzTool-6fd76d93) |Este exemplo demonstra como criar e gerenciar identidades de serviço no Controle de Acesso do Active Directory do Microsoft Azure (também conhecido como Serviço de Controle de Acesso ou ACS) a ser usado com o Barramento de Serviço. |N/D |Barramento de Serviço do Microsoft Azure |

## Próximas etapas
Confira os tópicos a seguir para obter visões gerais conceituais do Barramento de Serviço.

* [Visão geral de mensagens do Barramento de Serviço](../service-bus-messaging/service-bus-messaging-overview.md)
* [Arquitetura do Barramento de Serviço](service-bus-architecture.md)
* [Conceitos fundamentais do barramento de serviço](service-bus-fundamentals-hybrid-solutions.md)

<!---HONumber=AcomDC_0928_2016-->