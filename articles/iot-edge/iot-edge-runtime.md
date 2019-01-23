---
title: Saiba como o tempo de execução gerencia dispositivos – Azure IoT Edge | Microsoft Docs
description: Saiba como os módulos, segurança, comunicação e emissão de relatórios em seus dispositivos são gerenciados pelo tempo de execução do Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/13/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: a2412a286015cb403fe9a2af7754c7e5346fe98c
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2019
ms.locfileid: "54230417"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Reconhecer o tempo de execução do Azure IoT Edge e sua arquitetura

O tempo de execução do IoT Edge é uma coleção de programas que precisam ser instalados em um dispositivo para ser considerado um dispositivo IoT Edge. Coletivamente, os componentes do tempo de execução do IoT Edge permitem que dispositivos IoT Edge recebam o código para executar na borda e informem os resultados. 

O tempo de execução do IoT Edge executa as seguintes funções em dispositivos IoT Edge:

* Instala e atualiza as cargas de trabalho no dispositivo.
* Mantém os padrões de segurança do Azure IoT Edge no dispositivo.
* Garante que os [módulos do IoT Edge](iot-edge-modules.md) sempre estejam em execução.
* Fornece um relatório sobre a integridade do módulo para a nuvem para o monitoramento remoto.
* Facilita a comunicação entre os dispositivos de folha de downstream e os dispositivos IoT Edge.
* Facilita a comunicação entre os módulos e o dispositivo IoT Edge.
* Facilita a comunicação entre o dispositivo IoT Edge e a nuvem.

![O tempo de execução comunica insights e integridade de módulo para o Hub IoT](./media/iot-edge-runtime/Pipeline.png)

As responsabilidades do tempo de execução do IoT Edge se enquadram em duas categorias: gerenciamento de comunicação e de módulo. Essas duas funções são executadas por dois componentes que representam o tempo de execução do IoT Edge. O hub do IoT Edge é responsável pela comunicação, enquanto o agente do IoT Edge gerencia a implantação e o monitoramento de módulos. 

Tanto o hub do IoT Edge quanto o agente do IoT Edge são módulos, assim como qualquer outro módulo em execução em um dispositivo IoT Edge. 

## <a name="iot-edge-hub"></a>Hub do IoT Edge

O hub do IoT Edge é um dos dois módulos que compõem o tempo de execução do Azure IoT Edge. Ele atua como um proxy local para o Hub IoT expondo os mesmos pontos de extremidade de protocolo que o Hub IoT. Essa consistência significa que os clientes (sejam dispositivos ou módulos) podem se conectar no tempo de execução do IoT Edge como faria para no Hub IoT. 

>[!NOTE]
> O hub do IoT Edge dá suporte a clientes que se conectam usando MQTT ou AMQP. Ele não oferece suporte a clientes que usam HTTP. 

O hub do IoT Edge não é uma versão completa do Hub IoT executado localmente. Há algumas coisas que o hub do IoT Edge delega para o Hub IoT silenciosamente. Por exemplo, o hub do IoT Edge encaminha solicitações de autenticação para o Hub IoT quando um dispositivo tenta se conectar. Depois que a primeira conexão é estabelecida, as informações de segurança são armazenadas em cache localmente pelo hub do IoT Edge. São permitidas conexões subsequentes desse dispositivo sem ele precisar se autenticar na nuvem. 

>[!NOTE]
>O tempo de execução deve ser conectado toda vez que tenta autenticar um dispositivo.

Para reduzir a largura de banda que a solução IoT Edge usa, o hub do IoT Edge otimiza quantas conexões reais são feitas com a nuvem. O hub do IoT Edge usa conexões lógicas de clientes como módulos ou dispositivos de folha e combina-os em uma única conexão física com a nuvem. Os detalhes desse processo são transparentes para o restante da solução. Os clientes pensam que têm sua própria conexão para a nuvem, mesmo que estejam todos sendo enviados pela mesma conexão. 

![O hub do IoT Edge é um gateway entre os dispositivos físicos e o Hub IoT](./media/iot-edge-runtime/Gateway.png)

 O hub do IoT Edge pode determinar se ele está conectado ao Hub IoT. Se a conexão for perdida, o hub do IoT Edge salvará mensagens ou as atualizações duplicadas localmente. Depois que uma conexão é restabelecida, ela sincroniza todos os dados. A localização usada para esse cache temporário é determinado por uma propriedade do gêmeo do módulo do hub do IoT Edge. O tamanho do cache não tem limite e aumentará até atingir toda a capacidade de armazenamento do dispositivo. 

### <a name="module-communication"></a>Comunicação do módulo

 O hub do IoT Edge facilita a comunicação de módulo para módulo. O uso do hub do IoT Edge como um agente de mensagem mantém os módulos independentes uns dos outros. Os módulos precisam apenas especificar as entradas nas quais eles aceitam mensagens e as saídas nas quais eles gravam mensagens. Um desenvolvedor de soluções junta essas entradas e saídas para que os módulos processem os dados na ordem específica para essa solução. 

![O hub do IoT Edge facilita a comunicação de módulo para módulo](./media/iot-edge-runtime/module-endpoints.png)

