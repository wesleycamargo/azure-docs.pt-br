<properties 
    pageTitle="Barramento de Serviço e Python com AMQP 1.0 | Microsoft Azure"
    description="Usando o Barramento de Serviço do Python com AMQP."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/29/2016"
    ms.author="sethm" />


# <a name="using-service-bus-from-python-with-amqp-1.0"></a>Usando o Barramento de Serviço do Python com AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Proton-Python é uma associação da linguagem Pyhton a Proton-C; ou seja, Proton-Python é implementada como um wrapper em torno de um mecanismo implementado em C.

## <a name="download-the-proton-client-library"></a>Baixar a biblioteca do cliente do Proton

Você pode baixar o Proton-C e as associações relacionadas (inclusive Python) em [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html). O download está na forma de código-fonte. Para compilar o código, siga as instruções contidas no pacote baixado.

Observe que no momento da redação deste artigo, o suporte a SSL no Proton-C só está disponível para sistemas operacionais Linux. Como o barramento de serviço do Azure requer o uso de SSL, Proton-C (e as associações de linguagem) só pode ser usada para acessar o Barramento de Serviço do Linux no momento. O trabalho para habilitar o Proton-C com SSL no Windows está em andamento, portanto, verifique com frequência para saber se há atualizações.

## <a name="working-with-service-bus-queues,-topics,-and-subscriptions-from-python"></a>Trabalhando com filas, tópicos e assinaturas do Barramento de Serviço no Python

O código a seguir mostra como enviar e receber mensagens de uma entidade de mensagens do Barramento de Serviço.

### <a name="send-messages-using-proton-python"></a>Enviar mensagens usando Proton-Python

O código a seguir mostra como enviar uma mensagem a uma entidade de mensagens do Barramento de Serviço.

```
messenger = Messenger()
message = Message()
message.address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]"

message.body = u"This is a text string"
messenger.put(message)
messenger.send()
```

### <a name="receive-messages-using-proton-python"></a>Receber mensagens usando Proton-Python

O código a seguir mostra como receber uma mensagem de uma entidade de mensagens do Barramento de Serviço.

```
messenger = Messenger()
address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]"
messenger.subscribe(address)

messenger.start()
messenger.recv(1)
message = Message()
if messenger.incoming:
      messenger.get(message)
messenger.stop()
```

## <a name="messaging-between-.net-and-proton-python"></a>Mensagens entre .NET e Proton-Python

### <a name="application-properties"></a>Propriedades do aplicativo

#### <a name="proton-python-to-service-bus-.net-apis"></a>Proton-Python para APIs .NET do Barramento de Serviço

As mensagens Proton-Python oferecem suporte às propriedades de aplicativo dos seguintes tipos: **int**, **long**, **float**, **uuid**, **bool**, **string**. O código Python a seguir mostra como definir propriedades de uma mensagem usando cada um desses tipos de propriedade.

```
message.properties[u"TestString"] = u"This is a string"    
message.properties[u"TestInt"] = 1
message.properties[u"TestLong"] = 1000L
message.properties[u"TestFloat"] = 1.5    
message.properties[u"TestGuid"] = uuid.uuid1()    
```

Na API .NET do Barramento de Serviço, as propriedades do aplicativo de mensagens entram na coleação **Propriedades** de [BrokeredMessage][]. O código a seguir mostra como ler as propriedades do aplicativo de uma mensagem recebida de um cliente Python.

```
if (message.Properties.Keys.Count > 0)
{
  foreach (string name in message.Properties.Keys)
  {
    Object value = message.Properties[name];
    Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
  }
  Console.WriteLine();
}
```

A tabela a seguir mapeia os tipos de propriedades Python para os tipos de propriedade do .NET.

| Tipo de propriedade Python | Tipo de propriedade .NET |
|----------------------|--------------------|
| int                  | int                |
| flutuante                | double             |
| longo                 | int64              |
| uuid                 | guid               |
| bool                 | bool               |
| string               | string             |

#### <a name="service-bus-.net-apis-to-proton-python"></a>APIs .NET do Barramento de Serviço para Proton-Python

O tipo [BrokeredMessage][] oferece suporte às propriedades do aplicativo dos seguintes tipos: **byte**, **sbyte**, **char**, **short**, **ushort**, **int**, **uint**, **long**, **ulong**, **float**, **double**, **decimal**, **bool**, **Guid**, **string**, **Uri**, **DateTime**, **DateTimeOffset**, and **TimeSpan**. O código .NET a seguir mostra como definir propriedades em um objeto [BrokeredMessage][] usando cada um desses tipos de propriedade.

```
message.Properties["TestByte"] = (byte)128;
message.Properties["TestSbyte"] = (sbyte)-22;
message.Properties["TestChar"] = (char) 'X';
message.Properties["TestShort"] = (short)-12345;
message.Properties["TestUshort"] = (ushort)12345;
message.Properties["TestInt"] = (int)-100;
message.Properties["TestUint"] = (uint)100;
message.Properties["TestLong"] = (long)-12345;
message.Properties["TestUlong"] = (ulong)12345;
message.Properties["TestFloat"] = (float)3.14159;
message.Properties["TestDouble"] = (double)3.14159;
message.Properties["TestDecimal"] = (decimal)3.14159;
message.Properties["TestBoolean"] = true;
message.Properties["TestGuid"] = Guid.NewGuid();
message.Properties["TestString"] = "Service Bus";
message.Properties["TestUri"] = new Uri("http://www.bing.com");
message.Properties["TestDateTime"] = DateTime.Now;
message.Properties["TestDateTimeOffSet"] = DateTimeOffset.Now;
message.Properties["TestTimeSpan"] = TimeSpan.FromMinutes(60);
message.Properties["TestTimeSpan"] = TimeSpan.FromMinutes(60);
```

