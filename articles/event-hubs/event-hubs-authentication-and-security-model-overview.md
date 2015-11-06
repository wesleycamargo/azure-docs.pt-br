<properties 
   pageTitle="Visão geral do modelo de autenticação e de segurança dos Hubs de Eventos | Microsoft Azure"
   description="Perguntas frequentes dos Hubs de Eventos"
   services="event-hubs"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="10/07/2015"
   ms.author="sethm" />

# Visão geral do modelo de autenticação e segurança dos Hubs de Eventos

O modelo de segurança dos Hubs de Eventos atende aos seguintes requisitos:

- Somente dispositivos que apresentam credenciais válidas podem enviar dados para um Hub de Eventos.
- Um dispositivo não pode representar outro dispositivo.
- Um dispositivo não autorizado pode ser impedido de enviar dados para um Hub de Eventos.

## Autenticação de dispositivo

O modelo de segurança dos Hubs de Eventos se baseia em uma combinação de [tokens SAS (assinatura de acesso compartilhado)](service-bus-shared-access-signature-authentication.md) e editores de eventos. Um editor de eventos define um ponto de extremidade virtual para um Hub de Eventos. O editor só pode ser usado para enviar mensagens a um Hub de Eventos. Não é possível receber mensagens de um editor.

Normalmente, um Hub de Eventos emprega um editor por dispositivo. Todas as mensagens enviadas a qualquer um dos editores de um Hub de Eventos são enfileiradas nesse Hub de Eventos. Os editores permitem controle de acesso detalhado e limitação.

Cada dispositivo recebe um token exclusivo, que é carregado no dispositivo. Os tokens são produzidos de modo que cada token exclusivo concede acesso a um editor exclusivo diferente. Um dispositivo que possui um token só pode enviar para um editor específico e nenhum outro editor. Se vários dispositivos compartilharem o mesmo token, cada um desses dispositivos compartilhará um editor.

Embora não seja recomendado, é possível equipar os dispositivos com tokens que concedem acesso direto a um Hub de Eventos. Qualquer dispositivo que contém esse token pode enviar mensagens diretamente para esse Hub de Eventos. Esse dispositivo não estará sujeito à limitação. Além disso, o dispositivo não pode ser incluído na lista de bloqueados para ser impedido de enviar para esse Hub de Eventos.

Todos os tokens são assinados com uma chave SAS. Normalmente, todos os tokens são assinados com a mesma chave. Os dispositivos não estão cientes da chave; isso impede que dispositivos criem tokens.

### Criar a chave SAS

Ao criar um namespace, o Barramento de Serviço gera uma chave SAS de 256 bits chamada **RootManageSharedAccessKey**. Essa chave concede direitos de envio, escuta e gerenciamento ao namespace. Você pode criar chaves adicionais. É recomendável que você crie uma chave que conceda permissões de envio para o Hub de Eventos específico. No restante deste tópico, pressupõe-se que você nomeou esta chave `EventHubSendKey`.

O exemplo a seguir cria uma chave somente de envio ao criar o Hub de Eventos:

```
// Create namespace manager.
string serviceNamespace = "YOUR_NAMESPACE";
string namespaceManageKeyName = "RootManageSharedAccessKey";
string namespaceManageKey = "YOUR_ROOT_MANAGE_SHARED_ACCESS_KEY";
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, string.Empty);
TokenProvider td = TokenProvider.CreateSharedAccessSignatureTokenProvider(namespaceManageKeyName, namespaceManageKey);
NamespaceManager nm = new NamespaceManager(namespaceUri, namespaceManageTokenProvider);

// Create Event Hub with a SAS rule that allows sending to that Event Hub.
EventHubDescription ed = new EventHubDescription("MY_EVENT_HUB") { PartitionCount = 32 };
string eventHubSendKeyName = "EventHubSendKey";
string eventHubSendKey = SharedAccessAuthorizationRule.GenerateRandomKey();
SharedAccessAuthorizationRule eventHubSendRule = new SharedAccessAuthorizationRule(eventHubSendKeyName, eventHubSendKey, new[] { AccessRights.Send });
ed.Authorization.Add(eventHubSendRule); 
nm.CreateEventHub(ed);
```

### Gerar tokens

Você pode gerar tokens usando a chave SAS. Você deve criar somente um token por dispositivo. Tokens podem ser criados usando o seguinte método. Todos os tokens são gerados usando a chave **EventHubSendKey**. A cada token é atribuído um URI exclusivo.

```
public static string SharedAccessSignatureTokenProvider.GetSharedAccessSignature(string keyName, string sharedAccessKey, string resource, TimeSpan tokenTimeToLive)
```

Ao chamar esse método, o URI deve ser especificado como `//<NAMESPACE>.servicebus.windows.net/<EVENT_HUB_NAME>/publishers/<PUBLISHER_NAME>`. Para todos os tokens, o URI é idêntico, com exceção de `PUBLISHER_NAME`, que deve ser diferente para cada token. Idealmente, `PUBLISHER_NAME` representa a ID do dispositivo que recebe esse token.