Para enviar dados ao hub do IoT Edge, um módulo chamará o método SendEventAsync. O primeiro argumento especifica em qual saída enviar a mensagem. O pseudocódigo a seguir envia uma mensagem na saída1:

   ```csharp
   ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(transportSettings); 
   await client.OpenAsync(); 
   await client.SendEventAsync(“output1”, message); 
   ```

Para receber uma mensagem, registre um retorno de chamada que processa as mensagens recebidas em uma entrada específica. O pseudocódigo a seguir registra a função messageProcessor a ser usada para processar todas as mensagens recebidas em entrada1:

   ```csharp
   await client.SetInputMessageHandlerAsync(“input1”, messageProcessor, userContext);
   ```

Para obter mais informações sobre a classe ModuleClient e seus métodos de comunicação, consulte a referência de API para seu idioma preferencial do SDK: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C and Python](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable), or [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

O desenvolvedor da solução é responsável por especificar as regras que determinam como o hub do IoT Edge passa mensagens entre os módulos. As regras de roteamento são definidas na nuvem e propagadas para o hub do IoT Edge no seu dispositivo gêmeo. A mesma sintaxe para rotas do Hub IoT é usada para definir rotas entre módulos no Azure IoT Edge. 

<!--- For more info on how to declare routes between modules, see []. --->   

![As rotas entre módulos passam pelo hub do IoT Edge](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>Agente do IoT Edge

O agente do IoT Edge é o outro módulo que compõe o tempo de execução do Azure IoT Edge. Ele é responsável por instanciar módulos, fazendo com que continuem a funcionar, e indicar o status dos módulos para o Hub IoT. Assim como qualquer outro módulo, o agente do IoT Edge usa seu módulo gêmeo para armazenar esses dados de configuração. 

O [daemon de segurança do IoT Edge](iot-edge-security-manager.md) inicia o agente do IoT Edge na inicialização do dispositivo. O agente recupera seu módulo gêmeo do Hub IoT e inspeciona o manifesto de implantação. O manifesto de implantação é um arquivo JSON que declara os módulos que precisam ser iniciados. 

Cada item no manifesto de implantação contém informações específicas sobre um módulo e é usado pelo agente o IoT Edge para controlar o ciclo de vida do módulo. Estas são algumas das propriedades mais interessantes: 

* **Settings.Image**: a imagem de contêiner que o agente do IoT Edge usa para iniciar o módulo. O agente do IoT Edge deverá ser configurado com as credenciais para o registro de contêiner se a imagem estiver protegida por senha. As credenciais para o registro de contêiner pode ser configurado remotamente usando o manifesto de implantação ou no próprio dispositivo IoT Edge atualizando o `config.yaml` arquivo na pasta de programa do IoT Edge.
* **settings.createOptions**: uma cadeia de caracteres que é passada diretamente para o daemon do Docker ao iniciar o contêiner do módulo. A adição de opções de Docker a esta propriedade permite opções avançadas, como encaminhamento de porta ou montagem de volumes no contêiner do módulo.  
* **status** – o estado no qual o agente do IoT Edge coloca o módulo. Geralmente, esse valor é definido como *executando*, já que a maioria das pessoas deseja que o agente do IoT Edge inicie imediatamente todos os módulos no dispositivo. No entanto, você pode especificar o estado inicial de um módulo para ser interrompido e aguardar para mandar o agente do IoT Edge iniciar um módulo. O agente do IoT Edge relata o status de cada módulo para a nuvem nas propriedades relatadas. Uma diferença entre a propriedade desejada e a propriedade relatada é um indicador de um dispositivo com comportamento inadequado. Os status com suporte são:
   * Baixando
   * Executando
   * Não Íntegro
   * Com falha
   * Parado
* **restartPolicy** – como o agente do IoT Edge reinicia um módulo. Os valores possíveis incluem:
   * Nunca – o agente do IoT Edge nunca reinicia o módulo.
   * onFailure – se o módulo falhar, o agente do IoT Edge o reiniciará. Se o módulo é desligado corretamente, o agente do IoT Edge não o reinicia.
   * Não íntegro – se o módulo falha ou é considerado não íntegro, o agente do IoT Edge o reinicia.
   * Sempre – se o módulo falha, é considerado não íntegro ou é desligado de alguma forma, o agente do IoT Edge o reinicia. 

O agente do IoT Edge envia a resposta de tempo de execução para o Hub IoT. Aqui está uma lista das possíveis respostas:
  * 200 - OK
  * 400 - A configuração de implantação está malformada ou inválida.
  * 417 - O dispositivo não tem uma configuração de implantação definida.
  * 412 - A versão do esquema na configuração de implantação é inválida.
  * 406 – o dispositivo do IoT Edge está offline ou não está enviando relatórios de status.
  * 500 – ocorreu um erro no tempo de execução do IoT Edge.

### <a name="security"></a>Segurança

O agente do IoT Edge desempenha um papel fundamental na segurança de um dispositivo IoT Edge. Por exemplo, ele executa ações como verificar a imagem de um módulo antes de iniciá-lo. 

Para obter mais informações sobre a estrutura de segurança do Azure IoT Edge, leia sobre o [Gerenciador de segurança do IoT Edge](iot-edge-security-manager.md)

## <a name="next-steps"></a>Próximas etapas

[Entender os certificados do Azure IoT Edge](iot-edge-certs.md)
