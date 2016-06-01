<properties
	pageTitle="Usar o SDK do dispositivo IoT do Microsoft Azure para C | Microsoft Azure"
	description="Saiba mais sobre como começar a trabalhar com o exemplo de código no SDK do dispositivo IoT do Azure para C."
	services="iot-hub"
	documentationCenter=""
	authors="olivierbloch"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="03/29/2016"
     ms.author="obloch"/>

# Introdução ao SDK do dispositivo IoT do Azure para C

O **SDK do dispositivo IoT do Azure** é um conjunto de bibliotecas projetadas para simplificar o processo de envio de eventos e o recebimento de mensagens do serviço **Hub IoT do Azure**. Existem diferentes variações do SDK, cada uma visando uma plataforma específica, mas este artigo descreve o **SDK do dispositivo IoT do Azure para C**.

O SDK do dispositivo IoT do Azure para C foi escrito em ANSI (C99) para maximizar a portabilidade. Isso o torna bastante adequado para a operação em vários dispositivos e plataformas, especialmente quando a minimização do volume de disco e de memória for uma prioridade.

Há uma ampla variedade de plataformas nas quais o SDK foi testado (confira a [lista de plataformas e compatibilidade](iot-hub-tested-configurations.md) para obter detalhes). Embora este artigo inclua explicações de exemplo de código em execução na plataforma Windows, lembre-se de que o código descrito neste artigo é exatamente o mesmo entre as várias plataformas com suporte.

Neste artigo, você será apresentado à arquitetura do SDK do dispositivo IoT do Azure para C. Demonstraremos como inicializar a biblioteca de dispositivos, enviar eventos ao Hub IoT, bem como receber mensagens dele. As informações neste artigo devem ser suficientes para começar a usar o SDK, mas também há indicações para se saber mais sobre as bibliotecas.

>> [AZURE.NOTE] Este artigo inclui informações sobre como usar os recursos de *gerenciamento de dispositivo* das bibliotecas C no SDK. Para saber como usar os recursos de gerenciamento de dispositivos, confira [Apresentação da biblioteca de gerenciamento de dispositivos do Hub IoT do Azure para C](iot-hub-device-management-library.md).

## Arquitetura do SDK

Você pode encontrar o **SDK do dispositivo IoT do Azure para C** no seguinte repositório do GitHub:

