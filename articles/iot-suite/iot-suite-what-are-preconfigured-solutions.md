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
 ms.date="08/09/2016"
 ms.author="dobett"/>

# Quais são as soluções pré-configuradas do Pacote do Azure IoT?

As soluções pré-configuradas do Azure IoT Suite são implementações de padrões de solução IoT comuns que você pode implantar no Azure usando a sua assinatura. Você pode usar as soluções pré-configuradas:

- Como ponto de partida para suas próprias soluções de IoT.
- Para saber mais sobre os padrões comuns no desenvolvimento e no design da solução de IoT.

Cada solução pré-configurada é uma implementação completa de ponta a ponta que usa dispositivos simulados para gerar telemetria.

Além de implantar e de executar as soluções no Azure, você pode baixar o código-fonte completo, personalizar e estender a solução para atender às suas necessidades específicas de IoT.

> [AZURE.NOTE] Para implantar uma das soluções pré-configuradas, acesse o [Microsoft Azure IoT Suite][lnk-azureiotsuite]. O artigo [Introdução às soluções IoT pré-configuradas][lnk-getstarted-preconfigured] fornece mais informações sobre como implantar e executar uma das soluções.

A tabela a seguir mostra como essas soluções são mapeadas para recursos específicos de IoT:

| Solução | Ingestão de dados | Identidade do dispositivo | Comando e controle | Regras e ações | Análise preditiva |
|------------------------|-----|-----|-----|-----|-----|
| [Monitoramento remoto][lnk-getstarted-preconfigured] | Sim | Sim | Sim | Sim | - | | [Manutenção preditiva][lnk-predictive-maintenance] | Sim | Sim | Sim | Sim | Sim |

- *Ingestão de dados*: entrada de dados em escala para a nuvem.
- *Identidade do dispositivo*: gerenciar identidades exclusivas de cada dispositivo conectado.
- *Comando e controle*: enviar mensagens para um dispositivo a partir da nuvem para fazer com que o dispositivo execute uma ação.
- *Regras e ações*: o back-end da solução usa regras para atuar em dados específicos do dispositivo para a nuvem.
- *Análise preditiva*: o back-end da solução analisa dados de dispositivo para a nuvem para prever quando ações específicas devem ocorrer. Por exemplo, analisar a telemetria de motores de aeronave para determinar quando deve ser realizada a manutenção do motor.

## Visão geral da solução pré-configurada de Monitoramento Remoto

Optamos por abordar a solução pré-configurada de monitoramento remoto neste artigo porque ela ilustra vários elementos comuns de design compartilhados pelas outras soluções.

O diagrama a seguir ilustra os principais elementos da solução de monitoramento remoto. As seções abaixo fornecem mais informações sobre esses elementos.

![Arquitetura da solução pré-configurada de monitoramento remoto][img-remote-monitoring-arch]

## Dispositivos

Quando você implanta a solução pré-configurada de monitoramento remoto, quatro dispositivos simulados são previamente provisionados na solução que simula um dispositivo de resfriamento. Esses dispositivos simulados têm um modelo interno de temperatura interna e de umidade que emite a telemetria. Esses dispositivos simulados são incluídos para ilustrar o fluxo de dados de ponta a ponta por meio da solução e fornecer uma fonte conveniente de telemetria e um destino para os comandos se você for um desenvolvedor de back-end usando a solução como um ponto de partida para uma implementação personalizada.

Quando um dispositivo primeiro se conecta ao Hub IoT na solução pré-configurada de monitoramento remoto, a mensagem de informações do dispositivo enviada para o hub IoT enumera a lista de comandos aos quais o dispositivo pode responder. Na solução pré-configurada de monitoramento remoto, os comandos são:

- *Executar Ping*: o dispositivo responde a esse comando com uma confirmação. Isso é útil para verificar se o dispositivo ainda está ativo e ouvindo.
- *Iniciar Telemetria*: instrui o dispositivo a iniciar o envio de telemetria.
- *Parar Telemetria*: instrui o dispositivo a parar o envio de telemetria.
- *Alterar Ponto de Definição da Temperatura*: controla os valores de telemetria de temperatura simulados enviados pelo dispositivo. Isso é útil para testar a lógica de back-end:
- *Diagnosticar Telemetria*: controla se o dispositivo deve enviar a temperatura externa como telemetria.
- * Alterar Estado do Dispositivo*: define a propriedade de metadados do estado do dispositivo relatado pelo dispositivo. Isso é útil para testar a lógica de back-end:

Você pode adicionar mais dispositivos simulados à solução que emite a mesma telemetria e responde aos mesmos comandos.

## Hub IoT

Nesta solução pré-configurada, a instância do Hub IoT corresponde ao *Gateway de Nuvem* em uma [arquitetura da solução de IoT][lnk-what-is-azure-iot] típica.

