## Saída típica
Veja abaixo um exemplo da saída gravada no arquivo de log pela amostra do Hello World. Caracteres de Nova linha e Guia foram adicionados para legibilidade:

```
[{
    "time": "Mon Apr 11 13:48:07 2016",
    "content": "Log started"
}, {
    "time": "Mon Apr 11 13:48:48 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:48:55 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:49:01 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:49:04 2016",
    "content": "Log stopped"
}]
```

## Trechos de código
Esta seção aborda algumas partes fundamentais do código da amostra do Hello World.

### Criação do gateway
O desenvolvedor deve gravar o *processo de gateway*. Este programa cria a infraestrutura interna (o agente), carrega os módulos e configura tudo para funcionar corretamente. O SDK fornece a função **Gateway\_Create\_From\_JSON** para que você possa inicializar um gateway em um arquivo JSON. Para usar a função **Gateway\_Create\_From\_JSON**, é necessário transmitir a ela o caminho para um arquivo JSON que especifica os módulos a serem carregados.

É possível encontrar o código para o processo de gateway na amostra do Hello World no arquivo [main.c][lnk-main-c]. Para legibilidade, o trecho de código abaixo mostra uma versão abreviada do código do processo de gateway. Este programa cria um gateway e aguarda até que o usuário pressione a tecla **ENTER** antes de eliminar o gateway.

```
int main(int argc, char** argv)
{
    GATEWAY_HANDLE gateway;
    if ((gateway = Gateway_Create_From_JSON(argv[1])) == NULL)
    {
        printf("failed to create the gateway from JSON\n");
    }
    else
    {
        printf("gateway successfully created from JSON\n");
        printf("gateway shall run until ENTER is pressed\n");
        (void)getchar();
        Gateway_LL_Destroy(gateway);
    }
    return 0;
}
```

O arquivo de configurações do JSON contém uma lista dos módulos a serem carregados. Cada módulo deve especificar um:

* **module\_name**: um nome exclusivo para o módulo.
* **module\_path**: o caminho para a biblioteca que contém o módulo. Para o Linux, é um arquivo .so, no Windows, é um arquivo .dll.
* **args**: todas as informações de configuração de que o módulo precisa.

O arquivo JSON também contém os links entre os módulos que serão transmitidos para o agente. Um link tem duas propriedades:

* **source**: o nome de um módulo da seção `modules` ou "*".
* **sink**: o nome de um módulo da seção `modules`

Cada link define uma rota e uma direção para as mensagens. Mensagens do módulo `source` devem ser entregues ao módulo `sink`. O `source` pode ser definido como "*", indicando que as mensagens de qualquer módulo serão recebidas pelo `sink`.

A amostra a seguir apresenta o arquivo de configurações do JSON usado para configurar a amostra do Hello World no Linux. Cada mensagem produzida pelo módulo `hello_world` será consumida pelo módulo `logger`. A necessidade de um argumento por um módulo dependerá do design do módulo. Neste exemplo, o módulo do agente usa um argumento que é o caminho para o arquivo de saída e o módulo do Hello World não usa nenhum argumento:

```
{
    "modules" :
    [ 
        {
            "module name" : "logger",
            "module path" : "./modules/logger/liblogger_hl.so",
            "args" : {"filename":"log.txt"}
        },
        {
            "module name" : "hello_world",
            "module path" : "./modules/hello_world/libhello_world_hl.so",
            "args" : null
        }
    ],
    "links" :
    [
        {
            "source" : "hello_world",
            "sink" : "logger"
        }
    ]
}
```

### Publicação de mensagem do módulo do Hello World
É possível encontrar o código usado pelo módulo “hello world” para publicar mensagens no arquivo ['hello\_world.c'][lnk-helloworld-c]. O trecho de código abaixo mostra uma versão corrigida com comentários adicionais sem alguns códigos de tratamento de erro para legibilidade:

