<properties
	pageTitle="Trabalhos do dispositivo de gerenciamento de dispositivo Hub IoT | Microsoft Azure"
	description="Tutorial do Hub IoT do Azure para o gerenciamento de dispositivo que descreve como usar trabalhos do dispositivo para executar operações como atualização de firmware remota."
	services="iot-hub"
	documentationCenter=".net"
	authors="ellenfosborne"
	manager="timlt"
	editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="elfarber"/>

# Tutorial: Como usar trabalhos de dispositivo para atualizar o firmware do dispositivo (preview)

[AZURE.INCLUDE [iot-hub-device-management-job-selector](../../includes/iot-hub-device-management-jobs-selector.md)]

## Introdução
O gerenciamento de dispositivo IoT do Azure permite que você interaja com dispositivos físicos usando os trabalhos do dispositivo. Depois de identificar o dispositivo gêmeo (a representação de serviço de um dispositivo físico), é possível interagir com seu dispositivo físico correspondente usando trabalhos do dispositivo. Os trabalhos do dispositivo permitem a coordenação de processos complexos em vários dispositivos. Esse processo pode incluir várias etapas e operações de longa duração.

No momento, existem seis tipos de trabalhos do dispositivo que são fornecidos pelo gerenciamento de dispositivo Hub IoT do Azure (adicionaremos outros trabalhos conforme as necessidades dos clientes):

- **Atualização de firmware**: atualiza o firmware (ou a imagem do sistema operacional) no dispositivo físico.
- **Reinicialização**: reinicia o dispositivo físico.
- **Redefinição de fábrica**: reverte o firmware (ou a imagem do sistema operacional) do dispositivo físico para uma imagem de backup fornecida de fábrica armazenada no dispositivo.
- **Atualização da configuração**: configura o agente cliente Hub IoT em execução no dispositivo físico.
- **Ler propriedade do dispositivo**: obtém o valor de uma propriedade de dispositivo mais recente no dispositivo físico.
- **Gravar propriedade do dispositivo**: altera uma propriedade do dispositivo no dispositivo físico.

Para obter detalhes sobre como usar cada um desses trabalhos, confira a [documentação da API][lnk-apidocs].

É possível consultar o histórico de trabalhos para entender o estado dos trabalhos que foram iniciados. Para obter algumas consultas de exemplo, confira [nossa biblioteca de expressões de consulta][lnk-query-samples].

## Usando trabalhos do dispositivo para executar a atualização de firmware: arquitetura

O diagrama abaixo ilustra um trabalho do dispositivo de atualização de firmware interagindo com um único dispositivo físico.

![][img-architecture]

Etapas para o trabalho do dispositivo de atualização de firmware:

1.  A solução IoT baseada em nuvem inicia o trabalho do dispositivo de atualização de firmware e fornece o URI para o local do pacote de firmware. É responsabilidade da solução IoT colocar o pacote de firmware em um local por meio do qual o dispositivo poderá baixá-lo.

2.  Quando o dispositivo físico recebe esse URI, ele começa automaticamente a baixar por meio do URI fornecido.

3.  O código no dispositivo recebe a solicitação do Hub IoT e baixa o pacote de firmware do URI de local fornecido.

4.  Depois de receber a mensagem de status do dispositivo informando que o download foi concluído, o trabalho do dispositivo instrui o dispositivo a aplicar a imagem de firmware baixada.

5.  Depois que o dispositivo aplicar a imagem de firmware, ele será reiniciado, se reconectará ao Hub IoT e informará a ele que o novo firmware foi aplicado; em seguida, o trabalho de dispositivo será concluído.

## Executando a amostra de atualização de firmware

A amostra a seguir estende a funcionalidade do tutorial [Introdução ao gerenciamento de dispositivo Hub IoT do Azure][lnk-get-started]. Iniciando com diferentes dispositivos simulados em execução, ele iniciará um trabalho para atualizar o firmware em todos eles.

### Pré-requisitos 

Antes de executar esta amostra, é necessário concluir as etapas em [Introdução ao gerenciamento de dispositivo Hub IoT do Azure][lnk-get-started]. Isso significa que seus dispositivos simulados devem estar em execução. Se você concluiu o processo anteriormente, reinicie os dispositivos simulados agora.

### Iniciando a amostra

Para iniciar a amostra, é necessário executar o processo **FirmwareUpdate.exe**. Isso iniciará o processo de atualização de firmware em todos os dispositivos simulados. Siga as etapas abaixo para iniciar a amostra:

1.  Na pasta raiz em que você clonou o repositório **azure-iot-sdks**, navegue até a pasta **azure-iot-sdks\\csharp\\service\\samples\\bin**.  

2.  Execute o `FirmwareUpdate.exe <IoT Hub Connection String>`

Você deverá ver a saída na janela de linha de comando mostrando sete trabalhos do dispositivo acompanhando a atualização de firmware simulada nos seis dispositivos simulados.

### Iniciando um trabalho do dispositivo

Em geral, os trabalhos do dispositivo são iniciados usando uma série de métodos **Schedule&lt;JobName&gt;Async** na instância **JobClient**. Na amostra de atualização de firmware, chamamos o método **ScheduleFirmwareUpdateAsync**. Como transmitimos uma matriz com 6 IDs de dispositivo, 7 trabalhos do dispositivo são iniciados, um para cada dispositivo que está sendo atualizado e um trabalho do dispositivo pai usado para acompanhar os outros 6.