Um Hub IoT recebe telemetria dos dispositivos em um único ponto de extremidade. Um hub IoT também mantém os pontos de extremidade específicos do dispositivo onde cada dispositivo pode recuperar os comandos enviados a ele.

O Hub IoT disponibiliza a telemetria recebida por meio do ponto de extremidade de leitura de telemetria no lado do serviço.

## Stream Analytics do Azure

A solução pré-configurada usa três trabalhos do ASA ([Stream Analytics do Azure][lnk-asa]) para filtrar o fluxo de telemetria dos dispositivos:


- *Trabalho de DeviceInfo* – envia dados para um Hub de Eventos que roteia mensagens específicas de registro de dispositivos, enviadas quando um dispositivo se conecta pela primeira vez ou em resposta a um comando **Alterar estado do dispositivo**, para o registro do dispositivo da solução ( um banco de dados do Banco de Dados de Documentos).
- *Trabalho de telemetria* – envia toda a telemetria bruta para o armazenamento de blobs do Azure para armazenamento frio e calcula as agregações de telemetria exibidas no painel de solução.
- *Trabalho de regras* – filtra o fluxo de telemetria para os valores que excedem os limites de regras e retornam os dados para um Hub de Eventos. Quando uma regra é acionada, a exibição de painel do portal da solução mostra esse evento como uma nova linha na tabela de histórico do alarme e dispara uma ação com base nas configurações definidas nas exibições Regras e Ações no portal da solução.

Nesta solução pré-configurada, os trabalhos ASA fazem parte do **back-end da solução IoT** em uma [arquitetura de solução IoT][lnk-what-is-azure-iot] típica.

## Processador de eventos

Nesta solução pré-configurada, o processador de eventos faz parte do **back-end da solução IoT** em uma [arquitetura de solução IoT][lnk-what-is-azure-iot] típica.

Os trabalhos ASA **DeviceInfo** e **Regras** enviam suas saídas para os Hubs de Eventos para entrega em outros serviços de back-end. A solução usa uma instância de [EventPocessorHost][lnk-event-processor], em execução em um [WebJob][lnk-web-job], para ler as mensagens desses Hubs de Eventos. O **EventProcessorHost** usa os dados de **DeviceInfo** para atualizar os dados do dispositivo no banco de dados do Banco de Dados de Documentos e usa os dados de **Regras** para invocar o aplicativo lógico e atualizar os alertas exibidos no portal de solução.

## Registro de identidade do dispositivo e Banco de Dados de Documentos

Cada Hub IoT inclui um [registro de identidade do dispositivo][lnk-identity-registry] que armazena as chaves do dispositivo. O Hub IoT usa esses dispositivos de autenticação de informação – um dispositivo deve ser registrado e ter uma chave válida para poder ser conectado ao Hub.

Essa solução armazena informações adicionais sobre dispositivos como seu estado, os comandos a que eles dão suporte e outros metadados. A solução usa um banco de dados do Banco de Dados de Documentos para armazenar esses dados de dispositivos específicos da solução e o portal de solução recupera dados desse banco de dados do Banco de Dados de Documentos para exibição e edição.

A solução também deverá manter as informações no registro de identidade do dispositivo sincronizadas com os conteúdos do banco de dados do Banco de Dados de Documentos. O **EventProcessorHost** usa os dados do trabalho de análise de fluxo de **DeviceInfo** para gerenciar a sincronização.

## Portal de solução

![Painel da solução][img-dashboard]

O portal da solução é uma interface de usuário baseada na Web implantado na nuvem como parte da solução pré-configurada. Ele permite que você:

- Exiba o histórico de telemetria e de alarmes em um painel.
- Provisione novos dispositivos.
- Gerencie e monitore dispositivos.
- Envie comandos para dispositivos específicos.
- Gerencie regras e ações.

Nesta solução pré-configurada, o portal de solução faz parte do **back-end de solução IoT** e parte da **Conectividade de processamento e de negócios** na [arquitetura de solução IoT][lnk-what-is-azure-iot] típica.

## Próximas etapas

Para obter mais informações sobre as arquiteturas de solução de IoT, confira [Serviços de IoT do Microsoft Azure: arquitetura de referência][lnk-refarch].

Agora você sabe o que é uma solução pré-configurada, poderá começar pela implantação da solução pré-configurada de *monitoramento remoto*: [Introdução às soluções pré-configuradas][lnk-getstarted-preconfigured].

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[img-dashboard]: ./media/iot-suite-what-are-preconfigured-solutions/dashboard.png
[lnk-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-processor]: ../event-hubs/event-hubs-programming-guide.md#event-processor-host
[lnk-web-job]: ../app-service-web/web-sites-create-web-jobs.md
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide.md#device-identity-registry
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-getstarted-preconfigured]: iot-suite-getstarted-preconfigured-solutions.md

<!---HONumber=AcomDC_0810_2016-->