[azure-iot-sdks](https://github.com/Azure/azure-iot-sdks)

A versão mais recente das bibliotecas pode ser encontrada na ramificação **mestre** deste repositório:

  ![](media/iot-hub-device-sdk-c-intro/01-MasterBranch.PNG)

Esse repositório contém a família inteira de SDKs do dispositivo IoT do Azure. No entanto, este artigo é sobre o SDK do dispositivo IoT do Azure *para C*, que pode ser encontrado na pasta **c**.

  ![](media/iot-hub-device-sdk-c-intro/02-CFolder.PNG)

* A implementação principal do SDK pode ser encontrada na pasta **iothub\_client** que contém a implementação da camada de API mais baixa no SDK: a biblioteca **IoTHubClient**. A biblioteca **IoTHubClient** contém APIs que implementam mensagens brutas para enviar mensagens para o Hub IoT, além de receber mensagens dele. Ao usar essa biblioteca, você é responsável por implementar a serialização de mensagens (eventualmente usando a amostra de serializador descrita abaixo), mas outros detalhes da comunicação com o Hub IoT são tratados para você.
* A pasta **serializador** contém funções auxiliares e exemplos que mostram como serializar os dados antes de enviar para o Hub IoT do Azure usando a biblioteca de cliente. Observe que o uso do serializador não é obrigatório e só é fornecido como uma conveniência. Ao usar a biblioteca **serializer**, você começa definindo um modelo que especifica os eventos que deseja enviar ao Hub IoT, bem como as mensagens que espera receber dele. Depois que o modelo é definido, o SDK fornece uma superfície de API que permite que você trabalhe facilmente com eventos e mensagens sem ter que se preocupar com detalhes de serialização. A biblioteca depende de outras bibliotecas de código-fonte aberto que implementam o transporte usando vários protocolos (AMQP, MQTT).
* A biblioteca **IoTHubClient** depende de outras bibliotecas de código-fonte aberto:
   * A biblioteca de [utilitários compartilhados do Azure C](https://github.com/Azure/azure-c-shared-utility) que fornece funcionalidades comuns para tarefas básicas (como cadeia de caracteres, manipulação de listas, E/S, etc.) necessárias entre vários SDKs para C relacionados ao Azure
   * A biblioteca [uAMQP do Azure](https://github.com/Azure/azure-uamqp-c) é a implementação do lado do cliente do AMQP otimizada para dispositivos com restrição de recursos.
   * A biblioteca [uMQTT Azure](https://github.com/Azure/azure-umqtt-c) é uma biblioteca de finalidade geral, com o protocolo MQTT implementado e otimizada para dispositivos com restrição de recursos.

Mas é mais fácil entender tudo isso examinando exemplos de código. As seções a seguir explicam alguns exemplos de aplicativo incluídos no SDK. Isso deve dar uma boa ideia dos vários recursos das camadas de arquitetura do SDK, bem como uma introdução ao funcionamento da API.

## Antes de executar os exemplos

Antes de executar os exemplos no SDK do dispositivo IoT do Azure para C, você deverá criar uma instância do serviço em sua assinatura do Azure, se ainda não tiver uma, e concluir duas tarefas:
* preparar o seu ambiente de desenvolvimento
* obter credenciais do dispositivo.

Se precisar criar uma instância do Hub IoT do Azure em sua assinatura do Azure, siga as instruções [aqui](https://github.com/Azure/azure-iot-sdks/blob/master/doc/setup_iothub.md).

O [arquivo Leiame](https://github.com/Azure/azure-iot-sdks/tree/master/c) incluído com o SDK fornece instruções para preparar seu ambiente de desenvolvimento e obter credenciais de dispositivo. As seções a seguir incluem alguns comentários adicionais sobre essas instruções.

### Preparando o ambiente de desenvolvimento

Enquanto os pacotes são fornecidos para algumas plataformas (como o NuGet para Windows ou apt\_get para Debian e Ubuntu) e os exemplos usam esses pacotes quando estiverem disponíveis, as instruções abaixo detalham como criar a biblioteca e os exemplos diretamente do código.

Em primeiro lugar, será necessário obter uma cópia do SDK no GitHub e criar a origem. Você deve obter uma cópia da origem na ramificação **master** do [repositório GitHub](https://github.com/Azure/azure-iot-sdks).

Quando você tiver baixado uma cópia da origem, deve concluir as etapas descritas no artigo do SDK ["Preparar o seu ambiente de desenvolvimento"](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md).


Eis algumas dicas que ajudam você a concluir o procedimento descrito no guia de preparação:

-   Quando você instalar o utilitário **CMake**, escolha a opção para adicionar o **CMake** ao sistema PATH para **todos os usuários** (a adição ao **usuário atual** também funciona):

  ![](media/iot-hub-device-sdk-c-intro/08-CMake.PNG)


-   Antes de abrir o **Prompt de Comando do Desenvolvedor para VS2015**, instale as Ferramentas de linha de comando Git. Para instalar essas ferramentas, conclua as seguintes etapas:

	1. Inicie o programa de instalação do **Visual Studio 2015** (ou escolha **Microsoft Visual Studio 2015** no painel de controle **Programas e Recursos** e selecione **Alterar**).
	
	2. Verifique se o recurso **Git para Windows** está selecionado no instalador, mas é interessante também marcar a opção **Extensão GitHub para Visual Studio** para fornecer a integração IDE:

  		![](media/iot-hub-device-sdk-c-intro/10-GitTools.PNG)

	3. Conclua o assistente de instalação para instalar as ferramentas.

	4. Adicione o diretório **bin** das ferramentas Git à variável de ambiente **PATH** do sistema. No Windows, isso se parece com o seguinte:

  		![](media/iot-hub-device-sdk-c-intro/11-GitToolsPath.PNG)


Após concluir todas as etapas descritas na página ["Preparar seu ambiente de desenvolvimento"](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md), você estará pronto para compilar os aplicativos de exemplo.

### Obtendo credenciais do dispositivo

Agora que o seu ambiente de desenvolvimento está configurado, a última coisa a fazer será obter um conjunto de credenciais de dispositivo. Para um dispositivo poder acessar um Hub IoT, primeiro você deverá adicionar o dispositivo ao Registro de dispositivo do Hub IoT. Ao adicionar o dispositivo, você terá um conjunto de credenciais de dispositivo de que precisará para que o dispositivo possa se conectar a um Hub IoT. Os aplicativos de exemplo que veremos na próxima seção esperam essas credenciais na forma de uma **cadeia de conexão de dispositivo**.

São fornecidas algumas ferramentas no repositório de código-fonte aberto do SDK para ajudar a gerenciar o Hub IoT. Um é um aplicativo do Windows chamado Gerenciador de Dispositivos, o segundo é uma ferramenta CLI baseada em Node.js para várias plataformas chamada iothub-explorer. Você pode aprender mais sobre essas ferramentas [aqui](https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md).

Ao executarmos os exemplos do Windows neste artigo, usamos a ferramenta Gerenciador de Dispositivos. Mas também é possível usar o iothub-explorer se preferir ferramentas CLI.

A ferramenta [Gerenciador de Dispositivos](https://github.com/Azure/azure-iot-sdks/tree/master/tools/DeviceExplorer) usa as bibliotecas de serviço IoT do Azure para executar várias funções no Hub IoT, incluindo a adição de dispositivos. Se você usar o Gerenciador de Dispositivos para adicionar um dispositivo, você obterá uma cadeia de conexão correspondente. Você precisa dessa cadeia de conexão para executar os aplicativos de exemplo.

Caso você não esteja familiarizado com o processo, o procedimento a seguir descreve como usar o Gerenciador de Dispositivos para adicionar um dispositivo e obter uma cadeia de conexão de dispositivo.

Você pode encontrar um instalador do Windows para a ferramenta Device Explorer na [página de versão do SDK](https://github.com/Azure/azure-iot-sdks/releases). No entanto, você também pode executar a ferramenta diretamente do código abrindo **[DeviceExplorer.sln](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/DeviceExplorer.sln)** no **Visual Studio 2015** e compilando a solução.

Ao executar o programa, você verá esta interface:

  ![](media/iot-hub-device-sdk-c-intro/03-DeviceExplorer.PNG)

Insira a **Cadeia de Conexão do Hub IoT** no primeiro campo e clique em **Atualizar**. Isso configura a ferramenta para que ela possa se comunicar com o Hub IoT.

Depois que a cadeia de conexão do Hub IoT for configurada, clique na guia **Gerenciamento**:

  ![](media/iot-hub-device-sdk-c-intro/04-ManagementTab.PNG)

Esse é o local onde você vai gerenciar os dispositivos registrados no seu Hub IoT.

Você pode criar um dispositivo clicando no botão **Criar**. Uma caixa de diálogo é exibida com um conjunto de chaves pré-populadas (primárias e secundárias). Basta inserir uma **ID de Dispositivo** e clicar em **Criar**.

  ![](media/iot-hub-device-sdk-c-intro/05-CreateDevice.PNG)

Depois que o dispositivo for criado, a lista Dispositivos será atualizada com todos os dispositivos registrados, incluindo o que você acabou de criar. Se você clicar com o botão direito do mouse no novo dispositivo, este menu será exibido:

  ![](media/iot-hub-device-sdk-c-intro/06-RightClickDevice.PNG)

Se você escolher a opção **Copiar cadeia de conexão para o dispositivo selecionado**, a cadeia de conexão do seu dispositivo será copiada para a área de transferência. Manter uma cópia da cadeia de conexão. Você precisará dela ao executar os aplicativos de exemplo descritos nas seções a seguir.

Após concluir as etapas acima, você estará pronto para começar a executar códigos. Ambos os exemplos têm uma constante na parte superior do arquivo de origem principal que permite inserir uma cadeia de conexão. Por exemplo, a linha correspondente do aplicativo **iothub\_client\_sample\_amqp** aparece da forma a seguir.

```
static const char* connectionString = "[device connection string]";
```

Se quiser acompanhar, insira sua cadeia de conexão de dispositivo aqui, recompile a solução e o exemplo poderia ser executado.

## IoTHubClient

Na pasta **iothub\_client** no repositório azure-iot-sdks, há uma pasta **samples** que contém um aplicativo chamado **iothub\_client\_sample\_amqp**.

A versão do Windows do aplicativo **iothub\_client\_sample\_ampq** inclui a seguinte solução do Visual Studio:

  ![](media/iot-hub-device-sdk-c-intro/12-iothub-client-sample-amqp.PNG)

Essa solução contém um único projeto. Mas vale a pena observar que há quatro pacotes NuGet instalados na solução:

  ![](media/iot-hub-device-sdk-c-intro/17-iothub-client-sample-amqp-githubpackages.PNG)

Você sempre precisará do pacote **Microsoft.Azure.C.SharedUtility** quando estiver trabalhando com o SDK. Como esse exemplo se baseia no AMQP, você também deve incluir os pacotes **Microsoft.Azure.uamqp** e **Microsoft.Azure.IoTHub.AmqpTransport** (há pacotes equivalentes para HTTP e MQTT). Como o exemplo usa a biblioteca **IoTHubClient**, você também deve incluir o pacote **Microsoft.Azure.IoTHub.IoTHubClient** em sua solução.

Você pode encontrar a implementação para o exemplo de aplicativo no arquivo de origem **iothub\_client\_sample\_amqp.c**.

Usaremos esse exemplo de aplicativo para explicar o que é necessário para usar a biblioteca **IoTHubClient**.

### Inicializando a biblioteca

> [AZURE.NOTE] Antes de começar a trabalhar com as bibliotecas, talvez seja necessário executar alguma inicialização específica de plataforma. Por exemplo, se você planeja usar o AMQPS no Linux, você deverá inicializar a biblioteca OpenSSL. Os exemplos de [repositório GitHub](https://github.com/Azure/azure-iot-sdks) chamam a função de utilitário **platform\_init** quando o cliente inicia e chamam a função **platform\_deinit** antes de encerrar. Essas funções são declaradas no arquivo de cabeçalho "platform.h". Você deve examinar as definições dessas funções para sua plataforma de destino no [repositório](https://github.com/Azure/azure-iot-sdks) para determinar se é necessário incluir qualquer código de inicialização de plataforma no seu cliente.

Para começar a trabalhar com as bibliotecas, você deve primeiro alocar um identificador de cliente do Hub IoT:

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Observe que estamos passando uma cópia da nossa cadeia de conexão de dispositivo para essa função (aquela que obtivemos do Gerenciador de Dispositivos). Nós também designamos o protocolo que queremos usar. Este exemplo usa AMQP, mas MQTT e HTTP também são uma opção.

Quando você tiver um **IOTHUB\_CLIENT\_HANDLE** válido, será possível iniciar a chamada a APIs para enviar eventos ao Hub IoT e receber mensagens dele. Vamos analisar isso a seguir.

### Enviando eventos

O envio de eventos para o Hub IoT exige que você conclua as seguintes etapas:

Em primeiro lugar, crie uma mensagem:

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_Over_AMQP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText);
```

Em seguida, envie a mensagem:

```
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

Sempre que você enviar uma mensagem, será possível especificar uma referência a uma função de retorno de chamada que é invocada quando os dados são enviados:

```
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %d with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```

Observe a chamada à função **IoTHubMessage\_Destroy** quando terminar com a mensagem. Você optar por isso para liberar os recursos alocados quando criou a mensagem.

### Recebendo mensagens

O recebimento de uma mensagem é uma operação assíncrona. Primeiro, você registra um retorno de chamada a ser invocado quando o dispositivo recebe uma mensagem:

```
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

O último parâmetro é um ponteiro nulo para o que você quiser. No exemplo, é um ponteiro para um inteiro, mas poderia ser um ponteiro para uma estrutura de dados mais complexa. Isso permite que a função de retorno de chamada opere em estado compartilhado com o chamador dessa função.

Quando o dispositivo receber uma mensagem, a função de retorno de chamada registrada será invocada:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    int* counter = (int*)userContextCallback;
    const char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, (const unsigned char**)&buffer, &size) == IOTHUB_MESSAGE_OK)
    {
        (void)printf("Received Message [%d] with Data: <<<%.*s>>> & Size=%d\r\n", *counter, (int)size, buffer, (int)size);
    }

    /* Some device specific action code goes here... */
    (*counter)++;
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Observe que você usa a função **IoTHubMessage\_GetByteArray** para recuperar a mensagem, que, neste exemplo, é uma cadeia de caracteres.

### Cancelando a inicialização da biblioteca

Quando acabar de enviar eventos e de receber mensagens, você poderá cancelar a inicialização da biblioteca IoT. Para fazer isso, emita a seguinte chamada de função:

```
IoTHubClient_Destroy(iotHubClientHandle);
```

Isso libera os recursos anteriormente alocados pela função **IoTHubClient\_CreateFromConnectionString**.

Como você pode ver, é fácil enviar eventos e receber mensagens com a biblioteca **IoTHubClient**. A biblioteca lida com os detalhes de comunicação com o Hub IoT, incluindo o protocolo a ser usado (da perspectiva do desenvolvedor, isso é uma opção de configuração simples).

A biblioteca **IoTHubClient** também fornece controle preciso sobre como serializar os eventos que o seu dispositivo envia ao Hub IoT. Em alguns casos, essa é uma vantagem, mas em outros, é um detalhe de implementação com o qual você não quer se preocupar. Se for esse o caso, você poderá considerar usar a biblioteca **serializer**, que descreveremos na seção a seguir.

## Serializer

Conceitualmente, a biblioteca **serializer** fica acima da biblioteca **IoTHubClient** no SDK. Ela usa a biblioteca **IoTHubClient** para a comunicação subjacente com o Hub IoT, mas adiciona recursos de modelagem que removem a sobrecarga de lidar com a serialização de mensagens do desenvolvedor. O funcionamento dessa biblioteca é mais bem demonstrado por um exemplo.

Na pasta **serializer** do repositório azure-iot-sdks, há uma pasta **samples** que contém um aplicativo chamado **simplesample\_amqp**. A versão para Windows deste exemplo inclui a seguinte solução do Visual Studio:

  ![](media/iot-hub-device-sdk-c-intro/14-simplesample_amqp.PNG)

Assim como no exemplo anterior, este inclui vários pacotes NuGet:

  ![](media/iot-hub-device-sdk-c-intro/18-simplesample_amqp-githubpackages.PNG)

Já vimos a maior parte disso no exemplo anterior, mas **Microsoft.Azure.IoTHub.Serializer** é novo. Isso é necessário quando usamos a biblioteca **serializer**.

A implementação do aplicativo de exemplo pode ser encontrada no arquivo **simplesample\_amqp.c**.

As seções a seguir explicam as principais partes desse exemplo.

### Inicializando a biblioteca

Para começar a trabalhar com a biblioteca **serializer**, você deverá chamar as APIs de inicialização:

```
serializer_init(NULL);

IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);

ContosoAnemometer* myWeather = CREATE_MODEL_INSTANCE(WeatherStation, ContosoAnemometer);
```

A chamada à função **serializer\_init** é uma chamada única e é usada para inicializar a biblioteca subjacente. Então, você chama a função **IoTHubClient\_CreateFromConnectionString**, que é a mesma API do exemplo **IoTHubClient**. Essa chamada define a cadeia de conexão de dispositivo (também é onde você escolhe o protocolo que quer usar). Observe que este exemplo usa o AMQP como transporte, mas poderia ter usado o HTTP.

Por fim, chame a função **CREATE\_MODEL\_INSTANCE**. Observe que **WeatherStation** é o namespace do modelo e **ContosoAnemometer** é o nome do modelo. Depois que a instância do modelo for definida, você poderá usá-lo para começar a enviar eventos e a receber mensagens. No entanto, em primeiro lugar, é importante entender o que é um modelo.

### Definindo o modelo

Um modelo na biblioteca **serializer** define os eventos que seu dispositivo pode enviar ao Hub IoT e as mensagens, chamadas de *ações* na linguagem de modelagem, que ele pode receber. Defina um modelo usando um conjunto de macros C, como no exemplo de aplicativo **simplesample\_amqp**:

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

As macros **BEGIN\_NAMESPACE** e **END\_NAMESPACE** usam o namespace do modelo como um argumento. Espera-se que qualquer coisa entre essas macros seja a definição de seus modelos e as estruturas de dados que os modelos usam.

Neste exemplo, há um único modelo chamado **ContosoAnemometer**. Esse modelo define dois eventos que o dispositivo pode enviar ao Hub IoT: **DeviceId** e **WindSpeed**. Ele também define três ações (mensagens) que o dispositivo pode receber: **TurnFanOn**, **TurnFanOff** e **SetAirResistance**. Cada evento tem um tipo, e cada ação tem um nome (e, opcionalmente, um conjunto de parâmetros).

Os eventos e ações definidos no modelo definem uma superfície de API que você pode usar para enviar eventos ao Hub IoT, bem como para responder a mensagens que estão sendo enviadas ao dispositivo. Isso é mais bem entendido por meio de um exemplo.

### Enviando eventos

O modelo define os eventos que você pode enviar ao Hub IoT. Neste exemplo, isso significa um dos dois eventos definidos usando a macro **WITH\_DATA**. Por exemplo, se você quer enviar um evento **WindSpeed** a um Hub IoT, há algumas etapas envolvidas para que isso aconteça. A primeira é definir os dados que deseja enviar:

```
myWeather->WindSpeed = 15;
```

O modelo que definimos anteriormente nos permite fazer isso definindo um membro de um **struct**. Em seguida, serializamos o evento que queremos enviar:

```
unsigned char* destination;
size_t destinationSize;

SERIALIZE(&destination, &destinationSize, myWeather->WindSpeed);
```

Esse código serializa o evento em um buffer (conhecido como **destination**). Por fim, enviaremos o evento ao Hub IoT com este código:

```
sendMessage(iotHubClientHandle, destination, destinationSize);
```

Essa é uma função auxiliar no exemplo de aplicativo que envia nosso evento serializado ao Hub IoT:

```
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        if (IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId) != IOTHUB_CLIENT_OK)
        {
            printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
            printf("IoTHubClient accepted the message for delivery\r\n");
        }

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
    messageTrackingId++;
}
```

Esse código é muito semelhante ao que vimos no aplicativo **iothub\_client\_sample\_amqp**, em que criamos uma mensagem de uma matriz de bytes e usamos **IoTHubClient\_SendEventAsync** para enviá-la ao Hub IoT. Depois disso, temos apenas que liberar a identificação da mensagem e o buffer de dados serializados que alocamos anteriormente.

Do segundo ao último parâmetro de **IoTHubClient\_SendEventAsync** é uma referência a uma função de retorno de chamada que é chamada quando os dados são enviados com êxito. Veja o exemplo de uma função de retorno de chamada:

```
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    int messageTrackingId = (intptr_t)userContextCallback;

    (void)printf("Message Id: %d Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

O segundo parâmetro é um ponteiro para contexto do usuário, o mesmo ponteiro que passamos para **IoTHubClient\_SendEventAsync**. Nesse caso, esse contexto é um contador simples, mas ele pode ser o que você quiser.

E isso é tudo sobre o envio de eventos. Agora nos resta falar sobre o recebimento de mensagens.

### Recebendo mensagens

O recebimento de uma mensagem funciona de forma semelhante a como as mensagens funcionam na biblioteca **IoTHubClient**. Primeiramente, você registra uma função de retorno de chamada de mensagem:

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

Esse código é clichê, é o mesmo para qualquer solução. Essa função recebe a mensagem e cuida de encaminhá-la à função apropriada por meio da chamada a **EXECUTE\_COMMAND**. A função chamada nesse ponto depende da definição das ações em nosso modelo.

Ao definir uma ação em seu modelo, você precisa implementar uma função que é chamada quando o dispositivo recebe a mensagem correspondente. Por exemplo, se o seu modelo definir esta ação:

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

Observe que o nome da função corresponde ao nome da ação no modelo e que os parâmetros da função correspondem aos parâmetros especificados para a ação. O primeiro parâmetro é sempre obrigatório e contém um ponteiro para a instância do nosso modelo.

Quando o dispositivo recebe uma mensagem que corresponde a essa assinatura, a função correspondente é chamada. Portanto, além de ter que incluir o código clichê de **IoTHubMessage**, receber mensagens será apenas uma questão de definir uma função simples para cada ação definida no seu modelo.

### Cancelando a inicialização da biblioteca

Quando acabar de enviar dados e de receber mensagens, você poderá cancelar a inicialização da biblioteca IoT:

```
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

Cada uma dessas três funções se alinha às três funções de inicialização descritas anteriormente. Chamar essas APIs garante a liberação dos recursos alocados anteriormente.

## Próximas etapas

Este artigo abordou os conceitos básicos de como usar as bibliotecas no **SDK do dispositivo IoT do Azure para C**. Ele forneceu informações suficientes para entender o que está incluído no SDK, sua arquitetura e como começar a trabalhar com os exemplos do Windows. O próximo artigo continua a descrição do SDK, explicando [mais sobre a biblioteca IoTHubClient](iot-hub-device-sdk-c-iothubclient.md).

Para saber como usar os recursos de gerenciamento de dispositivos no **SDK do dispositivo IoT do Azure para C**, confira [Apresentação da biblioteca de gerenciamento de dispositivos do Hub IoT do Azure para C](iot-hub-device-management-library.md).

<!---HONumber=AcomDC_0518_2016-->