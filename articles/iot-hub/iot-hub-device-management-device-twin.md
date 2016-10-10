<properties
	pageTitle="Gêmeos do gerenciamento de dispositivo Hub IoT | Microsoft Azure"
	description="Tutorial do Hub IoT do Azure para o gerenciamento de dispositivo que descreve como usar dispositivos gêmeos."
	services="iot-hub"
	documentationCenter=".net"
	authors="juanjperez"
	manager="timlt"
	editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="juanpere"/>

# Tutorial: Como usar o dispositivo gêmeo com C# (visualização)

[AZURE.INCLUDE [iot-hub-device-management-twin-selector](../../includes/iot-hub-device-management-twin-selector.md)]
## Introdução

O gerenciamento de dispositivo Hub IoT do Azure introduz o dispositivo gêmeo, uma representação do lado do serviço de um dispositivo físico. Veja abaixo um diagrama mostrando os diferentes componentes do dispositivo gêmeo.

![][img-twin]

Neste tutorial, vamos nos concentrar nas propriedades de dispositivo. Para saber mais sobre os outros componentes, veja [Visão geral do gerenciamento de dispositivo Hub IoT do Azure][lnk-dm-overview].

As propriedades de dispositivo são um dicionário predefinido de propriedades que descrevem o dispositivo físico. O dispositivo físico é o mestre de cada propriedade do dispositivo e o repositório autoritativo de cada valor correspondente. Uma representação “eventualmente consistente” dessas propriedades é armazenada no dispositivo gêmeo na nuvem. A coerência e a atualização estão sujeitas a configurações de sincronização, descritas abaixo. Alguns exemplos de propriedades de dispositivo incluem a versão de firmware, o nível de bateria e o nome do fabricante.

## Sincronização de propriedades de dispositivo

O dispositivo físico é a fonte autoritativa de propriedades de dispositivo. Os valores selecionados no dispositivo físico são sincronizados automaticamente com o dispositivo gêmeo no Hub IoT por meio do padrão *observar/notificar* descrito pelo [LWM2M][lnk-lwm2m].

Quando o dispositivo físico se conecta ao Hub IoT, o serviço inicia a *observação* nas propriedades de dispositivo selecionadas. Em seguida, o dispositivo físico *notifica* o Hub IoT de alterações às propriedades de dispositivo. Para implementar a histerese, **pmin** (o tempo mínimo entre as notificações) é definido como 5 minutos. Isso significa que, para cada propriedade, o dispositivo físico não notifica o Hub IoT com uma frequência maior que uma vez a cada 5 minutos, mesmo que haja uma alteração. Para garantir a atualização, **pmax** (o tempo máximo entre as notificações) é definido como 6 horas. Isso significa que, para cada propriedade, o dispositivo físico notifica o Hub IoT com uma frequência mínima de uma vez a cada 6 horas, mesmo que a propriedade não tenha sido alterada nesse período.

Quando o dispositivo físico se desconecta, a sincronização é interrompida. A sincronização será reiniciada quando o dispositivo se reconectar ao serviço. Você sempre pode verificar a hora da última atualização de uma propriedade para garantir a atualização.

A lista completa de propriedades de dispositivo que são observadas automaticamente é listada abaixo:

![][img-observed]


## Executando a amostra de dispositivo gêmeo

A amostra a seguir estende a funcionalidade do tutorial [Introdução ao gerenciamento de dispositivo Hub IoT do Azure][lnk-get-started]. Iniciando com diferentes dispositivos simulados em execução, ele usa o dispositivo gêmeo para ler e alterar as propriedades em um dispositivo simulado.

### Pré-requisitos 

Antes de executar esta amostra, é necessário concluir as etapas em [Introdução ao gerenciamento de dispositivo Hub IoT do Azure][lnk-get-started]. Isso significa que seus dispositivos simulados devem estar em execução. Se você concluiu o processo anteriormente, reinicie os dispositivos simulados agora.

### Iniciando a amostra

Para iniciar a amostra, é necessário executar o processo **DeviceTwin.exe**. Isso lê as propriedades de dispositivo do dispositivo gêmeo e do dispositivo físico. Ele também altera uma propriedade de dispositivo no dispositivo físico. Siga as etapas abaixo para iniciar a amostra:

1.  Na pasta raiz em que você clonou o repositório **azure-iot-sdks**, navegue até a pasta **azure-iot-sdks\\csharp\\service\\samples\\bin**.

2.  Execute `DeviceTwin.exe <IoT Hub Connection String>`.

Você deverá ver a saída na janela de linha de comando mostrando o uso do dispositivo gêmeo. A amostra passa pelo seguinte processo:

1.  Imprime todas as propriedades de dispositivo em um dispositivo gêmeo.

2.  Leitura profunda: ler a propriedade de dispositivo no nível de bateria por meio do dispositivo físico (3 vezes).

3.  Gravação profunda: gravar a propriedade de dispositivo **Fuso horário** no dispositivo físico.

4.  Leitura profunda: ler a propriedade de dispositivo **Fuso horário** por meio do dispositivo físico para ver se foi alterada.

### Leitura superficial

Há uma diferença entre leituras/gravações *superficiais* e *profundas*. Uma leitura superficial retorna o valor da propriedade solicitada do dispositivo gêmeo armazenado no Hub IoT do Azure. Esse será o valor da operação de notificação anterior. Não é possível fazer uma gravação superficial, pois o dispositivo físico é a fonte autoritativa de propriedades de dispositivo. Uma leitura superficial é simplesmente uma leitura da propriedade do dispositivo gêmeo:

