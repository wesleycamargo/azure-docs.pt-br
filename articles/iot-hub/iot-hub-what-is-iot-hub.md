<properties
 pageTitle="O que é o Hub IoT do Azure | Microsoft Azure"
 description="Uma visão geral do serviço Hub IoT do Azure, incluindo a conectividade do dispositivo, os padrões de comunicação e o padrão de comunicação assistida de serviço"
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="kevinmil"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="09/29/2015"
 ms.author="elioda"/>

# O que é o Hub IoT do Azure?

 O Hub IoT do Azure é um serviço totalmente gerenciado que permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos IoT e um back-end de aplicativo. O Hub IoT do Azure oferece um sistema de mensagens confiável em grande escala da nuvem para dispositivo e do dispositivo para a nuvem, permite comunicações seguras usando credenciais de segurança e controle de acesso por dispositivo, além de incluir bibliotecas de dispositivos para os idiomas e as plataformas mais populares.

![O Hub IoT como um gateway de nuvem?][img-architecture]

## Conectividade do dispositivo IoT

Um dos maiores desafios enfrentados por projetos IoT é sobre conectar dispositivos de forma confiável e segura ao back-end de aplicativo. Geralmente, os dispositivos IoT têm características diferentes em comparação a outros clientes, como navegadores, outros servidores e aplicativos móveis:

-   Eles costumam ser sistemas inseridos sem operadores humanos.

-   Eles podem estar em locais remotos, onde o acesso físico é muito caro.

-   A conectividade com o back-end do aplicativo pode ser a única maneira de acessar o dispositivo.

-   Eles podem ter recursos limitados de energia e/ou de processamento.

-   A conectividade de rede pode ser intermitente, cara ou escassa para o dispositivo.

-   Talvez seja necessário usar protocolos de aplicativo proprietários, personalizados ou específicos do setor.

-   Há um amplo conjunto de plataformas de hardware e de software populares para a criação de dispositivos.

Além dos requisitos acima, todas as soluções IoT também deverão oferecer escala, segurança e confiabilidade. Todos esses aspectos se combinam em um conjunto de requisitos de conectividade de implementação difícil e demorada por meio das tecnologias tradicionais, como contêineres da Web e agentes de mensagens.

## Porque usar o Hub IoT do Azure

O Hub IoT do Azure aborda os requisitos de conectividade do dispositivo das seguintes maneiras:

-   **Autenticação por dispositivo e conectividade segura**. Cada dispositivo pode usar sua própria chave de segurança para se conectar ao Hub IoT. O back-end de aplicativo pode então incluir os dispositivos em listas brancas e negras, permitindo o controle total sobre o acesso do dispositivo.

-   **Um amplo conjunto de bibliotecas de dispositivos**. Os SDKs de dispositivo IoT do Azure estão disponíveis e têm suporte para uma variedade de idiomas e plataformas: C para várias distribuições do Linux, Windows e RTOS. SDKs do dispositivo IoT do Azure também dão suporte a linguagens gerenciadas, como C#, Java e JavaScript.

-   **Protocolos e extensibilidade do IoT**. Se sua solução não puder usar as bibliotecas de dispositivos, o Hub IoT exporá um protocolo público para permitir que os dispositivos usem os protocolos HTTP 1.1 e AMQP 1.0 nativamente. Você também pode estender o Hub IoT para dar suporte ao MQTT v3.1.1 com o componente de software livre do Gateway do Protocolo IoT do Azure. Você pode executar o Gateway do Protocolo IoT do Azure na nuvem ou no local e estendê-lo para oferecer suporte a protocolos personalizados.

-   **Escala**. O Hub IoT do Azure pode ser dimensionado para milhões de dispositivos conectados simultaneamente, além de milhões de eventos por segundo.

Além desses benefícios, que são genéricos em vários padrões de comunicação, o Hub IoT atualmente permite que você implemente os seguintes padrões de comunicação:

-   **Ingestão de dispositivo para nuvem baseada em evento.** Os dispositivos podem enviar de forma confiável milhões de eventos por segundo para processamento no mecanismo do processador de eventos de afunilamento ou para armazenamento para análise de dispersão. O Hub IoT retém os dados de eventos por até 7 dias para garantir o processamento confiável e para absorver picos na carga.

