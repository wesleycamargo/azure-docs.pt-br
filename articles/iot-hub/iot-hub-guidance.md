<properties
 pageTitle="Tópicos de orientação para o uso Hub IoT do Azure | Microsoft Azure"
 description="Uma coleção de tópicos de orientação para desenvolver soluções que usam o Hub IoT do Azure."
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/29/2015"
 ms.author="elioda"/>

# Diretrizes do Hub IoT do Azure

## Provisionamento de dispositivos

As soluções IoT mantêm as informações de dispositivo em muitos sistemas e armazenamentos diferentes. O [registro de identidade do Hub IoT][IoT Hub Developer Guide - identity registry] é um deles, entre outros armazenamentos que mantêm informações de dispositivo específicos do aplicativo. Chamamos de *provisionamento* o processo de criação das informações sobre o dispositivo necessárias em todos esses sistemas.

Há muitos requisitos e estratégias para o provisionamento de dispositivo (consulte as [diretrizes de gerenciamento de dispositivos do Hub IoT] para saber mais). O [registro de identidade do Hub IoT][IoT Hub Developer Guide - identity registry] fornece as APIs necessárias para integrar o Hub IoT ao processo de provisionamento.

## Gateways de campo

Um gateway de campo é um dispositivo especializado ou um software de finalidade geral que age como um habilitador de comunicação e, possivelmente, como um sistema de controle de dispositivo local e hub de processamento de dados do dispositivo. Um gateway de campo pode executar funções locais de processamento e controle para os dispositivos; por outro lado, ele pode filtrar ou agregar a telemetria de dispositivo e, portanto, reduzir a quantidade de dados transferidos para o back-end.

O escopo de um gateway de campo inclui o próprio gateway de campo e todos os dispositivos conectados a ele. Como o nome sugere, os gateways de campo atuam fora de instalações de processamento de dados dedicadas e costumam ser associados ao local. Um gateway de campo é diferente de um mero roteador de tráfego, pois tem uma função ativa no gerenciamento de fluxo de informações e de acesso. Isso significa que é um terminal de sessão ou de conexão de rede e de entidade endereçada ao aplicativo ou terminal de sessão (por exemplo, os gateways nesse contexto podem auxiliar no provisionamento do dispositivo, na transformação de dados, na conversão de protocolo e no processamento de regras de evento). Os dispositivos NAT ou firewalls, por outro lado, não se qualificam como gateways de campo, pois não são terminais explícitos de conexão ou de sessão, mas em vez disso, roteiam (ou negam) conexões ou sessões feitas por meio deles.

### Gateways de campo transparentes versus opacos

Com relação às identidades de dispositivo, os gateways de campo serão chamados de *transparentes* se outros dispositivos em seu escopo tiverem entradas de identidade do dispositivo no registro de identidade do Hub IoT. Eles serão chamados de *opacos* se a única identidade no registro de identidade do Hub IoT for a identidade do gateway de campo.

É importante observar que o uso de gateways *opacos* impede que o Hub IoT forneça recursos de [anti-falsificação de identidade do dispositivo][IoT Hub Developer Guide - Anti-spoofing]. Além disso, como todos os dispositivos no escopo do gateway de campo são representados como um único dispositivo no Hub IoT, eles estão sujeitos a cotas e a limites como um único dispositivo.

### Outras considerações

Ao implementar um gateway de campo, você poderá usar os [SDKs de dispositivo IoT do Azure][]. Alguns SDKs oferecem funcionalidades específicas ao gateway, como a habilidade de multiplexar a comunicação de vários dispositivos na mesma conexão com o Hub IoT. Como explicado no [Guia do desenvolvedor do Hub IoT - escolhendo seu protocolo de comunicação][], você deve evitar o uso de HTTP/1 como protocolo de transporte para gateways de campo.

## Usando serviços/esquemas personalizados de autenticação de dispositivo

O Hub IoT permite a configuração de credenciais de segurança por dispositivo e controle de acesso por meio do uso do [registro de identidade do dispositivo][IoT Hub Developer Guide - identity registry].

