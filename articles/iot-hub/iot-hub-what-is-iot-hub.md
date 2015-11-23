<properties
 pageTitle="Visão geral do Hub IoT do Azure | Microsoft Azure"
 description="Uma visão geral do serviço Hub IoT do Azure, incluindo a conectividade do dispositivo, os padrões de comunicação, e o padrão de comunicação assistida de serviço"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="11/09/2015"
 ms.author="dobett"/>

# O que é o Hub IoT do Azure?

Bem-vindo ao Hub IoT do Azure. Este artigo fornece uma visão geral do Hub IoT do Azure e descreve o motivo pelo qual você pode querer usar esse serviço ao implementar uma solução de IoT.

O Hub IoT do Azure é um serviço totalmente gerenciado que permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos IoT e um back-end da solução. O Hub IoT do Azure oferece um sistema de mensagens confiável em grande escala da nuvem para dispositivo e do dispositivo para a nuvem, permite comunicações seguras usando credenciais de segurança e controle de acesso por dispositivo, além de incluir bibliotecas de dispositivos para os idiomas e as plataformas mais populares.

![O Hub IoT como um gateway de nuvem?][img-architecture]

## Desafios de conectividade do dispositivo IoT

O Hub IoT e as bibliotecas de dispositivo ajudam a enfrentar os desafios de como conectar dispositivos de maneira confiável e segura ao back-end da solução. Dispositivos IoT:

- Com frequência, são sistemas internos sem operadores humanos.
- Eles podem estar em locais remotos, onde o acesso físico é muito caro.
- Só podem ser acessados por meio do back-end da solução.
- Podem ter recursos de energia e de processamento limitados.
- Podem ter conectividade de rede intermitente, lenta ou cara.
- Talvez precisem de protocolos de aplicativo proprietários, personalizados ou específicos do setor.
- Podem ser criados usando um grande conjunto popular de plataformas de hardware e de software.

Além dos requisitos acima, todas as soluções IoT também deverão oferecer escala, segurança e confiabilidade. O conjunto de requisitos de conectividade resultante tem implementação difícil e demorada por meio das tecnologias tradicionais, como contêineres da Web e agentes de mensagens.

## Porque usar o Hub IoT do Azure

O Hub IoT do Azure enfrenta os desafios de conectividade do dispositivo das seguintes maneiras:

-   **Autenticação por dispositivo e conectividade segura**. Você pode provisionar cada dispositivo com sua própria chave de segurança para permitir que ele se conecte ao Hub IoT. Um back-end de solução pode incluir dispositivos individuais em listas brancas e listas negras, permitindo o controle completo sobre o acesso a dispositivos.

-   **Um amplo conjunto de bibliotecas de dispositivos**. Os SDKs de dispositivo IoT do Azure estão disponíveis e têm suporte para uma variedade de idiomas e plataformas: C para várias distribuições do Linux, Windows e RTOS. SDKs do dispositivo IoT do Azure também dão suporte a linguagens gerenciadas, como C#, Java e JavaScript.

-   **Protocolos e extensibilidade do IoT**. Se sua solução não puder usar as bibliotecas de dispositivos, o Hub IoT exporá um protocolo público para permitir que os dispositivos usem os protocolos HTTP 1.1 e AMQP 1.0 nativamente. Você também pode estender o Hub IoT para dar suporte ao MQTT v3.1.1 com o componente de software livre do [Gateway do Protocolo IoT do Azure][protocol-gateway]. Você pode executar o Gateway do Protocolo IoT do Azure na nuvem ou no local e estendê-lo para oferecer suporte a protocolos personalizados.

-   **Escala**. O Hub IoT do Azure pode ser dimensionado para milhões de dispositivos conectados simultaneamente, além de milhões de eventos por segundo.

Esses benefícios são genéricos para vários padrões de comunicação. No momento, o Hub IoT permite que você implemente os seguintes padrões de comunicação específicos:

