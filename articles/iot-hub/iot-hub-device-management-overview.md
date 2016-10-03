<properties
 pageTitle="Visão geral do gerenciamento de dispositivo | Microsoft Azure"
 description="Visão geral do gerenciamento de dispositivo Hub IoT do Azure: dispositivos gêmeos, consultas de dispositivo e trabalhos do dispositivo"
 services="iot-hub"
 documentationCenter=""
 authors="juanjperez"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="juanpere"/>

# Visão geral do gerenciamento de dispositivos do Hub IoT do Azure (preview)

O gerenciamento de dispositivo Hub IoT do Azure permite o gerenciamento de dispositivo IoT baseado em padrões para que você possa gerenciar, configurar e atualizar os dispositivos remotamente.

Há três principais conceitos de gerenciamento de dispositivo no IoT do Azure:

1.  **Dispositivo gêmeo:** a representação do dispositivo físico no Hub IoT.

2.  **Consultas de dispositivo**: permitem encontrar dispositivos gêmeos e gerar uma compreensão agregada de vários dispositivos gêmeos. Por exemplo, você poderia executar uma consulta para encontrar todos os dispositivos gêmeos com uma versão de firmware 1.0.

3.  **Trabalhos do dispositivo**: uma ação a ser executada em um ou mais dispositivos físicos, como atualização de firmware, reinicialização e redefinição de fábrica.

## Dispositivo gêmeo

O dispositivo gêmeo é a representação de um dispositivo físico no IoT do Azure. O objeto **Microsoft.Azure.Devices.Device** é usado para representar o dispositivo gêmeo.

![][img-twin]

O dispositivo gêmeo tem os seguintes componentes:

1.  **Campos de dispositivo:** campos de dispositivo são propriedades predefinidas usadas para o gerenciamento de dispositivo e mensagens do Hub IoT. Eles ajudam o Hub IoT a identificar e se conectar com dispositivos físicos. Os campos de dispositivo não são sincronizados com o dispositivo e são armazenados exclusivamente no dispositivo gêmeo. Os campos de dispositivo incluem as informações de autenticação e a ID do dispositivo.

2.  **Propriedades de dispositivo:** propriedades de dispositivo são um dicionário predefinido de propriedades que descreve o dispositivo físico. O dispositivo físico é o mestre de cada propriedade do dispositivo e o repositório autoritativo de cada valor correspondente. Uma representação eventualmente consistente dessas propriedades é armazenada no dispositivo gêmeo na nuvem. A coerência e a atualização estão sujeitas a configurações de sincronização, descritas em [Tutorial: Como usar o dispositivo gêmeo][lnk-tutorial-twin]. Alguns exemplos de propriedades de dispositivo incluem a versão de firmware, o nível de bateria e o nome do fabricante.

3.  **Propriedades de serviço:** as propriedades de serviço são pares de **&lt;chave e valor&gt;** que o desenvolvedor adiciona ao dicionário de propriedades de serviço. Essas propriedades estendem o modelo de dados para o dispositivo gêmeo, permitindo que você caracterize melhor seu dispositivo. As propriedades de dispositivo não são sincronizadas com o dispositivo e são armazenadas exclusivamente no dispositivo gêmeo, na nuvem. Um exemplo de uma propriedade de serviço é **&lt;NextServiceDate, 11/12/2017&gt;**, que pode ser usada para encontrar dispositivos por sua próxima data de serviço.

4.  **Marcas:** marcas são um subconjunto das propriedades de serviço que são cadeias de caracteres arbitrárias, em vez de propriedades de dicionário. Elas podem ser usadas para anotar dispositivos gêmeos ou organizar os dispositivos em grupos. As marcas não são sincronizadas com o dispositivo e são armazenadas exclusivamente no dispositivo gêmeo. Por exemplo, se o dispositivo gêmeo representar um caminhão físico, você poderá adicionar uma marca para cada tipo de carga no caminhão – **maçãs**, **laranjas** e **bananas**.

## Consultas do dispositivo

Na seção anterior, você aprendeu sobre os diferentes componentes do dispositivo gêmeo. Agora, vamos explicar como encontrar dispositivos gêmeos no registro de dispositivo Hub IoT com base nas propriedades de dispositivo, propriedades de serviço ou marcas. Um exemplo de quando você usaria uma consulta é encontrar dispositivos que precisam ser atualizados. É possível consultar para todos os dispositivos com uma versão de firmware especificada e alimentar o resultado em uma ação específica (no Hub IoT, conhecido como um trabalho do dispositivo, explicado na próxima seção).

É possível consultar usando marcas e propriedades:

-   Para consultar dispositivos gêmeos usando marcas, você transmite uma matriz de cadeias de caracteres e a consulta retorna o conjunto de dispositivos marcados com todas essas cadeias de caracteres.

