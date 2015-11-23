<properties
	pageTitle="SDK do dispositivo IoT do Microsoft Azure para C – Serializador | Microsoft Azure"
	description="Aprenda mais sobre a biblioteca do Serializador no SDK do dispositivo IoT do Azure para C"
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
     ms.date="11/10/2015"
     ms.author="michelb"/>

# SDK do dispositivo IoT do Microsoft Azure para C – mais informações sobre o serializador

O [primeiro artigo](iot-hub-device-sdk-c-intro.md) desta série apresentou o **SDK do dispositivo IoT do Azure para C**. O próximo artigo fornece uma descrição mais detalhada sobre o [**IoTHubClient**](iot-hub-device-sdk-c-iothubclient.md). Este artigo conclui a abordagem do SDK com uma descrição mais detalhada do componente restante: a biblioteca do **serializador**.

O artigo de introdução descreve como usar a biblioteca do **serializador** para enviar eventos ao Hub IoT e receber mensagens dele. Neste artigo, estenderemos a discussão por meio de uma explicação mais completa de como modelar seus dados com a linguagem de macro do **serializador**. O artigo também inclui mais detalhes sobre como a biblioteca serializa as mensagens (e, em alguns casos, como você pode controlar o comportamento de serialização). Descreveremos alguns parâmetros que você poderá modificar e que determinam o tamanho dos modelos criados por você.

Por fim, o artigo revê alguns tópicos abordados em artigos anteriores, como a manipulação de mensagens e de propriedades. Como veremos, esses recursos funcionam da mesma forma ao usar a biblioteca do **serializador** e a biblioteca **IoTHubClient**.

Todo o conteúdo do artigo baseia-se nas amostras do SDK do **serializador**. Se você quiser acompanhar, consulte os aplicativos **simplesample\_amqp** e **simplesample\_http** incluídos no SDK do dispositivo IoT do Azure para C.

## A linguagem de modelagem

O [artigo de introdução](iot-hub-device-sdk-c-intro.md) desta série apresentou a linguagem de modelagem do **SDK do dispositivo IoT do Azure para o C** por meio do exemplo fornecido no aplicativo **simplesample\_amqp**:

```
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(double, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

Como você pode ver, a linguagem de modelagem baseia-se em macros do C. Sempre comece sua definição com **BEGIN\_NAMESPACE** e sempre termine com **END\_NAMESPACE**. É comum nomear o namespace de sua empresa ou, como acontece neste exemplo, o projeto no qual você está trabalhando.

O que ocorre dentro do namespace são definições de modelo. Neste caso, há um único modelo para um anemômetro. Novamente, o modelo pode receber qualquer nome, mas geralmente é nomeado de acordo com o dispositivo ou o tipo de dados que você deseja trocar com Hub IoT.

Os modelos contêm uma definição dos eventos que podem ser inseridos no Hub IoT (os *dados*), bem como as mensagens que você pode receber do Hub IoT (as *ações*). Como você pode ver no exemplo, os eventos apresentam um tipo e um nome; as ações apresentam um nome e parâmetros opcionais (cada um deles com um tipo).

O que não foi demonstrado neste exemplo são os tipos de dados adicionais que recebem suporte do SDK. Abordaremos isso na sequência.

> [AZURE.NOTE]O Hub IoT chama os dados enviados por um dispositivo de *eventos*, enquanto a linguagem de modelagem os chama de *dados* (definidos por meio de **WITH\_DATA**). Da mesma forma, o Hub IoT chama os dados enviados por você aos dispositivos de *mensagens*, enquanto a linguagem de modelagem os chama de *ações* (definidas por meio de **WITH\_ACTION**). Saiba que esses termos podem ser usados de forma intercambiável neste artigo.

### Tipos de dados com suporte

Os tipos de dados a seguir têm suporte em modelos criados com a biblioteca do **serializador**:

| Tipo | Descrição |
|-------------------------|----------------------------------------|
| double | número de ponto flutuante de precisão dupla |
| int | inteiro de 32 bits |
| flutuante | número de ponto flutuante de precisão única |
| longo | inteiro longo |
| int8\_t | inteiro de 8 bits |
| int16\_t | inteiro de 16 bits |
| int32\_t | inteiro de 32 bits |
| int64\_t | inteiro de 64 bits |
| bool | Booleano |
| ascii\_char\_ptr | Cadeia de caracteres ASCII |
| EDM\_DATE\_TIME\_OFFSET | diferença de data e horário |
| EDM\_GUID | GUID |
| EDM\_BINARY | binário |
| DECLARE\_STRUCT | tipo de dados complexo |

Vamos começar com o último tipo de dados. **DECLARE\_STRUCT** permite que você defina tipos de dados complexos, que são agrupamentos dos outros tipos primitivos. Esses agrupamentos nos permitem definir um modelo com a seguinte aparência:

```
DECLARE_STRUCT(TestType,
double, aDouble,
int, aInt,
float, aFloat,
long, aLong,
int8_t, aInt8,
uint8_t, auInt8,
int16_t, aInt16,
int32_t, aInt32,
int64_t, aInt64,
bool, aBool,
ascii_char_ptr, aAsciiCharPtr,
EDM_DATE_TIME_OFFSET, aDateTimeOffset,
EDM_GUID, aGuid,
EDM_BINARY, aBinary
);

