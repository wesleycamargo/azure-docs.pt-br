<properties
 pageTitle="Habilitar dispositivos gerenciados atrás de um gateway IoT | Microsoft Azure"
 description="Tópico de diretrizes usando um Gateway IoT criado com o SDK do Gateway juntamente com dispositivos gerenciados pelo Hub IoT."
 services="iot-hub"
 documentationCenter=""
 authors="chipalost"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="cstreet"/>
 
# Habilitar dispositivos gerenciados atrás de um gateway IoT

## Isolamento básico de dispositivos

Em geral, as organizações usam gateways IoT para aumentar a segurança geral de suas soluções IoT. Alguns dispositivos precisam enviar dados para a nuvem, mas não conseguem se proteger contra ameaças na Internet. É possível proteger esses dispositivos contra threads externos fazendo com que se comuniquem com o mundo exterior por meio de um gateway.

O gateway está na borda entre um ambiente seguro e a Internet aberta. Os dispositivos se comunicam com o gateway e o gateway transmite as mensagens para o destino de nuvem correto. O gateway está protegido contra threads externos, bloqueia solicitações não autorizadas, permite o tráfego de entrada autorizado e encaminha esse tráfego de entrada para o dispositivo correto.

![][1]

Também é possível colocar os dispositivos que podem se proteger atrás de um gateway para obter uma camada adicional de segurança. Nesse cenário, você precisa apenas manter o sistema operacional do gateway protegido contra as vulnerabilidades mais recentes, em vez de atualizá-lo em cada dispositivo.

## Isolamento mais inteligência

Um roteador protegido é um gateway suficiente para simplesmente isolar dispositivos. No entanto, as soluções IoT geralmente exigem que um gateway forneça mais inteligência do que simplesmente isolar dispositivos. Por exemplo, talvez você queira gerenciar seus dispositivos na nuvem. Você está apto a usar o LWM2M, um protocolo de gerenciamento de dispositivo padrão, para a parte do gerenciamento de nuvem da solução. No entanto, os dispositivos enviam telemetria usando um protocolo não habilitado para TCP/IP. Além disso, os dispositivos geram grandes quantidades de dados e você deseja carregar apenas um subconjunto filtrado da telemetria. É possível criar uma solução que incorpora um gateway IoT capaz de lidar com duas transmissões distintas de dados. O gateway deve:

-   Compreenda a **telemetria**, filtre-a e, em seguida, carregue-a na nuvem por meio do gateway. O gateway não é mais um roteador simples que simplesmente encaminha dados entre o dispositivo e a nuvem.

-   Basta trocar os **dados de gerenciamento de dispositivo LWM2M** entre os dispositivos e a nuvem. O gateway não precisa compreender os dados de entrada; ele só precisa se certificar de que os dados são transmitidos entre os dispositivos e a nuvem.

A figura a seguir ilustra esse cenário:

![][2]

## A solução: gerenciamento de dispositivo IoT do Azure e o SDK do Gateway 

A versão de preview pública do [gerenciamento de dispositivo IoT do Azure][lnk-device-management] e a versão beta do [SDK do Gateway IoT do Azure] permitem este cenário. O gateway trata cada fluxo de dados da seguinte maneira:

-   **Telemetria**: é possível usar o SDK do Gateway para criar um pipeline que compreende, filtra e envia dados telemétricos para a nuvem. O SDK do Gateway fornece um código que implementa partes desse pipeline em nome do desenvolvedor. É possível encontrar mais informações sobre a arquitetura do SDK no tutorial [SDK do Gateway IoT - Introdução][lnk-gateway-get-started].

-   **Gerenciamento de dispositivo**: o gerenciamento de dispositivo do Azure fornece um cliente LWM2M que é executado no dispositivo, bem como uma interface de nuvem para emitir comandos de gerenciamento para o dispositivo.
    
    Não é necessário nenhuma lógica especial no gateway, pois ele não precisa processar os dados do LWM2M trocados entre o dispositivo e o hub IoT. É possível habilitar o compartilhamento de conexão com a Internet, um recurso de vários sistemas operacionais modernos, no gateway para permitir a troca de dados do LWM2M. Você pode escolher um sistema operacional adequado para esse cenário, pois o SDK do gateway dá suporte a uma variedade de sistemas operacionais. Estas são as instruções para habilitar o compartilhamento da conexão com a Internet no [Windows 10] e [Ubuntu], dois dos vários sistemas operacionais com suporte.

A ilustração a seguir mostra a arquitetura de alto nível usada para habilitar esse cenário usando o [gerenciamento de dispositivo IoT do Azure][lnk-device-management] e o [SDK do Gateway IoT do Azure].

![][3]

## Próximas etapas

Para saber mais sobre como usar o SDK do Gateway, veja os tutoriais a seguir:

- [SDK do Gateway IoT - Introdução usando o Linux][lnk-gateway-get-started]
- [SDK do Gateway IoT - enviar mensagens do dispositivo para a nuvem com um dispositivo simulado usando o Linux][lnk-gateway-simulated]

É possível encontrar mais informações sobre o gerenciamento de dispositivo com o Hub IoT lendo a [Visão geral do gerenciamento de dispositivo Hub IoT do Azure][lnk-device-management].

<!-- Images and links -->
[1]: media/iot-hub-gateway-device-management/overview.png
[2]: media/iot-hub-gateway-device-management/manage.png
[SDK do Gateway IoT do Azure]: https://github.com/Azure/azure-iot-gateway-sdk/
[Windows 10]: http://windows.microsoft.com/pt-BR/windows/using-internet-connection-sharing#1TC=windows-7
[Ubuntu]: https://help.ubuntu.com/community/Internet/ConnectionSharing
[3]: media/iot-hub-gateway-device-management/manage_2.png
[lnk-gateway-get-started]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-gateway-simulated]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-device-management]: iot-hub-device-management-overview.md

<!---HONumber=AcomDC_0504_2016-->