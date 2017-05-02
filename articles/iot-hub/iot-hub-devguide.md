---
title: Guia do desenvolvedor para o Hub IoT do Azure | Microsoft Docs
description: "O guia do desenvolvedor do Hub IoT do Azure aborda os pontos de extremidade, segurança, Registro de identidade, gerenciamento de dispositivos, métodos diretos, dispositivos gêmeos, uploads de arquivos, trabalhos, linguagem de consulta do Hub IoT e mensagens."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: d534ff9d-2de5-4995-bb2d-84a02693cb2e
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/02/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 1915044f252984f6d68498837e13c817242542cf
ms.openlocfilehash: 4f12a4b3ba9c1d0b7ed10cf5d766bcea60205d24
ms.lasthandoff: 01/31/2017


---
# <a name="azure-iot-hub-developer-guide"></a>Guia do desenvolvedor do Hub IoT do Azure
O Hub IoT do Azure é um serviço totalmente gerenciado que ajuda a permitir comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos e um back-end de solução.

O Hub IoT do Azure oferece:

* Proteja as comunicações usando as credenciais de segurança e o controle de acesso por dispositivo.
* Várias opções de comunicação em larga escala do dispositivo para a nuvem e da nuvem para o dispositivo.
* Armazenamento que podem ser consultado com informações de estado por dispositivo e metadados.
* Facilitar a conectividade do dispositivo com as bibliotecas de dispositivo a fim de obter as plataformas e os idiomas mais populares.

Este guia do desenvolvedor do Hub IoT inclui os seguintes artigos:

* [Enviar e receber mensagens com o Hub IoT][devguide-messaging] descreve os recursos de sistema de mensagens (do dispositivo para a nuvem e da nuvem para o dispositivo) expostos pelo Hub IoT. O artigo também inclui informações sobre tópicos, como roteamento de mensagens, formatos de mensagem e os protocolos de comunicação com suporte, além dos números de porta que eles usam.
* [Orientação de comunicação do dispositivo para a nuvem][lnk-d2c-guidance] ajuda você a escolher entre mensagens do dispositivo para a nuvem, propriedades reportadas do dispositivo gêmeo ou carregamento do arquivo.
* [Orientação de comunicação da nuvem para o dispositivo][lnk-c2d-guidance] ajuda você a escolher entre os métodos diretos, as propriedades desejadas do dispositivo gêmeo e as mensagens da nuvem para o dispositivo.
* [Carregar arquivos de um dispositivo][devguide-upload] descreve como você pode carregar arquivos de um dispositivo. O artigo também inclui informações sobre tópicos como as notificações que podem ser enviadas pelo processo de carregamento.
* [Gerenciar identidades do dispositivo no Hub IoT][devguide-identities] descreve as informações são armazenadas pelo registro de identidade de cada Hub IoT, e como elas podem ser acessadas e modificadas.
* [Controlar o acesso ao Hub IoT][devguide-security] descreve o modelo de segurança usado para conceder acesso à funcionalidade do Hub IoT para componentes de dispositivos e da nuvem. O artigo inclui informações sobre como usar tokens e certificados X.509, e os detalhes das permissões que você pode conceder.
* [Usar dispositivo gêmeos para sincronizar o estado e as configurações][devguide-device-twins] descreve o conceito de *dispositivo gêmeo* e a funcionalidade que ele expõe, por exemplo, sincronizar um dispositivo com seu dispositivo gêmeo. O artigo inclui informações sobre os dados armazenados em um dispositivo gêmeo.
* [Invocar um método direto em um dispositivo][devguide-directmethods] descreve o ciclo de vida de um método direto, informações sobre como invocar métodos em um dispositivo de seu aplicativo back-end e manipular o método direto em seu dispositivo.
* [Agendar trabalhos em vários dispositivos][devguide-jobs] descreve como você pode agendar trabalhos em vários dispositivos. O artigo descreve como enviar trabalhos que executam tarefas, por exemplo, execução de um método direto, atualização de um dispositivo usando um dispositivo gêmeo. Ele também descreve como consultar o status de um trabalho.
* [Referência - Pontos de extremidade do Hub IoT][devguide-endpoints] descreve os vários pontos de extremidade que cada Hub IoT expõe para operações de tempo de execução e de gerenciamento. O artigo também descreve como você pode criar mais pontos de extremidade em seu Hub IoT e como usar um gateway de campo para habilitar a conectividade de dispositivos para seus pontos de extremidade de Hub IoT em cenários não padrão.
* [Referência - Linguagem de consulta do Hub IoT para dispositivos gêmeos e trabalhos][devguide-query] descreve a linguagem de consulta do Hub IoT que permite a recuperação de informações de seu hub sobre dispositivos gêmeos e trabalhos.
* [Referência - Cotas e limitação][devguide-quotas] resume as cotas definidas no serviço do Hub IoT e o comportamento de limitação que você pode esperar ao exceder a cota.
* [Referência - preços][devguide-pricing] fornece informações gerais sobre os diferentes SKUs e preços para o Hub IoT e detalhes sobre como as várias funcionalidades do Hub IoT são medidas como mensagens pelo Hub IoT.
* [Referência – SDKs de dispositivo e serviço][devguide-sdks] lista os SDKs do IoT do Azure que você pode usar no desenvolvimento de aplicativos de dispositivo e de serviço que interagem com o hub IoT. O artigo inclui links para documentação online da API.
* [Referência - Suporte ao MQTT do Hub IoT][devguide-mqtt] fornece informações detalhadas sobre como o Hub IoT oferece suporte ao protocolo MQTT. O artigo descreve o suporte para o protocolo MQTT interno para os SDKs do IoT do Azure e fornece informações sobre como usar o protocolo MQTT diretamente.
* [Glossário][devguide-glossary] uma lista de termos comuns relacionados ao Hub IoT.

[devguide-messaging]: iot-hub-devguide-messaging.md
[devguide-upload]: iot-hub-devguide-file-upload.md
[devguide-identities]: iot-hub-devguide-identity-registry.md
[devguide-security]: iot-hub-devguide-security.md
[devguide-device-twins]: iot-hub-devguide-device-twins.md
[devguide-directmethods]: iot-hub-devguide-direct-methods.md
[devguide-jobs]: iot-hub-devguide-jobs.md
[devguide-endpoints]: iot-hub-devguide-endpoints.md
[devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[devguide-query]: iot-hub-devguide-query-language.md
[devguide-sdks]: iot-hub-devguide-sdks.md
[devguide-mqtt]: iot-hub-mqtt-support.md
[devguide-glossary]: iot-hub-devguide-glossary.md
[devguide-pricing]: iot-hub-devguide-pricing.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md