DECLARE_MODEL(TestModel,
WITH_DATA(TestType, Test)
);
```

Nosso modelo contém um único evento de dados do tipo **TestType**. **TestType** é um tipo complexo que inclui vários membros, que demonstram coletivamente os tipos primitivos com suporte da linguagem de modelagem do **serializador**.

Com um modelo assim, podemos escrever um código parecido com o seguinte para enviar dados ao Hub IoT:

```
TestModel* testModel = CREATE_MODEL_INSTANCE(MyThermostat, TestModel);

testModel->Test.aDouble = 1.1;
testModel->Test.aInt = 2;
testModel->Test.aFloat = 3.0f;
testModel->Test.aLong = 4;
testModel->Test.aInt8 = 5;
testModel->Test.auInt8 = 6;
testModel->Test.aInt16 = 7;
testModel->Test.aInt32 = 8;
testModel->Test.aInt64 = 9;
testModel->Test.aBool = true;
testModel->Test.aAsciiCharPtr = "ascii string 1";

time_t now;
time(&now);
testModel->Test.aDateTimeOffset = GetDateTimeOffset(now);

EDM_GUID guid = { { 0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09, 0x0A, 0x0B, 0x0C, 0x0D, 0x0E, 0x0F } };
testModel->Test.aGuid = guid;

unsigned char binaryArray[3] = { 0x01, 0x02, 0x03 };
EDM_BINARY binaryData = { sizeof(binaryArray), &binaryArray };
testModel->Test.aBinary = binaryData;

SendAsync(iotHubClientHandle, (const void*)&(testModel->Test));
```

Basicamente, estamos atribuindo um valor a cada membro da estrutura **Test** e chamando **SendAsync** a fim de enviar os eventos **Test** para a nuvem. **SendAsync** é uma função auxiliar que envia um único evento de dados ao Hub IoT:

```
void SendAsync(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const void *dataEvent)
{
	unsigned char* destination;
	size_t destinationSize;
	if (SERIALIZE(&destination, &destinationSize, *(const unsigned char*)dataEvent) ==
	{
		// null terminate the string
		char* destinationAsString = (char*)malloc(destinationSize + 1);
		if (destinationAsString != NULL)
		{
			memcpy(destinationAsString, destination, destinationSize);
			destinationAsString[destinationSize] = '\0';
			IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromString(destinationAsString);
			if (messageHandle != NULL)
			{
				IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)0);

				IoTHubMessage_Destroy(messageHandle);
			}
			free(destinationAsString);
		}
		free(destination);
	}
}
```

Essa função serializa o evento de dados específico e o envia ao Hub IoT usando **IoTHubClient\_SendEventAsync**. Esse é o mesmo código que analisamos nos artigos anteriores (**SendAsync** encapsula a lógica em uma função conveniente).

Outra função auxiliar usada no código anterior é **GetDateTimeOffset**. Essa função transforma o horário especificado em um valor do tipo **EDM\_DATE\_TIME\_OFFSET**:

```
EDM_DATE_TIME_OFFSET GetDateTimeOffset(time_t time)
{
	struct tm newTime;
	gmtime_s(&newTime, &time);
	EDM_DATE_TIME_OFFSET dateTimeOffset;
	dateTimeOffset.dateTime = newTime;
	dateTimeOffset.fractionalSecond = 0;
	dateTimeOffset.hasFractionalSecond = 0;
	dateTimeOffset.hasTimeZone = 0;
	dateTimeOffset.timeZoneHour = 0;
	dateTimeOffset.timeZoneMinute = 0;
	return dateTimeOffset;
}
```

Se você executar esse código, a seguinte mensagem será enviada ao Hub IoT:

```
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