```
int helloWorldThread(void *param)
{
    // create data structures used in function.
    HELLOWORLD_HANDLE_DATA* handleData = param;
    MESSAGE_CONFIG msgConfig;
    MAP_HANDLE propertiesMap = Map_Create(NULL);

    // add a property named "helloWorld" with a value of "from Azure IoT
    // Gateway SDK simple sample!" to a set of message properties that
    // will be appended to the message before publishing it. 
    Map_AddOrUpdate(propertiesMap, "helloWorld", "from Azure IoT Gateway SDK simple sample!")

    // set the content for the message
    msgConfig.size = strlen(HELLOWORLD_MESSAGE);
    msgConfig.source = HELLOWORLD_MESSAGE;

    // set the properties for the message
    msgConfig.sourceProperties = propertiesMap;

    // create a message based on the msgConfig structure
    MESSAGE_HANDLE helloWorldMessage = Message_Create(&msgConfig);

    while (1)
    {
        if (handleData->stopThread)
        {
            (void)Unlock(handleData->lockHandle);
            break; /*gets out of the thread*/
        }
        else
        {
            // publish the message to the broker
            (void)Broker_Publish(handleData->brokerHandle, helloWorldMessage);
            (void)Unlock(handleData->lockHandle);
        }

        (void)ThreadAPI_Sleep(5000); /*every 5 seconds*/
    }

    Message_Destroy(helloWorldMessage);

    return 0;
}
```

### Processamento de mensagem do módulo do Hello World
O módulo do Hello World nunca precisa processar nenhuma mensagem publicada no agente por outros módulos. Isso torna a implementação do retorno de chamada de mensagem no módulo do Hello World uma função não operacional.

```
static void HelloWorld_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{
    /* No action, HelloWorld is not interested in any messages. */
}
```

### Publicação e processamento de mensagem de módulo do agente
O módulo do Agente recebe mensagens do agente e as grava em um arquivo. Ele nunca publica as mensagens. Portanto, o código do módulo do agente nunca chama a função **Broker\_Publish**.

A função **Logger\_Receive** no arquivo [logger.c][lnk-logger-c] é o retorno de chamada que invoca o agente para entregar mensagens ao módulo do agente. O trecho de código abaixo mostra uma versão corrigida com comentários adicionais sem alguns códigos de tratamento de erro para legibilidade:

```
static void Logger_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{

    time_t temp = time(NULL);
    struct tm* t = localtime(&temp);
    char timetemp[80] = { 0 };

    // Get the message properties from the message
    CONSTMAP_HANDLE originalProperties = Message_GetProperties(messageHandle); 
    MAP_HANDLE propertiesAsMap = ConstMap_CloneWriteable(originalProperties);

    // Convert the collection of properties into a JSON string
    STRING_HANDLE jsonProperties = Map_ToJSON(propertiesAsMap);

    //  base64 encode the message content
    const CONSTBUFFER * content = Message_GetContent(messageHandle);
    STRING_HANDLE contentAsJSON = Base64_Encode_Bytes(content->buffer, content->size);

    // Start the construction of the final string to be logged by adding
    // the timestamp
    STRING_HANDLE jsonToBeAppended = STRING_construct(",{"time":"");
    STRING_concat(jsonToBeAppended, timetemp);

    // Add the message properties
    STRING_concat(jsonToBeAppended, "","properties":"); 
    STRING_concat_with_STRING(jsonToBeAppended, jsonProperties);

    // Add the content
    STRING_concat(jsonToBeAppended, ","content":"");
    STRING_concat_with_STRING(jsonToBeAppended, contentAsJSON);
    STRING_concat(jsonToBeAppended, ""}]");

    // Write the formatted string
    LOGGER_HANDLE_DATA *handleData = (LOGGER_HANDLE_DATA *)moduleHandle;
    addJSONString(handleData->fout, STRING_c_str(jsonToBeAppended);
}
```

## Próximas etapas
Para saber mais sobre como usar o SDK do Gateway, veja o seguinte:

* [SDK do Gateway IoT - enviar mensagens do dispositivo para a nuvem com um dispositivo simulado usando o Linux][lnk-gateway-simulated].
* [SDK do Gateway IoT do Azure][lnk-gateway-sdk] no GitHub.

<!-- Links -->
[lnk-main-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/hello_world/src/main.c
[lnk-helloworld-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/hello_world/src/hello_world.c
[lnk-logger-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/logger/src/logger.c
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/
[lnk-gateway-simulated]: ../articles/iot-hub/iot-hub-linux-gateway-sdk-simulated-device.md

<!---HONumber=AcomDC_0928_2016-->