---
title: Monitorar um espaço com os Gêmeos Digitais do Azure | Microsoft Docs
description: Saiba como provisionar os recursos espaciais e monitorar as condições de trabalho com os Gêmeos Digitais do Azure usando as etapas deste tutorial.
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/26/2018
ms.author: dkshir
ms.openlocfilehash: cf45cb8de0e40dfe5f5772dcb1a0be2aa7585fd6
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50156673"
---
# <a name="tutorial-provision-your-building-and-monitor-working-conditions-with-azure-digital-twins"></a>Tutorial: Provisionar o prédio e monitorar as condições de trabalho com os Gêmeos Digitais do Azure

Este tutorial demonstra como usar os Gêmeos Digitais do Azure para monitorar as condições de temperatura e o nível de conforto desejados em seus espaços. Quando você tiver [configurado o prédio de exemplo](tutorial-facilities-setup.md), poderá provisionar o prédio e executar funções personalizadas em seus dados de sensor usando as etapas deste tutorial.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Definir condições a serem monitoradas
> * Criar uma função definida pelo usuário
> * Simular dados de sensor
> * Obter resultados da função definida pelo usuário

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que você [definiu a configuração dos Gêmeos Digitais do Azure](tutorial-facilities-setup.md). Antes de prosseguir, verifique se você tem:
- uma [conta do Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F);
- uma instância dos Gêmeos Digitais em execução; 
- as [amostras de C# dos Gêmeos Digitais](https://github.com/Azure-Samples/digital-twins-samples-csharp) baixadas e extraídas no computador de trabalho; 
- o [SDK do .NET Core versão 2.1.403 ou superior](https://www.microsoft.com/net/download) no computador de desenvolvimento para executar a amostra. Execute `dotnet --version` para verificar se a versão instalada é a correta. 
- Use o [Visual Studio Code](https://code.visualstudio.com/) para explorar o código de exemplo. 

## <a name="define-conditions-to-monitor"></a>Definir condições a serem monitoradas
Você pode definir um conjunto de condições específicas para monitorar dados de dispositivo ou sensor, chamados **Correspondências**. Em seguida, você pode definir funções chamadas *funções definidas pelo usuário*, que executam a lógica personalizada nos dados provenientes de seus espaços e dispositivos quando as condições especificadas pelas correspondências ocorrem. Para obter mais informações, leia o [Processamento de dados e funções definidas pelo usuário](concepts-user-defined-functions.md). 

No projeto de exemplo **_occupancy-quickstart_**, abra o arquivo **_src\actions\provisionSample.yaml_** no Visual Studio Code. Observe a seção que começa com o tipo de **matchers**. Cada entrada nesse tipo cria uma correspondência com um **Nome** especificado, que irá monitorar um sensor do tipo **dataTypeValue**. Observe como ela se relaciona com o espaço chamado *Sala de Foco A1*, que tem um nó **devices** contendo alguns **sensors**. Para provisionar uma correspondência que vai controlar um desses sensores, seu **dataTypeValue** deve corresponder ao **dataType** desse sensor. 

Adicione a correspondência a seguir embaixo das correspondências existentes, fazendo com que as chaves estejam alinhadas e que os espaços não sejam substituídos por tabulações:

```yaml
      - name: Matcher Temperature
        dataTypeValue: Temperature
```

Isso controlará o sensor *SAMPLE_SENSOR_TEMPERATURE* que você adicionou no [primeiro tutorial](tutorial-facilities-setup.md). Observe que essas linhas também estão presentes em *provisionSample.yaml* como linhas comentadas; você pode simplesmente remover as marcas de comentários removendo o caractere '#' na frente de cada linha. 

<a id="udf" />

## <a name="create-a-user-defined-function"></a>Criar uma função definida pelo usuário
As funções definidas pelo usuário, ou UDFs, permitem que você personalize o processamento de seus dados de sensor. Elas são um código JavaScript personalizado que pode ser executado dentro da instância dos Gêmeos Digitais quando ocorrem condições específicas descritas pelas correspondências. Você pode criar *correspondências* e *funções definidas pelo usuário* para cada sensor que deseja monitorar. Para obter mais informações detalhadas, leia [Processamento de dados e funções definidas pelo usuário](concepts-user-defined-functions.md). 

No arquivo de exemplo *provisionSample.yaml*, procure uma seção que começa com o tipo **userdefinedfunctions**. Esta seção provisiona uma função definida pelo usuário com um determinado **Nome**, que atua na lista de correspondências em **matcherNames**. Observe como você pode fornecer seu próprio arquivo JavaScript para a UDF como o **script**. Além disso, observe a seção denominada **roleassignments**. Ela atribui a função *Administrador de Espaço* à função definida pelo usuário; isso permite que ela acesse os eventos que chegam de um dos espaços provisionados. 

1. Configure a UDF para incluir a correspondência de temperatura adicionando ou removendo marcas de comentários na seguinte linha no nó `matcherNames` do arquivo *provisionSample.yaml*:

    ```yaml
            - Matcher Temperature
    ```

1. Abra o arquivo **_src\actions\userDefinedFunctions\availability.js_** em seu editor. Esse é o arquivo referenciado no elemento **script** de *provisionSample.yaml*. A função definida pelo usuário nesse arquivo procura condições quando nenhum movimento é detectado no espaço e quando os níveis de dióxido de carbono estão abaixo de 1000 ppm. Modifique o arquivo JavaScript para monitorar a temperatura, além de outras condições. Adicione as linhas de código a seguir para procurar condições quando nenhum movimento é detectado na sala, os níveis de dióxido de carbono estão abaixo de 1.000 ppm e a temperatura está abaixo de 25 graus Celsius.

   > [!NOTE]
   > Esta seção modifica o arquivo *src\actions\userDefinedFunctions\availability.js* para que você possa saber com detalhes como escrever uma função definida pelo usuário. No entanto, você pode optar por usar diretamente o arquivo [src\actions\userDefinedFunctions\availabilityForTutorial.js](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) em sua configuração. Esse arquivo tem todas as alterações exigidas por este tutorial. Se você usar esse arquivo, não deixe de usar o nome de arquivo correto para a chave **_script_** em [src\actions\provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml).

    1. Na parte superior do arquivo, adicione as seguintes linhas para a temperatura abaixo do comentário `// Add your sensor type here`:

        ```JavaScript
            var temperatureType = "Temperature";
            var temperatureThreshold = 78;
        ```
   
    1. Adicione as seguintes linhas depois da instrução que define `var motionSensor`, abaixo do comentário `// Add your sensor variable here`:

        ```JavaScript
            var temperatureSensor = otherSensors.find(function(element) {
                return element.DataType === temperatureType;
            });
        ```
    
    1. Adicione as seguintes linhas depois da instrução que define `var carbonDioxideValue`, abaixo do comentário `// Add your sensor latest value here`:

        ```JavaScript
            var temperatureValue = getFloatValue(temperatureSensor.Value().Value);
        ```
    
    1. Remova as seguintes linhas de código abaixo do comentário `// Modify this line to monitor your sensor value`: 

        ```JavaScript
            if(carbonDioxideValue === null || motionValue === null) {
                sendNotification(telemetry.SensorId, "Sensor", "Error: Carbon dioxide or motion are null, returning");
                return;
            }
        ```
       
       Substitua-as pelas seguintes linhas:

        ```JavaScript
            if(carbonDioxideValue === null || motionValue === null || temperatureValue === null){
                sendNotification(telemetry.SensorId, "Sensor", "Error: Carbon dioxide, motion, or temperature are null, returning");
                return;
            }
        ```
    
    1. Remova as seguintes linhas de código abaixo do comentário `// Modify these lines as per your sensor`:

        ```JavaScript
            var availableFresh = "Room is available and air is fresh";
            var noAvailableOrFresh = "Room is not available or air quality is poor";
        ```

       Substitua-as pelas seguintes linhas:

        ```JavaScript
            var alert = "Room with fresh air and comfortable temperature is available.";
            var noAlert = "Either room is occupied, or working conditions are not right.";
        ```
    
    1. Remova o seguinte bloco de código *if-else* após o comentário `// Modify this code block for your sensor`:

        ```JavaScript
            // If carbonDioxide less than threshold and no presence in the room => log, notify and set parent space computed value
            if(carbonDioxideValue < carbonDioxideThreshold && !presence) {
                log(`${availableFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
                setSpaceValue(parentSpace.Id, spaceAvailFresh, availableFresh);

                // Set up custom notification for air quality
                parentSpace.Notify(JSON.stringify(availableFresh));
            }
            else {
                log(`${noAvailableOrFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
                setSpaceValue(parentSpace.Id, spaceAvailFresh, noAvailableOrFresh);

                // Set up custom notification for air quality
                parentSpace.Notify(JSON.stringify(noAvailableOrFresh));
            }
        ```
      
       E substitua-o pelo bloco *if-else* a seguir:

        ```JavaScript
            // If sensor values are within range and room is available
            if(carbonDioxideValue < carbonDioxideThreshold && temperatureValue < temperatureThreshold && !presence) {
                log(`${alert}. Carbon Dioxide: ${carbonDioxideValue}. Temperature: ${temperatureValue}. Presence: ${presence}.`);

                // log, notify and set parent space computed value
                setSpaceValue(parentSpace.Id, spaceAvailFresh, alert);

                // Set up notification for this alert
                parentSpace.Notify(JSON.stringify(alert));
            }
            else {
                log(`${noAlert}. Carbon Dioxide: ${carbonDioxideValue}. Temperature: ${temperatureValue}. Presence: ${presence}.`);
    
                // log, notify and set parent space computed value
                setSpaceValue(parentSpace.Id, spaceAvailFresh, noAlert);
            }
        ```
        
        O UDF modificado irá procurar uma condição em que uma sala se torna disponível e tem o dióxido de carbono e a temperatura dentro dos limites toleráveis. Ele irá gerar uma notificação com a instrução `parentSpace.Notify(JSON.stringify(alert));` quando essa condição for atendida. Ela definirá o valor do espaço monitorado independentemente de a condição ser atendida, com a mensagem correspondente.
    
    1. Salve o arquivo. 
    
1. Abra a janela de comando e navegue até a pasta  **_occupancy-quickstart\src_**. Execute o comando a seguir para provisionar o grafo de inteligência espacial e a função definida pelo usuário. 

    ```cmd/sh
    dotnet run ProvisionSample
    ```

   > [!IMPORTANT]
   > Para impedir o acesso não autorizado à API de Gerenciamento dos Gêmeos Digitais, o aplicativo **_occupancy-quickstart_** exige que você entre com suas credenciais de conta do Azure. Ele salva suas credenciais por um breve período, para que você não precise entrar toda vez que for executá-lo. Quando o programa é executado pela primeira vez e quando as credenciais salvas expiram, ele o encaminha para uma página de entrada e fornece um código específico da sessão para inserir nessa página. Siga os prompts para entrar com sua conta do Azure.


1. Quando a conta é autenticada, o aplicativo começa a criação de um grafo de exemplo espacial conforme configurado em *provisionSample.yaml*. Aguarde até que o provisionamento seja concluído; isso levará alguns minutos. Depois de concluído, observe as mensagens na janela de comando e observe como o grafo espacial é criado. Observe como ele cria um hub IoT no nó raiz ou o `Venue`. 

1. Da saída na janela de comando, copie o valor da `ConnectionString`, na seção `Devices`, para a área de transferência. Você precisará desse valor para simular a conexão do dispositivo na seção a seguir.

    ![Exemplo de provisionamento](./media/tutorial-facilities-udf/run-provision-sample.png)

> [!TIP]
> Se você receber uma mensagem de erro semelhante a "A operação de E/S foi anulada devido a uma saída de thread ou solicitação do aplicativo" no meio do provisionamento, tente executar o comando novamente. Isso pode acontecer quando o cliente HTTP atingiu o tempo limite devido a um problema de rede.

<a id="simulate" />

## <a name="simulate-sensor-data"></a>Simular dados de sensor
Nesta seção, você usará o projeto chamado *device-connectivity* no exemplo para simular dados de sensor e detectar movimento, temperatura e dióxido de carbono. Esse projeto gera valores aleatórios para os sensores e os envia ao hub IoT usando a cadeia de conexão do dispositivo.

1. Em uma janela de comando separada, navegue até o exemplo dos Gêmeos Digitais e até a pasta **_device-connectivity_**.

1. Execute esse comando para ter certeza de que as dependências do projeto estão corretas:

    ```cmd/sh
    dotnet restore
    ```

1. Abra *appSettings.json* em seu editor de arquivo e edite os valores abaixo:
    1. *DeviceConnectionString*: atribua o valor de `ConnectionString` na janela de saída da seção anterior. Copie essa cadeia de caracteres completamente, entre aspas, para que o simulador se conecte corretamente ao hub IoT.

    1. *HardwareId* dentro da matriz *Sensors*: já que você está simulando eventos de sensores provisionados para sua instância dos Gêmeos Digitais, a ID de hardware e os nomes dos sensores nesse arquivo devem corresponder ao nó `sensors` do arquivo *provisionSample.yaml*. Adicione uma nova entrada para o sensor de temperatura; o nó **Sensors** em *appSettings.json* deve ser semelhante ao seguinte:

        ```JSON
        "Sensors": [{
          "DataType": "Motion",
          "HardwareId": "SAMPLE_SENSOR_MOTION"
        },{
          "DataType": "CarbonDioxide",
          "HardwareId": "SAMPLE_SENSOR_CARBONDIOXIDE"
        },{
          "DataType": "Temperature",
          "HardwareId": "SAMPLE_SENSOR_TEMPERATURE"
        }]
        ```

1. Execute este comando para iniciar a simulação de eventos de dispositivo para temperatura, movimento e dióxido de carbono:

    ```cmd/sh
    dotnet run
    ```

   > [!NOTE] 
   > Já que o exemplo de simulação não se comunica diretamente com a instância do Gêmeo Digital, ele não requer autenticação.

## <a name="get-results-of-user-defined-function"></a>Obter resultados da função definida pelo usuário
A função definida pelo usuário é executada sempre que sua instância recebe dados de sensor e de dispositivo. Essa seção consulta sua instância dos Gêmeos Digitais para obter os resultados da função definida pelo usuário. Você verá em tempo real quando uma sala está disponível, se o ar está fresco e se temperatura está correta. 

1. Abra a janela de comando que você usou para provisionar o exemplo, ou uma nova janela de comando, e navegue até a pasta **_occupancy-quickstart\src_** do exemplo novamente. 

1. Execute o seguinte comando e entre quando solicitado:

    ```cmd/sh
    dotnet run GetAvailableAndFreshSpaces
    ```

A janela de saída mostrará como a função definida pelo usuário é executada e intercepta eventos de simulação do dispositivo. 

   ![Executar UDF](./media/tutorial-facilities-udf/udf-running.png)

Dependendo de a condição monitorada ser atendida ou não, a função definida pelo usuário define o valor do espaço com a mensagem pertinente, como vimos na [seção acima](#udf), que a função `GetAvailableAndFreshSpaces` imprime no console. 

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser parar de explorar os Gêmeos Digitais do Azure além desse ponto, você pode excluir os recursos criados neste tutorial:

1. No menu à esquerda no [portal do Azure](http://portal.azure.com), clique em **Todos os recursos**, selecione o grupo de recursos dos Gêmeos Digitais e **Excluir**.
2. Se precisar, você também pode excluir os aplicativos de exemplo no seu computador de trabalho. 


## <a name="next-steps"></a>Próximas etapas

Agora que você provisionou os espaços e criou uma estrutura para disparar notificações personalizadas, pode prosseguir para um dos tutoriais a seguir. 

> [!div class="nextstepaction"]
> [Tutorial: Receber notificações de espaços dos Gêmeos Digitais do Azure usando o Aplicativo Lógico](tutorial-facilities-events.md)

Ou,

> [!div class="nextstepaction"]
> [Tutorial: Visualizar e analisar eventos de espaços dos Gêmeos Digitais do Azure usando o Time Series Insights](tutorial-facilities-analyze.md)
