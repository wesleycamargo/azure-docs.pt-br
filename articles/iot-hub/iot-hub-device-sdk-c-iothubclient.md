<properties
	pageTitle="SDK do dispositivo IoT do Azure para C - IoTHubClient | Microsoft Azure"
	description="Aprenda mais sobre a biblioteca do IoTHubClient no SDK do dispositivo IoT do Azure para C"
	services="iot-hub"
	documentationCenter=""
	authors="MichelBarnett"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/29/2015"
     ms.author="michelb"/>

# SDK do dispositivo IoT do Microsoft Azure para C — mais sobre o IoTHubClient

O [primeiro artigo](iot-hub-device-sdk-c-intro.md) desta série apresentou o **SDK do dispositivo IoT do Microsoft Azure para C**. Neste artigo, foi explicado que há duas camadas de arquitetura no SDK. Na base está a biblioteca **IoTHubClient**, que gerencia a comunicação direta com o Hub IoT. E há a biblioteca do **serializador**, que é criada sobre a outra para fornecer serviços de serialização. Neste artigo, forneceremos detalhes adicionais sobre a biblioteca **IoTHubClient**.

O artigo anterior descreveu como usar a biblioteca **IoTHubClient** para enviar eventos ao Hub IoT e receber mensagens. Neste artigo, estenderemos esta discussão explicando como gerenciar mais precisamente *quando* enviar e receber dados apresentando a você as **APIs de nível inferior**. Também vamos explicar como anexar propriedades aos eventos (e recuperá-las de mensagens) usando os recursos de tratamento de propriedade na biblioteca **IoTHubClient**. Em seguida, vamos fornecer uma explicação adicional sobre as diferentes formas de lidar com mensagens recebidas do Hub IoT.

O artigo será encerrado com a abordagem de diversos tópicos, incluindo mais sobre as credenciais do dispositivo e como alterar o comportamento da **IoTHubClient** por meio de opções de configuração.

Vamos usar os exemplos do SDK da **IoTHubClient** para explicar esses tópicos. Desse modo, se quiser acompanhar, verifique os aplicativos **iothub\_client\_sample\_http** e **iothub\_client\_sample\_amqp** que estão incluídos no SDK do dispositivo IoT do Azure para C. Tudo o que está descrito abaixo é demonstrado nesses exemplos.

## As APIs de nível inferior

No artigo anterior, descrevemos a operação básica da **IotHubClient** no contexto do aplicativo **iothub\_client\_sample\_amqp**. Por exemplo, explicamos como inicializar a biblioteca usando este código...

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

...como enviar eventos usando esta chamada de função...

```
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

… também descrevemos como receber mensagens registrando uma função de retorno de chamada...

```
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

… e como liberar recursos usando código como este...

```
IoTHubClient_Destroy(iotHubClientHandle);
```

No entanto, há funções complementares para cada uma dessas APIs:

-   IoTHubClient\_LL\_CreateFromConnectionString

-   IoTHubClient\_LL\_SendEventAsync

-   IoTHubClient\_LL\_SetMessageCallback

-   IoTHubClient\_LL\_Destroy


Todas essas funções incluem "LL" no nome da API. Além disso, os parâmetros de cada uma dessas funções são idênticos aos de seus equivalentes não LL. No entanto, o comportamento dessas funções tem uma diferença importante...

Quando você chama **IoTHubClient\_CreateFromConnectionString**, as bibliotecas subjacentes criam um novo thread que é executado em segundo plano. Esse thread trata do envio de eventos para o Hub IoT e do recebimento de mensagens do Hub IoT. Nenhum thread é criado quando se trabalha com as APIs "LL". A criação do thread de segundo plano é uma conveniência para o desenvolvedor. Você não precisa se preocupar em enviar eventos explicitamente e receber mensagens do Hub IoT — isso acontece automaticamente em segundo plano. Em contrapartida, as APIs "LL" dão a você controle explícito sobre a comunicação com o Hub IoT, caso você precise.

Para entender isso melhor, vamos ver um exemplo:

Quando você chama **IoTHubClient\_SendEventAsync**, na verdade está colocando o evento em um buffer. O thread em segundo plano criado quando você chama **IoTHubClient\_CreateFromConnectionString** monitora continuamente esse buffer e envia todos os dados que ele contém ao Hub IoT. Isso acontece em segundo plano ao mesmo tempo que o thread principal está executando outro trabalho.

Da mesma forma, quando você registra uma função de retorno de chamada para mensagens usando **IoTHubClient\_SetMessageCallback**, você está instruindo ao SDK para que o thread em segundo plano invoque a função de retorno de chamada quando uma mensagem for recebida, independentemente do thread principal.