```
device.DeviceProperties[DevicePropertyNames.BatteryLevel].Value.ToString();
```

Para determinar a atualização desses valores, é possível verificar a hora da última atualização:

```
device.DeviceProperties[DevicePropertyNames.BatteryLevel].LastUpdatedTime.ToString();
```

Da mesma forma, é possível ler as propriedades de serviço, que são armazenadas apenas no dispositivo gêmeo. Elas não são sincronizadas com o dispositivo.

### Leitura profunda

Uma leitura profunda inicia um trabalho de dispositivo para ler o valor da propriedade solicitada por meio do dispositivo físico. Os trabalhos do dispositivo foram introduzidos na [Visão geral do gerenciamento de dispositivo IoT do Azure][lnk-dm-overview] e são descritos em detalhes no [Tutorial: Como usar trabalhos do dispositivo para atualizar o firmware do dispositivo][lnk-dm-jobs]. A leitura profunda fornecerá um valor mais atualizado da propriedade de dispositivo, pois a atualização não é limitada pelo intervalo de notificação. O trabalho envia uma mensagem ao dispositivo físico e atualiza o dispositivo gêmeo com o valor mais recente apenas para a propriedade especificada. Ele não atualiza todo o dispositivo gêmeo.

```
JobResponse jobResponse = await deviceJobClient.ScheduleDevicePropertyReadAsync(Guid.NewGuid().ToString(), deviceId, propertyToRead);
```

Não é possível fazer uma leitura profunda nas propriedades de serviço ou nas marcas, pois elas não são sincronizadas com o dispositivo.

### Gravação profunda

Se desejar alterar uma propriedade de dispositivo gravável, você poderá fazer isso com uma gravação profunda que inicia um trabalho do dispositivo para gravar o valor no dispositivo físico. Nem todas as propriedades de dispositivo são graváveis. Para obter uma lista completa, veja o Apêndice A de [Introducing the Azure IoT Hub device management client library][lnk-dm-library] (Apresentando a biblioteca de cliente do gerenciamento de dispositivo Hub IoT do Azure).

O trabalho envia uma mensagem ao dispositivo físico para atualizar a propriedade especificada. O dispositivo gêmeo não é atualizado imediatamente quando o trabalho é concluído. É necessário aguardar até o próximo intervalo de notificação. Quando ocorrer a sincronização, será possível ver a alteração no dispositivo gêmeo com uma leitura superficial.

```
JobResponse jobResponse = await deviceJobClient.ScheduleDevicePropertyWriteAsync(Guid.NewGuid().ToString(), deviceId, propertyToSet, setValue); TODO
```

### Detalhes de implementação do simulador de dispositivo

Vamos investigar o que você precisa fazer no lado do dispositivo para implementar o padrão observar/notificar e as leituras/gravações profundas.

Como a sincronização das propriedades de dispositivo é abordada na íntegra por meio da biblioteca de cliente DM do Hub IoT do Azure, tudo que você precisa fazer é chamar a API para definir a propriedade de dispositivo (nível de bateria, neste exemplo) em intervalos regulares. Quando o serviço faz uma leitura profunda, o último valor definido é retornado. Quando o serviço faz uma gravação profunda, esse método Set é chamado. Em **iotdm\_simple\_sample.c**, é possível ver um exemplo disso:

```
int level = get_batterylevel();  // call to platform specific code 
set_device_batterylevel(0, level);
```

Em vez de usar o método Set, você poderá implementar um retorno de chamada. Para obter mais informações sobre essa opção, veja [Introducing the Azure IoT Hub device management client library][lnk-dm-library] (Apresentando a biblioteca de cliente do gerenciamento de dispositivo Hub IoT do Azure).

## Próximas etapas

Para saber mais sobre os recursos de gerenciamento de dispositivo Hub IoT do Azure, é possível acompanhar os tutoriais:

- [Como encontrar dispositivos gêmeos usando consultas][lnk-tutorial-queries]
- [Como usar trabalhos do dispositivo para atualizar o firmware do dispositivo][lnk-tutorial-jobs]
- [Habilitar dispositivos gerenciados atrás de um gateway IoT][lnk-dm-gateway]
- [Introdução à biblioteca de clientes gerenciamento de dispositivos do Hub IoT do Azure][lnk-library-c]
- As bibliotecas de cliente do gerenciamento de dispositivo fornecem uma amostra de ponta a ponta usando um [dispositivo Intel Edison][lnk-edison].

Para explorar melhor as funcionalidades do Hub IoT, consulte:

- [Projetar sua solução][lnk-design]
- [Guia do desenvolvedor][lnk-devguide]
- [Simular um dispositivo com o SDK do Gateway][lnk-gateway]
- [Usar o Portal do Azure para gerenciar o Hub IoT][lnk-portal]

<!-- images and links -->
[img-twin]: media/iot-hub-device-management-device-twin/image1.png
[img-observed]: media/iot-hub-device-management-device-twin/image2.png

[lnk-lwm2m]: http://technical.openmobilealliance.org/Technical/technical-information/release-program/current-releases/oma-lightweightm2m-v1-0
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-dm-library]: iot-hub-device-management-library.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-dm-jobs]: iot-hub-device-management-device-jobs.md
[lnk-edison]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/samples/iotdm_edison_sample


[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-tutorial-jobs]: iot-hub-device-management-device-jobs.md
[lnk-dm-gateway]: iot-hub-gateway-device-management.md
[lnk-library-c]: iot-hub-device-management-library.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0928_2016-->