Esse método gera um token com a seguinte estrutura:

```
SharedAccessSignature sr={URI}&sig={HMAC_SHA256_SIGNATURE}&se={EXPIRATION_TIME}&skn={KEY_NAME}
```

O tempo de expiração do token é especificado em segundos desde 1º de janeiro de 1970. Segue um exemplo de um token:

```
SharedAccessSignature sr=contoso&sig=nPzdNN%2Gli0ifrfJwaK4mkK0RqAB%2byJUlt%2bGFmBHG77A%3d&se=1403130337&skn=RootManageSharedAccessKey
```

Normalmente, os tokens têm um tempo de vida semelhante a superior ao tempo de vida do dispositivo. Se o dispositivo tiver a capacidade de obter um novo token, tokens com um tempo de vida mais curto podem ser usados.

### Dispositivos que enviam dados

Depois que os tokens são criados, cada dispositivo é configurado com seu próprio token exclusivo.

Quando envia dados a um Hub de Eventos, o dispositivo marca seu token com a solicitação de envio. Para evitar que um invasor intercepte e roube o token, a comunicação entre o dispositivo e o Hub de Eventos deve ocorrer em um canal criptografado.

### Colocando dispositivos em lista de bloqueados

Se um token for roubado por um invasor, o invasor pode representar o dispositivo cujo token foi roubado. Colocar um dispositivo na lista de bloqueados inutiliza o dispositivo até que ele receba um novo token que usa um outro editor.

## Autenticação de aplicativos back-end

Para autenticar aplicativos back-end que consomem os dados gerados por dispositivos, os Hubs de Eventos empregam um modelo de segurança que é semelhante ao modelo usado para os tópicos do Barramento de Serviço. Um grupo de consumidores de Hubs de Eventos é equivalente a uma assinatura de um tópico do Barramento de Serviço. Um cliente pode criar um grupo de consumidores se a solicitação para criar o grupo for acompanhada por um token que concede privilégios de gerenciamento para o Hub de Eventos ou para o namespace ao qual o Hub de Eventos pertence. Um cliente pode consumir dados de um grupo de consumidores, se a solicitação de recebimento é acompanhada por um token que concede direitos de recebimento no grupo consumidor, o Hub de eventos ou o namespace ao qual pertence o Hub de eventos.

A versão atual do Barramento de Serviço não dá suporte a regras SAS para assinaturas individuais. O mesmo se aplica a grupos de consumidores de Hubs de Eventos. O suporte a SAS será adicionado para os dois recursos no futuro.

Na ausência de autenticação SAS para grupos de consumidores individuais, você pode utilizar chaves SAS para proteger todos os grupos de consumidores com uma chave comum. Essa abordagem permite que um aplicativo consuma dados de qualquer um dos grupos de consumidores de um Hub de Eventos.

### Criar identidades de serviço, partes confiáveis e regras no ACS

O ACS dá suporte a várias maneiras de criar identidades de serviço, terceiras partes confiáveis e regras, mas a maneira mais fácil de fazer isso é usando o [SBAZTool](http://code.msdn.microsoft.com/Authorization-SBAzTool-6fd76d93). Por exemplo:

1. Crie uma identidade de serviço para um **EventHubSender**. Isso retorna o nome da identidade de serviço que foi criada e sua chave:

	```
	sbaztool.exe exe -n <namespace> -k <key>  makeid eventhubsender
	```

2. Conceda “Declarações de Envio” de **EventHubSender** ao Hub de Eventos:

	```
	sbaztool.exe -n <namespace> -k <key> grant Send /AuthTestEventHub eventhubsender
	```

3. Crie uma identidade de serviço de um receptor para o Grupo de Consumidores 1:

	```
	sbaztool.exe exe -n <namespace> -k <key> makeid consumergroup1receiver
	```

4. Conceda “Declarações de Escuta” de `consumergroup1receiver` a **ConsumerGroup1**:

	```
	sbaztool.exe -n <namespace> -k <key> grant Listen /AuthTestEventHub/ConsumerGroup1 consumergroup1receiver
	```

5. Crie uma identidade de serviço de um receptor para o **Grupo de Consumidores 2**:

	```
	sbaztool.exe exe -n <namespace> -k <key>  makeid consumergroup2receiver
	```

6. Conceda “Declarações de Escuta” de `consumergroup2receiver` a **ConsumerGroup2**:

	```
	sbaztool.exe -n <namespace> -k <key> grant Listen /AuthTestEventHub/ConsumerGroup2 consumergroup2receiver
	```

## Próximas etapas

Para saber mais sobre os Hubs de Eventos, consulte os tópicos a seguir:

- [Visão geral dos Hubs de Eventos].
- Um [aplicativo de exemplo completo que usa os Hubs de Evento].
- Uma [solução de mensagens na fila] usando filas do Barramento de Serviço.

[Visão geral dos Hubs de Eventos]: event-hubs-overview.md
[aplicativo de exemplo completo que usa os Hubs de Evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[solução de mensagens na fila]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 

<!---HONumber=Oct15_HO3-->