<properties 
   pageTitle="Barramento de Serviço e Java com AMQP 1.0 | Microsoft Azure"
   description="Usando o Barramento de Serviço do Java com AMQP."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" /> 
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/07/2015"
   ms.author="sethm" />

# Usando o Barramento de Serviço do Java com AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

O Java Message Service (JMS) é uma API padrão para trabalhar com middleware orientado a mensagens na plataforma Java. O Barramento de Serviço do Azure foi testado com o AMQP 1.0 com base em biblioteca de cliente do JMS desenvolvida pelo projeto Apache Qpid. Esta biblioteca oferece suporte à API JMS 1.1 completa e pode ser usada com qualquer serviço de mensagens compatível com AMQP 1.0. Este cenário também é suportado no Barramento de Serviço para o Windows Server (Barramento de Serviço local). Para obter mais informações, consulte [AMQP no Barramento de Serviço para Windows Server][].

## Baixando a biblioteca de cliente do JMS do AMQP 1.0

Para obter informações sobre onde baixar a versão mais recente da biblioteca do cliente Apache Qpid JMS do AMQP 1.0, acesse [http://people.apache.org/~rgodfrey/qpid-java-amqp-1-0-client-jms.html](http://people.apache.org/~rgodfrey/qpid-java-amqp-1-0-client-jms.html)

Você deve adicionar os seguintes quatro arquivos JAR do arquivamento de distribuição do Apache Qpid JMS do AMQP 1.0 ao CLASSPATH do Java ao criar e executar aplicativos do JMS com o Barramento de Serviço:

-   geronimo-jms\_1.1\_spec-[version].jar

-   qpid-amqp-1-0-client-[version].jar

-   qpid-amqp-1-0-client-jms-[version].jar

-   qpid-amqp-1-0-common-[version].jar

## Trabalhando Barramento de serviço, filas, tópicos e assinaturas do JMS

### Java Naming and Directory Interface (JNDI)

O JMS usa a Java Naming and Directory Interface (JNDI) para criar uma separação entre nomes lógicos e físicos. Dois tipos de objetos JMS são resolvidos usando a JNDI: **ConnectionFactory** e **Destino**. A JNDI usa um modelo de provedor no qual você pode conectar diferentes serviços de diretório para lidar com tarefas de resolução de nome. A biblioteca Apache Qpid JMS do AMQP 1.0 vem com um Provedor JNDI simples baseado em arquivo de propriedades que é configurado usando um arquivo de texto:

O provedor de JNDI do arquivo de propriedades Qpid é configurado usando um arquivo de propriedades do seguinte formato:

```
# servicebus.properties – sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCONNECTIONFACTORY = amqps://[username]:[password]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
topic.TOPIC = topic1
queue.QUEUE = queue1
```

#### Configurando o alocador de conexão

A entrada usada para definir um **ConnectionFactory** no Provedor JNDI de arquivo de propriedades do Qpid tem o seguinte formato:

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

Onde os parâmetros `[jndi\_name]` e `[ConnectionURL]` têm os seguintes significados:

| Nome | Significado | | | | |
|-----------------|--------------------------------------------------------------------------------------------------------------------------------------------|---|---|---|---|
| `[jndi\_name]` | O nome lógico do ConnectionFactory. Esse nome é resolvido no aplicativo Java usando o método `IntialContext.lookup()` do JNDI. | | | | |
| `[ConnectionURL]` | Uma URL que fornece à biblioteca JMS as informações necessárias para o agenciador do AMQP. | | | | |

O formato da URL de conexão é o seguinte:

```
amqps://[username]:[password]@[namespace].servicebus.windows.net
```

Onde os parâmetros `[namespace]`, `[username]` e `[password]` têm os seguintes significados:

| Nome | Significado | | | | |
|---------------|--------------------------------------------------------------------------------|---|---|---|---|
| `[namespace]` | O namespace do Barramento de Serviço é obtido no portal do Azure. | | | | |
| `[username]` | O emissor do Barramento de Serviço é obtido no portal do Azure. | | | | |
| `[password]` | A forma codificada da URL da chave do emissor do Barramento de Serviço é obtida no portal do Azure. | | | | |

> [AZURE.NOTE]você deve executar uma codificação de URL da senha manualmente. Um utilitário útil de codificação de URL está disponível em [http://www.w3schools.com/tags/ref\_urlencode.asp](http://www.w3schools.com/tags/ref_urlencode.asp).

Por exemplo, se as informações obtidas no portal forem as seguintes:

| Namespace: | test.servicebus.windows.net |
|--------------|----------------------------------------------|
| Nome do emissor: | proprietário |
| Chave do emissor: | abcdefg |

Então, para definir um objeto **ConnectionFactory** chamado `SBCONNECTIONFACTORY`, a cadeia de configuração é exibida da seguinte maneira:

```
connectionfactory.SBCONNECTIONFACTORY = amqps://owner:abcdefg@test.servicebus.windows.net
```

#### Configurando destinos

A entrada que define um destino no Provedor JNDI do arquivo de propriedades do Qpid tem o seguinte formato:

```
queue.[jndi_name] = [physical_name]
topic.[jndi_name] = [physical_name]
```

Onde os parâmetros `[jndi\_name]` e `[physical\_name]` têm os seguintes significados:

| Nome | Significado |
|-------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| `[jndi\_name]` | O nome lógico do destino. Esse nome é resolvido no aplicativo Java usando o método `IntialContext.lookup()` do JNDI. |
| `[physical\name]` | O nome da entidade do Barramento de Serviço para a qual o aplicativo envia ou recebe mensagens. |

Observe o seguinte:

- O valor `[physical\name]` pode ser uma fila ou tópico do Barramento de Serviço.
- ao receber de uma assinatura de tópico do Service Bus, o nome físico especificado na JNDI deve ser o nome do tópico. O nome da assinatura é fornecido quando a assinatura durável é criada no código do aplicativo JMS.
- Também é possível tratar uma assinatura de tópico do Barramento de Serviço como uma Fila JMS. Há várias vantagens nessa abordagem: o mesmo código receptor pode ser usado para filas e assinaturas de tópico e todas as informações de endereço (os nomes de tópico e assinatura) são exteriorizadas no arquivo de propriedades.
- Para tratar uma assinatura de tópico do Barramento de Serviço como uma Fila JMS, a entrada no arquivo de propriedades deve estar no formato: `queue.[jndi\_name] = [topic\_name]/Subscriptions/[subscription\_name]`.|

Para definir um destino JMS lógico chamado "TOPIC" que é mapeado para um tópico do Barramento de Serviço chamado "topic1", a entrada no arquivo de propriedades seria da seguinte maneira:

```
topic.TOPIC = topic1
```

### Enviando mensagens com JMS

O código a seguir mostra como enviar uma mensagem para um tópico do Barramento de Serviço. Supõe-se que `SBCONNECTIONFACTORY` e `TOPIC` são definidos em um arquivo de configuração **servicebus.properties**, conforme descrito na seção anterior.

```
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, 
        "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
 
InitialContext context = new InitialContext(env); 
 
ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCONNECTIONFACTORY");
Topic topic = (Topic) context.lookup("TOPIC");
Connection connection = cf.createConnection();
Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
MessageProducer producer = session.createProducer(topic);
TextMessage message = session.createTextMessage("This is a text string"); 
producer.send(message);
```

### Recebendo mensagens com JMS

O código a seguir mostra `how` para receber uma mensagem de uma assinatura de tópico do Barramento de Serviço. Supõe-se que `SBCONNECTIONFACTORY` e TOPIC são definidos em um arquivo de configuração **servicebus.properties**, conforme descrito na seção anterior. Presume-se também que o nome da assinatura é `subscription1`.

```
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, 
        "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
 
InitialContext context = new InitialContext(env);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCONNECTIONFACTORY");
Topic topic = (Topic) context.lookup("TOPIC");
Connection connection = cf.createConnection();
Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
TopicSubscriber subscriber = session.createDurableSubscriber(topic, "subscription1");
connection.start();
Message message = messageConsumer.receive();
```

### Diretrizes para criação de aplicativos robustos

A especificação JMS define como o contrato de exceção dos métodos de API e o código do aplicativo devem ser escrito para lidar com essas exceções. Aqui estão alguns outros pontos a serem considerados em relação ao tratamento de exceção:

-   Registrar um **ExceptionListener** com a conexão JMS usando **connection.setExceptionListener**. Isso permite que um cliente seja notificado de um problema de forma assíncrona. Essa notificação é especialmente importante para conexões que só consomem mensagens, que não teriam nenhuma outra maneira de saber que a conexão falhou. O **ExceptionListener** é chamado se houver um problema com a conexão subjacente de AMQP, sessão ou link. Nessa situação, o aplicativo deve recriar os objetos **Conexão JMS**, **Sessão**, **MessageProducer** e **MessageConsumer** a partir do zero.

-   Para verificar se uma mensagem foi enviada com êxito de uma **MessageProducer** para uma entidade do Barramento de Serviço, certifique-se de que o aplicativo foi configurado com o conjunto de propriedades do sistema **qpid.sync\_publish**. Você pode fazer isso iniciando o programa com a opção de Java VM **-Dqpid.sync\_publish=true** definida na linha de comando ao iniciar o aplicativo. Definir essa opção configura a biblioteca para não retornar da chamada de envio até que seja recebida a confirmação que a mensagem foi aceita pelo Barramento de Serviço. Se ocorrer um problema durante a operação de envio, uma **JMSException** é gerada. Há duas causas possíveis:
	1. Se o problema é devido ao Barramento de Serviço rejeitar a mensagem específica que está sendo enviada, então uma exceção **MessageRejectedException** será gerada. Esse erro é transitório ou devido a algum problema com a mensagem. O curso de ação recomendado é fazer várias tentativas para repetir a operação com alguma lógica de retirada. Se o problema persistir, a mensagem deve ser abandonada com um erro registrado localmente. Não é necessário recriar os objetos **Conexão JMS**, **Sessão** ou **MessageProducer** nessa situação. 
	2. Se o problema se deve ao fato do Barramento de Serviço fechar o Link AMQP, então uma exceção **InvalidDestinationException** será gerada. Isso pode ser devido a um problema transitório ou devido à entidade de mensagem que está sendo excluída. Em ambos os casos, os objetos **Conexão JMS**, **Sessão** e **MessageProducer** devem ser recriados. Se a condição de erro era transitória, essa operação eventualmente será bem-sucedida. Se a entidade tiver sido excluída, a falha será permanente.

## Mensagens entre .NET e JMS

### Corpos de mensagens

O JMS define cinco tipos diferentes de mensagens: **BytesMessage**, **MapMessage**, **ObjectMessage**, **StreamMessage** e **TextMessage**. A API .NET do Barramento de Serviço possui um tipo de mensagem, [BrokeredMessage][].

#### JMS para API .NET do Barramento de Serviço

As seções a seguir mostram como utilizar mensagens de cada um dos tipos de mensagem JMS do .NET. Um exemplo de **ObjectMessage** não foi incluído, já que o corpo de um **ObjectMessage** contém um objeto serializável na linguagem de programação Java, que não pode ser interpretado por um aplicativo .NET.

##### BytesMessage

O código a seguir mostra como utilizar o corpo de um objeto **BytesMessage** usando as APIs .NET do Barramento de Serviço.

```
Stream stream = message.GetBody<Stream>();
int streamLength = (int)stream.Length;

byte[] byteArray = new byte[streamLength];
stream.Read(byteArray, 0, streamLength);

Console.WriteLine("Length = " + streamLength);
for (int i = 0; i < stream.Length; i++)
{
  Console.Write("[" + (sbyte) byteArray[i] + "]");
}
```

##### MapMessage

O código a seguir mostra como utilizar o corpo de um objeto **MapMessage** usando as APIs .NET do Barramento de Serviço. Esse código itera por meio dos elementos do mapa, exibindo o nome e o valor de cada elemento.

```
Dictionary<String, Object> dictionary = message.GetBody<Dictionary<String, Object>>();

foreach (String mapItemName in dictionary.Keys)
{
  Object mapItemValue = null;
  if (dictionary.TryGetValue(mapItemName, out mapItemValue))
  {
    Console.WriteLine(mapItemName + ":" + mapItemValue);
  }
}
```

##### StreamMessage

O código a seguir mostra como utilizar o corpo de um objeto **StreamMessage** usando as APIs .NET do Barramento de Serviço. Esse código lista cada um dos itens do fluxo, juntamente com seus tipos.

```
List<Object> list = message.GetBody<List<Object>>();

foreach (Object item in list)
{
  Console.WriteLine(item + " (" + item.GetType() + ")");
}
```

##### TextMessage

O código a seguir mostra como utilizar o corpo de um objeto **TextMessage** usando as APIs .NET do Barramento de Serviço. Esse código exibe a cadeia de caracteres de texto contida no corpo da mensagem.

```
Console.WriteLine("Text: " + message.GetBody<String>());
```

#### APIs .NET do Barramento de Serviço para JMS

As seções a seguir mostram como um aplicativo .NET pode criar uma mensagem que é recebida no JMS em cada um dos diferentes tipos de mensagem JMS. Um exemplo de **ObjectMessage** não foi incluído, já que o corpo de um **ObjectMessage** contém um objeto serializável na linguagem de programação Java, que não pode ser interpretado por um aplicativo .NET.

##### BytesMessage

O código a seguir mostra como criar um objeto [BrokeredMessage][] no .NET que é recebido por um cliente JMS como um **BytesMessage**.

```
byte[] bytes = { 33, 12, 45, 33, 12, 45, 33, 12, 45, 33, 12, 45 };
message = new BrokeredMessage(bytes);
```

##### StreamMessage

O código a seguir mostra como criar um objeto [BrokeredMessage][] no .NET que é recebido por um cliente JMS como um **StreamMessage**.

```
List<Object> list = new List<Object>();
list.Add("String 1");
list.Add("String 2");
list.Add("String 3");
list.Add((double)3.14159);
message = new BrokeredMessage(list);
```

##### TextMessage

O código a seguir mostra como utilizar o corpo de um objeto **TextMessage** usando a API .NET do Barramento de Serviço. Esse código exibe a cadeia de caracteres de texto contida no corpo da mensagem.

```
message = new BrokeredMessage("this is a text string");
```

### Propriedades do aplicativo

####JMS para APIs .NET do Barramento de Serviço

As mensagens JMS oferecem suporte às propriedades de aplicativo dos seguintes tipos: **boolean**, **byte**, **short**, **int**, **long**, **float**, **double** e **String**. O código Java a seguir mostra como definir propriedades de uma mensagem usando cada um desses tipos de propriedade.

```
message.setBooleanProperty("TestBoolean", true); 
message.setByteProperty("TestByte", (byte) 33); 
message.setDoubleProperty("TestDouble", 3.14159D); 
message.setFloatProperty("TestFloat", 3.13159F); 
message.setIntProperty("TestInt", 100); 
message.setStringProperty("TestString", "Service Bus");
```

Nas APIs .NET do Barramento de Serviço, as propriedades do aplicativo de mensagens entram na coleação **Propriedades** de [BrokeredMessage][]. O código a seguir mostra como ler as propriedades do aplicativo de uma mensagem recebida de um cliente JMS.

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

A tabela a seguir mostra como os tipos de propriedades JMS são mapeados para os tipos de propriedade do .NET.

| Tipo de propriedade JMS | Tipo de propriedade .NET |
|-------------------|--------------------|
| Byte | sbyte |
| Número inteiro | int |
| Float | flutuante |
| Duplo | double |
| Booliano | bool |
| Cadeia de caracteres | cadeia de caracteres |

O tipo [BrokeredMessage][] oferece suporte às propriedades do aplicativo dos seguintes tipos: **byte**, **sbyte**, **char**, **short**, **ushort**, **int**, **uint**, **long**, **ulong**, **float**, **double**, **decimal**, **bool**, **Guid**, **string**, **Uri**, **DateTime**, **DateTimeOffset** e **TimeSpan**. O código .NET a seguir mostra como definir propriedades em um objeto [BrokeredMessage][] usando cada um desses tipos de propriedade.

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

O código a seguir mostra como ler as propriedades do aplicativo de uma mensagem recebida de um cliente .NET do Barramento de Serviço.

```
Enumeration propertyNames = message.getPropertyNames(); 
while (propertyNames.hasMoreElements()) 
{ 
  String name = (String) propertyNames.nextElement(); 
  Object value = message.getObjectProperty(name); 
  System.out.println(name + ": " + value + " (" + value.getClass() + ")"); 
}
```

A tabela a seguir mostra como os tipos de propriedades JMS são mapeados para os tipos de propriedade do JMS.

| Tipo de propriedade .NET | Tipo de propriedade JMS | Observações |
|--------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| byte | UnsignedByte | - | | sbyte | Byte | - | | char | Character | - | | short | Short | - | | UShort | UnsignedShort | - | | int | Integer | - | | uint | UnsignedInteger | - | | Long | Long | - | | ULong | UnsignedLong | - | | float | Float | - | | double | Double | - | | decimal | BigDecimal | - | | bool | Boolean | - | | GUID | UUID | - | | string | String | - | | DateTime | Date | - | | DateTimeOffset | DescribedType | DateTimeOffset.Utcticks mapeado para tipo AMQP:<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type> | | TimeSpan | DescribedType | Timespan.Ticks mapeado para tipo AMQP:<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> | | Uri | DescribedType | Uri.AbsoluteUri mapeado para tipo AMQP:<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type> |

### Cabeçalhos padrões

As tabelas a seguir mostram como os cabeçalhos JMS padrões e as propriedades padrões de [BrokeredMessage][] são mapeadas usando AMQP 1.0.

#### JMS para APIs .NET do Barramento de Serviço

| JMS | Barramento de Serviço do .NET | Observações |
|------------------|--------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| JMSCorrelationID | Message.CorrelationID | - | | JMSDeliveryMode | Não disponível no momento | O Barramento de Serviço oferece suporte apenas a mensagens duráveis; por exemplo, DeliveryMode.PERSISTENT, independentemente do que é especificado. | | JMSDestination | Message.To | - | | JMSExpiration | Message. TimeToLive | Conversion | | JMSMessageID | Message.MessageID | Por padrão, JMSMessageID é codificado no formato binário na mensagem do AMQP. Após o recebimento da id de mensagem binária, a biblioteca de cliente .NET converte uma representação de cadeia de caracteres com base nos valores unicode dos bytes. Para alternar a biblioteca JMS para usar ids de mensagem de cadeia de caracteres, acrescente a cadeia de caracteres "binary-messageid=false" para os parâmetros de consulta da ConnectionURL JNDI. Por exemplo: "amqps://[username]:[password]@[namespace].servicebus.windows.net? binary-messageid=false ". | | JMSPriority | Não disponível no momento | O Barramento de Serviço não oferece suporte à prioridade da mensagem. | | JMSRedelivered | Não disponível no momento | - | | JMSReplyTo | Mensagem. ReplyTo | - | | JMSTimestamp | Message.EnqueuedTimeUtc | Conversion | | JMSType | Message.Properties["jms-type"] | - |

#### APIs .NET do Barramento de Serviço para JMS

| Barramento de Serviço do .NET | JMS | Observações |
|-------------------------|------------------|-------------------------|
| ContentType | - | Não disponível no momento | | CorrelationId | JMSCorrelationID | - | | EnqueuedTimeUtc | JMSTimestamp | Conversion | | Label | n/d | Não disponível no momento | | MessageId | JMSMessageID | - | | ReplyTo | JMSReplyTo | - | | ReplyToSessionId | n/d | Não disponível no momento | | ScheduledEnqueueTimeUtc | n/d | Não disponível no momento | | SessionId | n/d | Não disponível no momento | | TimeToLive | JMSExpiration | Conversion | | To | JMSDestination | - |

## Restrições e recursos não suportados

As restrições a seguir ocorrem durante o uso do JMS sobre o AMQP 1.0 com o Barramento de Serviço, ou seja:

-   Apenas um **MessageProducer** ou **MessageConsumer** é permitido por sessão. Se precisar criar vários objetos **MessageProducer** ou **MessageConsumer** em um aplicativo, crie sessões dedicadas para cada um deles.

-   Assinaturas de tópico voláteis atualmente não têm suporte.

-   Objetos **MessageSelector** não são suportados.

-   Destinos temporários; por exemplo, **TemporaryQueue** ou **TemporaryTopic** não são suportados, juntamente com as APIs de **QueueRequestor** e **TopicRequestor** que os utilizam.

-   Não há suporte para sessões transacionadas.

-   Não há suporte para transações distribuídas.

## Próximas etapas

Está pronto(a) para saber mais? Visite os links a seguir:

- [Visão geral do AMQP do Barramento de Serviço]
- [AMQP no Barramento de Serviço para Windows Server]

[AMQP no Barramento de Serviço para Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx

[Visão geral do AMQP do Barramento de Serviço]: service-bus-amqp-overview.md

<!---HONumber=Oct15_HO3-->