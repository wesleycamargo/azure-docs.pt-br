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
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 37f9da7dcc8dd527fe0bfbf2fbcc40a3ba0e8a1c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="azure-iot-hub-developer-guide"></a>Guia do desenvolvedor do Hub IoT do Azure

O Hub IoT do Azure é um serviço totalmente gerenciado que ajuda a permitir comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos e um back-end de solução.

O Hub IoT do Azure oferece:

* Proteja as comunicações usando as credenciais de segurança e o controle de acesso por dispositivo.
* Várias opções de comunicação em larga escala do dispositivo para a nuvem e da nuvem para o dispositivo.
* Armazenamento que podem ser consultado com informações de estado por dispositivo e metadados.
* Facilitar a conectividade do dispositivo com as bibliotecas de dispositivo a fim de obter as plataformas e os idiomas mais populares.

Este guia do desenvolvedor do Hub IoT inclui os seguintes artigos:

* [Orientação de comunicação do dispositivo para a nuvem][lnk-d2c-guidance] ajuda você a escolher entre mensagens do dispositivo para a nuvem, propriedades reportadas do dispositivo gêmeo ou carregamento do arquivo.
* [Orientação de comunicação da nuvem para o dispositivo][lnk-c2d-guidance] ajuda você a escolher entre os métodos diretos, as propriedades desejadas do dispositivo gêmeo e as mensagens da nuvem para o dispositivo.
* [Mensagens de dispositivo para a nuvem e nuvem para o dispositivo com Hub IoT][devguide-messaging] descreve os recursos de mensagens (dispositivo para a nuvem e nuvem para o dispositivo) que o Hub IoT expõe.
  * [Enviar mensagens de dispositivo para a nuvem para o Hub IoT][devguide-messages-d2c].
  * [Ler mensagens de dispositivo para a nuvem do ponto de extremidade interno][devguide-builtin].
  * [Utilizar pontos de extremidade personalizados e regras de roteamento para mensagens de dispositivo para a nuvem][devguide-custom].
  * [Enviar mensagens de nuvem para o dispositivo do Hub IoT][devguide-messages-c2d].
  * [Criar e ler mensagens do Hub IoT][devguide-format].
* [Carregar arquivos de um dispositivo][devguide-upload] descreve como você pode carregar arquivos de um dispositivo. O artigo também inclui informações sobre tópicos como as notificações que podem ser enviadas pelo processo de carregamento.
* [Gerenciar identidades do dispositivo no Hub IoT][devguide-identities] descreve as informações armazenadas pelo registro de identidade de cada Hub IoT. O artigo também descreve como você pode acessar e modificá-lo.
* [Controlar o acesso ao Hub IoT][devguide-security] descreve o modelo de segurança usado para conceder acesso à funcionalidade do Hub IoT para componentes de dispositivos e da nuvem. O artigo inclui informações sobre como usar tokens e certificados X.509, e os detalhes das permissões que você pode conceder.
* [Usar dispositivos gêmeos para sincronizar o estado e as configurações][devguide-device-twins] descreve o conceito de *dispositivos gêmeos*. O artigo também descreve a funcionalidade que dispositivos gêmeos expõem, como a sincronização de um dispositivo com seus dispositivos gêmeos. O artigo inclui informações sobre os dados armazenados em um dispositivo gêmeo.
* [Invocar um método direto em um dispositivo][devguide-directmethods] descreve o ciclo de vida de um método direto. O artigo descreve como invocar métodos em um dispositivo de seu aplicativo de back-end e manipular o método direto no seu dispositivo.
* [Agendar trabalhos em vários dispositivos][devguide-jobs] descreve como você pode agendar trabalhos em vários dispositivos. O artigo descreve como enviar trabalhos que executam tarefas, por exemplo, execução de um método direto, atualização de um dispositivo usando um dispositivo gêmeo. Ele também descreve como consultar o status de um trabalho.
* [Referência - escolher um protocolo de comunicação][devguide-protocol] descreve os protocolos de comunicação que o Hub IoT dá suporte para comunicação de dispositivo e lista as portas que devem ser abertas.
* [Referência - Pontos de extremidade do Hub IoT][devguide-endpoints] descreve os vários pontos de extremidade que cada Hub IoT expõe para operações de tempo de execução e de gerenciamento. O artigo também descreve como você pode criar mais pontos de extremidade adicionais em seu Hub IoT e como usar um gateway de campo para habilitar a conectividade para seus pontos de extremidade de Hub IoT em cenários não padrão.
* [Referência - Linguagem de consulta do Hub IoT para dispositivos gêmeos, trabalhos e roteamento de mensagens][devguide-query] descreve que a linguagem de consulta do Hub IoT permite que você recupere informações do seu Hub sobre seus dispositivos gêmeos e trabalhos.
* [Referência – cotas e limitação][devguide-quotas] resume as cotas definidas no serviço do Hub IoT e a limitação que ocorre ao exceder a cota.
* [Referência - preços][devguide-pricing] fornece informações gerais sobre os diferentes SKUs e preços para o Hub IoT e detalhes sobre como as várias funcionalidades do Hub IoT são medidas como mensagens pelo Hub IoT.
* [Referência – SDKs de dispositivo e serviço][devguide-sdks] lista os SDKs do IoT do Azure para o desenvolvimento de aplicativos de dispositivo e de serviço que interagem com o Hub IoT. O artigo inclui links para documentação online da API.
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
[devguide-messages-d2c]: iot-hub-devguide-messages-d2c.md
[devguide-builtin]: iot-hub-devguide-messages-read-builtin.md
[devguide-custom]: iot-hub-devguide-messages-read-custom.md
[devguide-messages-c2d]: iot-hub-devguide-messages-c2d.md
[devguide-format]: iot-hub-devguide-messages-construct.md
[devguide-protocol]: iot-hub-devguide-protocols.md
