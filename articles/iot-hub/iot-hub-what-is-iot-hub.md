<properties
 pageTitle="Visão geral do Hub IoT do Azure | Microsoft Azure"
 description="Visão geral do serviço Hub IoT do Azure: o que é Hub IoT, conectividade do dispositivo, Internet dos padrões de comunicação e padrão de comunicação assistida de serviços"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/25/2016"
 ms.author="dobett"/>

# O que é o Hub IoT do Azure?

Bem-vindo ao Hub IoT do Azure. Este artigo fornece uma visão geral do Hub IoT do Azure e descreve o motivo pelo qual você deve usar esse serviço para implementar uma solução IoT (Internet das Coisas). O Hub IoT do Azure é um serviço totalmente gerenciado que permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos IoT e um back-end da solução. Hub IoT do Azure:

- Fornece mensagens confiáveis em escala do dispositivo para a nuvem e da nuvem para o dispositivo.
- Permite proteger as comunicações usando credenciais de segurança e controle de acesso por dispositivo.
- Fornece monitoramento abrangente para eventos de gerenciamento de identidade do dispositivo e de conectividade do dispositivo.
- Inclui bibliotecas de dispositivo para as plataformas e idiomas mais populares.

O artigo [Comparação do Hub IoT com os Hubs de Eventos][lnk-compare] descreve as principais diferenças entre esses dois serviços e destaca as vantagens de usar o Hub IoT em suas soluções de IoT.

![Hub IoT do Azure como gateway de nuvem em solução de Internet das coisas][img-architecture]

> [AZURE.NOTE] Para ver uma análise detalhada da arquitetura IoT, consulte a [Arquitetura de Referência do IoT do Microsoft Azure][lnk-refarch].

## Desafios de conectividade do dispositivo IoT

O Hub IoT e as bibliotecas de dispositivo ajudam a enfrentar os desafios de como conectar dispositivos de maneira confiável e segura ao back-end da solução. Dispositivos IoT:

- Com frequência, são sistemas internos sem operadores humanos.
- Podem estar em locais remotos, onde o acesso físico é caro.
- Só podem ser acessados por meio do back-end da solução.
- Podem ter recursos de energia e de processamento limitados.
- Podem ter conectividade de rede intermitente, lenta ou cara.
- Talvez precisem de protocolos de aplicativo proprietários, personalizados ou específicos do setor.
- Podem ser criados usando um grande conjunto de plataformas de hardware e de software conhecidas.

Além dos requisitos acima, todas as soluções IoT também deverão oferecer escala, segurança e confiabilidade. O conjunto de requisitos de conectividade resultante é de implementação difícil e demorada quando você usa tecnologias tradicionais, como contêineres da Web e agentes de mensagens.

## Porque usar o Hub IoT do Azure

O Hub IoT do Azure enfrenta os desafios de conectividade do dispositivo das seguintes maneiras:

-   **Autenticação por dispositivo e conectividade segura**. Você pode fornecer a cada dispositivo sua própria [chave de segurança][lnk-devguide-security] para permitir que ele se conecte ao Hub IoT. O [registro de identidades do Hub IoT][lnk-devguide-identityregistry] armazena as identidades e as chaves dos dispositivos em uma solução. Um back-end da solução pode adicionar dispositivos individuais para permitir ou negar listas para habilitar o controle completo sobre o acesso ao dispositivo.

-   **Monitoramento das operações de conectividade do dispositivo**. Você pode receber os logs de operação detalhados sobre operações de gerenciamento de identidade do dispositivo e eventos de conectividade do dispositivo. Esse recurso de monitoramento permite que sua solução de IoT identifique os problemas de conectividade, como os dispositivos que tentam se conectar com as credenciais erradas, envio de mensagens com muita frequência ou rejeição de todas as mensagens da nuvem para o dispositivo.

-   **Um amplo conjunto de bibliotecas de dispositivos**. [Os SDKs do dispositivo IoT do Azure][lnk-device-sdks] estão disponíveis e têm suporte para várias linguagens e plataformas: C para muitas distribuições Linux, Windows e sistemas operacionais em tempo real. Os SDKs de dispositivo IoT do Azure também oferecem suporte a linguagens gerenciadas, como C#, Java e JavaScript.

-   **Protocolos e extensibilidade do IoT**. Se sua solução não puder usar as bibliotecas de dispositivos, o Hub IoT exporá um protocolo público para permitir que os dispositivos usem os protocolos MQTT v3.1.1, HTTP 1.1 ou AMQP 1.0 nativamente. Você também pode estender o Hub IoT para dar suporte a protocolos personalizados por meio da:

    - Criação de um gateway de campo com o [SDK do Gateway IoT do Azure][lnk-gateway-sdk] que converte seu protocolo personalizado em um dos três protocolos compreendidos pelo Hub IoT.
    - Personalização do [gateway de protocolo IoT do Azure][protocol-gateway], um componente de código aberto que é executado na nuvem.

-   **Escala**. O Hub IoT do Azure pode ser dimensionado para milhões de dispositivos conectados simultaneamente, além de milhões de eventos por segundo.

Esses benefícios são genéricos para vários padrões de comunicação. No momento, o Hub IoT permite que você implemente os seguintes padrões de comunicação específicos:

-   **Ingestão de dispositivo para nuvem baseada em evento.** O Hub IoT pode receber de modo confiável milhões de eventos por segundo dos seus dispositivos. Assim, ele pode processá-los no respectivo afunilamento usando um mecanismo processador de eventos. Ele também pode armazená-los em seu caminho expandido para análise. O Hub IoT retém os dados de eventos por até sete dias para garantir o processamento confiável e para absorver picos na carga.