-   Para consultar dispositivos gêmeos usando as propriedades do serviço ou do dispositivo, é necessário usar uma expressão de consulta JSON. O exemplo abaixo mostra como é possível consultar para todos os dispositivos com a propriedade de dispositivo e com a chave **FirmwareVersion** e o valor **1.0**. É possível ver que o **tipo** da propriedade é **dispositivo**, indicando que estamos consultando com base nas propriedades de dispositivo, não as propriedades de serviço:

  ```
  {                           
      "filter": {                  
        "property": {                
          "name": "FirmwareVersion",   
          "type": "device"             
        },                           
        "value": "1.0",              
        "comparisonOperator": "eq",  
        "type": "comparison"         
      },                           
      "project": null,             
      "aggregate": null,           
      "sort": null                 
  }
  ```

## Trabalhos do dispositivo

O próximo conceito no gerenciamento de dispositivo são os trabalhos do dispositivo, que permitem a coordenação de orquestrações de várias etapas em vários dispositivos.

No momento, existem seis tipos de trabalhos do dispositivo que são fornecidos pelo gerenciamento de dispositivo Hub IoT do Azure (adicionaremos outros trabalhos conforme as necessidades dos clientes):

- **Atualização de firmware**: atualiza o firmware (ou a imagem do sistema operacional) no dispositivo físico.
- **Reinicialização**: reinicia o dispositivo físico.
- **Redefinição de fábrica**: reverte o firmware (ou a imagem do sistema operacional) do dispositivo físico para uma imagem de backup fornecida de fábrica armazenada no dispositivo.
- **Atualização da configuração**: configura o agente cliente Hub IoT em execução no dispositivo físico.
- **Ler propriedade de dispositivo**: obtém o valor mais recente de uma propriedade de dispositivo no dispositivo físico.
- **Gravar propriedade de dispositivo:** altera uma propriedade de dispositivo no dispositivo físico.

Para obter detalhes sobre como usar cada um desses trabalhos, confira a [documentação da API para C# e Node.js][lnk-apidocs].

Um trabalho pode operar em vários dispositivos. Quando você inicia um trabalho, um trabalho filho associado é criado para cada um desses dispositivos. Um trabalho filho opera em um único dispositivo. Cada trabalho filho tem um ponteiro para seu trabalho pai. O trabalho pai é apenas um contêiner para os trabalhos filho; ele não implementa nenhuma lógica para distinguir entre os tipos de dispositivos (como atualização de um Intel Edison versus um Raspberry Pi). O diagrama a seguir ilustra a relação entre um trabalho pai, seu filho e os dispositivos físicos associados.

![][img-jobs]

É possível consultar o histórico de trabalhos para entender o estado dos trabalhos que foram iniciados. Para obter algumas consultas de exemplo, confira [nossa biblioteca de consulta][lnk-query-samples].

## Implementação de dispositivo

Agora que abordamos os conceitos do lado do serviço, vamos discutir como criar um dispositivo físico gerenciado. A biblioteca do cliente do DM do Hub IoT do Azure permite que você gerencie seus dispositivos IoT com o IoT Hub do Azure. "Gerenciar" inclui as ações de reinicialização, a redefinição de fábrica e a atualização de firmware. Hoje, oferecemos uma biblioteca C independente de plataforma, mas adicionaremos suporte a outras linguagens em breve.

A biblioteca do cliente de DM terá duas responsabilidades principais no gerenciamento de dispositivos:

- Sincronizar propriedades no dispositivo físico com seu dispositivo gêmeo correspondente no Hub IoT
- Coreografar os trabalhos de dispositivo enviados pelo Hub IoT para o dispositivo

Você pode saber mais sobre essas responsabilidades e a implementação no dispositivo físico em [Introducing the Azure IoT Hub device management client library][lnk-library-c] \(Apresentando a biblioteca de cliente do gerenciamento de dispositivo Hub IoT do Azure).

## Próximas etapas

Para implementar aplicativos cliente em uma grande variedade de sistemas operacionais e plataformas de hardware do dispositivo, você pode usar os SDKs do dispositivo IoT. Os SDKs do dispositivo IoT incluem bibliotecas que facilitam o envio de telemetria para um hub IoT e o recebimento de comandos da nuvem para o dispositivo. Ao usar os SDKs, você poderá escolher entre vários protocolos de rede para comunicar-se com o Hub IoT. Para saber mais, confira as [informações sobre SDKs de dispositivo][lnk-device-sdks].

Para continuar a aprender sobre as funcionalidades de gerenciamento de dispositivos do Hub IoT do Azure, confira o tutorial [Introdução ao gerenciamento de dispositivos do Hub IoT do Azure][lnk-get-started].

<!-- Images and links -->
[img-twin]: media/iot-hub-device-management-overview/image1.png
[img-jobs]: media/iot-hub-device-management-overview/image2.png
[img-client]: media/iot-hub-device-management-overview/image3.png

[lnk-lwm2m]: http://technical.openmobilealliance.org/Technical/technical-information/release-program/current-releases/oma-lightweightm2m-v1-0
[lnk-library-c]: iot-hub-device-management-library.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-tutorial-twin]: iot-hub-device-management-device-twin.md
[lnk-apidocs]: http://azure.github.io/azure-iot-sdks/
[lnk-query-samples]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/doc/get_started/dm_queries/query-samples.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks

<!---HONumber=AcomDC_0720_2016-->