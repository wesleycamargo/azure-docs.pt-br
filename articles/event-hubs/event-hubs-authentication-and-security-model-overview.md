---
title: "Visão geral do modelo de autenticação e de segurança dos Hubs de Eventos | Microsoft Docs"
description: "Visão geral do modelo de autenticação e segurança dos Hubs de Eventos"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 93841e30-0c5c-4719-9dc1-57a4814342e7
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2016
ms.author: sethm;clemensv
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9447b863b62fc71d2619591f78be0494711dc6a3


---
# <a name="event-hubs-authentication-and-security-model-overview"></a>Visão geral do modelo de autenticação e segurança dos Hubs de Eventos
O modelo de segurança dos Hubs de Eventos atende aos seguintes requisitos:

* Somente dispositivos que apresentam credenciais válidas podem enviar dados para um Hub de Eventos.
* Um dispositivo não pode representar outro dispositivo.
* Um dispositivo não autorizado pode ser impedido de enviar dados para um Hub de Eventos.

## <a name="device-authentication"></a>Autenticação de dispositivo
O modelo de segurança dos Hubs de Eventos se baseia em uma combinação de tokens [SAS (Assinatura de Acesso Compartilhado)](../service-bus-messaging/service-bus-shared-access-signature-authentication.md) e de *editores de eventos*. Um editor de eventos define um ponto de extremidade virtual para um Hub de Eventos. O editor só pode ser usado para enviar mensagens a um Hub de Eventos. Não é possível receber mensagens de um editor.

Normalmente, um Hub de Eventos emprega um editor por dispositivo. Todas as mensagens enviadas a qualquer um dos editores de um Hub de Eventos são enfileiradas nesse Hub de Eventos. Os editores habilitam o controle de acesso detalhado e a limitação.

Cada dispositivo recebe um token exclusivo, que é carregado no dispositivo. Os tokens são produzidos de modo que cada token exclusivo concede acesso a um editor exclusivo diferente. Um dispositivo que possui um token só pode enviar para um editor específico e nenhum outro editor. Se vários dispositivos compartilharem o mesmo token, cada um desses dispositivos compartilhará um editor.

Embora não seja recomendado, é possível equipar os dispositivos com tokens que concedem acesso direto a um Hub de Eventos. Qualquer dispositivo que contenha esse token pode enviar mensagens diretamente para esse Hub de Eventos. Esse dispositivo não estará sujeito à limitação. Além disso, o dispositivo não pode ser incluído na lista negra para ser impedido de enviar para esse Hub de Eventos.

Todos os tokens são assinados com uma chave SAS. Normalmente, todos os tokens são assinados com a mesma chave. Os dispositivos não estão cientes da chave; isso impede que dispositivos criem tokens.

### <a name="create-the-sas-key"></a>Criar a chave SAS
Ao criar um namespace de Hubs de Eventos, os Hubs de Eventos do Azure geram uma chave SAS de 256 bits chamada **RootManageSharedAccessKey**. Essa chave concede direitos de envio, escuta e gerenciamento ao namespace. Você pode criar chaves adicionais. É recomendável que você crie uma chave que conceda permissões de envio para o Hub de Eventos específico. No restante deste tópico, pressupõe-se que você tenha nomeado esta chave `EventHubSendKey`.

O exemplo a seguir cria uma chave somente de envio ao criar o Hub de Eventos:

```csharp
// Create namespace manager.
string serviceNamespace = "YOUR_NAMESPACE";
string namespaceManageKeyName = "RootManageSharedAccessKey";
string namespaceManageKey = "YOUR_ROOT_MANAGE_SHARED_ACCESS_KEY";
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, string.Empty);
TokenProvider td = TokenProvider.CreateSharedAccessSignatureTokenProvider(namespaceManageKeyName, namespaceManageKey);
NamespaceManager nm = new NamespaceManager(namespaceUri, namespaceManageTokenProvider);

// Create Event Hub with a SAS rule that enables sending to that Event Hub
EventHubDescription ed = new EventHubDescription("MY_EVENT_HUB") { PartitionCount = 32 };
string eventHubSendKeyName = "EventHubSendKey";
string eventHubSendKey = SharedAccessAuthorizationRule.GenerateRandomKey();
SharedAccessAuthorizationRule eventHubSendRule = new SharedAccessAuthorizationRule(eventHubSendKeyName, eventHubSendKey, new[] { AccessRights.Send });
ed.Authorization.Add(eventHubSendRule); 
nm.CreateEventHub(ed);
```