-   **Mensagens confiáveis da nuvem para o dispositivo (ou *comandos*).** O back-end da solução pode usar o Hub IoT para enviar mensagens com uma garantia de entrega de pelo menos uma vez para dispositivos individuais. Cada mensagem tem uma configuração de vida útil individual, e o back-end pode solicitar recibos de entrega e de expiração. Essas confirmações garantem visibilidade completa do ciclo de vida de uma mensagem da nuvem para o dispositivo. Desse modo, você pode implementar lógica de negócios que inclua operações executadas nos dispositivos.

-   **Carregue arquivos e dados do sensor em cache para a nuvem.** Os dispositivos podem carregar arquivos para o Armazenamento do Azure usando URIs SAS gerenciados para você pelo Hub IoT. O Hub IoT poderá gerar notificações quando os arquivos chegarem na nuvem para permitir que o back-end os processe.

## Gateways

Um gateway em uma solução IoT geralmente é um [gateway de protocolo][lnk-gateway] que é implantado na nuvem ou um [gateway de campo][lnk-field-gateway] que é implantado localmente com seus dispositivos. Um gateway de protocolo executa a conversão do protocolo, por exemplo, de MQTT para AMQP. Um gateway de campo pode executar análises na borda, tomar decisões baseadas em tempo para reduzir a latência, fornecer serviços de gerenciamento de dispositivo, impor restrições de privacidade e segurança e também executar a conversão de protocolo. Ambos os tipos de gateway atuam como intermediários entre os seus dispositivos e o Hub IoT.

Um gateway de campo é diferente de um dispositivo de roteamento de tráfego simples (como um dispositivo conversão de endereços de rede ou firewall), pois geralmente ele executa uma função ativa no gerenciamento de acesso e no fluxo de informações da solução.

A mesma solução pode incluir gateways de protocolo e de campo.

## Como funciona o Hub IoT?

O Hub IoT do Azure implementa o padrão [comunicação assistida de serviço][lnk-service-assisted-pattern] para intermediar as interações entre os dispositivos e o back-end da solução. A meta da comunicação assistida de serviço é estabelecer caminhos de comunicação bidirecional e confiável entre um sistema de controle, como o Hub IoT, e dispositivos de finalidade especial implantados em um espaço físico não confiável. O padrão estabelece os seguintes princípios:

- A segurança tem precedência sobre todos os outros recursos.
- Os dispositivos não aceitam informações de rede não solicitadas. Um dispositivo estabelece todas as conexões e rotas de forma apenas de saída. Para que um dispositivo receba um comando do back-end, ele deverá iniciar regularmente uma conexão para verificar a existência de qualquer comando pendente a ser processado.
- Os dispositivos devem apenas se conectar a rotas ou estabelecê-las para serviços conhecidos com os quais eles estejam emparelhados, como o Hub IoT.
- O caminho de comunicação entre o dispositivo e o serviço ou entre o dispositivo e o gateway é protegido na camada de protocolo do aplicativo.
- A autenticação e a autorização no nível de sistema são baseadas nas identidades por dispositivo. Elas tornam as permissões e credenciais de acesso quase instantaneamente revogáveis.
- A comunicação bidirecional para dispositivos que se conectam esporadicamente devido a problemas com energia ou conectividade é facilitada pela retenção de comandos e de notificações de dispositivo até um dispositivo se conectar para recebê-los. O Hub IoT mantém filas específicas de dispositivo para os comandos enviados.
- Os dados de carga do aplicativo são protegidos separadamente para proteger o tráfego que passa pelos gateways até um serviço específico.

O setor de dispositivos móveis tem usado o padrão de comunicação assistida de serviço em enorme escala para implementar os serviços de notificação por push, como os [Serviços de Notificação por Push do Windows][lnk-wns], o [Google Cloud Messaging][lnk-google-messaging] e o [Apple Push Notification Service][lnk-apple-push].

## Próximas etapas

Para saber como o Hub IoT do Azure permite o gerenciamento de dispositivo IoT baseado em padrões para que você possa gerenciar, configurar e atualizar os dispositivos remotamente, confira [Visão geral do gerenciamento de dispositivos do Hub IoT do Azure][lnk-device-management].

Para implementar aplicativos cliente em uma grande variedade de sistemas operacionais e plataformas de hardware do dispositivo, você pode usar os SDKs do dispositivo IoT. Os SDKs do dispositivo IoT incluem bibliotecas que facilitam o envio de telemetria para um hub IoT e o recebimento de comandos da nuvem para o dispositivo. Ao usar os SDKs, você poderá escolher entre vários protocolos de rede para comunicar-se com o Hub IoT. Para saber mais, confira as [informações sobre SDKs de dispositivo][lnk-device-sdks].

Para começar a escrever código e executar alguns exemplos, confira o tutorial [Introdução ao Hub IoT][lnk-get-started].

[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png


[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[protocol-gateway]: https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md
[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "Comunicação Assistida de Serviço, postagem de blog feita por Clemens Vasters"
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-gateway]: iot-hub-protocol-gateway.md
[lnk-field-gateway]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-devguide-identityregistry]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-wns]: https://msdn.microsoft.com/library/windows/apps/mt187203.aspx
[lnk-google-messaging]: https://developers.google.com/cloud-messaging/
[lnk-apple-push]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk
[lnk-device-management]: iot-hub-device-management-overview.md

<!---HONumber=AcomDC_1005_2016-->