O código Python a seguir mostra como ler as propriedades do aplicativo de uma mensagem recebida de um cliente .NET do Barramento de Serviço.

```
if message.properties != None:
   for k,v in message.properties.items():         
         print "--   %s : %s (%s)" % (k, str(v), type(v))         
```

A tabela a seguir mapeia os tipos de propriedades .NET para os tipos de propriedade do Python.

| Tipo de propriedade .NET | Tipo de propriedade Python | Observações                                                                                                                                                               |
|--------------------|----------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| byte               | int                  | -                                                                                                                                                                     |
| sbyte              | int                  | -                                                                                                                                                                     |
| char               | char                 | Classe Proton-Python                                                                                                                                                 |
| short              | int                  | -                                                                                                                                                                     |
| ushort             | int                  | -                                                                                                                                                                     |
| int                | int                  | -                                                                                                                                                                     |
| uint               | int                  | -                                                                                                                                                                     |
| longo               | int                  | -                                                                                                                                                                     |
| ulong              | longo                 | Classe Proton-Python                                                                                                                                                 |
| flutuante              | flutuante                | -                                                                                                                                                                     |
| double             | flutuante                | -                                                                                                                                                                     |
| Decimal            | Cadeia de caracteres               | Atualmente, decimal não é compatível com Proton.                                                                                                                     |
| bool               | bool                 | -                                                                                                                                                                     |
| Guid               | uuid                 | Classe Proton-Python                                                                                                                                                 |
| string             | string               | -                                                                                                                                                                     |
| DateTime           | timestamp            | Classe Proton-Python                                                                                                                                                 |
| Datetimeoffset     | DescribedType        | DateTimeOffset.UtcTicks mapeado para o tipo AMQP:<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type> |
| TimeSpan           | DescribedType        | Timespan.Ticks mapeado para o tipo AMQP:<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type>                        |
| Uri                | DescribedType        | Uri.AbsoluteUri mapeado para o tipo AMQP:<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>                               |

### <a name="standard-properties"></a>Propriedades padrões

As tabelas a seguir mostram o mapeamento entre as propriedades de mensagem padrões do Proton-Python e as propriedades de mensagens padrões [BrokeredMessage][].

#### <a name="proton-python-to-service-bus-.net-apis"></a>Proton-Python para APIs .NET do Barramento de Serviço

| Proton-Python        | Barramento de Serviço do .NET         | Observações                                                     |
|----------------------|--------------------------|-----------------------------------------------------------|
| durável              | n/d                      | O Barramento de Serviço só oferece suporte a mensagens duráveis.               |
| prioridade             | n/d                      | O Barramento de serviço suporta apenas uma única prioridade de mensagem.      |
| Ttl                  | Message.TimeToLive       | Conversão, o TTL do Proton-Python é definido em milissegundos. |
| first\_acquirer      | n/d                      | -                                                           |
| delivery\_count      | n/d                      | -                                                           |
| ID                   | Message.MessageID        | -                                                           |
| user\_id             | n/d                      | -                                                           |
| endereço              | Message.To               | -                                                           |
| subject              | Message.Label            | -                                                           |
| reply\_to            | Message.ReplyTo          | -                                                           |
| correlation\_id      | Message.CorrelationID    | -                                                           |
| content\_type        | Message.ContentType      | -                                                           |
| content\_encoding    | n/d                      | -                                                           |
| expiry\_time         | n/d                      | -                                                           |
| creation\_time       | n/d                      | -                                                           |
| group\_id            | Message.SessionId        | -                                                           |
| group\_sequence      | n/d                      | -                                                           |
| reply\_to\_group\_id | Message.ReplyToSessionId | -                                                           |
| formato               | n/d                      | -                                                           |

| Barramento de Serviço do .NET        | Proton                       | Observações                                                     |
|-------------------------|------------------------------|-----------------------------------------------------------|
| ContentType             | Message.content\_type        | -                                                           |
| CorrelationId           | Message.correlation\_id      | -                                                           |
| EnqueuedTimeUtc         | n/d                          | -                                                           |
| Rótulo                   | Message.subject              | -                                                           |
| MessageId               | Message.id                   | -                                                           |
| ReplyTo                 | Message.reply\_to            | -                                                           |
| ReplyToSessionId        | Message.reply\_to\_group\_id | -                                                           |
| ScheduledEnqueueTimeUtc | n/d                          | -                                                           |
| SessionId               | Message.group\_id            | -                                                           |
| TimeToLive              | Message.ttl                  | Conversão, o TTL do Proton-Python é definido em milissegundos. |
| Para                      | Message.address              | -                                                           |

## <a name="next-steps"></a>Próximas etapas

Está pronto(a) para saber mais? Visite os links a seguir:

- [Visão geral do Barramento de Serviço para AMQP]
- [AMQP no Barramento de Serviço para Windows Server]

[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
[AMQP no Barramento de Serviço para Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx

[Visão geral do Barramento de Serviço para AMQP]: service-bus-amqp-overview.md



<!--HONumber=Oct16_HO2-->