-   **Sistema de mensagens confiáveis da nuvem para o dispositivo (ou *comandos*).** O back-end do aplicativo pode enviar mensagens confiáveis (ou seja, com garantia de pelo menos uma entrega) para dispositivos individuais. Cada mensagem tem uma configuração de vida útil individual, e o back-end pode solicitar recibos de entrega e de expiração para garantir a visibilidade completa da vida útil de uma mensagem da nuvem para o dispositivo.

Além desses padrões de comunicação, você pode implementar outros padrões populares, como upload e download de arquivos, aproveitando os recursos específicos do Hub IoT, como o gerenciamento consistente de identidades de dispositivo, o monitoramento de conectividade e a escala.

## Padrão Comunicação Assistida de Serviço

O Hub IoT do Azure intermedeia as interações entre seus dispositivos e seu back-end de aplicativo em uma implementação do padrão [Comunicação Assistida de Serviço][lnk-service-assisted-pattern]. A meta de comunicação assistida de serviço é estabelecer caminhos de comunicação bidirecional confiáveis entre sistemas de controle (como o Hub IoT) e dispositivos de finalidade especial implantados em um espaço físico não confiável. Para esse fim, o padrão estabelece os seguintes princípios:

- A segurança supera todos os outros recursos.
- Os dispositivos não aceitam informações de rede não solicitadas. Todas as rotas e conexões são estabelecidas somente para a saída de um dispositivo. Para que um dispositivo receba um comando do back-end, ele deverá iniciar regularmente uma conexão para verificar a existência de qualquer comando pendente a ser processado.
- Geralmente, os dispositivos só estabelecem rotas para serviços bem conhecidos com os quais estão emparelhados, ou só se conectam a eles, como uma instância do serviço Hub IoT.
- O caminho de comunicação entre o dispositivo e o serviço ou o dispositivo e o gateway é protegido na camada de protocolo de aplicativo.
- A autorização e a autenticação no nível do sistema devem se basear em identidades por dispositivo e as credenciais e as permissões de acesso devem ser revogáveis quase que instantaneamente.
- A comunicação bidirecional para dispositivos conectados esporadicamente devido a problemas de energia ou de conectividade pode ser facilitada por meio da retenção de comandos e notificações para os dispositivos até que eles se conectem para obtê-los. O Hub IoT mantém filas específicas de dispositivo para os comandos enviados aos dispositivos.
- Os dados de carga do aplicativo podem ser protegidos separadamente para proteger o tráfego que passa pelos gateways até um serviço específico.

O padrão de comunicação assistida de serviço tem sido usado com êxito no setor de serviços móveis em grande escala para a implementação de serviços de notificação por push, como o [Serviço de Notificação do Windows](https://msdn.microsoft.com/library/windows/apps/mt187203.aspx), o [Google Cloud Messaging](https://developers.google.com/cloud-messaging/) e o [Apple Push Notification Service](http://go.microsoft.com/fwlink/p/?linkid=272584&clcid=0x409).

## Próximas etapas

Para saber mais sobre o Hub IoT do Azure, consulte estes links:

* [Guia do desenvolvedor do Hub IoT]
* [Orientação sobre o Hub IoT]
* [Plataformas e idiomas do dispositivo com suporte]
* [Centro de Desenvolvedores do IoT do Azure]
* [Introdução ao Hub IoT]

[IoT Hub Overview]: iot-hub-what-is-iot-hub.md
[Orientação sobre o Hub IoT]: iot-hub-guidance.md
[Guia do desenvolvedor do Hub IoT]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[Introdução ao Hub IoT]: iot-hub-csharp-csharp-getstarted.md
[Plataformas e idiomas do dispositivo com suporte]: iot-hub-sdks-summary.md#os-platforms-and-hardware-compatibility
[Centro de Desenvolvedores do IoT do Azure]: http://www.azure.com/iotdev

[img-why-use]: media/iot-hub-what-is-iot-hub/image1.png
[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png

[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "Comunicação Assistida de Serviço, postagem de blog feita por Clemens Vasters"

<!---HONumber=Oct15_HO2-->