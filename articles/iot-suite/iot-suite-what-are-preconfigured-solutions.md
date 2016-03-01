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
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/19/2016"
 ms.author="dobett"/>

# Quais são as soluções pré-configuradas do Pacote do Azure IoT?

As soluções pré-configuradas do Pacote IoT do Azure são implementações de padrões de solução de IoT comuns que você pode implantar no Microsoft Azure usando sua assinatura do Azure. Você pode usar as soluções pré-configuradas:

- Como ponto de partida para suas próprias soluções de IoT.
- Para saber mais sobre os padrões comuns no desenvolvimento e no design da solução de IoT.

Cada solução pré-configurada implementa um cenário comum de IoT e é uma implementação completa de ponta a ponta que usa dispositivos para gerar telemetria.

Além de implantar e de executar as soluções no Azure, você pode baixar o código-fonte completo, personalizar e estender a solução para atender às suas necessidades específicas de IoT.

> [AZURE.NOTE] O artigo [Introdução às soluções da IoT pré-configuradas][lnk-preconf-get-started] descreve como implantar e executar uma das soluções.

A tabela a seguir mostra como essas soluções são mapeadas para recursos específicos de IoT:

| Solução | Ingestão de dados | Identidade do dispositivo | Comando e controle | Regras e ações | Análise preditiva |
|------------------------|-----|-----|-----|-----|-----|
| [Monitoramento remoto][lnk-remote-monitoring] | Sim | Sim | Sim | Sim | - |
| [Manutenção preditiva][lnk-predictive-maintenance] | Sim | Sim | Sim | Sim | Sim |

## Visão geral da solução pré-configurada de Monitoramento Remoto

Nós optamos por abordar a solução pré-configurada de monitoramento remoto neste artigo porque ela é a mais simples das soluções e ilustra os elementos comuns de design compartilhados pelas outras soluções.

O diagrama a seguir ilustra os principais elementos da solução de monitoramento remoto. As seções abaixo fornecem mais informações sobre esses elementos.

![Arquitetura da solução pré-configurada de monitoramento remoto][img-remote-monitoring-arch]

## Dispositivos

Quando você implanta a solução pré-configurada de monitoramento remoto, quatro dispositivos simulados são previamente provisionados na solução que simula um dispositivo de resfriamento. Esses dispositivos simulados têm um modelo interno de temperatura interna e de umidade que emite a telemetria.

Quando um dispositivo primeiro se conecta ao Hub IoT na solução pré-configurada de monitoramento remoto, a mensagem de informações do dispositivo enviada para o hub IoT enumera a lista de comandos aos quais o dispositivo pode responder. Na solução pré-configurada de monitoramento remoto, os comandos são:

- *Fazer Ping no Dispositivo*. O dispositivo responde a esse comando com uma confirmação. Isso é útil para verificar se o dispositivo ainda está ativo e ouvindo.
- *Iniciar Telemetria*. Instrui o dispositivo para iniciar o envio de telemetria.
- *Parar Telemetria*. Instrui o dispositivo para parar o envio de telemetria.
- *Alterar Ponto de Definição da Temperatura*. Controla os valores de telemetria de temperatura simulados enviados pelo dispositivo. Isso é útil para teste.
- *Telemetria de Diagnóstico*. Controla se o dispositivo deve enviar a temperatura externa como telemetria.
- *Alterar Estado do Dispositivo*. Define a propriedade de metadados do estado do dispositivo relatado pelo dispositivo. Isso é útil para teste.

Você pode adicionar outros dispositivos simulados à solução que emite a mesma telemetria e responde aos mesmos comandos.

## Hub IoT

Um Hub IoT recebe telemetria dos dispositivos de resfriamento em um único ponto de extremidade. Um hub IoT também mantém os pontos de extremidade específicos do dispositivo onde cada dispositivo pode recuperar os comandos enviados a ele.

O Hub IoT disponibiliza os dados de telemetria que recebe através de um ponto de extremidade do grupo de consumidores.

Nesta solução pré-configurada, a instância do Hub IoT corresponde ao *Gateway de Nuvem* em uma [arquitetura da solução de IoT][lnk-what-is-azure-iot] típica.

## Stream Analytics do Azure

A solução pré-configurada usa três trabalhos do ASA ([Stream Analytics do Azure][lnk-asa]) para filtrar o fluxo de telemetria dos dispositivos de resfriamento:


- *Trabalho DeviceInfo* - envia mensagens específicas de registro do dispositivo para o registro do dispositivo da solução (um banco de dados do Banco de Dados de Documentos).
- *Trabalho de telemetria* - envia toda a telemetria bruta para o armazenamento de blobs do Azure para armazenamento frio e calcula as agregações de telemetria exibidas no painel de solução.
- *Trabalho de regras* - filtra o fluxo de telemetria para valores que excedem os limites de regra. Quando uma regra é acionada, a exibição de painel do portal da solução mostra esse evento como uma nova linha na tabela de histórico do alarme e dispara uma ação com base nas configurações definidas nas exibições Regras e Ações no portal da solução.

Nesta solução pré-configurada, os trabalhos ASA fazem parte do *back-end da solução de IoT* em uma [arquitetura de solução de IoT][lnk-what-is-azure-iot] típica.

## Processador de eventos

Uma instância de [EventPocessorHost][lnk-event-processor], em execução em um [WebJob][lnk-web-job], processa a saída dos trabalhos DeviceInfo e Regras.

Nesta solução pré-configurada, o processador de eventos faz parte do *back-end da solução de IoT* em uma [arquitetura da solução de IoT][lnk-what-is-azure-iot] típica.

## Portal de solução

![Painel da solução][img-dashboard]

O portal da solução é uma interface de usuário baseada na Web implantado na nuvem como parte da solução pré-configurada. Ele permite que você:

- Exiba o histórico de telemetria e de alarmes em um painel.
- Provisione novos dispositivos.
- Gerencie e monitore dispositivos.
- Envie comandos para dispositivos específicos.
- Gerencie regras e ações.

> [AZURE.NOTE] A solução pré-configurada mantém o [registro de identidade do dispositivo][lnk-identity-registry] do Hub IoT sincronizado com o registro de dispositivo da solução (banco de dados do Banco de Dados de Documentos), que armazena metadados de dispositivo mais variados.

Nesta solução pré-configurada, o portal da solução faz parte do *back-end de solução de IoT* e parte da *Conectividade de processamento e de negócios* em uma [arquitetura da solução de IoT][lnk-what-is-azure-iot] típica.

## Próximas etapas

Explore estes recursos para saber mais sobre as soluções pré-configuradas de IoT:

- [Introdução às soluções da IoT pré-configuradas][lnk-preconf-get-started]
- [Visão geral da solução pré-configurada de manutenção preditiva][lnk-predictive-maintenance]

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[img-dashboard]: ./media/iot-suite-what-are-preconfigured-solutions/dashboard.png
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

<!---HONumber=AcomDC_0224_2016-->