As APIs "LL" não criam um thread em segundo plano. Em vez disso, uma nova API deve ser chamada para enviar e receber dados explicitamente do Hub IoT. Mais uma vez, vamos dar uma olhada em um exemplo...

O aplicativo **iothub\_client\_sample\_http** que está incluído no SDK mostra as APIs de nível inferior. Nesse exemplo, enviamos eventos ao Hub IoT com código como este:

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

As três primeiras linhas criam a mensagem e a última linha envia o evento. No entanto, como mencionado acima, "enviar" o evento significa que os dados são simplesmente colocados em um buffer. Nada está sendo transmitido na rede quando chamamos **IoTHubClient\_LL\_SendEventAsync**. Para inserir os dados de fato no Hub IoT, você precisará chamar **IoTHubClient\_LL\_DoWork**, como neste exemplo:

```
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1000);
}
```

Este trecho de código (do aplicativo **iothub\_client\_sample\_http**) chama repetidamente **IoTHubClient\_LL\_DoWork**. Toda vez que **IoTHubClient\_LL\_DoWork** é chamado, ele envia alguns eventos do buffer para o Hub IoT e recupera uma mensagem enfileirada que está sendo enviada ao dispositivo. No último caso, isso significa que se registramos uma função de retorno de chamada para mensagens, o retorno de chamada será invocado (supondo que todas as mensagens estejam enfileiradas). Teríamos registrado uma função de retorno de chamada com código como este:

```
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

O motivo pelo qual **IoTHubClient\_LL\_DoWork** muitas vezes é chamado em um loop é porque cada vez que ele é chamado, ele envia *alguns* eventos em buffer ao Hub IoT e recupera *a próxima* mensagem enfileirada para o dispositivo. Não há garantia de que cada chamada envie todos os eventos em buffer ou recupere todas as mensagens enfileiradas. Se quiser enviar todos os eventos no buffer e continuar com outro processamento, você poderá substituir o loop acima por um código como este:

```
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1000);
}
```

Essa lógica chama **IoTHubClient\_LL\_DoWork** até que todos os eventos no buffer tenham sido enviados ao Hub IoT. Observe que isso também não significa que todas as mensagens enfileiradas tenham sido recebidas. Isso porque, em parte, verificar "todas" as mensagens não é uma ação determinista — o que acontece se você recuperar "todas" as mensagens, mas outra for enviada ao dispositivo logo depois? Uma maneira melhor de lidar com isso é com um tempo limite programado. Por exemplo, a função de retorno de chamada de mensagem pode redefinir um timer sempre que for invocada. E, assim, você pode escrever a lógica para continuar processando se, por exemplo, nenhuma mensagem tiver sido recebida nos últimos X segundos.

Quando você tiver acabado de inserir os eventos e receber mensagens, certifique-se de que chamar a função correspondente para limpar os recursos.

```
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Isso é tudo. Basicamente, há um conjunto de APIs para enviar e receber dados com um thread em segundo plano e outro conjunto de APIs que faz a mesma coisa sem o thread em segundo plano. Muitos desenvolvedores podem preferir as APIs não LL, mas as APIs de nível inferior são úteis quando o desenvolvedor desejar controle explícito sobre transmissões de rede. Por exemplo, alguns dispositivos coletarão dados ao longo do tempo e inserir eventos apenas em intervalos especificados (por exemplo, de hora em hora ou uma vez por dia). As APIs de nível inferior permitem controlar explicitamente quando você envia e recebe dados do Hub IoT. Outros simplesmente vão preferir a simplicidade fornecida pelas APIs de nível inferior — tudo acontece no thread principal, enquanto algum trabalho está acontecendo em segundo plano.

Seja qual for o modelo escolhido, certifique-se de ser consistente em relação às APIs que usa. Se começar chamando **IoTHubClient\_LL\_CreateFromConnectionString**, esteja certo de usar apenas as APIs de nível inferior correspondentes para qualquer trabalho de acompanhamento:

-   IoTHubClient\_LL\_SendEventAsync

-   IoTHubClient\_LL\_SetMessageCallback

-   IoTHubClient\_LL\_Destroy

-   IoTHubClient\_LL\_DoWork

Assim, o contrário também é verdadeiro... se você começar com **IoTHubClient\_CreateFromConnectionString**, mantenha as APIs não LL para qualquer processamento adicional.

No SDK do dispositivo IoT do Azure para C, verifique o aplicativo **iothub\_client\_sample\_http** em busca de um exemplo completo das APIs de nível inferior. O aplicativo **iothub\_client\_sample\_amqp** pode ser referenciado para um exemplo completo das APIs não LL.

## Manipulação de propriedades

