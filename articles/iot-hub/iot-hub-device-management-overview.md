<properties
 pageTitle="Visão geral do gerenciamento de dispositivo | Microsoft Azure"
 description="Visão geral do gerenciamento de dispositivo Hub IoT do Azure: dispositivos gêmeos, consultas de dispositivo e trabalhos do dispositivo"
 services="iot-hub"
 documentationCenter=""
 authors="ellenfosborne"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="elfarber"/>

# Visão geral do gerenciamento de dispositivos do Hub IoT do Azure (preview)

O gerenciamento de dispositivo Hub IoT do Azure permite o gerenciamento de dispositivo IoT baseado em padrões para que você possa gerenciar, configurar e atualizar os dispositivos remotamente.

Há três principais conceitos de gerenciamento de dispositivo no IoT do Azure:

1.  **Dispositivo gêmeo:** a representação do dispositivo físico no Hub IoT.

2.  **Consultas de dispositivo**: permitem encontrar dispositivos gêmeos e gerar uma compreensão agregada deles. Por exemplo, você poderia encontrar todos os dispositivos gêmeos com uma versão de firmware 1.0.

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

Agora que abordamos os conceitos do lado do serviço, vamos discutir como criar um dispositivo com funcionalidades de gerenciamento. A biblioteca de cliente do gerenciamento de dispositivo Hub IoT do Azure no dispositivo fornece os artefatos necessários para implementar a comunicação entre o dispositivo físico e o Hub IoT.

A biblioteca de cliente do DM do Hub IoT do Azure abstrai o padrão [LWM2M][lnk-lwm2m] e o protocolo de solicitação/resposta baseado em CoAP. Portanto, a biblioteca tem um modelo de dispositivo de *objetos* e *definições de recurso*:

-   Os objetos descrevem um conjunto de entidades funcionais coerentes no sistema, tais como as atualizações de firmware e do dispositivo.
-   Os recursos descrevem atributos ou as ações incluídas nesses objetos, tais como informações em nível da bateria e a ação de reinicialização.

Quando você usa a biblioteca de cliente do DM do Hub IoT do Azure, é necessário implementar os retornos de chamada de operações de leitura, gravação e execução para cada recurso no dispositivo físico. A biblioteca manipula a atualização assíncrona do Hub IoT quando as propriedades são alteradas.

O diagrama abaixo mostra os diferentes componentes necessários no agente cliente Hub IoT.

![][img-client]

É possível saber mais sobre a implementação no dispositivo físico em [Introducing the Azure IoT Hub device management client library][lnk-library-c] (Apresentando a biblioteca de cliente do gerenciamento de dispositivo Hub IoT do Azure).

## Próximas etapas

Para saber mais sobre os recursos de gerenciamento de dispositivo Hub IoT do Azure, é possível acompanhar os tutoriais:

- [Introdução ao gerenciamento de dispositivo Hub IoT do Azure][lnk-get-started]

- [Como usar o dispositivo gêmeo][lnk-tutorial-twin]

- [Como encontrar dispositivos gêmeos usando consultas][lnk-tutorial-queries]

- [Como usar trabalhos do dispositivo para atualizar o firmware do dispositivo][lnk-tutorial-jobs]

<!-- Images and links -->
[img-twin]: media/iot-hub-device-management-overview/image1.png
[img-jobs]: media/iot-hub-device-management-overview/image2.png
[img-client]: media/iot-hub-device-management-overview/image3.png

[lnk-lwm2m]: http://technical.openmobilealliance.org/Technical/technical-information/release-program/current-releases/oma-lightweightm2m-v1-0
[lnk-library-c]: iot-hub-device-management-library.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-tutorial-twin]: iot-hub-device-management-device-twin.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-tutorial-jobs]: iot-hub-device-management-device-jobs.md
[lnk-apidocs]: http://azure.github.io/azure-iot-sdks/
[lnk-query-samples]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/doc/get_started/dm_queries/query-samples.md

<!---HONumber=AcomDC_0504_2016-->