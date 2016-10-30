<properties 
    pageTitle="Barramento de Serviço e PHP com AMQP 1.0 | Microsoft Azure"
    description="Usando o Barramento de Serviço do PHP com AMQP."
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


# <a name="using-service-bus-from-php-with-amqp-1.0"></a>Usando o Barramento de Serviço do PHP com AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Proton-PHP é uma associação da linguagem PHP a Proton-C; ou seja, Proton-PHP é implementada como um wrapper em torno de um mecanismo implementado em C.

## <a name="downloading-the-proton-client-library"></a>Baixando a biblioteca do cliente do Proton

Você pode baixar Proton-C e as associações relacionadas (inclusive PHP) em [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html). O download está na forma de código-fonte. Para compilar o código, siga as instruções contidas no pacote baixado.

> [AZURE.IMPORTANT] No momento da redação deste artigo, o suporte a SSL no Proton-C só está disponível para sistemas operacionais Linux. Como o barramento de serviço do Azure requer o uso de SSL, Proton-C (e as associações de linguagem) só pode ser usada para acessar o Barramento de Serviço do Linux no momento. O trabalho para habilitar o Proton-C com SSL no Windows está em andamento, portanto, verifique com frequência para saber se há atualizações.

## <a name="working-with-service-bus-queues,-topics,-and-subscriptions-from-php"></a>Trabalhando com filas, tópicos e assinaturas do Barramento de Serviço no PHP

O código a seguir mostra como enviar e receber mensagens de uma entidade de mensagens do Barramento de Serviço.

### <a name="sending-messages-using-proton-php"></a>Enviando mensagens usando Proton-PHP

O código a seguir mostra como enviar uma mensagem a uma entidade de mensagens do Barramento de Serviço.

```
$messenger = new Messenger();
$message = new Message();
$message->address = "amqps://[keyname]:[password]@[namespace].servicebus.windows.net/[entity]";

$message->body = "This is a text string";
$messenger->put($message);
$messenger->send();
```

### <a name="receiving-messages-using-proton-php"></a>Recebendo mensagens com Proton-PHP

O código a seguir mostra como receber uma mensagem de uma entidade de mensagens do Barramento de Serviço.

```
$messenger = new Messenger();
$address = "amqps://[keyname]:[password]@[namespace].servicebus.windows.net/[entity]";
$messenger->subscribe($address);

$messenger->start();
$messenger->recv(1);

if($messenger->incoming())
{
   $message = new Message();
   $messenger->get($message);      
}

$messenger->stop();
```

## <a name="messaging-between-.net-and-proton-php"></a>Mensagens entre .NET e Proton-PHP

### <a name="application-properties"></a>Propriedades do aplicativo

#### <a name="protonphp-to-service-bus-.net-apis"></a>ProtonPHP para APIs .NET do Barramento de Serviço

As mensagens do Proton-PHP oferecem suporte a propriedades de aplicativo dos seguintes tipos: **integer**, **double**, **Boolean**, **string** e **object**. O código PHP a seguir mostra como definir propriedades de uma mensagem usando cada um desses tipos de propriedade.

```
$message->properties["TestInt"] = 1;    
$message->properties["TestDouble"] = 1.5;      
$message->properties["TestBoolean"] = False;
$message->properties["TestString"] = "Service Bus";    
$message->properties["TestObject"] = new UUID("1234123412341234");   
```

Nas APIs .NET do Barramento de Serviço, as propriedades do aplicativo de mensagens entram na coleção **Propriedades** de [BrokeredMessage][]. O código a seguir mostra como ler as propriedades do aplicativo de uma mensagem recebida de um cliente PHP.

```
if (message.Properties.Keys.Count > 0)
{
  foreach (string name in message.Properties.Keys)
  {
    Object value = message.Properties[name];
    Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
  }
  Console.WriteLine();
}if (message.Properties.Keys.Count > 0)
{
foreach (string name in message.Properties.Keys)
{
  Object value = message.Properties[name];
  Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
}
Console.WriteLine();
}
```

A tabela a seguir mapeia os tipos de propriedades PHP para os tipos de propriedade do .NET.

| Tipo de propriedade PHP | Tipo de propriedade .NET |
|-------------------|--------------------|
| inteiro           | int                |
| double            | double             |
| Booleano           | bool               |
| string            | string             |
| objeto            | Objeto             |

