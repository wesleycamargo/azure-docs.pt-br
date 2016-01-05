<properties
 pageTitle="Passo a passo da solução pré-configurada de monitoramento remoto | Microsoft Azure"
 description="Uma descrição do monitoramento remoto pré-configurado da solução de IoT do Azure e sua arquitetura."
 services=""
 documentationCenter=""
 authors="stevehob"
 manager="timlt"
 editor=""/>

<tags
 ms.service="na"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/21/2015"
 ms.author="stevehob"/>

# Passo a passo da solução pré-configurada de monitoramento remoto

## Introdução

A solução pré-configurada de monitoramento remoto do IoT Suite é uma solução completa de monitoramento base para um cenário de negócios que opera vários computadores em locais remotos. A solução combina os principais serviços do Azure IoT Suite para fornecer uma implementação genérica do cenário de negócios e é um ponto de partida para clientes que pretendem implementar esse tipo de solução de IoT para atender às suas próprias necessidades de negócios específicas.

## Arquitetura lógica

O seguinte diagrama descreve os componentes lógicos da solução pré-configurada:

![](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)


### Dispositivos simulados

Na solução pré-configurada, o dispositivo simulado representa um dispositivo de resfriamento (como um ar-condicionado para edifícios ou uma unidade de tratamento de ar para instalações). Cada dispositivo simulado envia as seguintes mensagens de telemetria ao Hub IoT:


| Mensagem | Descrição |
|----------|-------------|
| Inicialização | Quando o dispositivo é iniciado, ele envia uma mensagem de **informações de dispositivo** contendo informações sobre si mesmo como a sua ID de dispositivo, metadados do dispositivo, uma lista de comandos que têm suporte no dispositivo e a configuração atual do dispositivo. |


Os dispositivos simulados enviam as seguintes propriedades de dispositivo como metadados:

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


Os dispositivos simulados podem lidar com os seguintes comandos enviados de um hub IoT:

| Command | Descrição |
|------------------------|-----------------------------------------------------|
| PingDevice | Envia um _ping_ ao dispositivo para verificar se ele está ativo |
| StartTelemetry | Inicia o dispositivo de envio de telemetria |
| StopTelemetry | Interrompe o dispositivo de enviar a telemetria |
| ChangeSetPointTemp | Altera o valor de ponto definido em torno do qual os dados aleatórios são gerados |
| DiagnosticTelemetry | Dispara o simulador de dispositivo para enviar um valor de telemetria adicional (externalTemp) |
| ChangeDeviceState | Altera uma propriedade estendida do estado do dispositivo e envia a mensagem de informações do dispositivo |


O reconhecimento de comando de dispositivo é fornecido por meio do hub IoT.


### Trabalhos do Stream Analytics do Azure

**Trabalho 1: a Telemetria** opera no fluxo de entrada da telemetria do dispositivo com o uso de dois comandos. O primeiro comando envia todas as mensagens de telemetria dos dispositivos para o armazenamento de blob persistente. O segundo comando calcula os valores de umidade média, mínima e máxima em uma janela deslizante de cinco minutos. Esses dados também são enviados para o armazenamento de blob.

**Trabalho 2: as Informações do Dispositivo** filtram as mensagens de informações de dispositivo do fluxo de mensagens de entrada e as envia para um ponto de extremidade do Hub de Eventos. Um dispositivo envia mensagens de informações do dispositivo na inicialização e em resposta a um comando **SendDeviceInfo**.

**Trabalho 3: as Regras** avaliam os valores de entrada da telemetria de temperatura e de umidade em relação aos limites de cada dispositivo. Os valores de limite são definidos no editor de regras incluído na solução. Cada par de dispositivo/valor é armazenado por carimbo de data/hora em um blob que é lido no Stream Analytics como **Dados de Referência**. O trabalho compara qualquer valor não vazio com o limite definido para o dispositivo. Se ele exceder a condição “>”, o trabalho gerará um evento de **alarme** que indica que o limite foi excedido e que fornece o dispositivo, o valor e os valores de carimbo de data/hora.

### Host do processador

O **Processador de Eventos** manipula as mensagens de informações de dispositivo e as respostas de comando. Ele usa:

- Mensagens de informações de dispositivo para atualizar o Registro do dispositivo (armazenado no banco de dados do Banco de Dados de Documentos) com as informações atuais do dispositivo.
- Mensagens de resposta de comando para atualizar o histórico de comandos do dispositivo (armazenados no banco de dados do Banco de Dados de Documentos).

## Vamos começar a praticar

Esta seção apresenta os componentes da solução, descreve o caso de uso pretendido e fornece exemplos.

### Painel de monitoramento remoto
Esta página no aplicativo Web usa os controles de JavaScript do Power BI (veja [repositório de elementos visuais do Power BI](https://www.github.com/Microsoft/PowerBI-visuals)) para visualizar os dados de saída dos trabalhos do Stream Analytics no armazenamento de blob.


### Portal de Administração do Dispositivo

O aplicativo Web permite que você:

- Provisione um novo dispositivo que define a ID exclusiva de dispositivo e gera a chave de autenticação.
- Gerencie as propriedades do dispositivo, que inclui a exibição das propriedades existentes e a atualização com novas propriedades.
- Envie comandos para um dispositivo.
- Exiba o histórico de comandos de um dispositivo.

### Observando o comportamento da solução de nuvem
Você pode exibir seus recursos provisionados acessando o [portal do Azure](https://portal.azure.com) e navegando até o grupo de recursos com o nome da solução especificada.

![](media/iot-suite-remote-monitoring-sample-walkthrough/azureportal_01.png)

Quando você executar o exemplo, há quatro dispositivos simulados pré-configurados:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_01.png)

É possível usar o portal de Administração do Dispositivo para adicionar um novo dispositivo simulado:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_02.png)

Inicialmente, o status do novo dispositivo no portal de Administração do Dispositivo é **Pendente**:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_03.png)

Quando o aplicativo concluir a implantação do dispositivo simulado, você verá o status do dispositivo alterar para **Em execução** no portal de Administração do Dispositivo, como mostrado na captura de tela a seguir. O trabalho **DeviceInfo** do Stream Analytics envia informações de status de dispositivo do dispositivo para o portal de Administração do Dispositivo.

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_04.png)

Usando o portal de solução, é possível enviar comandos como **ChangeSetPointTemp** para o dispositivo:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_05.png)

Quando o dispositivo relatar que ele executou o comando com êxito, o status será alterado para **Êxito**:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_06.png)

Usando o portal de solução, é possível pesquisar dispositivos com características específicas, como um número de modelo:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_07.png)

Você pode desabilitar um dispositivo e depois que ele for desabilitado, poderá removê-lo:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_08.png)

<!---HONumber=AcomDC_1203_2015-->