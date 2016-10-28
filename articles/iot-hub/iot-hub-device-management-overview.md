<properties
 pageTitle="Visão geral do gerenciamento de dispositivo | Microsoft Azure"
 description="Visão geral do gerenciamento de dispositivos do Hub IoT do Azure"
 services="iot-hub"
 documentationCenter=""
 authors="bzurcher"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/16/2016"
 ms.author="bzurcher"/>



# Visão geral do gerenciamento de dispositivos do Hub IoT do Azure (preview)

## A abordagem de gerenciamento de dispositivo do Azure IoT

O gerenciamento de dispositivo do Hub IoT do Azure fornece os recursos e o modelo de extensibilidade para os dispositivos e back-ends aproveitarem o gerenciamento de dispositivos IoT para a diversidade de dispositivos e protocolos em IoT. Os dispositivos na IoT variam de sensores muito restritos, microcontroladores com finalidade única, a gateways mais poderosos que capacitam outros dispositivos e protocolos. As soluções de IoT também variam consideravelmente em domínios verticais e aplicativos com casos de uso exclusivos dos operadores em cada domínio. As soluções de IoT podem aproveitar os recursos, padrões e bibliotecas de código de gerenciamento de dispositivo do Hub IoT, para habilitar um gerenciamento de dispositivo para seu conjunto diversificado de dispositivos e usuários.

## Introdução

Uma parte fundamental da criação de uma solução de IoT bem-sucedida é fornecer uma estratégia para os operadores lidarem com o gerenciamento contínuo de sua frota de dispositivos. Os operadores de IoT exigem ferramentas e aplicativos simples e confiáveis, e que os permitam se concentrar nos aspectos mais estratégicos de seus trabalhos. O Hub IoT do Azure fornece a você os blocos de construção para criar aplicativos de IoT que facilitam os padrões mais importantes de gerenciamento de dispositivo.

Considera-se que os dispositivos estão sendo gerenciados pelo Hub IoT quando eles executam um aplicativo simples, chamado de agente de gerenciamento de dispositivo, que conecta o dispositivo com segurança à nuvem. O código do agente permite que um operador do lado do aplicativo confirme remotamente o status do dispositivo e realize operações de gerenciamento, como aplicar alterações de configuração de rede ou implantar atualizações de firmware.

## Princípios de gerenciamento de dispositivo IoT

O IoT traz com ele um conjunto exclusivo de desafios de gerenciamento e uma solução deve considerar os seguintes princípios de gerenciamento de dispositivo IoT:

![][img-dm_principles]

- **Escala e automação**: o IoT exige ferramentas simples que podem automatizar tarefas de rotina e capacitar uma equipe de operações relativamente pequena a gerenciar milhões de dispositivos. Diariamente, os operadores esperam lidar com operações de dispositivo remotamente, em massa, e só receberem alertas quando surgirem problemas que exijam sua atenção direta.

- **Abertura e compatibilidade**: o ecossistema de dispositivos do IoT é incrivelmente diverso. Ferramentas de gerenciamento devem ser personalizadas a fim de acomodar vários protocolos, classes e plataformas de dispositivos. Os operadores devem ser capazes de dar suporte a todos os dispositivos, desde os chips de processo único incorporados mais restritos, até computadores eficientes e totalmente funcionais.

- **Reconhecimento de contexto**: os ambientes de IoT são dinâmicos e estão em constante mudança. A confiabilidade do serviço é fundamental. Operações de gerenciamento de dispositivo devem considerar as janelas de manutenção do SLA, os estados de energia e de rede, as condições em uso e a localização geográfica do dispositivo para garantir que esse tempo de inatividade para a manutenção não afete operações comerciais críticas ou crie condições perigosas.

- **Atende muitas funções**: o suporte aos fluxos de trabalho e processos exclusivos das funções de operações do IoT é crucial. A equipe de operações também deve trabalhar de forma harmoniosa com as restrições específicas de departamentos de TI internos, e divulgar informações relevantes sobre as operações do dispositivo para os supervisores e outras funções de gerenciamento.

## Ciclo de vida do dispositivo IoT 

Embora os projetos de IoT sejam bastante variados, há um conjunto de padrões comuns de gerenciamento de dispositivos. No Azure IoT, esses padrões são identificados dentro do ciclo de vida do dispositivo IoT, que é composto por cinco estágios distintos:

![][img-device_lifecycle]

1. **Planejamento**: permita que os operadores criem um esquema de propriedade de dispositivo que os permita consultar de forma fácil e precisa, e selecionar um grupo de dispositivos para operações de gerenciamento em massa.

    *Blocos de construção relacionados*: [Introdução ao gêmeos de dispositivo][lnk-twins-getstarted], [Como usar propriedades gêmeas][lnk-twin-properties]