Observe que a serialização é JSON, que é o formato gerado pela biblioteca do **serializador**. Perceba também que cada membro do objeto JSON serializado corresponde os membros de **TestType** que definimos em nosso modelo. Os valores também correspondem exatamente aos valores utilizados no código. No entanto, observe que os dados binários são codificados com base64; "AQID" é a codificação base64 de {0x01, 0x02, 0x03}.

Esse exemplo demonstra a vantagem de usar a biblioteca do **serializador**. Ela nos permite enviar JSON para a nuvem, sem precisar lidar explicitamente com a serialização em nosso aplicativo. Só precisamos nos preocupar com a configuração dos valores dos eventos de dados em nosso modelo e chamar APIs simples para enviar esses eventos para a nuvem.

Com essas informações, podemos definir os modelos que incluem o intervalo de tipos de dados com suporte, incluindo tipos complexos (poderíamos incluir até mesmo tipos complexos dentro de outros tipos complexos). No entanto, o JSON serializado gerado pelo exemplo acima apresenta um ponto importante. O modo *como* enviamos dados com a biblioteca do **serializador** determina exatamente como o JSON é formado. Abordaremos esse ponto específico na sequência.

## Mais informações sobre a serialização

A seção anterior realça um exemplo de saída gerada pela biblioteca do **serializador**. Nesta seção, explicaremos como a biblioteca serializa os dados e como você pode controlar esse comportamento usando as APIs de serialização.

Para avançarmos na discussão sobre serialização, trabalharemos com um novo modelo baseado em um termostato. Primeiro, veja algumas informações básicas sobre o cenário que estamos tentando abordar.

Queremos modelar um termostato que mede a temperatura e a umidade. Cada parte dos dados é enviada ao Hub IoT de um modo diferente. Por padrão, o termostato recebe um evento de temperatura a cada 2 minutos e um evento de umidade é inserido a cada 15 minutos. Quando um evento for inserido, ele deverá incluir um carimbo de data e hora que indica o horário de medição da temperatura ou umidade correspondente.

Com base nesse cenário, demonstraremos duas maneiras diferentes de modelar os dados, e explicaremos o efeito da modelagem sobre a saída serializada.

### Modelo 1

Esta é a primeira versão de um modelo compatível com o cenário anterior:

```
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(TemperatureEvent,
int, Temperature,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_STRUCT(HumidityEvent,
int, Humidity,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureEvent, Temperature),
WITH_DATA(HumidityEvent, Humidity)
);

END_NAMESPACE(Contoso);
```

Observe que o modelo inclui dois eventos de dados: **Temperatura** e **Umidade**. Ao contrário dos exemplos anteriores, o tipo de cada evento é uma estrutura definida usando **DECLARE\_STRUCT**. **TemperatureEvent** inclui uma medição de temperatura e um carimbo de data e hora; **HumidityEvent** contém uma medição de umidade e um carimbo de data e hora. Esse modelo nos proporciona uma forma natural de modelar os dados para o cenário descrito acima. Quando enviamos um evento à nuvem, enviamos um par de temperatura/carimbo de data e hora ou um par de umidade/carimbo de data e hora.

Podemos enviar um evento de temperatura à nuvem usando um código como o seguinte:

```
time_t now;
time(&now);
thermostat->Temperature.Temperature = 75;
thermostat->Temperature.Time = GetDateTimeOffset(now);

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Usaremos valores codificados para temperatura e umidade no exemplo de código, mas imagine que estamos realmente recuperando esses valores por meio de uma amostragem dos sensores correspondentes no termostato.

O código acima usa o auxiliar **GetDateTimeOffset** apresentado anteriormente. Por motivos que esclareceremos mais tarde, esse código separa de forma explícita a tarefa de serialização e envio do evento. O código anterior serializa o evento de temperatura em um buffer. Na sequência, **sendMessage** é uma função auxiliar (incluída no **simplesample\_amqp**) que envia o evento ao Hub IoT:

```
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId);

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
}
```

Esse código é um subconjunto do auxiliar **SendAsync** descrito na seção anterior, portanto, não falaremos novamente sobre isso nesta seção.

Quando executamos o código anterior a fim de enviar o evento Temperatura, essa forma serializada do evento é enviada ao Hub IoT:

```
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Estamos enviando uma temperatura que pertence ao tipo **TemperatureEvent**, e essa estrutura contém um membro **Temperatura** e outro **Hora**. Isso reflete diretamente nos dados serializados.