### <a name="generate-tokens"></a>Gerar tokens
Você pode gerar tokens usando a chave SAS. Você deve criar somente um token por dispositivo. Tokens podem ser criados usando o seguinte método. Todos os tokens são gerados usando a chave **EventHubSendKey** . A cada token é atribuído um URI exclusivo.

```csharp
public static string SharedAccessSignatureTokenProvider.GetSharedAccessSignature(string keyName, string sharedAccessKey, string resource, TimeSpan tokenTimeToLive)
```

Ao chamar esse método, o URI deve ser especificado como `//<NAMESPACE>.servicebus.windows.net/<EVENT_HUB_NAME>/publishers/<PUBLISHER_NAME>`. Para todos os tokens, o URI é idêntico, com exceção de `PUBLISHER_NAME`, que deve ser diferente para cada token. Idealmente, `PUBLISHER_NAME` representa a ID do dispositivo que recebe esse token.

Esse método gera um token com a seguinte estrutura:

```csharp
SharedAccessSignature sr={URI}&sig={HMAC_SHA256_SIGNATURE}&se={EXPIRATION_TIME}&skn={KEY_NAME}
```

O tempo de expiração do token é especificado em segundos desde 1º de janeiro de 1970. Segue um exemplo de um token:

```csharp
SharedAccessSignature sr=contoso&sig=nPzdNN%2Gli0ifrfJwaK4mkK0RqAB%2byJUlt%2bGFmBHG77A%3d&se=1403130337&skn=RootManageSharedAccessKey
```

Normalmente, os tokens têm um tempo de vida semelhante a superior ao tempo de vida do dispositivo. Se o dispositivo tiver a capacidade de obter um novo token, tokens com um tempo de vida mais curto podem ser usados.

### <a name="devices-sending-data"></a>Dispositivos que enviam dados
Depois que os tokens são criados, cada dispositivo é configurado com seu próprio token exclusivo.

Quando envia dados a um Hub de Eventos, o dispositivo marca seu token com a solicitação de envio. Para evitar que um invasor intercepte e roube o token, a comunicação entre o dispositivo e o Hub de Eventos deve ocorrer em um canal criptografado.

### <a name="blacklisting-devices"></a>Colocando dispositivos em lista negra
Se um token for roubado por um invasor, o invasor pode representar o dispositivo cujo token foi roubado. Colocar um dispositivo na lista negra inutiliza o dispositivo até que ele receba um novo token que usa um outro editor.

## <a name="authentication-of-back-end-applications"></a>Autenticação de aplicativos back-end
Para autenticar aplicativos back-end que consomem os dados gerados por dispositivos, os Hubs de Eventos empregam um modelo de segurança que é semelhante ao modelo usado para os tópicos do Barramento de Serviço. Um grupo de consumidores de Hubs de Eventos é equivalente a uma assinatura de um tópico do Barramento de Serviço. Um cliente pode criar um grupo de consumidores se a solicitação para criar o grupo for acompanhada por um token que concede privilégios de gerenciamento para o Hub de Eventos ou para o namespace ao qual o Hub de Eventos pertence. Um cliente pode consumir dados de um grupo de consumidores, se a solicitação de recebimento é acompanhada por um token que concede direitos de recebimento no grupo consumidor, o Hub de eventos ou o namespace ao qual pertence o Hub de eventos.

A versão atual do Barramento de Serviço não dá suporte a regras SAS para assinaturas individuais. O mesmo se aplica a grupos de consumidores de Hubs de Eventos. O suporte a SAS será adicionado para os dois recursos no futuro.

Na ausência de autenticação SAS para grupos de consumidores individuais, você pode utilizar chaves SAS para proteger todos os grupos de consumidores com uma chave comum. Essa abordagem permite que um aplicativo consuma dados de qualquer um dos grupos de consumidores de um Hub de Eventos.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os Hubs de Eventos, veja os tópicos a seguir:

* [Visão geral de Hubs de Evento]
* Uma [solução de mensagens na fila] usando filas do Barramento de Serviço.
* Um [aplicativo de exemplo completo que usa os Hubs de Evento].

[Visão geral de Hubs de Evento]: event-hubs-overview.md
[aplicativo de exemplo completo que usa os Hubs de Evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[solução de mensagens na fila]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md




<!--HONumber=Nov16_HO3-->