Até agora, quando descrevemos o envio de dados, nos referimos ao corpo da mensagem. Por exemplo, considere este trecho de código:

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Este exemplo envia uma mensagem ao Hub IoT com o texto "Hello World". Mas o Hub IoT também permite que as propriedades sejam anexadas a cada mensagem. As propriedades são apenas pares de valor nomeados que podem ser anexados à mensagem. Por exemplo, podemos modificar o código acima para anexar uma propriedade à mensagem:

```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Começamos chamando **IoTHubMessage\_Properties** e passando-o ao identificador da nossa mensagem. O que obtemos é uma referência de **MAP\_HANDLE** que nos permite começar adicionando propriedades. O último é realizado chamando **Map\_AddOrUpdate**, que usa uma referência para uma MAP\_HANDLE, o nome da propriedade, e o valor da propriedade. Com essa API, podemos adicionar quantas propriedades quisermos.

Quando o evento é lido no **Hub de Eventos**, o receptor pode enumerar as propriedades e recuperar seus valores correspondentes. Por exemplo, no .NET isso seria conseguido acessando a [Coleção de propriedades no objeto EventData](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.properties.aspx).

No exemplo acima, estamos anexando propriedades a um evento que enviamos ao Hub IoT. Mas as propriedades também podem ser anexadas a mensagens recebidas do Hub IoT. Se quisermos recuperar propriedades de uma mensagem, podemos usar código como este em nossa função de retorno de chamada de mensagem:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .

    // Retrieve properties from the message
    MAP_HANDLE mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                printf("Message Properties:\r\n");
                for (size_t index = 0; index < propertyCount; index++)
                {
                    printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                printf("\r\n");
            }
        }
    }

    . . .
}
```

A chamada a **IoTHubMessage\_Properties** nos dá nossa referência **MAP\_HANDLE**. Em seguida, passamos essa referência para **Map\_GetInternals** a fim de obter uma referência para uma matriz de pares de nome/valor (bem como uma contagem das propriedades). Nesse momento, é uma questão de enumerar as propriedades para obter os valores que queremos.

Não é preciso usar propriedades em seu aplicativo. Mas se precisar defini-las em eventos ou recuperá-las de mensagens, a biblioteca **IoTHubClient** tornará essa tarefa bastante fácil.

## Manipulação de mensagens

Anteriormente foi explicado que quando as mensagens chegam do Hub IoT, a biblioteca **IoTHubClient** responde invocando uma função de retorno de chamada registrada. Mas há um parâmetro de retorno dessa função que merece uma explicação adicional. Veja um trecho da função de retorno de chamada no aplicativo de exemplo **iothub\_client\_sample\_http**:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
	. . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Observe que o tipo de retorno é **IOTHUBMESSAGE\_DISPOSITION\_RESULT** e, nesse caso específico, retornamos **IOTHUBMESSAGE\_ACCEPTED**. Há outros valores que podemos retornar dessa função que mudam como a biblioteca **IoTHubClient** reage ao retorno de chamada de mensagem. Veja as opções.

-   **IOTHUBMESSAGE\_ACCEPTED**: a mensagem foi processada com êxito. A biblioteca **IoTHubClient** não invocará a função de retorno de chamada novamente com a mesma mensagem.

-   **IOTHUBMESSAGE\_REJECTED**: a mensagem não foi processada e não há intenção de fazer isso no futuro. A biblioteca **IoTHubClient** não deve invocar a função de retorno de chamada novamente com a mesma mensagem.

-   **IOTHUBMESSAGE\_ABANDONED**: a mensagem não foi processada com êxito. Mas a biblioteca **IoTHubClient** deve invocar a função de retorno de chamada novamente com a mesma mensagem.

Para os dois primeiros códigos de retorno, a biblioteca **IoTHubClient** envia uma mensagem ao Hub IoT indicando que a mensagem deve ser excluída da fila do dispositivo e não entregue novamente. O efeito líquido é o mesmo (a mensagem será excluída da fila do dispositivo), mas se a mensagem foi aceita ou rejeitada, ela ainda será registrada. O registro dessa distinção é útil para os remetentes da mensagem, que podem ouvir comentários e descobrir se um dispositivo aceitou ou rejeitou uma mensagem específica.

No último caso, uma mensagem também é enviada ao Hub IoT, mas ela indica que a mensagem deve ser entregue novamente. Geralmente, você abandonará uma mensagem se encontrar algum erro, mas é conveniente tentar processar a mensagem novamente. Em contrapartida, rejeitar uma mensagem é apropriado quando você encontrar um erro irrecuperável (ou se você simplesmente decidir que não quer processar a mensagem).