Se uma solução IoT já tiver um investimento considerável em um registro de identidade de dispositivo personalizado e/ou em um esquema de autenticação, ainda poderá usar os outros recursos do Hub IoT por meio da criação de um *serviço de token* para o Hub IoT.

O serviço de token é um serviço de nuvem implantado automaticamente e que usa uma *Política de Acesso Compartilhado* do Hub IoT com permissões **DeviceConnect** para criar tokens no *escopo do dispositivo*.

  ![][img-tokenservice]

Estas são as principais etapas do padrão de serviço do token.

1. Crie uma [Política de Acesso Compartilhado do Hub IoT][IoT Hub Developer Guide - Security] com permissões **DeviceConnect** para seu Hub IoT. Essa política será usada pelo serviço de token para assinar os tokens.
2. Quando um dispositivo quiser acessar o Hub IoT, ele solicitará um token assinado ao serviço de token. O dispositivo pode usar o registro de identidade/esquema de autenticação do dispositivo personalizado.
3. O serviço de token retorna um token, criado de acordo com o [Guia do desenvolvedor do Hub IoT - Segurança][], usando `/devices/{deviceId}` como `resourceURI`, com `deviceId` como o dispositivo que está sendo autenticado.
4. O dispositivo usa o token diretamente com o Hub IoT.

O serviço de token pode definir a expiração do token como desejado. Ao expirar, o Hub IoT servirá a conexão e o dispositivo deverá solicitar um novo token ao serviço de token. Claramente, um tempo de expiração curto aumentará a carga no dispositivo e no serviço de token.

Vale a pena especificar que, para que o dispositivo consiga se conectar, a identidade do dispositivo ainda deverá ser criada no Hub IoT. Isso também significa que o controle de acesso por dispositivo (desabilitando as identidades do dispositivo de acordo com o [Guia do desenvolvedor do Hub IoT - registro de identidade][]), ainda está funcionando, mesmo se o dispositivo se autenticar com um token. Isso reduz a existência de tokens de longa duração.

### Comparação com um gateway personalizado

O padrão do serviço do token é a maneira recomendada de implementar um registro de identidade/ esquema de autenticação personalizado com o Hub IoT, pois permite que o Hub IoT lide com a maior parte do tráfego de solução. No entanto, há casos em que o esquema de autenticação personalizado está tão entremeado com o protocolo (por exemplo, [TLS-PSK][]) que um serviço de processamento de todo o tráfego (*gateway personalizado*) se torna necessário. Consulte o tópico [Gateway do protocolo][] para saber mais.

## Próximas etapas

Para saber mais sobre o Hub IoT do Azure, siga estes links:

- [Introdução aos Hubs IoT (Tutorial)][lnk-get-started]
- [O que é o Hub IoT do Azure?][]

[img-tokenservice]: ./media/iot-hub-guidance/tokenservice.png

[IoT Hub Developer Guide - identity registry]: iot-hub-devguide.md#identityregistry
[Guia do desenvolvedor do Hub IoT - registro de identidade]: iot-hub-devguide.md#identityregistry
[diretrizes de gerenciamento de dispositivos do Hub IoT]: iot-hub-device-management.md
[IoT Hub Developer Guide - Anti-spoofing]: iot-hub-devguide.md#antispoofing
[SDKs de dispositivo IoT do Azure]: iot-hub-sdks-summary.md
[Guia do desenvolvedor do Hub IoT - escolhendo seu protocolo de comunicação]: iot-hub-devguide.md#amqpvshttp
[IoT Hub Developer Guide - Security]: iot-hub-devguide.md#security
[Guia do desenvolvedor do Hub IoT - Segurança]: iot-hub-devguide.md#security
[TLS-PSK]: https://tools.ietf.org/html/rfc4279
[Gateway do protocolo]: iot-hub-protocol-gateway.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[O que é o Hub IoT do Azure?]: iot-hub-what-is-iot-hub.md

<!---HONumber=Oct15_HO3-->