#### <a name="service-bus-.net-apis-to-php"></a>APIs .NET do Barramento de Serviço para PHP

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
```

O código PHP a seguir mostra como ler as propriedades do aplicativo de uma mensagem recebida de um cliente .NET do Barramento de Serviço.

```
if ($message->properties != null)
{
  foreach($message->properties as $key => $value)
  {
    printf("-- %s : %s (%s) \n", $key, $value, gettype($value));                       
  }         
}
```

A tabela a seguir mapeia os tipos de propriedades .NET para os tipos de propriedade do PHP.

| Tipo de propriedade .NET | Tipo de propriedade PHP | Observações                                                                                                                                                               |
|--------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| byte               | inteiro           | -                                                                                                                                                                     |
| sbyte              | inteiro           | -                                                                                                                                                                     |
| char               | Char              | Classe Proton-PHP                                                                                                                                                    |
| short              | inteiro           | -                                                                                                                                                                     |
| ushort             | inteiro           | -                                                                                                                                                                     |
| int                | inteiro           | -                                                                                                                                                                     |
| uint               | Número inteiro           | -                                                                                                                                                                     |
| longo               | inteiro           | -                                                                                                                                                                     |
| ulong              | inteiro           | -                                                                                                                                                                     |
| flutuante              | double            | -                                                                                                                                                                     |
| double             | double            | -                                                                                                                                                                     |
| Decimal            | string            | Atualmente, decimal não é compatível com Proton.                                                                                                                     |
| bool               | Booliano           | -                                                                                                                                                                     |
| Guid               | UUID              | Classe Proton-PHP                                                                                                                                                    |
| string             | string            | -                                                                                                                                                                     |
| DateTime           | inteiro           | -                                                                                                                                                                     |
| Datetimeoffset     | DescribedType     | DateTimeOffset.UtcTicks mapeado para o tipo AMQP:<type name="datetime-offset" class=restricted source="long"> <descriptor name="com.microsoft:datetime-offset" /></type> |
| TimeSpan           | DescribedType     | Timespan.Ticks mapeado para o tipo AMQP:<type name="timespan" class=restricted source="long"> <descriptor name="com.microsoft:timespan" /></type>                        |
| Uri                | DescribedType     | Uri.AbsoluteUri mapeado para o tipo AMQP:<type name="uri" class=restricted source="string"> <descriptor name="com.microsoft:uri" /></type>                               |

### <a name="standard-properties"></a>Propriedades padrões

As tabelas a seguir mostram o mapeamento entre as propriedades de mensagem padrão do Proton-PHP e as propriedades de mensagens padrão [BrokeredMessage][].

| Proton-PHP           | Barramento de Serviço do .NET         | Observações                                                    |
|----------------------|--------------------------|----------------------------------------------------------|
| Durável              | n/d                      | O Barramento de Serviço só oferece suporte a mensagens duráveis.          |
| Prioridade             | n/d                      | O Barramento de serviço suporta apenas uma única prioridade de mensagem. |
| Ttl                  | Message.TimeToLive       | Conversão, o TTL do Proton-PHP é definido em milissegundos.   |
| first\_acquirer      | -                          | -                                                          |
| delivery\_count      | -                          | -                                                          |
| ID                   | Message.Id               | -                                                          |
| user\_id             | -                          | -                                                          |
| Endereço              | Message.To               | -                                                          |
| Assunto              | Message.Label            | -                                                          |
| reply\_to            | Message.ReplyTo          | -                                                          |
| correlation\_id      | Message.CorrelationId    | -                                                          |
| content\_type        | Message.ContentType      | -                                                          |
| content\_encoding    | n/d                      | -                                                          |
| expiry\_time         | Message.ExpiresAtUTC     | -                                                          |
| creation\_time       | n/d                      | -                                                          |
| group\_id            | Message.SessionId        | -                                                          |
| group\_sequence      | -                          | -                                                          |
| reply\_to\_group\_id | Message.ReplyToSessionId | -                                                          |
| Formatar               | n/d                      | -

#### <a name="service-bus-.net-apis-to-proton-php"></a>APIs .NET do Barramento de Serviço para Proton-PHP

| Barramento de Serviço do .NET        | Proton-PHP                                             | Observações                                                  |
|-------------------------|--------------------------------------------------------|--------------------------------------------------------|
| ContentType             | Message-\>content\_type                                | -                                                        |
| CorrelationId           | Message-\>correlation\_id                              | -                                                        |
| EnqueuedTimeUtc         | Message-\>annotations[x-opt-enqueued-time]             | -                                                        |
| Rótulo                   | Message-\>subject                                      | -                                                        |
| MessageId               | Message-\>id                                           | -                                                        |
| ReplyTo                 | Message-\>reply\_to                                    | -                                                        |
| ReplyToSessionId        | Message-\>reply\_to\_group\_id                         | -                                                        |
| ScheduledEnqueueTimeUtc | Message-\>annotations ["x-opt-scheduled-enqueue-time"] | -                                                        |
| SessionId               | Message-\>group\_id                                    | -                                                        |
| TimeToLive              | Message-\>ttl                                          | Conversão, o TTL do Proton-PHP é definido em milissegundos. |
| Para                      | Message-\>address                                      | -                                                        |

## <a name="next-steps"></a>Próximas etapas

Está pronto(a) para saber mais? Visite os links a seguir:

- [Visão geral do AMQP do Barramento de Serviço]
- [AMQP no Barramento de Serviço para Windows Server]


[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
[AMQP no Barramento de Serviço para Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
[Visão geral do AMQP do Barramento de Serviço]: service-bus-amqp-overview.md



<!--HONumber=Oct16_HO2-->