Da mesma forma, podemos enviar um evento de umidade com este código:

```
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

A forma serializada enviada ao Hub IoT tem esta aparência:

```
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Novamente, tudo conforme o esperado.

Com esse modelo, você pode imaginar como outros eventos podem ser facilmente adicionados. Defina mais estruturas usando **DECLARE\_STRUCT** e inclua o evento correspondente no modelo usando **WITH\_DATA**.

Agora, vamos modificar o modelo para que ele inclua os mesmos dados, mas com uma estrutura diferente.

### Modelo 2

Compare este modelo alternativo com o que acabamos de mostrar:

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Neste caso, eliminamos as macros **DECLARE\_STRUCT** e simplesmente definimos os itens de dados de nosso cenário usando tipos simples da linguagem de modelagem.

Por ora, vamos ignorar o evento **Hora**. Com isso posto de lado, este é o código para inserir **Temperatura**:

```
time_t now;
time(&now);
thermostat->Temperature = 75;

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Esse código envia o seguinte evento serializado ao Hub IoT:

```
{"Temperature":75}
```

E o código para enviar o evento Umidade parece com o seguinte:

```
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Este código envia o seguinte para o Hub IoT:

```
{"Humidity":45}
```

Até o momento, ainda não há surpresas. Agora, vamos alterar o modo como usamos a macro SERIALIZE.

A macro **SERIALIZE** pode receber vários eventos de dados como argumentos. Isso nos permite serializar os eventos **Temperatura** e **Umidade** juntos e enviá-los ao Hub IoT em uma chamada:

```
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Você pode imaginar que o resultado desse código é o envio de dois eventos de dados ao Hub IoT:

[

{"Temperatura":75},

{"Umidade":45}

]

Em outras palavras, você pode esperar que esse código é o mesmo que enviar **Temperatura** e **Umidade** separadamente. Ele é apenas uma conveniência para enviar os dois eventos para **SERIALIZE** na mesma chamada. No entanto, esse não é o caso. Em vez disso, o código acima envia este evento de dados único ao Hub IoT:

{"Temperatura":75, "Umidade":45}

Isso pode parecer estranho, pois nosso modelo define **Temperatura** e **Umidade** como dois eventos *separados*:

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Mais especificamente, não modelamos esses eventos nos quais a **Temperatura** e a **Umidade** estão na mesma estrutura:

```
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

Se tivéssemos usado esse modelo, seria mais fácil entender como a **Temperatura** e a **Umidade** seriam enviadas na mesma mensagem serializada. Entretanto, talvez não esteja tão claro o motivo disso funcionar dessa maneira quando você passa os dois eventos de dados para **SERIALIZE** usando o modelo 2.

É mais fácil entender esse comportamento se você souber quais são as suposições feitas pela biblioteca do **serializador**. Para entendermos tudo isso, vamos voltar ao nosso modelo:

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Pense nesse modelo em termos orientados ao objeto. Neste caso, estamos modelando um dispositivo físico (um termostato), e esse dispositivo inclui atributos como **Temperatura** e **Umidade**.

Podemos enviar todo o estado de nosso modelo com um código como este:

```
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Supondo que os valores de Temperatura, Umidade e Hora estivessem definidos, veríamos um evento como este sendo enviado ao Hub IoT:

```
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Às vezes, você pode querer apenas enviar *algumas* propriedades do modelo para a nuvem (principalmente se o modelo contiver uma grande quantidade de eventos de dados). Será útil enviar apenas um subconjunto de eventos de dados, como em nosso exemplo anterior:

```
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Isso gera exatamente o mesmo evento serializado que a definição de um **TemperatureEvent** com os membros **Temperatura** e **Hora**, como fizemos com o modelo 1. Nesse caso, pudemos gerar exatamente o mesmo evento serializado com um modelo diferente (modelo 2), pois chamamos **SERIALIZE** de uma maneira diferente.

O mais importante é que, se você passar vários eventos de dados para **SERIALIZE**, ele assumirá que cada evento é uma propriedade em um único objeto JSON.

A melhor abordagem dependerá da sua escolha e de como você pensa a respeito de seu modelo. Se você estiver enviando “eventos” para a nuvem e cada evento contiver um conjunto definido de propriedades, a primeira abordagem é apropriada. Nesse caso, você usaria **DECLARE\_STRUCT** para definir a estrutura de cada evento e os incluiria em seu modelo com a macro **WITH\_DATA**. Em seguida, enviaria cada evento, como fizemos no primeiro exemplo acima. Nessa abordagem, você só passaria um único evento de dados ao **SERIALIZER**.

Se você pensar em seu modelo de forma orientada ao objeto, a segunda abordagem pode ser mais adequada. Nesse caso, os elementos definidos usando **WITH\_DATA** são as "propriedades" do objeto. Você passa qualquer subconjunto de eventos que queira para **SERIALIZE**, dependendo de quanto do estado de seu "objeto" você deseja enviar para a nuvem.

Nenhuma abordagem é certa ou errada. Esteja apenas ciente de como a biblioteca do **serializador** funciona, e escolha a abordagem de modelagem que melhor atenda à sua necessidade.

## Manipulação de mensagens

Até o momento, este artigo discutiu apenas o envio de eventos ao Hub IoT e não abordou o recebimento das mensagens. O motivo para isso é que o que precisamos saber a respeito do recebimento de mensagens foi amplamente discutido em um [artigo anterior](iot-hub-device-sdk-c-intro.md). O artigo mostrava o processamento de mensagens por meio do registro de uma função de retorno de chamada da mensagem:

```
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

Em seguida, você grava a função de retorno de chamada que é invocada quando uma mensagem é recebida:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
            memcpy(temp, buffer, size);
            temp[size] = '\0';
            EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
            result =
                (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
                (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
                IOTHUBMESSAGE_REJECTED;
            free(temp);
        }
    }
    return result;
}
```

Essa implementação do **IoTHubMessage** chama a função específica para cada ação no seu modelo. Por exemplo, se o seu modelo definir esta ação:

```
WITH_ACTION(SetAirResistance, int, Position)
```

Você deverá definir uma função com esta assinatura:

```
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

**SetAirResistance** é chamado quando a mensagem é enviada ao dispositivo.

O que ainda não explicamos é a aparência da versão serializada da mensagem. Em outras palavras, se você quiser enviar uma mensagem **SetAirResistance** ao seu dispositivo, qual será a aparência dela?

Se você estiver enviando uma mensagem para um dispositivo, o fará por meio do SDK do serviço IoT do Azure. Você ainda precisa saber qual cadeia de caracteres deve enviar para invocar uma ação específica. O formato geral para enviar uma mensagem se parece com o seguinte:

```
{"Name" : "", "Parameters" : "" }
```

Você está enviando um objeto JSON serializado com duas propriedades: **Name** é o nome da ação (mensagem) e **Parameters** contém os parâmetros da ação.

Por exemplo, para invocar **SetAirResistance**, você pode enviar essa mensagem para um dispositivo:

```
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

O nome da ação deve corresponder exatamente a uma ação definida em seu modelo. Os nomes de parâmetro também devem corresponder. Observe também a diferenciação de maiúsculas e minúsculas. **Name** e **Parameters** devem estar sempre com a primeira letra maiúscula. Não deixe de manter o mesmo uso de maiúsculas e minúsculas do nome e dos parâmetros da ação em seu modelo. Neste exemplo, o nome da ação é "SetAirResistance" e não "setairresistance".

Esta seção descreve tudo o que você precisa saber ao enviar eventos e receber mensagens com a biblioteca do **serializador**. Antes de continuarmos, vamos abordar alguns parâmetros que você pode configurar e que controlam o tamanho de seu modelo.

## Configuração de macro

Se você estiver usando a biblioteca do **serializador** há uma parte importante do SDK no seguinte local:

```
.\\c\\common\\tools\\macro\_utils\_h\_generator.
```

Essa pasta contém uma solução do Visual Studio chamada **macro\_utils\_h\_generator.sln**:

  ![](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.PNG)

O programa nesta solução gera o arquivo **macro\_utils.h**, localizado no diretório .\\c\\common\\inc. Há um arquivo macro\_utils.h padrão incluído com o SDK. Essa solução permite que você modifique alguns parâmetros e, em seguida, recrie o arquivo de cabeçalho com base nesses parâmetros.

Os dois principais parâmetros com os quais você precisa se preocupar são **nArithmetic** e **nMacroParameters**, que são definidos nestas duas linhas do macro\_utils.tt:

```
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>