-   **Ingestão de dispositivo para nuvem baseada em evento.** O Hub IoT pode receber milhões de eventos por segundo de forma confiável de seus dispositivos e pode processá-los em seu afunilamento usando um mecanismo de processador de eventos ou armazená-los em seu caminho de ampliação para análise. O Hub IoT retém os dados de eventos por até sete dias para garantir o processamento confiável e para absorver picos na carga.

-   **Sistema de mensagens confiáveis da nuvem para o dispositivo (ou *comandos*).** O back-end da solução pode usar o Hub IoT para enviar mensagens com uma garantia de entrega de pelo menos uma vez para dispositivos individuais. Cada mensagem tem uma configuração de vida útil individual, e o back-end pode solicitar recibos de entrega e de expiração para garantir a visibilidade completa da vida útil de uma mensagem da nuvem para o dispositivo. Isso permite que você implemente a lógica de negócios que inclui as operações executadas em dispositivos.

Você também pode implementar outros padrões comuns, como carregamento e download de arquivos, aproveitando os recursos específicos do Hub IoT, como o gerenciamento consistente de identidades de dispositivo, o monitoramento de conectividade e a escala.

## Como funciona o Hub IoT?

O Hub IoT do Azure implementa o padrão [Comunicação Assistida de Serviço][lnk-service-assisted-pattern] para intermediar as interações entre seus dispositivos e seu back-end da solução. A meta de comunicação assistida de serviço é estabelecer caminhos de comunicação bidirecional confiáveis entre um sistema de controle como o Hub IoT e dispositivos de finalidade especial implantados em um espaço físico não confiável. O padrão estabelece os seguintes princípios:

- A segurança tem precedência sobre todos os outros recursos.
- Os dispositivos não aceitam informações de rede não solicitadas. Um dispositivo estabelece todas as conexões e as rotas somente de saída. Para que um dispositivo receba um comando do back-end, ele deverá iniciar regularmente uma conexão para verificar a existência de qualquer comando pendente a ser processado.
- Os dispositivos só devem se conectar ou estabelecer rotas para serviços bem conhecidos, como um hub IoT, com quem estejam emparelhados.
- O caminho de comunicação entre o dispositivo e o serviço ou o dispositivo e o gateway é protegido na camada de protocolo de aplicativo.
- A autorização e a autenticação no nível do sistema se baseiam em identidades por dispositivo e tornam as credenciais e as permissões de acesso instantaneamente revogáveis.
- A comunicação bidirecional para dispositivos que se conectam esporadicamente devido a preocupações com energia ou conectividade é facilitada pela retenção de comandos e de notificações de dispositivo até um dispositivo se conectar para recebê-los. O Hub IoT mantém filas específicas de dispositivo para os comandos enviados.
- Os dados de carga do aplicativo são protegidos separadamente para proteger o tráfego que passa pelos gateways até um serviço específico.

O padrão de comunicação assistida de serviço tem sido usado com êxito no setor de serviços móveis em grande escala para a implementação de serviços de notificação por push, como o [Serviço de Notificação do Windows](https://msdn.microsoft.com/library/windows/apps/mt187203.aspx), o [Google Cloud Messaging](https://developers.google.com/cloud-messaging/) e o [Apple Push Notification Service](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9).

## Próximas etapas

Para saber mais sobre o Hub IoT do Azure, consulte estes links:

* [Introdução ao Hub IoT]
* [Conectar seu dispositivo]
* [Processar mensagens de dispositivo para nuvem]

[Introdução ao Hub IoT]: iot-hub-csharp-csharp-getstarted.md
[Conectar seu dispositivo]: https://azure.microsoft.com/develop/iot/
[Processar mensagens de dispositivo para nuvem]: iot-hub-csharp-csharp-process-d2c.md
[protocol-gateway]: https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md
[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png

[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "Comunicação Assistida de Serviço, postagem de blog feita por Clemens Vasters"

<!---HONumber=Nov15_HO3-->