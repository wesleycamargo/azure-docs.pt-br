<properties
 pageTitle="Passo a passo da solução pré-configurada de monitoramento remoto | Microsoft Azure"
 description="Uma descrição do monitoramento remoto pré-configurado da solução de IoT do Azure e sua arquitetura."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="07/18/2016"
 ms.author="dobett"/>

# Passo a passo da solução pré-configurada de monitoramento remoto

## Introdução

A [solução pré-configurada][lnk-preconfigured-solutions] de monitoramento remoto do IoT Suite é uma implementação de uma solução de monitoramento de ponta a ponta para vários computadores em execução em locais remotos. A solução combina os principais serviços do Azure para fornecer uma implementação genérica do cenário de negócios, e você pode usá-la como ponto de partida para sua própria implementação. Você pode [personalizar][lnk-customize] a solução para atender a seus requisitos de negócios específicos.

Este artigo explica alguns dos principais elementos da solução de monitoramento remoto para que você possa entender como ela funciona. Esse conhecimento é útil para solucionar problemas da solução, para o planejamento de como personalizar a solução para atender a seus requisitos específicos e para o planejamento de sua própria solução de IoT que usa os serviços do Azure.

## Arquitetura lógica

O seguinte diagrama descreve os componentes lógicos da solução pré-configurada:

![Arquitetura lógica](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)


## Dispositivos simulados

Na solução pré-configurada, o dispositivo simulado representa um dispositivo de resfriamento (como um ar-condicionado para edifícios ou uma unidade de tratamento de ar para instalações). Ao implantar a solução pré-configurada, você também provisiona automaticamente quatro dispositivos simulados que são executados em um [Trabalho Web do Azure][lnk-webjobs]. Os dispositivos simulados facilitam a exploração do comportamento da solução sem a necessidade de implantar dispositivos físicos. Para implantar um dispositivo físico real, confira o tutorial [Conectar o dispositivo à solução pré-configurada de monitoramento remota][lnk-connectyourdevice].

Cada dispositivo simulado pode enviar os seguintes tipos de mensagens ao Hub IoT:

| Mensagem | Descrição |
|----------|-------------|
| Inicialização | Quando o dispositivo é iniciado, ele envia uma mensagem de **informações de dispositivo** contendo informações sobre si mesmo como a sua ID de dispositivo, metadados do dispositivo, uma lista de comandos que têm suporte no dispositivo e a configuração atual do dispositivo. |
| Presença | Um dispositivo envia periodicamente uma mensagem de **presença** para relatar se o dispositivo pode detectar ou não a presença de um sensor. |
| Telemetria | Um dispositivo envia periodicamente uma mensagem de **telemetria** que relata valores simulados de temperatura e umidade coletados dos sensores simulados conectados ao dispositivo simulado. |


Os dispositivos simulados enviam as seguintes propriedades de dispositivo em uma mensagem **device-info**:

| Propriedade | Finalidade |
|------------------------|--------- |
| Id do Dispositivo | ID que é fornecida ou atribuída quando um dispositivo é criado na solução. |
| Fabricante | Fabricante do dispositivo |
| Número do modelo | O número do modelo do dispositivo |
| Número de série | Número de série do dispositivo |
| Firmware | Versão atual do firmware no dispositivo |
| Plataforma | Arquitetura da plataforma do dispositivo |
| Processador | Processador que executa o dispositivo |
| RAM instalada | Quantidade de RAM instalada no dispositivo |
| Estado habilitado para hub | Propriedade de estado do Hub IoT do dispositivo |
| Hora de criação | Hora de criação do dispositivo na solução |
| Tempo atualizado | As propriedades da última vez foram atualizadas para o dispositivo |
| Latitude | Localização de latitude do dispositivo |
| Longitude | Localização de longitude do dispositivo |

O simulador propaga essas propriedades em dispositivos simulados com valores de exemplo. Sempre que o simulador inicializa um dispositivo simulado, o dispositivo posta os metadados predefinidos no Hub IoT. Observe que isso substitui todas as atualizações de metadados feitas no portal do dispositivo.


Os dispositivos simulados podem lidar com os seguintes comandos enviados do painel de solução por meio do hub IoT:

| Command | Descrição |
|------------------------|-----------------------------------------------------|
| PingDevice | Envia um _ping_ ao dispositivo para verificar se ele está ativo |
| StartTelemetry | Inicia o dispositivo de envio de telemetria |
| StopTelemetry | Interrompe o dispositivo de enviar a telemetria |
| ChangeSetPointTemp | Altera o valor de ponto definido em torno do qual os dados aleatórios são gerados |
| DiagnosticTelemetry | Dispara o simulador de dispositivo para enviar um valor de telemetria adicional (externalTemp) |
| ChangeDeviceState | Altera uma propriedade estendida do estado do dispositivo e envia a mensagem de informações do dispositivo |

O reconhecimento de comando de dispositivo para o back-end da solução é fornecido por meio do hub IoT.

## Hub IoT

O [hub IoT][lnk-iothub] consome dados enviados dos dispositivos para a nuvem e os disponibiliza para os trabalhos do Stream Analytics Azure (ASA). O hub IoT também envia comandos aos dispositivos em nome do portal do dispositivo. Cada trabalho de fluxo do ASA usa um grupo separado de consumidores do Hub IoT para ler o fluxo de mensagens dos dispositivos.

## Stream Analytics do Azure

Na solução de monitoramento remota, o [Azure Stream Analytics][lnk-asa] (ASA) envia mensagens recebidas pelo hub IoT dos dispositivos para outros componentes de back-end para processamento ou armazenamento. Diferentes trabalhos do ASA executam funções específicas com base no conteúdo das mensagens.

**Trabalho 1: as Informações do Dispositivo** filtram as mensagens de informações de dispositivo do fluxo de mensagens de entrada e as enviam para um ponto de extremidade do Hub de Eventos. Um dispositivo envia mensagens de informações do dispositivo na inicialização e em resposta a um comando **SendDeviceInfo**. Essa tarefa usa a seguinte definição de consulta para identificar mensagens **device-info**:

```
SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'
```

Esse trabalho envia a saída a um Hub de Eventos para processamento adicional.

**Trabalho 2: as Regras** avaliam os valores de entrada da telemetria de temperatura e de umidade em relação aos limites de cada dispositivo. Valores de limite são definidos no editor de regras disponível no painel de solução. Cada par de dispositivo/valor é armazenado por carimbo de data/hora em um blob que o Stream Analytics lê como **Dados de Referência**. O trabalho compara qualquer valor não vazio com o limite definido para o dispositivo. Se ele exceder a condição “>”, o trabalho gerará um evento de **alarme** que indica que o limite foi excedido e que fornece o dispositivo, o valor e os valores de carimbo de data/hora. Esse trabalho usa a seguinte definição de consulta para identificar mensagens de telemetria que devem disparar um alarme:

```
WITH AlarmsData AS 
(
SELECT
     Stream.DeviceID,
     'Temperature' as ReadingType,
     Stream.Temperature as Reading,
     Ref.Temperature as Threshold,
     Ref.TemperatureRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.DeviceID = Ref.DeviceID
WHERE
     Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature

UNION ALL

SELECT
     Stream.DeviceID,
     'Humidity' as ReadingType,
     Stream.Humidity as Reading,
     Ref.Humidity as Threshold,
     Ref.HumidityRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.DeviceID = Ref.DeviceID
WHERE
     Ref.Humidity IS NOT null AND Stream.Humidity > Ref.Humidity
)

SELECT *
INTO DeviceRulesMonitoring
FROM AlarmsData

SELECT *
INTO DeviceRulesHub
FROM AlarmsData
```

O trabalho envia a saída a um Hub de Eventos para processamento adicional e salva os detalhes de cada alerta no armazenamento de blobs, em que o painel de solução pode ler as informações do alerta.

**Trabalho 3: a Telemetria** opera no fluxo de entrada da telemetria do dispositivo de duas maneiras. A primeira envia todas as mensagens de telemetria dos dispositivos ao armazenamento de blobs persistente para armazenamento de longo prazo. A segunda calcula valores de umidade média, mínima e máxima em um janela deslizante de cinco minutos e envia os dados ao armazenamento de blobs. O painel de solução lê os dados de telemetria do armazenamento de blobs para popular os gráficos. Esse trabalho usa a seguinte definição de consulta:

```
WITH 
    [StreamData]
AS (
    SELECT
        *
    FROM 
      [IoTHubStream] 
    WHERE
        [ObjectType] IS NULL -- Filter out device info and command responses
) 

SELECT
    *
INTO
    [Telemetry]
FROM
    [StreamData]

SELECT
    DeviceId,
    AVG (Humidity) AS [AverageHumidity], 
    MIN(Humidity) AS [MinimumHumidity], 
    MAX(Humidity) AS [MaxHumidity], 
    5.0 AS TimeframeMinutes 
INTO
    [TelemetrySummary]
FROM
    [StreamData]
WHERE
    [Humidity] IS NOT NULL
GROUP BY
    DeviceId, 
    SlidingWindow (mi, 5)
```

## Hubs de Eventos

Os trabalhos do ASA de **informações do dispositivo** e **regras** enviam seus dados a Hubs de Eventos para encaminhamento confiável ao **Processador de Eventos** em execução no Trabalho Web.

## Armazenamento do Azure

A solução usa o armazenamento de blobs do Azure para persistir todos os dados de telemetria brutos e resumidos dos dispositivos na solução. O painel lê os dados de telemetria do armazenamento de blobs para popular os gráficos. Para exibir alertas, o painel lê os dados do armazenamento de blobs que registra quando os valores de telemetria excederam os valores de limite configurados. A solução também usa o armazenamento de blobs para registrar os valores de limite definidos por um usuário no painel.

## Trabalhos Web

Além de hospedar os simuladores de dispositivo, os Trabalhos Web na solução também hospedam o **Processador de Eventos** em execução em um Trabalho Web do Azure que manipula mensagens de informações de dispositivo e respostas de comando. Ele usa:

- Mensagens de informações de dispositivo para atualizar o Registro do dispositivo (armazenado no banco de dados do Banco de Dados de Documentos) com as informações atuais do dispositivo.
- Mensagens de resposta de comando para atualizar o histórico de comandos do dispositivo (armazenados no banco de dados do Banco de Dados de Documentos).

## Banco de Dados de Documentos

A solução usa um banco de dados do Banco de Dados de Documentos para armazenar informações sobre os dispositivos conectados à solução, como metadados de dispositivo e o histórico de comandos enviados aos dispositivos do painel.

## Aplicativos Web

### Painel de monitoramento remoto
Essa página no aplicativo Web usa controles JavaScript do PowerBI (confira [Repositório PowerBI-visuals](https://www.github.com/Microsoft/PowerBI-visuals)) para visualizar os dados de telemetria dos dispositivos. A solução usa o trabalho de telemetria do ASA para gravar os dados de telemetria no armazenamento de blobs.


### Portal de administração de dispositivo

O aplicativo Web permite que você:

- Provisione um novo dispositivo. Isso define a ID de dispositivo exclusiva e gera a chave de autenticação. Ele grava informações sobre o dispositivo no registro de identidade do Hub IoT e no banco de dados do Banco de Dados de Documentos específico da solução.
- Gerencie propriedades de dispositivo. Isso inclui a exibição de propriedades existentes e a atualização com novas propriedades.
- Envie comandos para um dispositivo.
- Exiba o histórico de comandos de um dispositivo.
- Habilite e desabilite dispositivos.

## Próximas etapas

As postagens de blog do TechNet abaixo fornecem mais detalhes sobre a solução pré-configurada de monitoramento remoto:

- [IoT Suite - Under the Hood - Remote Monitoring (Monitoramento remoto)](http://social.technet.microsoft.com/wiki/contents/articles/32941.iot-suite-under-the-hood-remote-monitoring.aspx)
- [IoT Suite - Remote Monitoring - Adding Live and Simulated Devices (Adicionando dispositivos ativos e simulados)](http://social.technet.microsoft.com/wiki/contents/articles/32975.iot-suite-remote-monitoring-adding-live-and-simulated-devices.aspx)


[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-connectyourdevice]: iot-suite-connecting-devices.md
[lnk-iothub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-webjobs]: https://azure.microsoft.com/documentation/articles/websites-webjobs-resources/

<!---HONumber=AcomDC_0720_2016-->