```

Esses valores são os parâmetros padrão incluídos com o SDK. Cada parâmetro tem o seguinte significado:

-   nMacroParameters – Controla a quantidade de parâmetros que você pode ter em uma definição de macro DECLARE\_MODEL.

-   nArithmetic – Controla o número total de membros permitidos em um modelo.

O motivo pelo qual que esses parâmetros são importantes é porque eles controlam o tamanho do modelo. Por exemplo, veja esta definição de modelo:

```
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

Conforme mencionado anteriormente, **DECLARE\_MODEL** é apenas uma macro de C. O nome do modelo e a instrução **WITH\_DATA** (outra macro) são parâmetros de **DECLARE\_MODEL**. **nMacroParameters** define quantos parâmetros podem ser incluídos em **DECLARE\_MODEL**. Efetivamente, isso define quantos eventos de dados e declarações de ação você pode ter. Dessa forma, com o limite padrão de 124, isso significa que você pode definir um modelo com uma combinação de cerca de 60 ações e eventos de dados. Se você tentar exceder esse limite, receberá erros do compilador parecidos com o seguinte:

  ![](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.PNG)

O parâmetro **nArithmetic** tem mais a ver com o funcionamento interno da linguagem de macro do que com seu aplicativo. Ele controla o número total de membros que você pode ter em seu modelo, incluindo macros **DECLARE\_STRUCT**. Portanto, se você começar a ver erros do compilador como esse, tente aumentar **nArithmetic**:

   ![](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.PNG)

Se quiser alterar esses parâmetros, modifique os valores no arquivo macro\_utils.tt, recompile a solução macro\_utils\_h\_generator.sln e execute o programa compilado. Quando você faz isso, um novo arquivo macro\_utils.h é gerado e colocado no diretório .\\common\\inc.

Para usar a nova versão de macro\_utils.h, remova o pacote NuGet **serializador** da sua solução e, em seu lugar, inclua o projeto **serializador** do Visual Studio. Isso permite que seu código seja compilado contra o código-fonte da biblioteca do serializador. Isso inclui a macro\_utils.h atualizada. Para fazer isso para **simplesample\_amqp**, comece movendo o pacote NuGet para a biblioteca do serializador da solução:

   ![](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.PNG)

Em seguida, adicione esse projeto à sua solução do Visual Studio:

> .\\c\\serializer\\build\\windows\\serializer.vcxproj

Quando terminar, sua solução deve ter esta aparência:

   ![](media/iot-hub-device-sdk-c-serializer/05-serializer-project.PNG)

Agora, quando você compila a solução, a macro\_utils.h atualizada é incluída no seu binário.

Observe que aumentar demais esses valores pode exceder os limites do compilador. Nesse ponto, **nMacroParameters** é o principal parâmetro com o qual se preocupar. A especificação C99 estipula a permissão de no mínimo 127 parâmetros em uma definição de macro. O compilador da Microsoft segue exatamente a especificação (e tem um limite de 127), portanto, não será possível aumentar **nMacroParameters** além do padrão. Outros compiladores podem permitir que você faça isso (por exemplo, o compilador GNU dá suporte a um limite superior).

Até o momento, abordamos quase tudo o que você precisa saber sobre como escrever um código com a biblioteca do **serializador**. Antes da conclusão, vamos rever alguns tópicos dos artigos anteriores sobre os quais você pode estar se perguntando.

## As APIs de nível inferior

O exemplo de aplicativo no qual este artigo se concentra é **simplesample\_amqp**. Esse exemplo usa as APIs de nível superior (não "LL") para enviar eventos e receber mensagens. Se você usar essas APIs, haverá um thread em execução em segundo plano que cuida dos eventos de envio e recebimento de mensagens. No entanto, é possível usar as APIs de nível inferior (LL) para eliminar esse thread em segundo plano e assumir o controle explícito ao enviar eventos ou receber mensagens da nuvem.