No trecho de código abaixo, em **Program.cs** no projeto **FirmwareUpdate**, um trabalho de atualização de firmware é iniciado. A chamada usa uma matriz de cadeia de caracteres de valores **deviceId** como um parâmetro, representando os dispositivos que queremos atualizar.

```
var jobResponse = await deviceJobClient.ScheduleFirmwareUpdateAsync(Guid.NewGuid().ToString(), deviceIds, packageURI, TimeSpan.FromMinutes(25));
```

### Consultando o histórico de trabalhos

Na amostra, uma lista dos dispositivos que estão executando de forma ativa um trabalho do dispositivo é exibida periodicamente. Quando um trabalho do dispositivo for concluído, o dispositivo associado desaparecerá da lista exibida. A figura abaixo é uma captura de tela de **FirmwareUpdate.exe** em execução:

![][img-output1]

A lista acima é gerada por meio da consulta de todos os trabalhos ativos, conforme mostrado no trecho de código abaixo em **Program.cs** do projeto **FirmwareUpdate**:

```
private static async Task OutputRunningJobs()
{
  JobClient deviceJobClient = JobClient.CreateFromConnectionString(connectionString);

  string json = JsonConvert.SerializeObject(
    new
    {
      filter = new
      {
        property = new
        {
          name = "Status",
          type = "default"
        },
        value = "Running",
        comparisonOperator = "eq",
        type = "comparison"
      }
    }
  );

  JobQueryResult result = await deviceJobClient.QueryJobHistoryJsonAsync(json);

  // List query result
  foreach (JobResponse job in result.Result)
  {
    Console.WriteLine("DeviceID: {0}\t\tJobId: {1}",
      job.DeviceId,
      job.JobId
      );
  }
}
```

Depois de concluir o trabalho do dispositivo pai, a amostra gera uma lista de todos os trabalhos do dispositivo. Isso é mostrado na figura abaixo. O trabalho pai só será concluído depois que todos os trabalhos associados filho forem concluídos. Na captura de tela abaixo, o trabalho pai é o último trabalho da lista, que pode ser observado devido ao fato de **ParentJobId** ser **''**. O trabalho pai também tem o campo **Reason** definido como uma cadeia de caracteres que indica o resultado de agregação da consulta. Nesse caso, ele mostra que 6 dispositivos foram atualizados e todos eles tiveram êxito.

![][img-output2]

A lista acima é gerada por meio da consulta de todos os trabalhos e da classificação por hora de início. Qualquer uma das propriedades do objeto **JobResponse**, mostrada abaixo, pode ser usada para consultar o histórico de trabalhos do dispositivo.

![][img-properties]

Para obter mais amostras de como consultar o histórico de trabalhos do dispositivo, confira [nossa biblioteca de expressões de consulta][lnk-query-samples].

### Detalhes de implementação do simulador de dispositivo

Nas seções anteriores, mostramos os detalhes de implementação da atualização de firmware do ponto de vista do serviço (como iniciar um trabalho do dispositivo e consultar seu status). Agora, vamos discutir a implementação correspondente no lado do dispositivo.

A biblioteca de cliente do gerenciamento de dispositivo Hub IoT do Azure lida com a comunicação entre o dispositivo e o serviço e, portanto, tudo o que resta é a implementação da lógica específica do dispositivo. Isso consiste em duas partes:

- Implementar o processo de atualização de firmware específico do dispositivo: isso envolve a escrita de uma lógica específica do dispositivo para baixar o pacote de firmware e aplicar a atualização nos retornos de chamada apropriados listados abaixo. Na amostra simulada, isso é implementado na [amostra][lnk-github-firmware]\:

  ```
  object_firmwareupdate *obj = get_firmwareupdate_object(0);
  obj->firmwareupdate_packageuri_write_callback = start_firmware_download;
  // platform specific code
  obj->firmwareupdate_update_execute_callback = start_firmware_update;
  //platform specific code
  ```

- Informar o serviço do processo de atualização de firmware: isso envolve a modificação do estado de atualização de firmware e dos campos de resultado da atualização de firmware. É possível fazer isso chamando as APIs **set\_firmwareupdate\_state** e **set\_firmwareupdate\_updateresult**. Na amostra simulada, isso é implementado na [amostra][lnk-github-firmware].

## Próximas etapas

Para saber mais sobre os recursos de gerenciamento de dispositivo Hub IoT do Azure, é possível acompanhar os tutoriais:

- a biblioteca de cliente do DM do Hub IoT do Azure fornece uma amostra de ponta a ponta usando um [dispositivo Intel Edison][lnk-edison].

- [Como usar o dispositivo gêmeo][lnk-twin-tutorial]

- [Como encontrar dispositivos gêmeos usando consultas][lnk-tutorial-queries]

<!-- Images and links -->

[img-architecture]: media/iot-hub-device-management-device-jobs/image1.png
[img-output1]: media/iot-hub-device-management-device-jobs/image2.png
[img-output2]: media/iot-hub-device-management-device-jobs/image3.png
[img-properties]: media/iot-hub-device-management-device-jobs/image4.png

[lnk-apidocs]: iot-hub-sdks-summary.md
[lnk-twin-tutorial]: iot-hub-device-management-device-twin.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-edison]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/samples/iotdm_edison_sample
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-github-firmware]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/c/iotdm_client/samples/iotdm_simple_sample/iotdm_simple_sample.c
[lnk-query-samples]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/doc/get_started/dm_queries/query-samples.md

<!---HONumber=AcomDC_0518_2016-->