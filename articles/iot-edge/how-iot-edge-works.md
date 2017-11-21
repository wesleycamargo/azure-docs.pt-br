---
title: "O que é Azure IoT Edge | Microsoft Docs"
description: "Visão geral do serviço Azure IoT Edge"
services: iot-Edge
documentationcenter: 
author: kgremban
manager: timlt
editor: chipalost
ms.assetid: 
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: kgremban
ms.custom: 
ms.openlocfilehash: 5f69041572729d1458a22a855128639056d61586
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="what-is-azure-iot-edge---preview"></a>O que é Azure IoT Edge - versão prévia

O Azure IoT Edge move análises de nuvem e lógica de negócios personalizada para dispositivos, de modo que sua organização pode se concentrar em ideias de negócios em vez de gerenciamento de dados. Faça com que a sua solução seja realmente dimensionada configurando o seu software de IoT, implantando-o em dispositivos por meio de contêineres padrão e monitorando tudo pela nuvem.

A análise impulsiona o valor comercial em soluções de IoT, mas nem todas as análise precisam estar na nuvem. Se desejar que um dispositivo responda em caso de emergência assim que possível, você pode executar a detecção de anomalias no próprio dispositivo. Da mesma forma, se você deseja reduzir os custos de largura de banda e evitar a transferência de terabytes de dados brutos, você pode executar a limpeza de dados e agregação localmente. Em seguida, envie as informações para a nuvem. 

O Azure IoT Edge é composto de três componentes:
* Os módulos do IoT Edge são contêineres que executam serviços do Azure, serviços de terceiros ou o seu próprio código. Eles são implantados nos dispositivos do IoT Edge e são executados localmente nesses dispositivos. 
* O tempo de execução do IoT Edge é executado em cada dispositivo IoT Edge e gerencia os módulos implantados em cada dispositivo. 
* Uma interface baseada em nuvem permite monitorar e gerenciar dispositivos do IoT Edge remotamente.

## <a name="iot-edge-modules"></a>Módulos do IoT Edge

Os módulos do IoT Edge são unidades de execução, atualmente implementados como contêineres compatíveis do Docker, que executam a lógica de negócios na borda. Vários módulos podem ser configurados para se comunicar entre si, criando um pipeline de processamento de dados. Você pode desenvolver módulos personalizados ou empacotar determinados serviços do Azure em módulos que fornecem informações offline e na borda. 

### <a name="artificial-intelligence-on-the-edge"></a>Inteligência Artificial na borda

O Azure IoT Edge permite implantar processamento de eventos complexos, aprendizado de máquina, reconhecimento de imagem e outras inteligências artificiais de alto valor sem gravar internamente. Serviços do Azure como o Azure Functions, o Azure Stream Analytics e o Azure Machine Learning podem todos ser executados localmente por meio do Azure IoT Edge; no entanto você não está limitado aos serviços do Azure. Qualquer pessoa é capaz de criar os módulos de IA e disponibilizá-los para uso pela comunidade. 

### <a name="bring-your-own-code"></a>Traga o seu próprio código

Quando você deseja implantar seu próprio código para seus dispositivos, o Azure IoT Edge também oferece suporte para essa situação. O Azure IoT Edge mantém o mesmo modelo de programação de outros serviços Azure IoT. O mesmo código pode ser executado em um dispositivo ou na nuvem. O Azure IoT Edge oferece suporte para Linux e Windows, portanto você pode codificar para a plataforma de sua escolha. Ele oferece suporte para Java, .NET Core 2.0, Node. js, C e Python para que os desenvolvedores possam codificar na linguagem que já conhecem e usar a lógica de negócios existentes sem gravá-la a partir do zero.

## <a name="iot-edge-runtime"></a>Tempo de execução do IoT Edge

O tempo de execução do Azure IoT Edge permite lógica personalizada e de nuvem em dispositivos IoT Edge. Ele se encontra no dispositivo IoT Edge e executa operações de gerenciamento e comunicação. O tempo de execução executa várias funções:

* Instala e atualiza as cargas de trabalho no dispositivo.
* Mantém os padrões de segurança do Azure IoT Edge no dispositivo.
* Garante que os módulos do IoT Edge sempre estão em execução.
* Fornece um relatório sobre a integridade do módulo para a nuvem para o monitoramento remoto.
* Facilita a comunicação entre os dispositivos de folha de downstream e o dispositivo IoT Edge.
* Facilita a comunicação entre os módulos e o dispositivo IoT Edge.
* Facilita a comunicação entre o dispositivo IoT Edge e a nuvem.

![O tempo de execução do IoT Edge envia informações e relatórios para o Hub IoT][1]

Como você usa um dispositivo Azure IoT Edge cabe completamente a você. O tempo de execução geralmente é usado para implantar IA para gateways que agregam e processam dados de vários outros dispositivos locais, no entanto, isso é apenas uma opção. Os dispositivos de folha também podem ser dispositivos do Azure IoT Edge, independentemente deles estarem conectados a um gateway ou diretamente na nuvem.

O tempo de execução do Azure IoT Edge é executado em um grande conjunto de dispositivos IoT habilitado usando o tempo de execução de várias maneiras diferentes. Ele oferece suporte a sistemas operacionais Windows e Linux, bem como abstrai detalhes de hardware. Use um dispositivo menor do que um Raspberry Pi 3 se você não estiver processando muitos dados ou escale verticalmente para um servidor industrializado para executar cargas de trabalho com muitos recursos.

## <a name="iot-edge-cloud-interface"></a>Interface de nuvem do IoT Edge

Gerenciar o ciclo de vida de software para dispositivos corporativos é complicado. Gerenciar o ciclo de vida de software para milhões de dispositivos de IoT heterogêneos é ainda mais difícil. As cargas de trabalho devem ser criadas e configuradas para um tipo específico de dispositivo, implantadas na escala de milhões de dispositivos em sua solução e monitoradas para detectar todos os dispositivos com comportamento inadequado. Essas atividades não podem ser feitas por dispositivo e devem ser feitas em escala.

O Azure IoT Edge se integra perfeitamente com o Azure IoT Suite para fornecer um plano de controle para as necessidades da sua solução. Os serviços de nuvem permitem aos usuários:

* Criar e configurar uma carga de trabalho a ser executado em um tipo específico de dispositivo.
* Enviar uma carga de trabalho para um conjunto de dispositivos.
* Monitorar cargas de trabalho que estão sendo executadas em dispositivos em campo.

![Coordenar a telemetria, informações e ações de dispositivos com a nuvem][2]

## <a name="next-steps"></a>Próximas etapas

Experimente esses conceitos [Implantando o IoT Edge em um dispositivo simulado][lnk-quickstart].

<!-- Images -->
[1]: ./media/how-iot-edge-works/runtime.png
[2]: ./media/how-iot-edge-works/cloud-interface.png

<!-- Links -->
[lnk-quickstart]: quickstart.md