2. **Provisionamento**: autentique novos dispositivos com segurança no Hub IoT e permita que os operadores descubram imediatamente os recursos e o estado atual do dispositivo.

    *Blocos de construção relacionados*: [Introdução ao Hub IoT][lnk-hub-getstarted], [Como usar propriedades gêmeas][lnk-twin-properties]

3. **Configurar**: facilite as alterações de configuração em massa e atualizações de firmware em dispositivos enquanto mantém a integridade e a segurança.

    *Blocos de construção relacionados*: [Como usar propriedades gêmeas][lnk-twin-properties], [Métodos C2D][lnk-c2d-methods], [Trabalhos de transmissão/agendamento][lnk-jobs]

4. **Monitoramento**: monitore a integridade geral da frota de dispositivos e o status das implementações de atualização em andamento para alertar os operadores sobre problemas que possam exigir atenção.

    *Blocos de construção relacionados*: [Como usar propriedades gêmeas][lnk-twin-properties]

5. **Desativação**: substitua ou encerre os dispositivos após uma falha, ciclo de atualização ou ao final da vida útil do serviço.

    *Blocos de construção relacionados*:
    
## Padrões de gerenciamento de dispositivo do Hub IoT

O Hub IoT habilita o seguinte conjunto de padrões de gerenciamento de dispositivo (iniciais). Conforme exibido nos [tutoriais][lnk-get-started], você pode estender esses padrões para ajustar ao seu cenário exato e criar novos padrões para outros cenários com base nesses padrões principais.

1. **Reinicialização** - o aplicativo back-end informa ao dispositivo por meio de um método D2C que uma reinicialização foi iniciada. O dispositivo usa as propriedades reportadas do dispositivo gêmeo para atualizar o status de reinicialização do dispositivo.

    ![][img-reboot_pattern]

2. **Reinicialização de fábrica** - o aplicativo back-end informa ao dispositivo por meio de um método D2C que uma reinicialização de fábrica foi iniciada. O dispositivo usa as propriedades reportadas do dispositivo gêmeo para atualizar o status de reinicialização de fábrica do dispositivo.

    ![][img-facreset_pattern]

3. **Configuração** - o aplicativo de back-end usa as propriedades do dispositivo gêmeo desejado para configurar o software em execução no dispositivo. O dispositivo usa as propriedades reportadas do dispositivo gêmeo para atualizar o status de configuração do dispositivo.

    ![][img-config_pattern]

4. **Atualização de firmware** - o aplicativo back-end informa ao dispositivo por meio de um método D2C que uma atualização de firmeware foi iniciada. O dispositivo inicia um processo de várias etapas para baixar o pacote de firmware, aplicar o pacote de firmware e finalmente se reconectar ao serviço do Hub IoT. Durante o processo com várias etapas, o dispositivo usa as propriedades reportadas do dispositivo gêmeo para atualizar o progresso e o status do dispositivo.

    ![][img-fwupdate_pattern]

5. **Relatório de progresso e status** - O back-end do aplicativo executa consultas do dispositivo gêmeo, em um conjunto de dispositivos, para reportar o status e o progresso das ações em execução no dispositivo.

    ![][img-report_progress_pattern]

## Próximas etapas

Usando os blocos de construção fornecidos pelo Hub IoT do Azure, os desenvolvedores podem criar aplicativos de IoT que atendem os requisitos específicos de operador de IoT dentro em cada estágio do ciclo de vida de dispositivo.

Para continuar a aprender sobre as funcionalidades de gerenciamento de dispositivos do Hub IoT do Azure, confira o tutorial [Introdução ao gerenciamento de dispositivos do Hub IoT do Azure][lnk-get-started].

<!-- Images and links -->
[img-dm_principles]: media/iot-hub-device-management-overview/image4.png
[img-device_lifecycle]: media/iot-hub-device-management-overview/image5.png
[img-config_pattern]: media/iot-hub-device-management-overview/configuration-pattern.png
[img-facreset_pattern]: media/iot-hub-device-management-overview/facreset-pattern.png
[img-fwupdate_pattern]: media/iot-hub-device-management-overview/fwupdate-pattern.png
[img-reboot_pattern]: media/iot-hub-device-management-overview/reboot-pattern.png
[img-report_progress_pattern]: media/iot-hub-device-management-overview/report-progress-pattern.png

[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-twins-getstarted]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-hub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-methods]: iot-hub-c2d-methods.md
[lnk-jobs]: iot-hub-schedule-jobs.md

<!---HONumber=AcomDC_1005_2016-->