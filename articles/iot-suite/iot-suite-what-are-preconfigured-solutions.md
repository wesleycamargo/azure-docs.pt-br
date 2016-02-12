<properties
 pageTitle="Soluções pré-configuradas do Azure IoT | Microsoft Azure"
 description="Uma descrição das soluções pré-configuradas IoT Azure e sua arquitetura com links para recursos adicionais."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="11/30/2015"
 ms.author="dobett"/>

# Quais são as soluções pré-configuradas do Pacote do Azure IoT?

As soluções pré-configuradas do Pacote IoT do Azure são implementações de padrões de solução de IoT comuns que você pode implantar no Microsoft Azure usando sua assinatura do Azure. Você pode usar as soluções pré-configuradas:

- Como ponto de partida para suas próprias soluções de IoT.
- Para saber mais sobre os padrões comuns no desenvolvimento e no design da solução de IoT.

Cada solução pré-configurada implementa um cenário comum de IoT e é uma implementação completa de ponta a ponta que usa dispositivos para gerar telemetria.

Além de implantar e de executar as soluções pré-configuradas no Azure, você pode baixar o código-fonte completo, personalizar e estender a solução para atender às suas necessidades específicas de IoT.

As soluções pré-configuradas disponíveis são:

- [Monitoramento remoto][lnk-remote-monitoring]
- [Manutenção preditiva][lnk-predictive-maintenance]

A tabela a seguir mostra como essas soluções pré-configuradas são mapeadas para recursos específicos de IoT:

| Solução | Ingestão de dados | Identidade do dispositivo | Comando e controle | Regras e ações | Análise preditiva |
|------------------------|-----|-----|-----|-----|-----|
| Monitoramento remoto | Sim | Sim | Sim | Sim | - |
| Manutenção preditiva | Sim | Sim | Sim | Sim | Sim |

## Visão geral da solução pré-configurada de Monitoramento Remoto

Esta seção descreve alguns dos principais elementos da solução pré-configurada de monitoramento remoto. O monitoramento remoto é a mais simples das soluções pré-configuradas e ilustra os elementos comuns de design compartilhados pelas outras soluções pré-configuradas.

O diagrama a seguir ilustra os principais elementos da solução de monitoramento remoto. As seções abaixo fornecem mais informações sobre esses elementos.

![Arquitetura da solução pré-configurada de monitoramento remoto][img-remote-monitoring-arch]

## Dispositivos

Quando você implanta a solução pré-configurada de monitoramento remoto, a implantação inclui instâncias de um simulador de dispositivo de software que simula um dispositivo físico de resfriamento. Os dispositivos simulados enviam telemetria de temperatura e umidade a um ponto de extremidade de Hub IoT. Os dispositivos simulados também respondem aos seguintes comandos enviados do portal da solução por meio do Hub IoT:

- Executar Ping
- Iniciar Telemetria
- Parar Telemetria
- Alterar Ponto de Definição da Temperatura
- Diagnosticar Telemetria
- Alterar Estado do Dispositivo

## Hub IoT

Um Hub IoT recebe telemetria dos dispositivos de resfriamento em um único ponto de extremidade. Um Hub IoT também mantém os pontos de extremidade específicos do dispositivo onde cada dispositivo possa recuperar os comandos, como o comando **Executar Ping**, que são enviados a ele.

O Hub IoT disponibiliza os dados de telemetria que recebe através de um ponto de extremidade do grupo de consumidores.

Nesta solução pré-configurada, a instância do Hub IoT corresponde ao *Gateway de Nuvem* em uma [arquitetura da solução de IoT][lnk-what-is-azure-iot] típica.

## Stream Analytics do Azure

A solução pré-configurada usa três trabalhos do ASA ([Stream Analytics do Azure][lnk-asa]) para filtrar o fluxo de telemetria dos dispositivos de resfriamento:

- O Trabalho 1 envia toda a telemetria ao Armazenamento de Blobs do Azure para armazenamento frio
- O Trabalho 2 filtra o fluxo de telemetria a fim de identificar mensagens de resposta do comando e mensagens de atualização de status dos dispositivos, e envia essas mensagens específicas a um ponto de extremidade de Hub de Eventos do Azure.
- O Trabalho 3 filtra o fluxo de telemetria para valores que disparam alarmes. Quando um valor dispara um alarme, a solução exibe a notificação na tabela de histórico de alarmes, no modo de exibição do painel do portal da solução.

Nesta solução pré-configurada, os trabalhos do ASA formam parte do *back-end da solução de IoT* em uma [arquitetura da solução de IoT][lnk-what-is-azure-iot] típica.

## Processador de eventos

Uma instância do [EventPocessorHost][lnk-event-processor], em execução em um [trabalho da web][lnk-web-job], processa a resposta do comando e as mensagens de status do dispositivo identificadas pelo Trabalho 2 do ASA, e armazena essas informações em um banco de dados do [Banco de Dados de Documentos do Azure][lnk-document-db].

Nesta solução pré-configurada, o processador de eventos faz parte do *back-end da solução de IoT* em uma [arquitetura da solução de IoT][lnk-what-is-azure-iot] típica.

## Portal de solução

O portal da solução é uma interface de usuário baseada na Web implantado na nuvem como parte da solução pré-configurada. Ele permite que você:

- Exiba o histórico de telemetria e de alarmes em um painel.
- Provisione novos dispositivos.
- Gerencie e monitore dispositivos.
- Envie comandos para dispositivos específicos.
- Gerencie regras e ações.

> [AZURE.NOTE] O portal da solução também mantém o [registro de identidade de dispositivo][lnk-identity-registry] do Hub IoT sincronizado com o armazenamento de informações mais detalhadas de estado do dispositivo no banco de dados do Banco de Dados de Documentos da solução.

Nesta solução pré-configurada, o portal da solução faz parte do *back-end de solução de IoT* e parte da *Conectividade de processamento e de negócios* em uma [arquitetura de solução de IoT][lnk-what-is-azure-iot] típica.

## Próximas etapas

Explore estes recursos para saber mais sobre as soluções pré-configuradas de IoT:

- [Visão geral das soluções do Azure IoT pré-configuradas][lnk-suite-overview]
- [Introdução às soluções da IoT pré-configuradas][lnk-preconf-get-started]

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-processor]: event-hubs-programming-guide.md#event-processor-host
[lnk-web-job]: web-sites-create-web-jobs.md
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-identity-registry]: iot-hub-devguide.md#device-identity-registry
[lnk-suite-overview]: iot-suite-overview.md
[lnk-preconf-get-started]: iot-suite-getstarted-preconfigured-solutions.md
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md

<!---HONumber=AcomDC_0204_2016-->