Conforme descrevemos em um [artigo anterior](iot-hub-device-sdk-c-iothubclient.md), há um conjunto de funções compostas por APIs de nível superior:

-   IoTHubClient\_CreateFromConnectionString

-   IoTHubClient\_SendEventAsync

-   IoTHubClient\_SetMessageCallback

-   IoTHubClient\_Destroy

Essas APIs são demonstradas em **simplesample\_amqp**.

Há também um conjunto semelhante de APIs de nível inferior.

-   IoTHubClient\_LL\_CreateFromConnectionString

-   IoTHubClient\_LL\_SendEventAsync

-   IoTHubClient\_LL\_SetMessageCallback

-   IoTHubClient\_LL\_Destroy

Observe que as APIs de nível inferior funcionam exatamente como descrevemos nos artigos anteriores. Use o primeiro conjunto de APIs se quiser que um thread em segundo plano lide com eventos de envio e recebimento de mensagens. Use o segundo conjunto de APIs se quiser ter controle explícito sobre o momento de envio e recebimento de dados do Hub IoT. O conjunto de APIs funciona igualmente bem com a biblioteca do **serializador**.

Para obter um exemplo de como as APIs de nível inferior são usadas com a biblioteca do **serializador**, consulte o aplicativo **simplesample\_http**.

## Tópicos adicionais

Outros tópicos que vale a pena mencionar novamente são relacionados ao tratamento de propriedades, ao uso de credenciais alternativas de dispositivo e às opções de configuração. Todos estes tópicos foram abordados em um [artigo anterior](iot-hub-device-sdk-c-iothubclient.md). O principal aqui é que todos esses recursos funcionam da mesma forma quando você usa a biblioteca do **serializador** e a biblioteca **IoTHubClient**. Por exemplo, se você quiser associar propriedades a um evento a partir de seu modelo, use **IoTHubMessage\_Properties** e **Map**\_**AddorUpdate** da mesma maneira descrita anteriormente:

```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Não importa se o evento foi gerado a partir da biblioteca **serializadora** ou se foi criado manualmente usando a biblioteca **IoTHubClient**.

Com relação às credenciais alternativas de dispositivo, o uso de **IoTHubClient\_LL\_Create** funciona tão bem quanto **IoTHubClient\_CreateFromConnectionString** para alocar uma **IOTHUB\_CLIENT\_HANDLE**.

Por fim, se você estiver usando a biblioteca do **serializador**, poderá definir opções de configuração com **IoTHubClient\_LL\_SetOption** da mesma forma que você faz ao usar a biblioteca **IoTHubClient**.

Um recurso exclusivo da biblioteca do **serializador** são as APIs de inicialização. Antes de começar a trabalhar com a biblioteca, é necessário chamar **serializer\_init**:

```
serializer_init(NULL);
```

Isso é feito antes de chamar **IoTHubClient\_CreateFromConnectionString**.

Da mesma forma, ao terminar de trabalhar com a biblioteca, a última chamada que você fará é **serializer\_deinit**:

```
serializer_deinit();
```

Caso contrário, todos os outros recursos listados acima funcionarão da mesma forma na biblioteca **serializadora** e na biblioteca **IoTHubClient**. Para saber mais sobre qualquer um desses tópicos, consulte o [artigo anterior](iot-hub-device-sdk-c-iothubclient.md) desta série.

## Próximas etapas

Este artigo apresenta detalhes sobre os aspectos exclusivos da biblioteca do **serializador** contida no **SDK do dispositivo IoT do Azure para C**. Com base nas informações fornecidas, você deverá ter uma boa compreensão de como usar modelos para enviar eventos e receber mensagens do Hub IoT.

Isso também conclui a série de três partes sobre como desenvolver aplicativos com o **SDK do dispositivo IoT do Azure para C**. Essas informações devem ser suficientes para começar, mas também proporcionam um entendimento muito detalhado do funcionamento das APIs. Para saber mais, há alguns exemplos no SDK não abordados aqui. Caso contrário, a [documentação do SDK](https://github.com/Azure/azure-iot-sdks) é um ótimo recurso para saber mais.

<!---HONumber=Nov15_HO3-->