Em qualquer caso, esteja ciente dos diferentes códigos de retorno para que você possa extrair o comportamento que deseja da biblioteca **IoTHubClient**.

## Credenciais de dispositivo alternativas

Como explicado anteriormente, a primeira coisa que precisamos fazer ao trabalhar com a biblioteca **IoTHubClient** é obter um **IOTHUB\_CLIENT\_HANDLE** com uma chamada como esta:

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Basicamente, os argumentos de **IoTHubClient\_CreateFromConnectionString** são a cadeia de conexão do nosso dispositivo e um parâmetro que indica o protocolo que usaremos para nos comunicar com o Hub IoT. A cadeia de conexão tem um formato parecido com este:

> HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY

Basicamente, há quatro partes de informações aqui: nome do Hub IoT, sufixo do Hub IoT, id do dispositivo e tecla de acesso compartilhado. Você obtém o FQDN (nome de domínio totalmente qualificado) de um Hub IoT quando cria a instância do Hub IoT no Portal de Gerenciamento do Azure — isso fornece o nome do Hub IoT (a primeira parte do FQDN) e o sufixo do Hub IoT (o restante do FQDN). Obtenha a Id do dispositivo e a tecla de acesso compartilhado ao registrar seu dispositivo no Hub IoT (como descrito no [artigo anterior](iot-hub-device-sdk-c-intro.md)).

**IoTHubClient\_CreateFromConnectionString** oferece uma maneira de inicializar a biblioteca. Mas se preferir, você pode criar um novo **IOTHUB\_CLIENT\_HANDLE** usando esses parâmetros individuais em vez da cadeia de conexão. Isso é feito com o seguinte código:

```
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

Ele faz a mesma coisa que **IoTHubClient\_CreateFromConnectionString**.

Pode parecer óbvio que você queira usar **IoTHubClient\_CreateFromConnectionString** no lugar desse método mais detalhado de inicialização. Mas tenha em mente que, ao registrar um dispositivo no Hub IoT, o que obtemos é uma id de dispositivo e uma chave do dispositivo (e não uma cadeia de conexão). A ferramenta SDK do **Gerenciador de Dispositivos** que apresentei no [artigo anterior](iot-hub-device-sdk-c-intro.md) usa bibliotecas no **SDK do serviço IoT do Azure** para criar a cadeia de conexão a partir da id do dispositivo, da chave do dispositivo e do nome de host do Hub IoT. Dessa forma, chamar **IoTHubClient\_LL\_Create** pode ser preferível porque economia a etapa de gerar uma cadeia de conexão. Use o método que for conveniente.

## Opções de configuração

Até agora, tudo que foi descrito sobre como a biblioteca **IoTHubClient** funciona reflete seu comportamento padrão. Mas há algumas opções que você pode definir para alterar como ela funciona. Isso pode ser feito aproveitando a API **IoTHubClient\_LL\_SetOption**. Considere este exemplo:

```
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

Há duas opções que normalmente são usadas:

-   **SetBatching** (booliano): se for true, os dados enviados ao Hub IoT serão enviados em lote. Se for false, as mensagens serão enviadas individualmente. O padrão é false.

-   **Timeout** (inteiro não atribuído): esse valor é representado em milissegundos. Se o envio de uma solicitação HTTP ou o recebimento de uma resposta demorar mais que esse tempo, o tempo limite da conexão se esgotará.

É importante conhecer a opção de envio em lote. Por padrão, a biblioteca insere eventos individualmente (um evento único é tudo o que você passar para **IoTHubClient\_LL\_SendEventAsync**). Mas se a opção de envio em lote for true, a biblioteca coletará o máximo de eventos que puder do buffer (até o tamanho máximo de mensagem que o Hub IoT aceitar). O lote do evento é enviado ao Hub IoT em uma única chamada de HTTP (os eventos individuais são agrupados em uma matriz JSON). A ativação do envio em lote geralmente resulta em grandes ganhos de desempenho, uma vez que você está reduzindo as viagens de ida e volta da rede. E ela reduz significativamente a largura de banda, uma vez que você está enviando um conjunto de cabeçalhos HTTP com um lote de evento, em vez de um conjunto de cabeçalhos para cada evento individual. A menos que você tenha um motivo específico para fazer o contrário, o comum é ativar o envio em lote.

## Próximas etapas

Este artigo detalha o comportamento da biblioteca **IoTHubClient** encontrada no **SDK do dispositivo IoT do Azure para C**. Com essas informações, você deve ter uma boa compreensão dos recursos da biblioteca **IoTHubClient**. No [próximo artigo](iot-hub-device-sdk-c-serializer.md), forneceremos detalhes semelhantes sobre a biblioteca do **serializador**.

<!---HONumber=Oct15_HO4-->